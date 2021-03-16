---
title: .NET 中的选项模式
author: IEvangelist
description: 了解如何使用选项模式来表示 .NET 应用中的相关设置组。
ms.author: dapine
ms.date: 02/18/2021
ms.openlocfilehash: df30928cdb1832e94f89abbdf8cc41e1304f8e2e
ms.sourcegitcommit: bdbf6472de867a0a11aaa5b9384a2506c24f27d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102401982"
---
# <a name="options-pattern-in-net"></a>.NET 中的选项模式

选项模式使用类来提供对相关设置组的强类型访问。 当[配置设置](configuration.md)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：

- [接口分隔原则 (ISP) 或封装](../../architecture/modern-web-apps-azure/architectural-principles.md#encapsulation)：依赖于配置设置的方案（类）仅依赖于其使用的配置设置。
- [关注点分离](../../architecture/modern-web-apps-azure/architectural-principles.md#separation-of-concerns)：应用的不同部件的设置不彼此依赖或相互耦合。

选项还提供验证配置数据的机制。 有关详细信息，请参阅[选项验证](#options-validation)部分。

## <a name="bind-hierarchical-configuration"></a>绑定分层配置

读取相关配置值的首选方法是使用选项模式。 选项模式可以通过 <xref:Microsoft.Extensions.Options.IOptions%601> 接口实现，其中泛型类型参数 `TOptions` 被约束为 `class`。 以后可以通过依赖关系注入来提供 `IOptions<TOptions>`。 有关详细信息，请参阅 [.NET 中的依赖关系注入](dependency-injection.md)。

例如，若要读取以下配置值，请执行以下操作：

:::code language="json" source="snippets/configuration/console-json/appsettings.json" range="3-6":::

创建以下 `TransientFaultHandlingOptions` 类：

:::code language="csharp" source="snippets/configuration/console-json/TransientFaultHandlingOptions.cs" range="5-9":::

<span id="options-class"></span>在使用选项模式时，选项类：

- 必须是非抽象类，有一个公共无参数构造函数
- 包含要绑定的公共读写属性（字段不绑定）

下面的代码：

* 调用 [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A) 将 `TransientFaultHandlingOptions` 类绑定到 `"TransientFaultHandlingOptions"` 部分。
* 显示配置数据。

:::code language="csharp" source="snippets/configuration/console-json/Program.cs" range="31-38":::

在上面的代码中，已读取在应用启动后对 JSON 配置文件所做的更改。

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get%2A) 绑定并返回指定的类型。 使用 `ConfigurationBinder.Get<T>` 可能比使用 `ConfigurationBinder.Bind` 更方便。 下面的代码演示如何将 `ConfigurationBinder.Get<T>` 与 `TransientFaultHandlingOptions` 类配合使用：

```csharp
IConfigurationRoot configurationRoot = configuration.Build();

var options =
    configurationRoot.GetSection(nameof(TransientFaultHandlingOptions))
                     .Get<TransientFaultHandlingOptions>();

Console.WriteLine($"TransientFaultHandlingOptions.Enabled={options.Enabled}");
Console.WriteLine($"TransientFaultHandlingOptions.AutoRetryDelay={options.AutoRetryDelay}");
```

在上面的代码中，已读取在应用启动后对 JSON 配置文件所做的更改。

> [!IMPORTANT]
> <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 类公开了几个 API，例如不被约束为 `class` 的 `.Bind(object instance)` 和 `.Get<T>()`。 使用任何一个[选项接口](#options-interfaces)时，都必须遵守前面提到的[选项类约束](#options-class)。

使用选项模式时的另一种方法是绑定 `"TransientFaultHandlingOptions"` 部分，并将其添加到[依赖关系注入服务容器](dependency-injection.md)中。 在以下代码中，`TransientFaultHandlingOptions` 已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure%2A> 被添加到了服务容器并已绑定到了配置：

```csharp
services.Configure<TransientFaultHandlingOptions>(
    configurationRoot.GetSection(
        key: nameof(TransientFaultHandlingOptions)));
```

> [!TIP]
> `key` 参数是要搜索的配置部分的名称。 它不必与代表它的类型名称相匹配。 例如，你可以有一个名为 `"FaultHandling"` 的部分，该部分可以由 `TransientFaultHandlingOptions` 类来表示。 在这种情况下，可以改为将 `"FaultHandling"` 传递到 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A> 函数。 在命名部分与其对应的类型相匹配时，为了方便，使用 `nameof` 运算符。

通过使用前面的代码，以下代码将读取位置选项：

:::code language="csharp" source="snippets/configuration/console-json/ExampleService.cs":::

在上面的代码中，不会读取在应用启动后对 JSON 配置文件所做的更改。 若要读取在应用启动后的更改，请使用 [IOptionsSnapshot](#use-ioptionssnapshot-to-read-updated-data)。

## <a name="options-interfaces"></a>选项接口

<xref:Microsoft.Extensions.Options.IOptions%601>:

- 不支持：
  - 在应用启动后读取配置数据。
  - [命名选项](#named-options-support-using-iconfigurenamedoptions)
- 注册为[单一实例](dependency-injection.md#singleton)且可以注入到任何[服务生存期](dependency-injection.md#service-lifetimes)。

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:

- 对于在[一定范围内或暂时生存期](dependency-injection.md#service-lifetimes)中应对每个注入解析重新计算选项的场景，它非常有用。 有关详细信息，请参阅[使用 IOptionsSnapshot 读取已更新的数据](#use-ioptionssnapshot-to-read-updated-data)。
- 注册为[范围内](dependency-injection.md#scoped)，因此无法注入到单一实例服务。
- 支持[命名选项](#named-options-support-using-iconfigurenamedoptions)

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:

- 用于检索选项并管理 `TOptions` 实例的选项通知。
- 注册为[单一实例](dependency-injection.md#singleton)且可以注入到任何[服务生存期](dependency-injection.md#service-lifetimes)。
- 支持：
  - 更改通知
  - [命名选项](#named-options-support-using-iconfigurenamedoptions)
  - [可重载配置](#use-ioptionssnapshot-to-read-updated-data)
  - 选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

<xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。 它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory%601.Create%2A> 方法。 默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。 它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601.TryRemove%2A>)。 可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601.TryAdd%2A> 手动引入值。 在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601.Clear%2A> 方法。

### <a name="options-interfaces-benefits"></a>选项接口优点

使用泛型包装器类型，可以将选项的生存期从 DI 容器中解耦出来。 <xref:Microsoft.Extensions.Options.IOptions%601.Value?displayProperty=nameWithType> 接口对选项类型提供了一个抽象层，包括泛型约束。 这提供了以下好处：

- `T` 配置实例的评估推迟到在访问 <xref:Microsoft.Extensions.Options.IOptions%601.Value?displayProperty=nameWithType> 时进行，而不是在注入时进行。 这一点很重要，因为你可以从各种不同的位置使用 `T` 选项，并选择生存期语义，而无需更改关于 `T` 的任何内容。
- 注册 `T` 类型的选项时，不需要显式注册 `T` 类型。 如果你使用简单的默认值[创建库](options-library-authors.md)，并且不想强制调用方将选项注册到具有特定生存期的 DI 容器中，这可以带来很多便利。
- 从 API 的角度来看，它允许对类型 `T` 进行约束（在本例中，`T` 被约束为引用类型）。

## <a name="use-ioptionssnapshot-to-read-updated-data"></a>使用 IOptionsSnapshot 读取已更新的数据

当你使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 时，在访问每个请求时会计算一次选项，并在请求的生存期内缓存这些选项。 当使用支持读取已更新的配置值的配置提供程序时，将在应用启动后读取对配置所做的更改。

`IOptionsMonitor` 和 `IOptionsSnapshot` 之间的区别在于：

- `IOptionsMonitor` 是一种[单一示例服务](dependency-injection.md#singleton)，可随时检索当前选项值，这在单一实例依赖项中尤其有用。
- `IOptionsSnapshot` 是一种[作用域服务](dependency-injection.md#scoped)，并在构造 `IOptionsSnapshot<T>` 对象时提供选项的快照。 选项快照旨在用于暂时性和有作用域的依赖项。

以下代码使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。

:::code language="csharp" source="snippets/configuration/console-json/ScopedService.cs":::

以下代码注册 `TransientFaultHandlingOptions` 绑定的配置实例：

```csharp
services.Configure<TransientFaultHandlingOptions>(
    configurationRoot.GetSection(
        nameof(TransientFaultHandlingOptions)));
```

在上面的代码中，已读取在应用启动后对 JSON 配置文件所做的更改。

## <a name="ioptionsmonitor"></a>IOptionsMonitor

以下代码注册 `TransientFaultHandlingOptions` 绑定的配置实例。

```csharp
services.Configure<TransientFaultHandlingOptions>(
    configurationRoot.GetSection(
        nameof(TransientFaultHandlingOptions)));
```

下面的示例使用 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>：

:::code language="csharp" source="snippets/configuration/console-json/MonitorService.cs":::

在上面的代码中，已读取在应用启动后对 JSON 配置文件所做的更改。

## <a name="named-options-support-using-iconfigurenamedoptions"></a>命名选项支持使用 IConfigureNamedOptions

命名选项：

- 当多个配置节绑定到同一属性时有用。
- 区分大小写。

请考虑使用以下 appsettings.json 文件：

```json
{
  "Features": {
    "Personalize": {
      "Enabled": true,
      "ApiKey": "aGEgaGEgeW91IHRob3VnaHQgdGhhdCB3YXMgcmVhbGx5IHNvbWV0aGluZw=="
    },
    "WeatherStation": {
      "Enabled": true,
      "ApiKey": "QXJlIHlvdSBhdHRlbXB0aW5nIHRvIGhhY2sgdXM/"
    }
  }
}
```

下面的类用于每个节，而不是创建两个类来绑定 `Features:Personalize` 和 `Features:WeatherStation`：

```csharp
public class Features
{
    public const string Personalize = nameof(Personalize);
    public const string WeatherStation = nameof(WeatherStation);

    public bool Enabled { get; set; }
    public string ApiKey { get; set; }
}
```

下面的代码将配置命名选项：

```csharp
ConfigureServices(services =>
{
    services.Configure<Features>(
        Features.Personalize,
        Configuration.GetSection("Features:Personalize"));

    services.Configure<Features>(
        Features.WeatherStation,
        Configuration.GetSection("Features:WeatherStation"));
});
```

下面的代码将显示命名选项：

```csharp
public class Service
{
    private readonly Features _personalizeFeature;
    private readonly Features _weatherStationFeature;

    public Service(IOptionsSnapshot<Features> namedOptionsAccessor)
    {
        _personalizeFeature = namedOptionsAccessor.Get(Features.Personalize);
        _weatherStationFeature = namedOptionsAccessor.Get(Features.WeatherStation);
    }
}
```

所有选项都是命名实例。 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向 `Options.DefaultName` 实例，即 `string.Empty`。 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。 <xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。 `null` 命名选项用于面向所有命名实例，而不是某一特定命名实例。 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll%2A> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll%2A> 使用此约定。

## <a name="optionsbuilder-api"></a>OptionsBuilder API

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。 `OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。 选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。

`OptionsBuilder` 在[选项验证](#options-validation)部分中使用。

## <a name="use-di-services-to-configure-options"></a>使用 DI 服务配置选项

在配置选项时，可以通过以下两种方式通过依赖关系注入访问服务：

- 将配置委托传递给 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder%601) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder%601.Configure%2A)。 `OptionsBuilder<TOptions>` 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder%601.Configure%2A) 的重载，该重载允许使用最多五个服务来配置选项：

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<ExampleService, ScopedService, MonitorService>(
          (options, es, ss, ms) =>
              options.Property = DoSomethingWith(es, ss, ms));
  ```

- 创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的类型，并将该类型注册为服务。

建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder%601.Configure%2A)，因为创建服务较复杂。 在调用 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder%601.Configure%2A) 时，创建类型等效于框架执行的操作。 调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder%601.Configure%2A) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。

## <a name="options-validation"></a>选项验证

通过选项验证，可以验证选项值。

请考虑使用以下 appsettings.json 文件：

```json
{
  "Settings": {
    "SiteTitle": "Amazing docs from Awesome people!",
    "Scale": 10,
    "VerbosityLevel": 32
  }
}
```

下面的类绑定到 `"Settings"` 配置节，并应用若干 `DataAnnotations` 规则：

:::code language="csharp" source="snippets/configuration/console-json/SettingsOptions.cs":::

下面的代码：

- 调用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> 以获取绑定到 `SettingsOptions` 类的 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder%601)。
- 调用 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> 以使用 `DataAnnotations` 启用验证。

```csharp
services.AddOptions<SettingsOptions>()
    .Bind(Configuration.GetSection(SettingsOptions.Settings))
    .ValidateDataAnnotations();
```

`ValidateDataAnnotations` 扩展方法在 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet 包中定义。

下面的代码显示配置值或验证错误：

:::code language="csharp" source="snippets/configuration/console-json/ValidationService.cs":::

下面的代码使用委托应用更复杂的验证规则：

```csharp
services.AddOptions<SettingsOptions>()
    .Bind(Configuration.GetSection(SettingsOptions.Settings))
    .ValidateDataAnnotations()
    .Validate(config =>
    {
        if (config.Scale != 0)
        {
            return config.VerbosityLevel > config.Scale;
        }

        return true;
    }, "VerbosityLevel must be > than Scale.");
```

### <a name="ivalidateoptions-for-complex-validation"></a>用于复杂验证的 IValidateOptions

下面的类实现了 <xref:Microsoft.Extensions.Options.IValidateOptions%601>：

:::code language="csharp" source="snippets/configuration/console-json/ValidateSettingsOptions.cs":::

`IValidateOptions` 允许将验证代码移入类中。

使用前面的代码，使用以下代码在 `ConfigureServices` 中启用验证：

```csharp
services.Configure<SettingsOptions>(
    Configuration.GetSection(
        SettingsOptions.Settings));
services.TryAddEnumerable(
    ServiceDescriptor.Singleton
        <IValidateOptions<SettingsOptions>, ValidateSettingsOptions>());
```

## <a name="options-post-configuration"></a>选项后期配置

使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。 后期配置在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置发生后运行，在需要重写配置的场景中非常有用：

```csharp
services.PostConfigure<CustomOptions>(customOptions =>
{
    customOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions%601.PostConfigure%2A> 可用于对命名选项进行后期配置：

```csharp
services.PostConfigure<CustomOptions>("named_options_1", customOptions =>
{
    customOptions.Option1 = "post_configured_option1_value";
});
```

使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll%2A> 对所有配置实例进行后期配置：

```csharp
services.PostConfigureAll<CustomOptions>(customOptions =>
{
    customOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="see-also"></a>另请参阅

- [.NET 中的配置](configuration.md)
- [面向 .NET 库创建者的选项模式指南](options-library-authors.md)

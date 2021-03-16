---
title: .NET 中的日志记录
author: IEvangelist
description: 了解如何使用由 Microsoft Extension.Logging NuGet 包提供的日志记录框架。
ms.author: dapine
ms.date: 02/19/2021
ms.openlocfilehash: 834331b9e103138012bbe91586d0d5a7c08654ce
ms.sourcegitcommit: bdbf6472de867a0a11aaa5b9384a2506c24f27d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102401983"
---
# <a name="logging-in-net"></a>.NET 中的日志记录

.NET 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。 本文介绍了如何将日志记录 API 与内置提供程序一起使用。 本文中提供的大多数代码示例都适用于使用[通用主机](generic-host.md)的 .NET 应用。 对于不使用通用主机的应用，请参阅[非主机控制台应用](#non-host-console-app)。

## <a name="create-logs"></a>创建日志

若要创建日志，请使用[依赖关系注入 (DI)](dependency-injection.md) 中的 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。

如下示例中：

- 创建一个记录器 `ILogger<Worker>`，该记录器使用类型为 `Worker` 的完全限定名称的日志类别。 日志类别是与每个日志关联的字符串。
- 调用 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> 以在 `Information` 级别登录。 日志“级别”代表所记录事件的严重程度  。

:::code language="csharp" source="snippets/configuration/worker-service/Worker.cs" range="9-24" highlight="12":::

本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。

## <a name="configure-logging"></a>配置日志记录

日志配置通常由 appsettings `{Environment}`.json 文件的 `Logging` 部分提供 。 以下 appsettings.Development.json 文件由 .NET 辅助角色服务模板生成：

:::code language="json" source="snippets/configuration/worker-service/appsettings.Development.json":::

在上述 JSON 中：

- 指定了 `"Default"`、`"Microsoft"` 和 `"Microsoft.Hosting.Lifetime"` 类别。
- `"Microsoft"` 类别适用于以 `"Microsoft"` 开头的所有类别。
- `"Microsoft"` 类别在日志级别 `Warning` 或更高级别记录。
- `"Microsoft.Hosting.Lifetime"` 类别比 `"Microsoft"` 类别更具体，因此 `"Microsoft.Hosting.Lifetime"` 类别在日志级别“Information”和更高级别记录。
- 未指定特定的日志提供程序，因此 `LogLevel` 适用于所有启用的日志记录提供程序，但 [Windows EventLog](logging-providers.md#windows-eventlog) 除外。

`Logging` 属性可以具有 <xref:Microsoft.Extensions.Logging.LogLevel> 和日志提供程序属性。 `LogLevel` 指定要针对所选类别进行记录的最低[级别](#log-level)。 在前面的 JSON 中，指定了 `Information` 和 `Warning` 日志级别。 `LogLevel` 表示日志的严重性，范围为 0 到 6：

`Trace` = 0、`Debug` = 1、`Information` = 2、`Warning` = 3、`Error` = 4、`Critical` = 5 和 `None` = 6。

指定 `LogLevel` 时，将为指定级别和更高级别的消息启用日志记录。 在前面的 JSON 中，记录了 `Information` 及更高级别的 `Default` 类别。 例如，记录了 `Information`、`Warning`、`Error` 和 `Critical` 消息。 如果未指定 `LogLevel`，则日志记录默认为 `Information` 级别。 有关详细信息，请参阅[日志级别](#log-level)。

提供程序属性可以指定 `LogLevel` 属性。 提供程序下的 `LogLevel` 指定要为该提供程序记录的级别，并替代非提供程序日志设置。 请考虑使用以下 appsettings.json 文件：

:::code language="json" source="snippets/configuration/worker-service/appsettings.Staging.json":::

`Logging.{ProviderName}.LogLevel` 中的设置将替代 `Logging.LogLevel` 中的设置。 在前面的 JSON 中，`Debug` 提供程序的默认日志级别设置为 `Information`：

`Logging:Debug:LogLevel:Default:Information`

前面的设置为每个 `Logging:Debug:` 类别（`Microsoft.Hosting` 除外）指定 `Information` 日志级别。 当列出特定类别时，该特定类别将替代默认类别。 在前面的 JSON 中，`Logging:Debug:LogLevel` 类别 `"Microsoft.Hosting"` 和 `"Default"` 替代 `Logging:LogLevel` 中的设置

可以为以下任何一项指定最低日志级别：

- 特定提供程序：例如，`Logging:EventSource:LogLevel:Default:Information`
- 特定类别：例如，`Logging:LogLevel:Microsoft:Warning`
- 所有提供程序和所有类别：`Logging:LogLevel:Default:Warning`

低于最低级别的任何日志均不会执行以下操作：

- 传递到提供程序。
- 记录或显示。

要阻止所有日志，请指定 [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel)。 `LogLevel.None` 的值为 6，该值高于 `LogLevel.Critical` (5)。

如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。 有关详细信息，请参阅[日志范围](#log-scopes)

以下 appsettings.json 文件包含所有内置提供程序的设置：

:::code language="json" source="snippets/configuration/worker-service/appsettings.Production.json":::

在上述示例中：

- 类别和级别不是建议的值。 提供该示例是为了显示所有默认提供程序。
- `Logging.{ProviderName}.LogLevel` 中的设置将替代 `Logging.LogLevel` 中的设置。 例如，`Debug.LogLevel.Default` 中的级别将替代 `LogLevel.Default` 中的级别。
- 将使用每个提供程序的别名。 每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称  。 内置提供程序的别名包括：
  - 控制台
  - 调试
  - EventSource
  - EventLog
  - AzureAppServicesFile
  - AzureAppServicesBlob
  - ApplicationInsights

### <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>通过命令行、环境变量和其他配置设置日志级别

日志级别可以由任何[配置提供程序](configuration-providers.md)设置。 例如，可以创建一个名为 `Logging:LogLevel:Microsoft` 且值为 `Information` 的持久性环境变量。

## <a name="command-line"></a>[命令行](#tab/command-line)

在给定日志级别值的情况下，创建并分配持久性环境变量。

```CMD
:: Assigns the env var to the value
setx "Logging__LogLevel__Microsoft" "Information" /M
```

在命令提示符的新实例中，读取环境变量。

```CMD
:: Prints the env var value
echo %Logging__LogLevel__Microsoft%
```

## <a name="powershell"></a>[PowerShell](#tab/powershell)

在给定日志级别值的情况下，创建并分配持久性环境变量。

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable(
    "Logging__LogLevel__Microsoft", "Information", "Machine")
```

在 PowerShell 的新实例中，读取环境变量。

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable(
    "Logging__LogLevel__Microsoft", "Machine")
```

## <a name="bash"></a>[Bash](#tab/bash)

在给定日志级别值的情况下，创建并分配持久性环境变量。

```Bash
# Assigns the env var to the value, persists it across sessions
echo export Logging__LogLevel__Microsoft="Information" >> ~/.bashrc && source ~/.bashrc
```

读取环境变量。

```Bash
# Prints the env var value
echo $Logging__LogLevel__Microsoft

# Or use printenv:
# printenv Logging__LogLevel__Microsoft
```

> [!NOTE]
> 在使用包含 `.`（句点）的名称配置环境变量时，请注意 Stack Exchange 上提出的“导出带有点 (.) 的变量”问题以及相应的 [已接受解答](https://unix.stackexchange.com/a/93533)。

---

前面的环境设置会在环境中持续存在。 若要在使用通过 .NET 辅助角色服务模板创建的应用时测试这些设置，请在分配环境变量后，在项目目录中使用 `dotnet run` 命令。

```dotnetcli
dotnet run
```

> [!TIP]
> 设置环境变量后，请重启集成开发环境 (IDE)，以确保新添加的环境变量可用。

在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)上，选择“设置”>“配置”页面上的“新应用程序设置” 。 Azure 应用服务应用程序设置：

- 已静态加密且通过加密的通道进行传输。
- 已作为环境变量公开。

若要详细了解如何使用环境变量设置 .NET 配置值，请参阅[环境变量](configuration-providers.md#environment-variable-configuration-provider)。

## <a name="how-filtering-rules-are-applied"></a>如何应用筛选规则

创建 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象时，<xref:Microsoft.Extensions.Logging.ILoggerFactory> 对象将根据提供程序选择一条规则，将其应用于该记录器。 将按所选规则筛选 `ILogger` 实例写入的所有消息。 从可用规则中为每个提供程序和类别对选择最具体的规则。

在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：

- 选择匹配提供程序或其别名的所有规则。 如果找不到任何匹配项，则选择提供程序为空的所有规则。
- 根据上一步的结果，选择具有最长匹配类别前缀的规则。 如果找不到任何匹配项，则选择未指定类别的所有规则。
- 如果选择了多条规则，则采用最后一条  。
- 如果未选择任何规则，请使用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel(Microsoft.Extensions.Logging.ILoggingBuilder,Microsoft.Extensions.Logging.LogLevel)?displayProperty=nameWithType> 指定最小日志记录级别。

## <a name="log-category"></a>日志类别

创建 `ILogger` 对象时，将指定类别。 该类别包含在由此 `ILogger` 实例创建的每条日志消息中。 类别字符串是任意的，但约定将使用类名称。 例如，在服务定义类似于以下对象的应用程序中，类别可能为 `"Example.DefaultService"`：

```csharp
namespace Example
{
    public class DefaultService : IService
    {
        private readonly ILogger<DefaultService> _logger;

        public DefaultService(ILogger<DefaultService> logger) =>
            _logger = logger;

        // ...
    }
}
```

要显式指定类别，请调用 <xref:Microsoft.Extensions.Logging.LoggerFactory.CreateLogger%2A?displayProperty=nameWithType>：

```csharp
namespace Example
{
    public class DefaultService : IService
    {
        private readonly ILogger _logger;

        public DefaultService(ILoggerFactory loggerFactory) =>
            _logger = loggerFactory.CreateLogger("CustomCategory");

        // ...
    }
}
```

在多个类/类型中使用时，使用固定名称调用 `CreateLogger` 很有用，这样可以按类别组织事件。

`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。

## <a name="log-level"></a>日志级别

下表列出了 <xref:Microsoft.Extensions.Logging.LogLevel> 值、方便的 `Log{LogLevel}` 扩展方法以及建议的用法：

| LogLevel | “值” | 方法 | 描述 |
|--|--|--|--|
| [Trace](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | 包含最详细的消息。 这些消息可能包含敏感的应用数据。 这些消息默认情况下处于禁用状态，并且不应在生产中启用。 |
| [调试](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | 用于调试和开发。 由于量大，请在生产中小心使用。 |
| [信息](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | 跟踪应用的常规流。 可能具有长期值。 |
| [警告](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | 对于异常事件或意外事件。 通常包括不会导致应用失败的错误或情况。 |
| [错误](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | 表示无法处理的错误和异常。 这些消息表示当前操作或请求失败，而不是整个应用失败。 |
| [严重](xref:Microsoft.Extensions.Logging.LogLevel) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | 需要立即关注的失败。 例如数据丢失、磁盘空间不足。 |
| [无](xref:Microsoft.Extensions.Logging.LogLevel) | 6 |  | 指定不应写入任何消息。 |

在上表中，`LogLevel` 按严重性由低到高的顺序列出。

[Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) 方法的第一个参数 <xref:Microsoft.Extensions.Logging.LogLevel> 指示日志的严重性。 大多数开发人员调用 [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) 扩展方法，而不调用 `Log(LogLevel, ...)`。 `Log{LogLevel}` 扩展方法[调用 Log 方法并指定 LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。 例如，以下两个日志记录调用功能相同，并生成相同的日志：

```csharp
public void LogDetails()
{
    var logMessage = "Details for log.";

    _logger.Log(LogLevel.Information, AppLogEvents.Details, logMessage);
    _logger.LogInformation(AppLogEvents.Details, logMessage);
}
```

`AppLogEvents.Details` 为事件 ID，用常量 <xref:System.Int32> 值隐式表示。 `AppLogEvents` 是一个公开各种命名的标识符常量并显示在[日志事件 ID](#log-event-id) 部分中的类。

下面的代码会创建 `Information` 和 `Warning` 日志：

```csharp
public async Task<T> GetAsync<T>(string id)
{
    _logger.LogInformation(AppLogEvents.Read, "Reading value for {Id}", id);

    var result = await _repository.GetAsync(id);
    if (result is null)
    {
        _logger.LogWarning(AppLogEvents.ReadNotFound, "GetAsync({Id}) not found", id);
    }

    return result;
}
```

在前面的代码中，第一个 `Log{LogLevel}` 参数 `AppLogEvents.Read` 是[日志事件 ID](#log-event-id)。 第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。 稍后将在本文的[消息模板](#log-message-template)部分介绍方法参数。

配置适当的日志级别并调用正确的 `Log{LogLevel}` 方法来控制写入特定存储介质的日志输出量。 例如：

- 生产中：
  - 在 `Trace` 或 `Information` 级别记录日志会产生大量详细的日志消息。 为了控制成本且不超过数据存储限制，请将 `Trace` 和 `Information` 级别消息记录到容量大、成本低的数据存储中。 考虑将 `Trace` 和 `Information` 限制为特定类别。
  - 从 `Warning` 到 `Critical` 级别的日志记录应该很少产生日志消息。
    - 成本和存储限制通常不是问题。
    - 很少有日志可以为数据存储选择提供更大的灵活性。
- 在开发过程中：
  - 设置为 `Warning`。
  - 在进行故障排除时，添加 `Trace` 或 `Information` 消息。 若要限制输出，请仅对正在调查的类别设置 `Trace` 或 `Information`。

以下 JSON 设置了 `Logging:Console:LogLevel:Microsoft:Information`：

:::code language="json" source="snippets/configuration/worker-service/appsettings.MSFT.json":::

## <a name="log-event-id"></a>日志事件 ID

每个日志可以指定一个事件标识符，<xref:Microsoft.Extensions.Logging.EventId> 是一个具有 `Id` 和可选 `Name` 只读属性的结构。 示例源代码使用 `AppLogEvents` 类来定义事件 ID：

```csharp
internal static class AppLogEvents
{
    internal const int Create = 1000;
    internal const int Read = 1001;
    internal const int Update = 1002;
    internal const int Delete = 1003;

    internal const int Details = 3000;
    internal const int Error = 3001;

    internal const int ReadNotFound = 4000;
    internal const int UpdateNotFound = 4001;

    // ...
}
```

事件 ID 与一组事件相关联。 例如，与从存储库读取值相关的所有日志都可能是 `1001`。

日志记录提供程序可将事件 ID 记录在 ID 字段中，记录在日志记录消息中，或者不进行记录。 调试提供程序不显示事件 ID。 控制台提供程序在类别后的括号中显示事件 ID：

```console
info: Example.DefaultService.GetAsync[1001]
      Reading value for a1b2c3
warn: Example.DefaultService.GetAsync[4000]
      GetAsync(a1b2c3) not found
```

一些日志记录提供程序将事件 ID 存储在一个字段中，该字段允许对 ID 进行筛选。

## <a name="log-message-template"></a>日志消息模板

每个日志 API 都使用一个消息模板。 消息模板可包含要填写参数的占位符。 请在占位符中使用名称而不是数字。 占位符的顺序（而非其名称）决定了为其提供值的参数。 在以下代码中，消息模板中的参数名称不按顺序排列：

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

上面的代码按顺序通过参数值创建日志消息：

```text
Parameter values: param1, param2
```

此方法允许日志记录提供程序实现[语义或结构化日志记录](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging)。 参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。 这使日志记录提供程序可以将参数值存储为字段。 请考虑使用以下记录器方法：

```csharp
_logger.LogInformation("Getting item {Id} at {RunTime}", id, DateTime.Now);
```

例如，登录到 Azure 表存储时：

- 每个 Azure 表实体都可以有 `ID` 和 `RunTime` 属性。
- 具有属性的表简化了对记录数据的查询。 例如，查询可以找到特定 `RunTime` 范围内的所有日志，而不必分析文本消息中的时间。

## <a name="log-exceptions"></a>记录异常

记录器方法的重载采用异常参数：

```csharp
public void Test(string id)
{
    try
    {
        if (id == "none")
        {
            throw new Exception("Default Id detected.");
        }
    }
    catch (Exception ex)
    {
        _logger.LogWarning(
            AppLogEvents.Error, ex,
            "Failed to process iteration: {Id}", id)
    }
}
```

异常日志记录是特定于提供程序的。

### <a name="default-log-level"></a>默认日志级别

如果未设置默认日志级别，则默认的日志级别值为 `Information`。

例如，请考虑以下辅助角色服务应用：

- 使用 .NET 辅助角色模板创建的应用。
- 已删除 appsettings.json 和 appsettings.Development.json 或对其进行重命名 。

使用上述设置，导航到隐私或主页会生成许多 `Trace`、`Debug` 和 `Information` 消息，并在类别名称中包含 `Microsoft`。

如果未在配置中设置默认日志级别，以下代码会设置默认日志级别：

```csharp
class Program
{
    static Task Main(string[] args) =>
        CreateHostBuilder(args).Build().RunAsync();

    static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureLogging(logging => logging.SetMinimumLevel(LogLevel.Warning));
}
```

### <a name="filter-function"></a>筛选器函数

对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数：

```csharp
class Program
{
    static Task Main(string[] args) =>
        CreateHostBuilder(args).Build().RunAsync();

    static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureLogging(logging =>
                logging.AddFilter((provider, category, logLevel) =>
                {
                    return provider.Contains("ConsoleLoggerProvider")
                        && (category.Contains("Example") || category.Contains("Microsoft"))
                        && logLevel >= LogLevel.Information;
                }));
}
```

如果类别包含 `Example` 或 `Microsoft`，并且日志级别为 `Information` 或更高级别，以上代码会显示控制台日志。

## <a name="log-scopes"></a>日志作用域

 “作用域”可对一组逻辑操作分组  。 此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。 例如，在处理事务期间创建的每个日志都可包括事务 ID。

范围：

- 是 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope%2A> 方法返回的 <xref:System.IDisposable> 类型。
- 持续到处置完毕。

以下提供程序支持范围：

- `Console`
- [AzureAppServicesFile 和 AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

要使用作用域，请在 `using` 块中包装记录器调用：

```csharp
public async Task<T> GetAsync<T>(string id)
{
    T result;

    using (_logger.BeginScope("using block message"))
    {
        _logger.LogInformation(
            AppLogEvents.Read, "Reading value for {Id}", id);

        var result = await _repository.GetAsync(id);
        if (result is null)
        {
            _logger.LogWarning(
                AppLogEvents.ReadNotFound, "GetAsync({Id}) not found", id);
        }
    }

    return result;
}
```

以下 JSON 为控制台提供程序启用范围：

:::code language="json" source="snippets/configuration/worker-service/appsettings.IncludeScopes.json" highlight="9":::

下列代码为控制台提供程序启用作用域：

```csharp
class Program
{
    static Task Main(string[] args) =>
        CreateHostBuilder(args).Build().RunAsync();

    static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureLogging((_, logging) =>
                logging.ClearProviders()
                    .AddConsole(options => options.IncludeScopes = true));
}
```

## <a name="non-host-console-app"></a>非托管控制台应用

对于没有[通用主机](generic-host.md)的应用，日志记录代码在[添加提供程序](logging-providers.md#built-in-logging-providers)和[创建记录器](#create-logs)的方式上有所不同。 在非主机控制台应用中，在创建 `LoggerFactory` 时调用提供程序的 `Add{provider name}` 扩展方法：

```csharp
class Program
{
    static void Main(string[] args)
    {
        using var loggerFactory = LoggerFactory.Create(builder =>
        {
            builder
                .AddFilter("Microsoft", LogLevel.Warning)
                .AddFilter("System", LogLevel.Warning)
                .AddFilter("LoggingConsoleApp.Program", LogLevel.Debug)
                .AddConsole();
        });

        ILogger logger = loggerFactory.CreateLogger<Program>();
        logger.LogInformation("Example log message");
    }
}
```

`loggerFactory` 对象用于创建 <xref:Microsoft.Extensions.Logging.ILogger> 实例。

## <a name="create-logs-in-main"></a>在 Main 中创建日志

以下代码通过在构建主机之后从 DI 获取 `ILogger` 实例来登录 `Main`：

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

class Program
{
    static Task Main(string[] args)
    {
        IHost host = Host.CreateDefaultBuilder(args).Build();

        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("Host created.");

        return host.RunAsync();
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>没有异步记录器方法

日志记录应该会很快，不值得牺牲性能来使用异步代码。 如果日志记录数据存储很慢，请不要直接写入它。 考虑先将日志消息写入快速存储，然后再将其移至慢速存储。 例如，登录到 SQL Server 时，请勿直接使用 `Log` 方法登录，因为 `Log` 方法是同步的。 相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。

## <a name="change-log-levels-in-a-running-app"></a>更改正在运行的应用中的日志级别

不可使用日志记录 API 在应用运行时更改日志记录。 但是，一些配置提供程序可重新加载配置，这将对日志记录配置立即产生影响。 例如，[文件配置提供程序](configuration-providers.md#file-configuration-provider)默认情况下会重载日志记录配置。 如果在应用运行时在代码中更改了配置，则该应用可调用 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload%2A) 来更新应用的日志记录配置。

## <a name="nuget-packages"></a>NuGet 包

<xref:Microsoft.Extensions.Logging.ILogger%601> 和 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 接口和实现都包含在 .NET Core SDK 中。 它们还可以通过以下 NuGet 包获得：

- 这些接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions) 中。
- 默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging) 中。

## <a name="apply-log-filter-rules-in-code"></a>在代码中应用日志筛选器规则

设置日志筛选器规则的首选方法是使用[配置](configuration.md)。

下面的示例演示了如何在代码中注册筛选规则：

```csharp
class Program
{
    static Task Main(string[] args) =>
        CreateHostBuilder(args).Build().RunAsync();

    static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureLogging(logging =>
               logging.AddFilter("System", LogLevel.Debug)
                  .AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)
                  .AddFilter<ConsoleLoggerProvider>("Microsoft", LogLevel.Trace));
}
```

`logging.AddFilter("System", LogLevel.Debug)` 指定 `System` 类别和日志级别 `Debug`。 筛选器将应用于所有提供程序，因为未配置特定的提供程序。

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` 指定以下项：

- `Debug` 日志记录提供程序。
- 日志级别 `Information` 及更高级别。
- 以 `"Microsoft"` 开头的所有类别。

## <a name="see-also"></a>另请参阅

- [.NET 中的日志记录提供程序](logging-providers.md)
- [在 .NET 中实现自定义日志记录提供程序](custom-logging-provider.md)
- [控制台日志格式设置](console-log-formatter.md)
- [.NET 中的高性能日志记录](high-performance-logging.md)
- 应在 [github.com/dotnet/extensions](https://github.com/dotnet/extensions/issues) 存储库中创建日志记录 bug

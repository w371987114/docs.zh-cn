---
title: 依赖关系注入指南
description: 了解各种依赖关系注入准则和 .NET 应用程序开发的最佳做法。
author: IEvangelist
ms.author: dapine
ms.date: 10/29/2020
ms.topic: guide
ms.openlocfilehash: 105536df873829dc79dcca1b86d080360e71303f
ms.sourcegitcommit: f2ab02d9a780819ca2e5310bbcf5cfe5b7993041
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "102401976"
---
# <a name="dependency-injection-guidelines"></a>依赖关系注入指南

本文介绍在 .NET 应用程序中实现依赖关系注入的一般准则和最佳做法。

## <a name="design-services-for-dependency-injection"></a>设计能够进行依赖关系注入的服务

在设计能够进行依赖注入的服务时：

- 避免有状态的、静态类和成员。 通过将应用设计为改用单一实例服务，避免创建全局状态。
- 避免在服务中直接实例化依赖类。 直接实例化会将代码耦合到特定实现。
- 不在服务中包含过多内容，确保设计规范，并易于测试。

如果一个类有很多已注入的依赖关系，这可能表明该类拥有过多的责任，并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。 尝试通过将某些职责移动到一个新类来重构类。

### <a name="disposal-of-services"></a>服务释放

容器负责清除它创建的类型，并在 <xref:System.IDisposable> 实例上调用 <xref:System.IDisposable.Dispose%2A>。 从容器中解析的服务绝对不应由开发人员释放。 如果类型或工厂注册为单一实例，则容器自动释放单一实例。

在下面的示例中，服务由服务容器创建，并自动释放：

:::code language="csharp" source="snippets/configuration/console-di-disposable/TransientDisposable.cs":::

前面的可释放服务应具有暂时性生存期。

:::code language="csharp" source="snippets/configuration/console-di-disposable/ScopedDisposable.cs":::

前面的可释放服务应具有作用域内生存期。

:::code language="csharp" source="snippets/configuration/console-di-disposable/SingletonDisposable.cs":::

前面的可释放服务应具有单一实例生存期。

:::code language="csharp" source="snippets/configuration/console-di-disposable/Program.cs" range="1-21,41-60" highlight="":::

调试控制台在运行后显示下面的示例输出：

```console
Scope 1...
ScopedDisposable.Dispose()
TransientDisposable.Dispose()

Scope 2...
ScopedDisposable.Dispose()
TransientDisposable.Dispose()

info: Microsoft.Hosting.Lifetime[0]
      Application started.Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
     Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
     Content root path: .\configuration\console-di-disposable\bin\Debug\net5.0
info: Microsoft.Hosting.Lifetime[0]
     Application is shutting down...
SingletonDisposable.Dispose()
```

### <a name="services-not-created-by-the-service-container"></a>不由服务容器创建的服务

考虑下列代码：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton(new ExampleService());

    // ...
}
```

在上述代码中：

- `ExampleService` 实例不是由服务容器创建的。
- 框架不会自动释放服务。
- 开发人员负责释放服务。

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>暂时和共享实例的 IDisposable 指南

#### <a name="transient-limited-lifetime"></a>暂时、有限的生存期

**方案**

应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：

- 在根范围（根容器）内解析实例。
- 应在作用域结束之前释放实例。

**解决方案**

使用工厂模式在父作用域外创建实例。  在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。 如果最终类型具有其他依赖项，则工厂可以：

- 在其构造函数中接收 <xref:System.IServiceProvider>。
- 使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。

#### <a name="shared-instance-limited-lifetime"></a>共享实例，有限的生存期

**方案**

应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 实例应具有有限的生存期。

**解决方案**

为实例注册作用域生存期。 使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 创建新 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。 使用作用域的 <xref:System.IServiceProvider> 获取所需的服务。 如果不再需要范围，请将其释放。

#### <a name="general-idisposable-guidelines"></a>通用 `IDisposable` 准则

- 不要为 <xref:System.IDisposable> 实例注册暂时性生存期。 请改用工厂模式。
- 不要在根范围内解析具有暂时性或范围内生存期的 <xref:System.IDisposable> 实例。 唯一的例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，但这不是理想模式。
- 通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。 <xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。
- 使用范围控制服务的生存期。  作用域不区分层次，并且在各作用域之间没有特定联系。

有关资源清理的详细信息，请参阅[实现 `Dispose` 方法](../../standard/garbage-collection/implementing-dispose.md)或[实现 `DisposeAsync` 方法](../../standard/garbage-collection/implementing-disposeasync.md)。 另外，请考虑[容器捕获的可释放的暂时性服务](#disposable-transient-services-captured-by-container)方案，因为它与资源清理相关。

## <a name="default-service-container-replacement"></a>默认服务容器替换

内置的服务容器旨在满足框架和大多数消费者应用的需求。 我们建议使用内置容器，除非你需要的特定功能不受它支持，例如：

- 属性注入
- 基于名称的注入
- 子容器
- 自定义生存期管理
- 对迟缓初始化的 `Func<T>` 支持
- 基于约定的注册

以下第三方容器可用于 ASP.NET Core 应用：

- [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
- [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
- [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
- [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
- [Lamar](https://jasperfx.github.io/lamar/)
- [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
- [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a>线程安全

创建线程安全的单一实例服务。 如果单一实例服务依赖于一个暂时服务，那么暂时服务可能也需要线程安全，具体取决于单一实例使用它的方式。

单一实例服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A) 的第二个参数）不必是线程安全的。 像类型 (`static`) 构造函数一样，它保证仅由单个线程调用一次。

## <a name="recommendations"></a>建议

- 不支持基于`async/await` 和 `Task` 的服务解析。 由于 C# 不支持异步构造函数，因此请在同步解析服务后使用异步方法。
- 避免在服务容器中直接存储数据和配置。 例如，用户的购物车通常不应添加到服务容器中。 配置应使用 选项模型。 同样，避免“数据持有者”对象，也就是仅仅为实现对另一个对象的访问而存在的对象。 最好通过 DI 请求实际项。
- 避免静态访问服务。 例如，避免将 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 捕获为静态字段或属性以便在其他地方使用。
- 使 [DI 工厂](#async-di-factories-can-cause-deadlocks)保持快速且同步。
- 避免使用[服务定位器模式](#scoped-service-as-singleton)。 例如，可以改为使用 DI 时，不要调用 <xref:System.IServiceProvider.GetService%2A> 来获取服务实例。
- 要避免的另一个服务定位器变体是注入需在运行时解析依赖项的工厂。 这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。
- 避免在 `ConfigureServices` 中调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>。 当开发人员想要在 `ConfigureServices` 中解析服务时，通常会调用 `BuildServiceProvider`。
- [可释放的暂时性服务由容器捕获](#disposable-transient-services-captured-by-container)以进行释放。 如果从顶级容器解析，这会变为内存泄漏。
- 启用范围验证，确保应用没有捕获范围内服务的单一实例。 有关详细信息，请参阅[作用域验证](dependency-injection.md#scope-validation)。

像任何一组建议一样，你可能会遇到需要忽略某建议的情况。 例外情况很少见，主要是框架本身内部的特殊情况。

DI 是静态/全局对象访问模式的替代方法。 如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。

## <a name="example-anti-patterns"></a>示例反模式

除了本文中介绍的指导原则之外，还应避免几种反模式。 其中的某些反模式是开发运行时本身的知识。

> [!WARNING]
> 这些是示例反模式，请不要复制代码，不要使用这些模式，并不惜一切代价避免使用这些模式。

### <a name="disposable-transient-services-captured-by-container"></a>由容器捕获的可释放的暂时性服务

当你注册可实现 <xref:System.IDisposable> 的暂时性服务时，默认情况下，DI 容器将捕获这些引用，而不是这些引用的 <xref:System.IDisposable.Dispose>，如果它们是从容器中解析的，则直到应用程序停止且容器被释放，如果它们是从作用域中解析的，则直到作用域被释放。 如果从容器级解析，这会变为内存泄漏。

:::code language="csharp" source="snippets/configuration/di-anti-patterns/Program.cs" range="18-30":::

在上述反模式中，1,000 个 `ExampleDisposable` 对象将被实例化并为根对象。 在释放 `serviceProvider` 实例之前，它们不会被释放。

有关调试内存泄漏的详细信息，请参阅[调试 .NET 中的内存泄漏](../diagnostics/debug-memory-leak.md)。

### <a name="async-di-factories-can-cause-deadlocks"></a>异步 DI 工厂可能导致死锁

术语“DI 工厂”指的是在调用 `Add{LIFETIME}` 时存在的重载方法。 有一些重载接受 `Func<IServiceProvider, T>`，其中 `T` 是要注册的服务，参数命名为 `implementationFactory`。 `implementationFactory` 可以作为 Lambda 表达式、局部函数或方法提供。 如果工厂是异步的，并且你使用 <xref:System.Threading.Tasks.Task%601.Result?displayProperty=nameWithType>，则会导致死锁。

:::code language="csharp" source="snippets/configuration/di-anti-patterns/Program.cs" range="32-45" highlight="4-8":::

在前面的代码中，为 `implementationFactory` 指定了一个 Lambda 表达式，其中主体在 `Task<Bar>` 返回方法上调用 <xref:System.Threading.Tasks.Task%601.Result?displayProperty=nameWithType>。 这会导致死锁。 `GetBarAsync` 方法只使用 <xref:System.Threading.Tasks.Task.Delay%2A?displayProperty=nameWithType> 模拟异步工作操作，然后调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%60%601(System.IServiceProvider)>。

:::code language="csharp" source="snippets/configuration/di-anti-patterns/Program.cs" range="47-53":::

有关异步指南的详细信息，请参阅[异步编程：重要信息和建议](../../csharp/async.md#important-info-and-advice)。 有关调试死锁的详细信息，请参阅[调试 .NET 中的死锁](../diagnostics/debug-deadlock.md)。

在运行此反模式并发生死锁时，可以从 Visual Studio 的“并行堆栈”窗口查看等待的两个线程。 有关详细信息，请参阅[在“并行堆栈”窗口中查看线程和任务](/visualstudio/debugger/using-the-parallel-stacks-window)。

### <a name="captive-dependency"></a>捕获依赖项

术语“[捕获依赖项](https://blog.ploeh.dk/2014/06/02/captive-dependency)”由 [Mark Seeman](https://blog.ploeh.dk/about) 提出，指的是服务生存期的配置不正确，其中具有较长生存期的服务捕获了具有较短生存期的服务。

:::code language="csharp" source="snippets/configuration/di-anti-patterns/Program.cs" range="55-65":::

在前面的代码中，`Foo` 注册为单一实例，并确定了 `Bar` 的作用域，这在表面上看似乎是有效的。 不过，请考虑 `Foo` 的实现。

:::code language="csharp" source="snippets/configuration/di-anti-patterns/Foo.cs" highlight="5":::

`Foo` 对象需要一个 `Bar` 对象，因为 `Foo` 是单一实例，并且已确定 `Bar` 的作用域 - 这是错误的配置。 因此，将只会实例化 `Foo` 一次，并且它会在自己的生存期内捕获 `Bar`，这比所需的作用域内的 `Bar` 生存期要长。 应考虑通过将 `validateScopes: true` 传递到 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider(Microsoft.Extensions.DependencyInjection.IServiceCollection,System.Boolean)> 来验证作用域。 验证作用域时，你将收到 <xref:System.InvalidOperationException> 和一条消息，类似于“无法使用来自单一实例‘Foo’的作用域内的服务‘Bar’”。

有关详细信息，请参阅[作用域验证](dependency-injection.md#scope-validation)。

### <a name="scoped-service-as-singleton"></a>作为单一实例的作用域服务

使用作用域内服务时，如果你不是在现有作用域内创建作用域，则该服务将成为单一实例。

:::code language="csharp" source="snippets/configuration/di-anti-patterns/Program.cs" range="68-82" highlight="13-14":::

在前面的代码中，在 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 中检索 `Bar`，这是正确的。 反模式是作用域外的 `Bar` 检索，变量命名为 `avoid` 以显示不正确的示例检索。

## <a name="see-also"></a>另请参阅

- [.NET 中的依赖关系注入](dependency-injection.md)
- [教程：在 .NET 中使用依赖关系注入](dependency-injection-usage.md)

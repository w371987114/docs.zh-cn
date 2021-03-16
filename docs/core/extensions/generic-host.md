---
title: .NET 通用主机
author: IEvangelist
description: 了解 .NET 通用主机，该主机负责应用启动和生存期管理。
ms.author: dapine
ms.date: 12/18/2020
ms.openlocfilehash: bf6d5ad624bbed46994633abace0af64712757f3
ms.sourcegitcommit: 3d6d6595a03915f617349781f455f838a44b0f44
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2020
ms.locfileid: "102401954"
---
# <a name="net-generic-host"></a>.NET 通用主机

辅助角色服务模板会创建一个 .NET 通用主机 <xref:Microsoft.Extensions.Hosting.HostBuilder>。 通用主机可用于其他类型的 .NET 应用程序，如控制台应用。

主机是封装应用资源的对象，例如：

- 依赖关系注入 (DI)
- Logging
- Configuration
- `IHostedService` 实现

当主机启动时，它将对在托管服务的服务容器集合中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的每个实现调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType>。 在辅助角色服务应用中，包含 <xref:Microsoft.Extensions.Hosting.BackgroundService> 实例的所有 `IHostedService` 实现都调用其 <xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync%2A?displayProperty=nameWithType> 方法。

一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。 这是通过 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) NuGet 包实现的。

## <a name="set-up-a-host"></a>设置主机

主机通常由 `Program` 类中的代码配置、生成和运行。 `Main` 方法：

- 调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder> 方法以创建和配置生成器对象。
- 调用 <xref:Microsoft.Extensions.Hosting.IHostBuilder.Build> 以创建 <xref:Microsoft.Extensions.Hosting.IHost> 实例。
- 对主机对象调用 <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run%2A> 或 <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync%2A> 方法。

.NET 辅助角色服务模板会生成以下代码来创建通用主机：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
            });
}
```

## <a name="default-builder-settings"></a>默认生成器设置

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 方法：

- 将内容根路径设置为由 <xref:System.IO.Directory.GetCurrentDirectory> 返回的路径。
- 通过以下对象加载[主机配置](#host-configuration)：
  - 前缀为 `DOTNET_` 的环境变量。
  - 命令行参数。
- 通过以下对象加载应用配置：
  - appsettings.json。
  - appsettings.{Environment}.json。
  - 密钥管理器 当应用在 `Development` 环境中运行时。
  - 环境变量。
  - 命令行参数。
- 添加以下日志记录提供程序：
  - 控制台
  - 调试
  - EventSource
  - EventLog（仅当在 Windows 上运行时）
- 当环境为 `Development` 时，启用范围验证和[依赖关系验证](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。

`ConfigureServices` 方法公开了向 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection?displayProperty=nameWithType> 实例添加服务的功能。 以后，可以通过依赖关系注入获取这些服务。

## <a name="framework-provided-services"></a>框架提供的服务

自动注册以下服务：

- [IHostApplicationLifetime](#ihostapplicationlifetime)
- [IHostLifetime](#ihostlifetime)
- [IHostEnvironment](#ihostenvironment)

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

将 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> 服务注入任何类以处理启动后和正常关闭任务。 接口上的三个属性是用于注册应用启动和应用停止事件处理程序方法的取消令牌。 该接口还包括 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication> 方法。

以下示例是注册 `IHostApplicationLifetime` 事件的 `IHostedService` 实现：

:::code language="csharp" source="snippets/configuration/app-lifetime/ExampleHostedService.cs" highlight="18-20":::

可以修改辅助角色服务模板以添加 `ExampleHostedService` 实现：

:::code language="csharp" source="snippets/configuration/app-lifetime/Program.cs" range="1-16,36" highlight="15":::

应用程序会编写以下示例输出：

:::code language="csharp" source="snippets/configuration/app-lifetime/Program.cs" range="17-35":::

## <a name="ihostlifetime"></a>IHostLifetime

<xref:Microsoft.Extensions.Hosting.IHostLifetime> 实现控制主机何时启动和何时停止。 使用了已注册的最后一个实现。

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是默认的 `IHostLifetime` 实现。 `ConsoleLifetime`:

- 侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>、[SIGINT](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGINT) 或 [SIGTERM](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGTERM) 并调用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> 来启动关闭进程。
- 解除阻止 `RunAsync` 和 `WaitForShutdownAsync` 等扩展。

## <a name="ihostenvironment"></a>IHostEnvironment

将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 服务注册到一个类，获取关于以下设置的信息：

- <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName?displayProperty=nameWithType>
- <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider?displayProperty=nameWithType>
- <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath?displayProperty=nameWithType>
- <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName?displayProperty=nameWithType>

## <a name="host-configuration"></a>主机配置

主机配置用于配置 [IHostEnvironment](#ihostenvironment) 实现的属性。

主机配置在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 方法的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) 中可用。 调用 `ConfigureAppConfiguration` 方法时，`HostBuilderContext` 和 `IConfigurationBuilder` 将传递到 `configureDelegate` 中。 `configureDelegate` 被定义为 `Action<HostBuilderContext, IConfigurationBuilder>`。 主机生成器上下文公开 `.Configuration` 属性，该属性是 `IConfiguration` 的一个实例。 它表示从主机生成的配置，而 `IConfigurationBuilder` 是用于配置应用的生成器对象。

> [!TIP]
> 在调用 `ConfigureAppConfiguration` 后，`HostBuilderContext.Configuration` 被替换为[应用配置](#app-configuration)。

若要添加主机配置，请对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A>。 可多次调用 `ConfigureHostConfiguration`，并得到累计结果。 主机使用上一次在一个给定键上设置值的选项。

以下示例创建主机配置：

:::code language="csharp" source="snippets/configuration/console-host/Program.cs" highlight="19-25":::

## <a name="app-configuration"></a>应用配置

通过对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 创建应用配置。 可多次调用 `ConfigureAppConfiguration`，并得到累计结果。 应用使用上一次在一个给定键上设置值的选项。

可以在 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration%2A) 中获取由 `ConfigureAppConfiguration` 创建的配置以用于后续操作，并将其作为 DI 的服务。 主机配置也会添加到应用配置。

有关详细信息，请参阅 [.NET 中的配置](configuration.md)。

## <a name="see-also"></a>另请参阅

- [.NET 中的配置](configuration.md)
- [ASP.NET Core Web 主机](/aspnet/core/fundamentals/host/web-host)
- 应在 [github.com/dotnet/extensions](https://github.com/dotnet/extensions/issues) 存储库中创建通用主机 bug

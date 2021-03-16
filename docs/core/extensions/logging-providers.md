---
title: .NET 中的日志记录提供程序
description: 了解如何在 .NET 应用程序中使用日志记录提供程序 API。
author: IEvangelist
ms.author: dapine
ms.date: 02/16/2021
ms.openlocfilehash: 7265e51a4d92cd99abebef2ebf0bc5db37b306e3
ms.sourcegitcommit: 456b3cd82a87b453fa737b4661295070d1b6d684
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "102401978"
---
# <a name="logging-providers-in-net"></a>.NET 中的日志记录提供程序

日志提供程序会保留日志，但 `Console` 提供程序除外，后者仅将日志显示为标准输出。 例如，Azure Application Insights 提供程序将日志存储在 Azure Application Insights 中。 可以启用多个提供程序。

默认的 .NET 辅助角色应用模板：

- 使用[通用主机](generic-host.md)。
- 调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>，这将添加以下日志记录提供程序：
  - [控制台](#console)
  - [调试](#debug)
  - [EventSource](#event-source)
  - [EventLog](#windows-eventlog)：仅限 Windows

:::code language="csharp" source="snippets/configuration/console/Program.cs" highlight="18":::

上面的代码显示了使用 .NET 辅助角色应用模板创建的 `Program` 类。 接下来的几个部分提供了一些示例，它们基于使用通用主机的 .NET 辅助角色应用模板。

若要替代 `Host.CreateDefaultBuilder` 添加的默认日志记录提供程序集，请调用 `ClearProviders` 并添加所需的日志记录提供程序。 例如，以下代码：

- 调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> 以从生成器中删除所有 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 实例。
- 添加[控制台](#console)日志记录提供程序。

```csharp
static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.ClearProviders();
            logging.AddConsole();
        });
```

有关其他提供程序，请参阅：

- [内置日志记录提供程序](#built-in-logging-providers)。
- [第三方日志记录提供程序](#third-party-logging-providers)。

## <a name="configure-a-service-that-depends-on-ilogger"></a>配置依赖于 ILogger 的服务

若要配置依赖于 `ILogger<T>` 的服务，请使用构造函数注入或提供工厂方法。 只有在没有其他选择的情况下，才建议使用工厂方法。 例如，假设某个服务需要由 DI 提供的 `ILogger<T>` 实例：

```csharp
.ConfigureServices(services =>
    services.AddSingleton<IExampleService>(container =>
        new DefaultExampleService
        {
            Logger = container.GetRequiredService<ILogger<IExampleService>>()
        }));
```

上述代码是 [Func<IServiceProvider, IExampleService>](/dotnet/api/system.func-2)，它在 DI 容器首次需要构造 `IExampleService` 实例时运行。 可以用这种方式访问任何已注册的服务。

## <a name="built-in-logging-providers"></a>内置日志记录提供程序

Microsoft 扩展包含以下日志记录提供程序作为运行时库的一部分：

- [控制台](#console)
- [调试](#debug)
- [EventSource](#event-source)
- [EventLog](#windows-eventlog)

以下日志记录提供程序由 Microsoft 提供，但不是运行时库的一部分。 它们必须作为附加 NuGet 包安装。

- [AzureAppServicesFile 和 AzureAppServicesBlob](#azure-app-service)
- [ApplicationInsights](#azure-application-insights)

### <a name="console"></a>控制台

`Console` 提供程序将输出记录到控制台。

### <a name="debug"></a>调试

`Debug` 提供程序通过使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类来写入日志输出。 对 `System.Diagnostics.Debug.WriteLine` 的调用写入到 `Debug` 提供程序。

在 Linux 上，`Debug` 提供程序日志位置取决于分发，并且可以是以下位置之一：

- */var/log/message*
- */var/log/syslog*

### <a name="event-source"></a>事件来源

`EventSource` 提供程序写入名称为 `Microsoft-Extensions-Logging` 的跨平台事件源。 在 Windows 上，提供程序使用的是 [ETW](/windows/win32/etw/event-tracing-portal)。

#### <a name="dotnet-trace-tooling"></a>dotnet 跟踪工具

[dotnet-trace](../diagnostics/dotnet-trace.md) 工具是一种跨平台 CLI 全局工具，可用于收集正在运行的进程的 .NET Core 跟踪。 该工具会使用 <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> 收集 <xref:Microsoft.Extensions.Logging.EventSource> 提供程序数据。

有关安装说明，请参阅 [dotnet-trace](../diagnostics/dotnet-trace.md)。 有关使用 `dotnet-trace` 的诊断教程，请查看[在 .NET Core 中调试 CPU 使用率高的问题](../diagnostics/debug-highcpu.md)。

### <a name="windows-eventlog"></a>Windows 事件日志

`EventLog` 提供程序将日志输出发送到 Windows 事件日志。 与其他提供程序不同，`EventLog` 提供程序不继承默认的非提供程序设置。 如果未指定 `EventLog` 日志设置，则它们默认为 `LogLevel.Warning`。

若要记录低于 <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 的事件，请显式设置日志级别。 以下示例将事件日志的默认日志级别设置为 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>：

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)可以传入 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>。 如果为 `null` 或未指定，则使用以下默认设置：

- `LogName`：“Application”
- `SourceName`：“.NET Runtime”
- `MachineName`：使用本地计算机名称。

以下代码将 `SourceName` 从默认值 `".NET Runtime"` 更改为 `CustomLogs`：

```csharp
public class Program
{
    static async Task Main(string[] args)
    {
        using IHost host = CreateHostBuilder(args).Build();

        // Application code should start here.

        await host.RunAsync();
    }

    static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureLogging(logging =>
                logging.AddEventLog(configuration =>
                    configuration.SourceName = "CustomLogs"));
}
```

### <a name="azure-app-service"></a>Azure 应用服务

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](/azure/storage/blobs/storage-quickstart-blobs-dotnet#what-is-blob-storage)。

运行时库中不包括该提供程序包。 若要使用提供程序，请将提供程序包添加到项目。

要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：

```csharp
class Program
{
    static async Task Main(string[] args)
    {
        using IHost host = CreateHostBuilder(args).Build();

        // Application code should start here.

        await host.RunAsync();
    }

    static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureLogging(logging =>
                logging.AddAzureWebAppDiagnostics())
            .ConfigureServices(services =>
                services.Configure<AzureFileLoggerOptions>(options =>
                {
                    options.FileName = "azure-diagnostics-";
                    options.FileSizeLimit = 50 * 1024;
                    options.RetainedFileCountLimit = 5;
                })
                .Configure<AzureBlobLoggerOptions>(options =>
                {
                    options.BlobName = "log.txt";
                }));
}
```

部署到 Azure 应用服务时，应用使用 Azure 门户的“应用服务”页面的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows)部分中的设置。 更新以下设置后，更改立即生效，无需重启或重新部署应用。

- 应用程序日志记录(Filesystem)
- 应用程序日志记录(Blob)

日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt   。 默认文件大小上限为 10 MB，默认最大保留文件数为 2。 默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。

仅当项目在 Azure 环境中运行时，此提供程序才记录日志。

#### <a name="azure-log-streaming"></a>Azure 日志流式处理

Azure 日志流式处理支持从以下位置实时查看日志活动：

- 应用服务器
- Web 服务器
- 请求跟踪失败

要配置 Azure 日志流式处理，请执行以下操作：

- 从应用的门户页导航到“应用服务日志”页。
- 将“应用程序日志记录(Filesystem)”设置为“开”   。
- 选择日志级别  。 此设置仅适用于 Azure 日志流式处理。

导航到“日志流”页面以查看日志。 记录的消息使用 `ILogger` 接口进行记录。

### <a name="azure-application-insights"></a>Azure Application Insights

[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 提供程序包将日志写入 [Azure Application Insights](/azure/azure-monitor/app/cloudservices)。 Application Insights 是一项服务，可监视 Web 应用并提供用于查询和分析遥测数据的工具。 如果使用此提供程序，则可以使用 Application Insights 工具来查询和分析日志。

有关更多信息，请参见以下资源：

- [Application Insights 概述](/azure/application-insights/app-insights-overview)
- [.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 如果想要在没有其他 Application Insights 遥测的情况下实现日志记录提供程序，请从这里开始。
- [Application Insights 日志记录适配器](/azure/azure-monitor/app/asp-net-trace-logs)。
- [安装、配置和初始化 Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 网站上的交互式教程。

## <a name="third-party-logging-providers"></a>第三方日志记录提供程序

下面是适用于 .NET 工作负载的一些第三方日志记录提供程序：

- [elmah.io](https://elmah.io)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）
- [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）
- [JSNLog](http://jsnlog.com)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）
- [KissLog.net](https://kisslog.net)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）
- [Log4Net](https://logging.apache.org/log4net)（[GitHub 存储库](https://github.com/apache/logging-log4net)）
- [Loggr](https://loggr.net)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）
- [NLog](https://nlog-project.org)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）
- [NReco.Logging](https://github.com/nreco/logging/blob/master/README.md)（[GitHub 存储库](https://github.com/nreco/logging)）
- [Sentry](https://sentry.io/welcome)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）
- [Serilog](https://serilog.net)（[GitHub 存储库](https://github.com/serilog/serilog-sinks-console)）
- [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[GitHub 存储库](https://github.com/googleapis/google-cloud-dotnet)）

某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。

使用第三方框架类似于使用以下内置提供程序之一：

1. 将 NuGet 包添加到你的项目。
1. 调用日志记录框架提供的 `ILoggerFactory` 或 `ILoggingBuilder` 扩展方法。

有关详细信息，请参阅各提供程序的相关文档。 Microsoft 不支持第三方日志记录提供程序。

## <a name="see-also"></a>另请参阅

- [.NET 中的日志记录](logging.md)。
- [在 .NET 中实现自定义日志记录提供程序](custom-logging-provider.md)。
- [.NET 中的高性能日志记录](high-performance-logging.md)。

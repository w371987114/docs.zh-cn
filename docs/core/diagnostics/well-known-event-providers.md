---
title: .NET 中的已知事件提供程序
description: 查看 .NET 运行时和库发布的提供程序和事件。
ms.topic: reference
ms.date: 12/21/2020
ms.openlocfilehash: 37aa9551397bb3e3e8858a1289ddc3921327b722
ms.sourcegitcommit: 42d436ebc2a7ee02fc1848c7742bc7d80e13fc2f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102105162"
---
# <a name="well-known-event-providers-in-net"></a>.NET 中的已知事件提供程序

.NET 运行时和库通过许多不同的事件提供程序编写诊断事件。 根据诊断需求，可以选择要启用的相应提供程序。 本文介绍了 .NET 运行时和库中最常用的一些事件提供程序。

## <a name="coreclr"></a>CoreCLR

### <a name="microsoft-windows-dotnetruntime-provider"></a>“Microsoft-Windows-DotNETRuntime”提供程序

此提供程序从 .NET 运行时发出各种事件，包括 GC、加载程序、JIT、异常和其他事件。 请在[运行时提供程序事件列表](../../fundamentals/diagnostics/runtime-events.md)中详细了解此提供程序中的各种事件。

### <a name="microsoft-dotnetcore-sampleprofiler-provider"></a>“Microsoft-DotNETCore-SampleProfiler”提供程序

此提供程序是 .NET 运行时事件提供程序，用于对托管调用堆栈进行 CPU 采样。 启用后，它会每隔 10 毫秒捕获一次每个线程的托管调用堆栈的快照。 若要启用此捕获功能，必须将 <xref:System.Diagnostics.Tracing.EventLevel> 指定为 `Informational` 或更高级别。

## <a name="framework-libraries"></a>框架库

### <a name="microsoft-extensions-dependencyinjection-provider"></a>“Microsoft-Extensions-DependencyInjection”提供程序

此提供程序记录来自 DependencyInjection 的信息。 下表显示 `Microsoft-Extensions-DependencyInjection` 提供程序记录的事件：

|事件名称|Level|说明|
|----------|-----|-----------|
|`CallSiteBuilt`|详细级别 (5)|调用站点已生成。|
|`ServiceResolved`|详细级别 (5)|服务已解决。|
|`ExpressionTreeGenerated`|详细级别 (5)|表达式树已生成。|
|`DynamicMethodBuilt`|详细级别 (5)|<xref:System.Reflection.Emit.DynamicMethod> 已生成。|

### <a name="systembuffersarraypooleventsource-provider"></a>“System.Buffers.ArrayPoolEventSource”提供程序

此提供程序记录来自 ArrayPool 的信息。 下表显示了 `ArrayPoolEventSource` 记录的事件：

|事件名称|Level|说明|
|----------|-----|-----------|
|`BufferRented`|详细级别 (5)|缓冲区已成功租用。|
|`BufferAllocated`|信息性 (4)|缓冲区由池分配。|
|`BufferReturned`|详细级别 (5)|缓冲区返回到池中。|
|`BufferTrimmed`|信息性 (4)|由于内存不足或不活动，试图释放缓冲区。|
|`BufferTrimPoll`|信息性 (4)|正在检查以剪裁缓冲区。|

### <a name="systemnethttp-provider"></a>“System.Net.Http”提供程序

此提供程序记录来自 HTTP 堆栈的信息。 下表显示了 `System.Net.Http` 提供程序记录的事件：

|事件名称|Level|说明|
|----------|-----|-----------|
|RequestStart|信息性 (4)|HTTP 请求已启动。|
|RequestStop|信息性 (4)|HTTP 请求已完成。|
|RequestFailed|错误 (2)|HTTP 请求失败。|
|ConnectionEstablished|信息性 (4)|HTTP 连接已建立。|
|ConnectionClosed|信息性 (4)|HTTP 连接已关闭。|
|RequestLeftQueue|信息性 (4)|HTTP 请求已离开请求队列。|
|RequestHeadersStart|信息性 (4)|针对标头的 HTTP 请求已启动。|
|RequestHeaderStop|信息性 (4)|针对标头的 HTTP 请求已完成。|
|RequestContentStart|信息性 (4)|针对内容的 HTTP 请求已启动。|
|RequestContentStop|信息性 (4)|针对内容的 HTTP 请求已完成。|
|ResponseHeadersStart|信息性 (4)|针对标头的 HTTP 响应已启动。|
|ResponseHeaderStop|信息性 (4)|针对标头的 HTTP 响应已完成。|
|ResponseContentStart|信息性 (4)|针对内容的 HTTP 响应已启动。|
|ResponseContentStop|信息性 (4)|针对内容的 HTTP 响应已完成。|

### <a name="systemnetnameresolution-provider"></a>“System.Net.NameResolution”提供程序

此提供程序记录与域名解析有关的信息。 下表显示了 `System.Net.NameResolution` 记录的事件：

|事件名称|Level|说明|
|----------|-----|-----------|
|`ResolutionStart`|信息性 (4)|域名解析已启动。|
|`ResolutionStop`|信息性 (4)|域名解析已完成。|
|`ResolutionFailed`|信息性 (4)|域名解析失败。|

### <a name="systemnetsockets-provider"></a>“System.Net.Sockets”提供程序

此提供程序记录来自 <xref:System.Net.Sockets.Socket> 的信息。 下表显示了 `System.Net.Sockets` 提供程序记录的事件：

|事件名称|Level|说明|
|----------|-----|-----------|
|`ConnectStart`|信息性 (4)|尝试开始套接字连接已启动。|
|`ConnectStop`|信息性 (4)|尝试开始套接字连接已完成。|
|`ConnectFailed`|信息性 (4)|尝试开始套接字连接失败。|
|`AcceptStart`|信息性 (4)|尝试接受套接字连接已启动。|
|`AcceptStop`|信息性 (4)|尝试接受套接字连接已完成。|
|`AcceptFailed`|信息性 (4)|尝试接受套接字连接失败。|

### <a name="systemthreadingtaskstpleventsource-provider"></a>“System.Threading.Tasks.TplEventSource”提供程序

此提供程序记录有关[任务并行库](../../standard/parallel-programming/task-parallel-library-tpl.md)的信息，例如任务计划程序事件。 下表显示了 `TplEventSource` 记录的事件：

|事件名称|关键字|Level|说明|
|----------|-------|-----|-----------|
|`TaskScheduled`|`TaskTransfer`(`0x1`)<br /><br />`Tasks`(`0x2`)|信息性 (4)|<xref:System.Threading.Tasks.Task> 已加入任务计划程序的队列中。|
|`TaskStarted`|`Tasks`(`0x2`)|信息性 (4)|<xref:System.Threading.Tasks.Task> 已开始执行。|
|`TaskCompleted`|`TaskStops`(`0x40`)|信息性 (4)|<xref:System.Threading.Tasks.Task> 已完成执行。|
|`TaskWaitBegin`|`TaskTransfer`(`0x1`)<br /><br />`TaskWait`(`0x2`)|信息性 (4)|当对 <xref:System.Threading.Tasks.Task> 完成的隐式或显式等待启动时触发。|
|`TaskWaitEnd`|`Tasks`(`0x2`)|详细级别 (5)|等待 <xref:System.Threading.Tasks.Task> 完成返回时触发。|
|`TaskWaitContinuationStarted`|`Tasks`(`0x2`)|详细级别 (5)|当与 `TaskWaitEnd` 关联的工作（方法）启动时触发。|
|`TaskWaitContinuationCompleted`|`TaskStops`(`0x40`)|详细级别 (5)|当与 `TaskWaitEnd` 关联的工作（方法）完成时触发。|
|`AwaitTaskContinuationScheduled`|`TaskTransfer`(`0x1`)<br /><br />`Tasks`(`0x2`)|信息性 (4)|在安排 <xref:System.Threading.Tasks.Task> 的异步持续时触发。|

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core 还提供了数种事件来帮助诊断 ASP.NET Core 堆栈中的问题。

若要详细了解 ASP.NET Core 中的事件以及如何使用这些事件，请参阅[登录 .NET Core 和 ASP.NET Core](/aspnet/core/fundamentals/logging/)。

## <a name="entity-framework-core"></a>Entity Framework Core

EF Core 也提供了事件来帮助你诊断 EF Core 中的问题。

若要详细了解 EF Core 中的事件及其使用方式，请查看 [EF Core 中的 .NET 事件](/ef/core/logging-events-diagnostics/events)。

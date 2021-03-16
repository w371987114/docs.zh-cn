---
title: .NET 中的高性能日志记录
author: IEvangelist
description: 了解如何使用 LoggerMessage 创建可缓存的委托。对于高性能日志记录方案，这些委托需要更少的对象分配。
ms.author: dapine
ms.date: 01/04/2021
ms.openlocfilehash: 0031ff7a9f70cb0cf724fdf6dfa4fbe0a44af7c1
ms.sourcegitcommit: 5d9cee27d9ffe8f5670e5f663434511e81b8ac38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "102401972"
---
# <a name="high-performance-logging-in-net"></a>.NET 中的高性能日志记录

<xref:Microsoft.Extensions.Logging.LoggerMessage> 类公开了用于创建可缓存委托的功能，该功能比[记录器扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A>）需要的对象分配和计算开销要少。 对于高性能日志记录方案，请使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式。

与记录器扩展方法相比，<xref:Microsoft.Extensions.Logging.LoggerMessage> 具有以下性能优势：

- 记录器扩展方法需要将值类型（例如 `int`）“装箱”（转换）到 `object` 中。 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式使用带强类型参数的静态 <xref:System.Action> 字段和扩展方法来避免装箱。
- 记录器扩展方法每次写入日志消息时必须分析消息模板（命名的格式字符串）。 如果已定义消息，那么 <xref:Microsoft.Extensions.Logging.LoggerMessage> 只需分析一次模板即可。

示例应用展示了具有优先级队列处理辅助角色服务的 <xref:Microsoft.Extensions.Logging.LoggerMessage> 功能。 应用按优先级顺序处理工作项。 发生这些操作时，通过 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式生成日志消息。

## <a name="define-a-logger-message"></a>定义记录器消息

使用 [Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define%2A) 创建一个用于记录消息的 <xref:System.Action> 委托。 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define%2A> 重载允许向命名的格式字符串（模板）传递最多六个类型参数。

提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define%2A> 方法的字符串是一个模板，而不是内插字符串。 占位符按照指定类型的顺序填充。 模板中的占位符名称在各个模板中应当具备描述性和一致性。 它们在结构化的日志数据中充当属性名称。 对于占位符名称，建议使用[帕斯卡拼写法](../../standard/design-guidelines/capitalization-conventions.md)。 例如：`{Item}`、`{DateTime}`。

每条日志消息都是一个 <xref:System.Action>，保存在由 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define%2A) 创建的静态字段中。 例如，示例应用创建一个字段来描述处理工作项的日志消息：

:::code language="csharp" source="snippets/configuration/worker-service-options/Extensions/LoggerExtensions.cs" id="FailedProcessingField":::

对于 <xref:System.Action>，指定：

- 日志级别。
- 具有静态扩展方法名称的唯一事件标识符 (<xref:Microsoft.Extensions.Logging.EventId>)。
- 消息模板（命名的格式字符串）。

由于对工作项处理取消排队，辅助角色服务应用会进行下列设置：

- 将日志级别设置为 <xref:Microsoft.Extensions.Logging.LogLevel.Critical?displayProperty=nameWithType>。
- 将事件 ID 设置为具有 `FailedToProcessWorkItem` 方法名称的 `13`。
- 将消息模板（命名的格式字符串）设置为字符串。

:::code language="csharp" source="snippets/configuration/worker-service-options/Extensions/LoggerExtensions.cs" id="FailedProcessingAssignment":::

结构化日志记录存储可以使用事件名称（当它获得事件 ID 时）来丰富日志记录。 例如，[Serilog](https://github.com/serilog/serilog-extensions-logging) 使用该事件名称。

通过强类型扩展方法调用 <xref:System.Action>。 `PriorityItemProcessed` 方法会在每次处理工作项时记录一条消息。 而在（如果）发生异常时则调用 `FailedToProcessWorkItem`：

:::code language="csharp" source="snippets/configuration/worker-service-options/Worker.cs" range="18-39" highlight="15-18":::

检查应用的控制台输出：

```console
crit: WorkerServiceOptions.Example.Worker[13]
      Epic failure processing item!
      System.Exception: Failed to verify communications.
         at WorkerServiceOptions.Example.Worker.ExecuteAsync(CancellationToken stoppingToken) in
         ..\Worker.cs:line 27
```

要将参数传递给日志消息，创建静态字段时最多定义六种类型。 在通过定义 <xref:System.Action> 字段的 `WorkItem` 类型来处理项时，示例应用会记录工作项的详细信息：

:::code language="csharp" source="snippets/configuration/worker-service-options/Extensions/LoggerExtensions.cs" id="ProcessingItemField":::

委托的日志消息模板从提供的类型接收其占位符值。 示例应用定义一个委托，用于在 item 参数是 `WorkItem` 的位置添加一个工作项：

:::code language="csharp" source="snippets/configuration/worker-service-options/Extensions/LoggerExtensions.cs" id="ProcessingItemAssignment":::

用于记录正在处理工作项的静态扩展方法 `PriorityItemProcessed` 接收工作项参数值并将其传递给 <xref:System.Action> 委托：

:::code language="csharp" source="snippets/configuration/worker-service-options/Extensions/LoggerExtensions.cs" id="ProcessingItemMethod":::

在辅助角色服务的 `ExecuteAsync` 方法中，调用 `PriorityItemProcessed` 以记录消息：

:::code language="csharp" source="snippets/configuration/worker-service-options/Worker.cs" range="18-39" highlight="12":::

检查应用的控制台输出：

```console
info: WorkerServiceOptions.Example.Worker[1]
      Processing priority item: Priority-Extreme (50db062a-9732-4418-936d-110549ad79e4): 'Verify communications'
```

## <a name="define-logger-message-scope"></a>指定记录器消息作用域

[DefineScope(string)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope%2A) 方法创建一个用于定义[日志作用域](logging.md#log-scopes)的 <xref:System.Func%601> 委托。 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope%2A> 重载允许向命名的格式字符串（模板）传递最多三个类型参数。

<xref:Microsoft.Extensions.Logging.LoggerMessage.Define%2A> 方法也一样，提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope%2A> 方法的字符串是一个模板，而不是内插字符串。 占位符按照指定类型的顺序填充。 模板中的占位符名称在各个模板中应当具备描述性和一致性。 它们在结构化的日志数据中充当属性名称。 对于占位符名称，建议使用[帕斯卡拼写法](../../standard/design-guidelines/capitalization-conventions.md)。 例如：`{Item}`、`{DateTime}`。

使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope%2A> 方法定义一个[日志作用域](logging.md#log-scopes)，以应用到一系列日志消息中。 在 appsettings.json 的控制台记录器部分启用 `IncludeScopes`：

:::code language="json" source="snippets/configuration/worker-service-options/appsettings.json" highlight="3-5":::

要创建日志作用域，请添加一个字段来保存该作用域的 <xref:System.Func%601> 委托。 示例应用创建一个名为 `_processingWorkScope` (Internal/LoggerExtensions.cs) 的字段：

:::code language="csharp" source="snippets/configuration/worker-service-options/Extensions/LoggerExtensions.cs" id="ProcessingWorkField":::

使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope%2A> 来创建委托。 调用委托时最多可以指定三种类型作为模板参数使用。 示例应用使用包含处理开始的日期时间的消息模板：

:::code language="csharp" source="snippets/configuration/worker-service-options/Extensions/LoggerExtensions.cs" id="ProcessingWorkAssignment":::

为日志消息提供一种静态扩展方法。 包含已命名属性的任何类型参数（这些参数出现在消息模板中）。 示例应用接受自定义时间戳的 `DateTime` 以记录和返回 `_processingWorkScope`：

:::code language="csharp" source="snippets/configuration/worker-service-options/Extensions/LoggerExtensions.cs" id="ProcessingWorkMethod":::

该作用域将日志记录扩展调用包装在 [using](../../csharp/language-reference/keywords/using-statement.md) 块中：

:::code language="csharp" source="snippets/configuration/worker-service-options/Worker.cs" range="18-39" highlight="4":::

检查应用控制台输出中的日志消息。 以下结果显示日志消息的优先级排序，其中包括日志作用域消息：

```console
info: WorkerServiceOptions.Example.Worker[1]
      => Processing work, started at: 09/25/2020 14:30:45
      Processing priority item: Priority-Extreme (f5090ede-a337-4041-b914-f6bc0db5ae64): 'Verify communications'
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: ..\worker-service-options
info: WorkerServiceOptions.Example.Worker[1]
      => Processing work, started at: 09/25/2020 14:30:45
      Processing priority item: Priority-High (496d440f-2007-4391-b179-09d75ab52373): 'Validate collection'
info: WorkerServiceOptions.Example.Worker[1]
      => Processing work, started at: 09/25/2020 14:30:45
      Processing priority item: Priority-Medium (dea9e3f4-d7df-46d2-b7cd-5e0232eb98a5): 'Propagate selections'
info: WorkerServiceOptions.Example.Worker[1]
      => Processing work, started at: 09/25/2020 14:30:45
      Processing priority item: Priority-Medium (089d7f0d-da72-4b55-92fe-57b147838056): 'Enter pooling [contention]'
info: WorkerServiceOptions.Example.Worker[1]
      => Processing work, started at: 09/25/2020 14:30:45
      Processing priority item: Priority-Low (6e68c4be-089f-4450-9080-1ea63fcbb686): 'Health check network'
info: WorkerServiceOptions.Example.Worker[1]
      => Processing work, started at: 09/25/2020 14:30:45
      Processing priority item: Priority-Deferred (6f324134-6bb6-455f-81d4-553ab307c421): 'Ping weather service'
info: WorkerServiceOptions.Example.Worker[1]
      => Processing work, started at: 09/25/2020 14:30:45
      Processing priority item: Priority-Deferred (37bf736c-7a26-4a2a-9e56-e89bcf3b8f35): 'Set process state'
```

## <a name="see-also"></a>另请参阅

- [.NET 中的日志记录](logging.md)

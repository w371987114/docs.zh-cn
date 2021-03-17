---
title: 跟踪应用程序和在应用程序中插入检测点
description: 在 .NET 中跟踪和检测应用程序。 利用跟踪功能，可以在应用程序运行时监视其执行情况。 检测使你可以衡量性能级别。
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- tracing [.NET Framework]
- debugging [.NET Framework], instrumentation
- performance monitoring, instrumentation
- instrumentation, about instrumentation
- tracing [.NET Framework], about tracing
- performance monitoring, tracing code
- Trace class, instrumentation for .NET applications
ms.assetid: 773b6fc4-9013-4322-b728-5dec7a72e743
ms.openlocfilehash: e477a474ab850239068246057e94b94c983a99c8
ms.sourcegitcommit: d623f686701b94bef905ec5e93d8b55d031c5d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2021
ms.locfileid: "103624196"
---
# <a name="tracing-and-instrumenting-applications"></a>跟踪应用程序和在应用程序中插入检测点

跟踪是在应用程序运行时监视其执行情况的一种方式。 可在开发 .NET Framework 应用程序时向其添加跟踪和调试检测，也可在开发应用程序时或在部署此应用程序之后使用此检测。 可使用 <xref:System.Diagnostics.Trace?displayProperty=nameWithType>、<xref:System.Diagnostics.Debug?displayProperty=nameWithType>和 <xref:System.Diagnostics.TraceSource?displayProperty=nameWithType> 类将错误和应用程序执行的相关信息记录在日志、文本文件或其他设备中，以供将来分析。  
  
 术语“检测”是指监视或测量产品的性能级别和诊断错误的功能。 在编程中，即指应用程序合并以下功能的能力：  
  
- 代码跟踪 - 接收运行时有关应用程序执行的信息性消息。  
  
- 调试 - 跟踪并修复开发中的应用程序的编程错误。 有关详细信息，请参阅[调试](/visualstudio/debugger/debugger-feature-tour)。  
  
- 性能计数器 - 可用于跟踪应用程序性能的组件。 有关详细信息，请参阅 [性能计时器](performance-counters.md)。  
  
- **事件日志** -可用于接收和跟踪应用程序执行中的主要事件的组件。 有关更多信息，请参见 <xref:System.Diagnostics.EventLog> 类。  
  
 通过将跟踪语句置于代码中的关键位置来检测应用程序非常适合分布式应用程序。 通过使用跟踪语句，可检测应用程序，从而在出现故障时显示相关信息并监视应用程序的运行情况。  
  
 <xref:System.Diagnostics.TraceSource> 类提供了增强的跟踪功能，可用来代替较旧的 <xref:System.Diagnostics.Trace> 和 <xref:System.Diagnostics.Debug> 跟踪类的静态方法。 常见的 <xref:System.Diagnostics.Trace> 和 <xref:System.Diagnostics.Debug> 类仍然使用广泛，但建议对新的跟踪命令（如 <xref:System.Diagnostics.TraceSource.TraceEvent%2A> 和 <xref:System.Diagnostics.TraceSource.TraceData%2A>）使用 <xref:System.Diagnostics.TraceSource> 类。  
  
 <xref:System.Diagnostics.Trace> 和 <xref:System.Diagnostics.Debug> 类相同，只不过 <xref:System.Diagnostics.Trace> 类的过程和函数默认编译为发布版本，<xref:System.Diagnostics.Debug> 类的并非如此。  
  
 <xref:System.Diagnostics.Trace> 和 <xref:System.Diagnostics.Debug> 类提供了在开发期间或部署之后监视和检查应用程序性能的方法。 例如，您可以使用 <xref:System.Diagnostics.Trace> 类跟踪在已部署的应用程序中发生的特定类型的操作（例如新建数据库连接），因而可以监视该应用程序的效率。  
  
## <a name="code-tracing-and-debugging"></a>代码跟踪和调试  

 在开发过程中，使用 <xref:System.Diagnostics.Debug> 类的输出方法，可以在 Visual Studio 集成开发环境 (IDE) 的“输出”窗口中显示消息。 例如：  
  
```vb  
Trace.WriteLine("Hello World!")  
Debug.WriteLine("Hello World!")  
```  
  
```csharp  
System.Diagnostics.Trace.WriteLine("Hello World!");  
System.Diagnostics.Debug.WriteLine("Hello World!");  
```  
  
 上述每个示例将在“输出窗口”中显示“Hello World!” 当应用程序在调试器中运行时。  
  
 由此，你可以根据应用程序在测试环境中的行为对它进行调试并优化其性能。 在调试版本中调试应用程序时，可以打开 <xref:System.Diagnostics.Debug> 条件特性，以便接收所有调试输出。 当您的应用程序准备好发布时，可以在关闭 <xref:System.Diagnostics.Debug> 条件特性的情况下编译您的发布版本，使编译器不会在最终可执行文件中包含调试代码。 有关详细信息，请参阅[如何：使用跟踪和调试进行条件编译](how-to-compile-conditionally-with-trace-and-debug.md)。 有关应用程序的不同生成配置的详细信息，请参阅[编译和生成](/visualstudio/ide/compiling-and-building-in-visual-studio)。  
  
 你也可以使用 <xref:System.Diagnostics.Trace> 类的方法来跟踪已安装的应用程序中的代码执行情况。 可以将 [Trace 开关](trace-switches.md)放置到代码中，从而控制是否进行跟踪以及跟踪的范围。 这样即可在生产环境中监视应用程序的状态。 这在业务应用程序中特别重要，因为业务应用程序会在多个计算机中使用多个运行的组件。 可通过配置文件控制如何在部署后使用开关。 有关详细信息，请参阅[如何：创建、初始化和配置跟踪开关](how-to-create-initialize-and-configure-trace-switches.md)。  
  
 开发要对其使用跟踪的应用程序时，应用程序代码中通常要包含跟踪和调试消息。 准备好部署应用程序时，无需打开“调试”条件属性就可编译发布版本。 但是，也可以打开“跟踪”条件属性，以便编译器将跟踪代码包括到可执行文件中。 有关详细信息，请参阅[如何：使用跟踪和调试进行条件编译](how-to-compile-conditionally-with-trace-and-debug.md)。  
  
### <a name="phases-of-code-tracing"></a>代码跟踪的阶段  

 代码跟踪有 3 个阶段：  
  
1. 检测 - 向应用程序添加跟踪代码。  
  
2. 跟踪 - 跟踪代码向指定目标写入相应信息。  
  
3. 分析 - 评估跟踪信息以确定和了解应用程序中的问题。  
  
 开发过程中，所有调试和跟踪输出方法都默认将信息写入 Visual Studio 的输出窗口。 在已部署的应用程序中，方法将跟踪信息写入所指定的目标。 有关指定跟踪或调试的输出目标的详细信息，请参阅[跟踪侦听器](trace-listeners.md)。  
  
 以下内容概述了使用跟踪来分析和更正已部署的应用程序中的潜在问题时通常涉及的主要步骤。 有关如何执行这些步骤的详细信息，请参阅相应链接。  
  
##### <a name="to-use-tracing-in-an-application"></a>若要在应用程序中使用跟踪  
  
1. 请思考部署应用程序后想要现场接收哪种跟踪输出。  
  
2. 创建一组开关。 有关详细信息，请参阅[如何：配置 Trace 开关](how-to-create-initialize-and-configure-trace-switches.md)。  
  
3. 向应用程序代码添加跟踪语句。  
  
4. 确定要显示跟踪输出的位置并添加相应的侦听器。 有关详细信息，请参阅[创建和初始化跟踪侦听器](how-to-create-and-initialize-trace-listeners.md)。  
  
5. 测试和调试应用程序及其包含的跟踪代码。  
  
6. 使用下面一个过程将应用程序编译为可执行代码：  
  
    - 使用“构建”菜单以及“解决方案资源管理器”中“属性页”对话框的“调试”页。 在 Visual Studio 中编译时，请使用此选项。  
  
         \- 或 -  
  
    - 对编译的命令行方法使用“跟踪”和“调试”编译器指令。 有关详细信息，请参阅[使用跟踪和调试进行条件编译](how-to-compile-conditionally-with-trace-and-debug.md)。 从命令行进行编译时，请使用此选项。  
  
7. 如果在运行时出现问题，请打开相应的跟踪开关。 有关详细信息，请参阅[配置跟踪开关](how-to-create-initialize-and-configure-trace-switches.md)。  
  
     跟踪代码向指定目标写入跟踪消息，如屏幕、文本文件或事件日志。 集合中包含的侦听器类型 <xref:System.Diagnostics.Trace.Listeners%2A?displayProperty=nameWithType> 确定目标。  
  
8. 分析跟踪消息以确定和了解应用程序中的问题。  
  
## <a name="trace-instrumentation-and-distributed-applications"></a>跟踪检测和分布式应用程序  

 在创建分布式应用程序时，可能难以按要使用的方式测试应用程序。 开发团队几乎都不能测试操作系统或 Web 浏览器（包括所有本地化的语言选项）的所有潜在组合，也不能模拟将同时访问应用程序的大量用户。 在这些情况下，无法测试分布式应用程序将如何响应高容量、不同的设置和最终用户的唯一行为。 此外，分布式应用程序的很多部件都不具有可直接与之交互或可用于查看这些部件的行为的用户界面。  
  
 但是，可通过以下方式补偿此不足之处：启用分布式应用程序以描述系统管理员感兴趣的某些事件（尤其是故障事件），并检测应用程序（即，将跟踪语句置于代码中的关键位置）。 由此，如果运行时发生意外事件（例如，响应时间过于缓慢），就可确定可能的原因。  
  
 借助跟踪语句，可避免执行以下艰难任务：检查原始源代码、对其进行修改、重新编译以及试图在调试环境中生成运行时错误。 请记住，检测应用程序这一操作可用于显示错误以及监视性能。  
  
## <a name="strategic-placement-of-trace-statements"></a>将跟踪语句置于关键位置  

 放置供运行时期间使用的跟踪语句时，必须特别小心。 注意考虑部署的应用程序中可能需要什么跟踪信息，以便充分涵盖所有可能的跟踪方案。 然而，使用跟踪的应用程序差别很大，因此关于将跟踪置于关键位置，没有通用准则。 有关放置跟踪语句的详细信息，请参阅[如何：向应用程序代码添加跟踪语句](how-to-add-trace-statements-to-application-code.md)。  
  
## <a name="output-from-tracing"></a>跟踪的输出  

 跟踪输出由名为“侦听器”的对象收集。 侦听器是接收跟踪输出，并将它写入输出设备（通常为窗口、日志或文本文件）的对象。 跟踪侦听器在创建后，通常会被添加到 <xref:System.Diagnostics.Trace.Listeners%2A?displayProperty=nameWithType> 集合中，这样侦听器就可以接收所有跟踪输出。  
  
 在任何情况下，跟踪信息至少会写入默认的 <xref:System.Diagnostics.Trace> 输出目标 <xref:System.Diagnostics.DefaultTraceListener>。 如果您因为某种原因删除了 <xref:System.Diagnostics.DefaultTraceListener> 而没有在 <xref:System.Diagnostics.Trace.Listeners%2A> 集合中添加任何其他侦听器，则将收不到任何跟踪消息。 有关详细信息，请参阅 [跟踪侦听器](trace-listeners.md)。  
  
 下表列出了六个写入跟踪信息的 <xref:System.Diagnostics.Debug> 成员和 <xref:System.Diagnostics.Trace> 方法。  
  
|方法|输出|  
|------------|------------|  
|`Assert`|指定的文本；若未指定，则为调用堆栈。 仅当在语句中指定为参数的条件为 false 时，才写入输出 `Assert` 。 |  
|`Fail`|指定的文本；若未指定，则为调用堆栈。|  
|`Write`|指定的文本。|  
|`WriteIf`|如果满足指定为语句中的参数的条件，则为指定的文本 `WriteIf` 。|  
|`WriteLine`|指定的文本和一个回车符。|  
|`WriteLineIf`|如果满足指定为语句中的参数的条件，则为指定的文本和一个回车符 `WriteLineIf` 。|  
  
 <xref:System.Diagnostics.Trace.Listeners%2A> 集合中所有的侦听器都会接收到上表中介绍的消息，但种类不同的侦听器在接到消息后所执行的操作可能也不同。 例如， <xref:System.Diagnostics.DefaultTraceListener> 当接收到或失败通知时，将显示一个断言对话框 `Fail` `Assert` ，而只是将 <xref:System.Diagnostics.TextWriterTraceListener> 输出写入流中。  
  
 可通过实现自己的侦听器生成自定义结果。 例如，自定义跟踪侦听器可能将消息显示给消息框，或连接到数据库以将消息添加到表中。 所有自定义侦听器都应支持上述 6 种方法。 有关创建开发人员定义的侦听器的详细信息，请参阅 .NET Framework 参考中的 <xref:System.Diagnostics.TraceListener>。  
  
 `Write`和 `WriteLine` 方法始终写入指定的文本。 `Assert`、 `WriteIf` 和 `WriteLineIf` 需要一个布尔参数，用于控制它们是否写入指定的文本; 只有在表达式为 **true** 时才会写入指定的文本 (对于 `WriteIf` 和 `WriteLineIf`) ，或为 **false** (`Assert`) 。 `Fail`方法始终写入指定的文本。 有关详细信息，请参阅[如何：向应用程序代码添加跟踪语句](how-to-add-trace-statements-to-application-code.md)和 .NET Framework 参考。  
  
## <a name="security-concerns"></a>安全问题  

 如果在部署 ASP.NET 应用程序之前未禁用跟踪和调试，你的应用程序可能会泄漏自身的相关信息，从而被恶意程序利用。 有关详细信息，请参阅[如何：使用跟踪和调试进行条件编译](how-to-compile-conditionally-with-trace-and-debug.md)、[编译和生成](/visualstudio/ide/compiling-and-building-in-visual-studio)和[如何：创建、初始化和配置跟踪开关](how-to-create-initialize-and-configure-trace-switches.md)。 也可通过 Internet 信息服务 (IIS) 配置调试。  
  
## <a name="see-also"></a>另请参阅

- <xref:System.Diagnostics.Trace>
- <xref:System.Diagnostics.TraceSource>
- [代码协定](code-contracts.md)
- [C#, F#, and Visual Basic Project Types](/visualstudio/debugger/debugging-preparation-csharp-f-hash-and-visual-basic-project-types)（C#、F# 和 Visual Basic 项目类型）
- [如何：向应用程序代码添加跟踪语句](how-to-add-trace-statements-to-application-code.md)
- [如何：使用跟踪和调试执行有条件编译](how-to-compile-conditionally-with-trace-and-debug.md)
- [如何：创建、初始化和配置跟踪开关](how-to-create-initialize-and-configure-trace-switches.md)
- [如何：创建和初始化跟踪源](how-to-create-and-initialize-trace-sources.md)
- [如何：将 TraceSource 和筛选器与跟踪侦听器一起使用](how-to-use-tracesource-and-filters-with-trace-listeners.md)
- [跟踪侦听器](trace-listeners.md)
- [跟踪开关](trace-switches.md)

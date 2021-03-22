---
title: MDbg.exe（.NET Framework 命令行调试程序）
description: 了解 MDbg.exe，它是 .NET 的命令行调试器，可帮助工具供应商和应用程序开发人员查找和修复面向 CLR 的程序中的错误。
ms.date: 03/30/2017
helpviewer_keywords:
- command-line debugger [.NET Framework]
- MDbg.exe
ms.assetid: 28a3f509-07e2-4dbe-81df-874c5e969cc4
ms.openlocfilehash: 6118e20293f6076d080402b4e6ce52bd0e86e5cd
ms.sourcegitcommit: 1dbe25ff484a02025d5c34146e517c236f7161fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104653941"
---
# <a name="mdbgexe-net-framework-command-line-debugger"></a>MDbg.exe（.NET Framework 命令行调试程序）

.NET Framework 命令行调试器可以帮助工具供应商和应用程序开发人员查找并修复面向 .NET Framework 公共语言运行时的程序中的 Bug。 此工具使用运行时调试 API 提供调试服务。 可以使用 MDbg.exe 来仅调试托管代码；不支持调试非托管代码。  
  
通过 NuGet 可以使用此工具。 有关安装信息，请参阅 [MDbg 0.1.0](https://www.nuget.org/packages/MDbg/0.1.0)。 若要运行该工具，请使用程序包管理器控制台。 有关如何使用程序包管理器控制台的详细信息，请参阅[程序包管理器控制台](/nuget/tools/package-manager-console)一文。
  
在“程序包管理器”提示符处，键入以下内容：  
  
## <a name="syntax"></a>语法  
  
```console  
MDbg [ProgramName[arguments]] [options]  
```  
  
## <a name="commands"></a>命令  

 在调试器中（由 **mdbg>** 提示指示），键入下一节中所述的命令之一：  
  
 **command** [*arguments*]  
  
 MDbg.exe 命令区分大小写。  
  
|命令|描述|  
|-------------|-----------------|  
|**ap**[**rocess**] [*number*]|切换到另一个调试的进程或打印可用进程。 数字不是真正的进程 ID (PID)，而是一个从 0 开始索引的列表。|  
|**a**[**ttach**] [*pid*]|附加到进程或打印可用进程。|  
|**b**[**reak**] [*ClassName.Method* &#124 *FileName:LineNo*]|在指定方法处设置断点。 按顺序扫描模块。<br /><br /> -   break FileName:LineNo 在源中的某个位置设置断点。<br />-   break ~number 在当前使用 x 命令显示的符号处设置断点。<br />-   break module!ClassName.Method+IlOffset 在完全限定的位置设置断点。|  
|block[ingObjects] |显示用来阻止线程的监视器锁。|  
|ca[tch] [exceptionType] |使调试器在所有异常（而不只是未经处理的异常）上中断。|  
|cl[earException] |将当前异常标记为已处理，以便继续执行。 如果不处理异常的原因，异常可能会很快再次引发。|  
|conf[ig] [option value] |显示所有可配置选项并显示在无任何选项值的情况下如何调用选项。 如果指定了选项，请将 `value` 设置为当前选项。 当前，下列选项可用：<br /><br /> -   `extpath`：设置路径，使用 `load` 命令时在该路径中搜索扩展。<br />-   `extpath+`：添加用于加载扩展的路径。|  
|del[ete] |删除断点。|  
|de[tach] |与调试的进程分离。|  
|d[own] [frames] |下移活动堆栈帧。|  
|echo|向控制台回显消息。|  
|enableNotif[ication] typeName 0&#1241 |为指定类型启用 (1) 或禁用 (0) 自定义通知。|  
|ex[it] [exitcode] |退出 MDbg.exe shell，并选择指定进程退出代码。|  
|fo[reach] [OtherCommand] |对所有线程执行命令。 OtherCommand 是对一个线程执行的有效命令；foreach OtherCommand 对所有线程执行同一命令。|  
|**f**[**unceval**] [`-ad` *Num*] *functionName* [*args ...* ]|对当前活动线程进行函数求值，其中，要求值的函数为 functionName。 函数名必须完全限定，包括命名空间。<br /><br /> `-ad` 选项指定用于解析函数的应用程序域。 如果未指定 `-ad` 选项，则用于解析的应用程序域默认为用于函数求值的线程所在的应用程序域。<br /><br /> 如果要进行求值的函数不是静态的，则传入的第一个参数应为 `this` 指针。 在所有应用程序域中搜索函数求值所需的参数。<br /><br /> 若要请求应用程序域值，请使用模块和应用程序域名为变量添加前缀；例如 `funceval -ad 0 System.Object.ToString hello.exe#0!MyClass.g_rootRef`。 此命令在应用程序域 `System.Object.ToString` 中对函数 `0` 进行求值。 由于 `ToString` 方法为实例函数，因此，第一个参数必须为 `this` 指针。|  
|g[o] |使程序继续直到其遇到断点，退出程序，或事件（例如，未经处理的异常）使程序停止。|  
|h[elp] [command] <br /><br /> \- 或 -<br /><br /> **?** [command]|显示所有命令的说明或对指定命令的详细说明。|  
|ig[nore] [event] |让调试器仅在遇到未经处理的异常时停止。|  
|int[ercept] FrameNumber |将调试器回滚到指定的帧号码。<br /><br /> 如果调试器遇到异常，使用此命令可以将调试器回滚到指定的帧号码。 可通过使用 set 命令来更改程序状态并通过使用 go 命令继续操作。 |  
|k[ill] |停止活动进程。|  
|l[ist] [modules &#124 appdomains &#124 assemblies]   |显示已加载的模块、应用程序域或程序集。|  
|lo[ad] assemblyName |按以下方式加载扩展：加载指定的程序集，然后尝试从 `LoadExtension` 类型运行静态方法 `Microsoft.Tools.Mdbg.Extension.Extension`。|  
|log [eventType]|设置或显示要记录的事件。|  
|mo[de] [option on/off] |设置不同的调试器选项。 使用不带任何选项的 `mode` 来获取调试模式及其当前设置的列表。|  
|mon[itorInfo] monitorReference |显示对象监视器锁信息。|  
|newo[bj] typeName [arguments...]  |创建 typeName 类型的新对象。|  
|**n**[**ext**]|运行代码并移动到下一行（即使下一行包含多个函数调用）。|  
|**Opendump** *pathToDumpFile*|打开指定的转储文件以进行调试。|  
|o[ut] |移动到当前函数的末尾。|  
|pa[th] [pathName] |如果二进制文件中的位置不可用，则搜索源文件的指定路径。|  
|p[rint] [var] &#124 [-d] `-d`|打印相应范围内的所有变量 (print)，打印指定变量 (print var)，或者打印调试器变量 (print`-d`)。|  
|printe[xception] [-r] |输出当前线程上的最后一个异常。 使用 `–r`（递归）选项遍历异常对象上的 `InnerException` 属性，以获取有关整个异常链的信息。|  
|pro[cessenum] |显示活动进程。|  
|q[uit] [exitcode] |退出 MDbg.exe shell，可以选择指定进程退出代码。|  
|re[sume] [`*` &#124 [`~`]threadNumber] |继续当前线程或由 threadNumber 参数指定的线程。<br /><br /> 如果指定 threadNumber 参数为 `*`，或者线程号以 `~` 开始，则该命令将应用于所有线程（threadNumber 指定的线程除外）。 <br /><br /> 无法继续未挂起的线程。|  
|r[un] [`-d`(`ebug`) &#124; -`o`(`ptimize`) &#124;`-enc`] [[path_to_exe] [args_to_exe]]  |停止当前进程（如果存在）并启动一个新进程。 如果未传递可执行参数，此命令将运行上次使用 `run` 命令执行的程序。 如果提供了可执行参数，将使用提供的可选参数运行指定的程序。<br /><br /> 如果忽略类加载、模块加载和线程启动事件（默认情况下如此），程序将在遇到主线程的第一条可执行指令时停止。<br /><br /> 可通过使用下列三个标记之一迫使调试程序对代码进行实时 (JIT) 编译：<br /><br /> -   `-d` ( `ebug` ) 禁用优化。  这是 MDbg.exe 的默认值。<br />-   `-o` ( `ptimize` ) 强制代码更像在调试器外部一样运行，而且也使调试体验更困难。  这是在调试器外部使用的默认值。<br />-   `-enc` 启用“编辑并继续”功能，但会导致性能问题。|  
|**Set** *variable*=*value*|更改任意范围内变量的值。<br /><br /> 你还可以创建自己的调试器变量，然后在自己的应用程序中将引用值赋给这些变量。 这些值充当原始值的句柄，即使原始值超出范围。 所有调试器变量都必须以 `$` 开头（例如 `$var`）。 使用以下命令将这些句柄设置为空，便可以清除这些句柄：<br /><br /> `set $var=`|  
|Setip [`-il`] number|将文件中的当前指令指针 (IP) 设置为指定位置。 如果指定 `-il` 选项，则数字表示方法中的 Microsoft 中间语言 (MSIL) 偏移量。 否则，数字表示源代码行号。|  
|sh[ow] [lines] |指定要显示的行数。|  
|s[tep] |开始执行当前行的下一个函数；如果没有要单步执行的函数，则移动到下一行。|  
|su[spend] [\* &#124; [~]threadNumber] |挂起当前线程或 threadNumber 参数指定的线程。  如果 threadNumber 指定为 `*`，则该命令将应用于所有线程。 如果线程号以 `~` 开头，则该命令应用于除 threadNumber 指定的线程之外的所有线程。 使用 go 或 step 命令运行进程时，不会运行挂起的线程。  如果进程中没有未挂起的线程，即使发出 go 命令，该进程也不会继续。 在这种情况下，按 Ctrl-C 可中断进程。|  
|sy[mbol] commandName [commandValue]  |指定下列命令之一：<br /><br /> -   `symbol path` [`"value"`] - 显示或设置当前符号路径。<br />-   `symbol addpath` `"value"` - 添加到当前符号路径。<br />-   `symbol reload` [`"module"`]- 重新加载所有符号或指定模块的符号。<br />-   `symbol list` [`module`] - 显示当前为所有模块或指定模块加载的符号。|  
|t[hread] [newThread] [-nick nickname`]`  |不带参数的线程命令显示当前进程中的所有托管线程。 线程通常由其线程号标识；但是，如果线程有分配的别名，则显示该别名。 可使用 `-nick` 参数将别号分配给线程。<br /><br /> -   thread `-nick` threadName 为当前运行的线程分配一个昵称。<br /><br /> 别名不能为数字。 如果已为当前线程分配了别名，则使用新的别名来替换旧的别名。 如果新别名为空字符串 ("")，则删除当前线程的别名并且不为线程分配新别名。|  
|u[p] |上移活动堆栈帧。|  
|uwgc[handle] [var] &#124 [address]  |打印句柄所跟踪的变量。 可以按名称或地址来指定句柄。|  
|**when**|显示当前处于活动状态的 `when` 语句。<br /><br /> **when** **delete all** &#124; `num` [`num` [`num` …]] - 删除按编号指定的 `when` 语句，或所有 `when` 语句，前提是指定了 `all`。<br /><br /> **when** `stopReason` [`specific_condition`] **do**`cmd` [`cmd` [`cmd` …] ] - stopReason 参数可以为下列参数之一：<br /><br /> `StepComplete`, `ProcessExited`, `ThreadCreated`, `BreakpointHit`, `ModuleLoaded`, `ClassLoaded`, `AssemblyLoaded`, `AssemblyUnloaded`, `ControlCTrapped`, `ExceptionThrown`, `UnhandledExceptionThrown`, `AsyncStop`, `AttachComplete`, `UserBreak`, `EvalComplete`, `EvalException`, `RemapOpportunityReached`, `NativeStop`.<br /><br /> specific_condition 可以为下列情况之一：<br /><br /> -   number - 对于 `ThreadCreated` 和 `BreakpointHit`，仅当具有相同值的线程 ID/断点号引起停止时才会触发操作。<br />-   [`!`]name - 对于 、`ModuleLoaded`、`ClassLoaded`、`AssemblyLoaded`、`AssemblyUnloaded`、`ExceptionThrown` 和 `UnhandledExceptionThrown`，仅当该名称与 stopReason 的名称匹配时才会触发操作。 <br /><br /> 对于 stopReason 的其他值，specific_condition 必须为空。 |  
|**w**[**here**] [`-v`] [`-c` *depth*] [*threadID*]|显示有关堆栈帧的调试信息。<br /><br /> -   `-v` 选项提供有关显示的每个堆栈帧的详细信息。<br />-   为 `depth` 指定一个数字可限制显示的帧数。 使用 all 命令可显示所有帧。 默认值为 100。<br />-   如果指定 threadID 参数，则可以控制与堆栈关联的线程。 默认只与当前线程关联。 使用 all 命令可获取所有线程。|  
|x [`-c`numSymbols] [module[`!`pattern]]  |显示匹配模块的 `pattern` 的函数。<br /><br /> 如果指定了 numSymbols，则输出将限制为指定数目。 如果未针对 pattern 指定 `!`（指示正则表达式），则会显示所有函数。 如果未提供 module，则显示加载的所有模块。 符号 (~#) 可用于通过 break 命令设置断点。|  
  
## <a name="remarks"></a>备注  

 使用编译器特定的标志编译要调试的应用程序会使你的编译器生成调试符号。 有关这些标志的详细信息，请参阅编译器的文档。 可以调试优化的应用程序，但某些调试信息将缺失。 例如，许多局部变量将无法看到，并且源代码行也会变得不准确。  
  
 在编译应用程序之后，在命令提示符下键入 mdbg 以启动调试会话，如以下示例所示。  
  
```console  
C:\Program Files\Microsoft Visual Studio 8\VC>mdbg  
MDbg (Managed debugger) v2.0.50727.42 (RTM.050727-4200) started.  
Copyright (C) Microsoft Corporation. All rights reserved.  
  
For information about commands type "help";  
to exit program type "quit".  
mdbg>  
```  
  
 `mdbg>` 提示符指示你处于调试器中。  
  
 处于调试器中之后，请使用上一部分所述的命令和参数。  
  
## <a name="see-also"></a>请参阅

- [工具](index.md)

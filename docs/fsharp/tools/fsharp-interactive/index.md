---
title: F# 交互窗口 (dotnet) 引用
description: 了解如何使用 F# 交互窗口 (dotnet fsi) 在控制台以交互方式运行 F# 代码，或执行 F# 脚本。
ms.date: 11/29/2020
f1_keywords:
- VS.ToolsOptionsPages.F#_Tools.F#_Interactive
ms.openlocfilehash: 200caf6be5977e4a4d11bda8ba57f276cb4f5ff6
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605121"
---
# <a name="interactive-programming-with-f"></a>使用 F\# 进行交互式编程

使用 F# 交互窗口 (dotnet fsi) 在控制台以交互方式运行 F# 代码，或执行 F# 脚本。 换句话说，F# Interactive 对 F# 语言执行 REPL（读取、计算、打印循环）。

若要从控制台运行 F# 交互窗口，请运行 `dotnet fsi`。 你将在任何 .NET SDK 中找到 `dotnet fsi`。

若要了解可用的命令行选项，请参阅 [F# 交互窗口选项](../../language-reference/fsharp-interactive-options.md)。

## <a name="executing-code-directly-in-f-interactive"></a>在 F# 交互窗口中直接执行代码

由于 F# 交互窗口是 REPL（读取-求值-打印循环），因此可以在其中以交互方式执行代码。 下面的示例展示了通过命令行执行 `dotnet fsi` 后的交互会话：

```console
Microsoft (R) F# Interactive version 11.0.0.0 for F# 5.0
Copyright (c) Microsoft Corporation. All Rights Reserved.

For help type #help;;

> let square x = x *  x;;
val square : x:int -> int

> square 12;;
val it : int = 144

> printfn "Hello, FSI!"
- ;;
Hello, FSI!
val it : unit = ()
```

你将会注意到两件主要的事情：

1. 所有代码都必须以双分号 (`;;`) 结尾才能计算
2. 代码计算并存储在 `it` 值中。 可以交互方式引用 `it`。

F# 交互窗口还支持多行输入。 只需要使用双分号 (`;;`) 终止提交。 假设以下代码片段已粘贴到 F# 交互窗口中并由其计算：

```console
> let getOddSquares xs =
-     xs
-     |> List.filter (fun x -> x % 2 <> 0)
-     |> List.map (fun x -> x * x)
-
- printfn "%A" (getOddSquares [1..10]);;
[1; 9; 25; 49; 81]
val getOddSquares : xs:int list -> int list
val it : unit = ()

>
```

代码的格式被保留，并有终止输入的双分号 (`;;`)。 然后，F# 交互窗口计算了代码，并打印出了结果！

## <a name="scripting-with-f"></a>使用 F 编写脚本\#

在 F# 交互窗口中以交互方式计算代码，它可以是一种很好的学习工具，但你很快就会发现，它不如在普通编辑器中编写代码那么高效。 为了支持普通的代码编辑，可以编写 F# 脚本。

脚本使用文件扩展名 .fsx。 可以不编译源代码再运行编译的程序集，仅运行 dotnet fsi 并指定 F# 源代码脚本的文件名，F# 交互窗口会实时读取并执行代码。 例如，假设以下脚本名为 `Script.fsx`：

```fsharp
let getOddSquares xs =
    xs
    |> List.filter (fun x -> x % 2 <> 0)
    |> List.map (fun x -> x * x)

printfn "%A" (getOddSquares [1..10])
```

在计算机中创建此文件后，可以使用 `dotnet fsi` 运行它，然后直接在终端窗口中查看输出：

```console
dotnet fsi Script.fsx
[1; 9; 25; 49; 81]
```

F# 脚本在 [Visual Studio](../../get-started/get-started-visual-studio.md)、[Visual Studio Code](../../get-started/get-started-vscode.md) 和 [Visual Studio for Mac](../../get-started/get-started-with-visual-studio-for-mac.md) 中都是原生支持的。

## <a name="referencing-packages-in-f-interactive"></a>在 F# 交互窗口中引用包

> [!NOTE]
> 程序包管理系统是可扩展的；详细了解[其他扩展](https://fsharp.github.io/FSharp.Compiler.Service/reference/Microsoft.DotNet.DependencyManager.html)。

F# 交互窗口支持使用 `#r "nuget:"` 语法和可选版本来引用 NuGet 包：

```fsharp
#r "nuget: Newtonsoft.Json"
open Newtonsoft.Json

let data = {| Name = "Don Syme"; Occupation = "F# Creator" |}
JsonConvert.SerializeObject(data)
```

如果未指定版本，则采用最高可用版本的非预览包。 若要引用特定版本，请通过逗号引入版本。 这在引用包的预览版本时非常方便。 例如，假设下面的脚本使用 [DiffSharp](https://diffsharp.github.io/) 的预览版本：

```fsharp
#r "nuget: DiffSharp-lite, 1.0.0-preview-328097867"
open DiffSharp

// A 1D tensor
let t1 = dsharp.tensor [ 0.0 .. 0.2 .. 1.0 ]

// A 2x2 tensor
let t2 = dsharp.tensor [ [ 0; 1 ]; [ 2; 2 ] ]

// Define a scalar-to-scalar function
let f (x: Tensor) = sin (sqrt x)

printfn $"{f (dsharp.tensor 1.2)}"
```

### <a name="specifying-a-package-source"></a>指定包源

还可通过 `#i` 命令指定包源。 以下示例指定一个远程源和一个本地源：

```fsharp
#i "nuget:https://my-remote-package-source/index.json"
#i @"path-to-my-local-source"
```

这将告知后台的解析引擎同时考虑添加到脚本的远程和/或本地源。

你可以根据需要在脚本中指定任意数量的包引用。

> [!NOTE]
> 目前对使用框架引用的脚本（例如 `Microsoft.NET.Sdk.Web` 或 `Microsoft.NET.Sdk.WindowsDesktop`）有限制。 Saturn、Giraffe、WinForms 等包不可用。 问题 [#9417](https://github.com/dotnet/fsharp/issues/9417) 中正在跟踪此问题。

有关详细信息，请参阅[包管理扩展性和其他扩展](https://fsharp.github.io/FSharp.Compiler.Service/reference/Microsoft.DotNet.DependencyManager.html)。

## <a name="referencing-assemblies-on-disk-with-f-interactive"></a>使用 F# 交互窗口引用磁盘上的程序集

另外，如果磁盘上有程序集，并且你希望在脚本中引用此程序集，则可以使用 `#r` 语法指定程序集。 假设项目中的以下代码编译成 `MyAssembly.dll`：

```fsharp
// MyAssembly.fs
module MyAssembly
let myFunction x y = x + 2 * y
```

编译后，就可以在名为 `Script.fsx` 的文件中引用它了，如下所示：

```fsharp
#r "path/to/MyAssembly.dll"

printfn $"{MyAssembly.myFunction 10 40}"
```

输出如下所示：

```console
dotnet fsi Script.fsx
90
```

你可以根据需要在脚本中指定任意数量的程序集引用。

## <a name="loading-other-scripts"></a>加载其他脚本

在编写脚本时，对不同的任务使用不同的脚本通常是有帮助的。 有时，不妨在另一个脚本中重用脚本中的代码。 可以使用 `#load` 直接加载并计算它，而不用将它的内容复制粘贴到文件中。

假设为以下 `Script1.fsx`：

```fsharp
let square x = x * x
```

以及正在使用的文件 `Script2.fsx`：

```fsharp
#load "Script1.fsx"
open Script1

printfn $"%d{square 12}"
```

请注意，`open Script1` 声明是必需的。 这是因为 F# 脚本中的构造被编译成顶级模块，此模块是它所在脚本文件的名称。

可以这样计算 `Script2.fsx`：

```console
dotnet fsi Script2.fsx
144
```

你可以根据需要在脚本中指定任意数量的 `#load` 指令。

## <a name="using-the-fsi-object-in-f-code"></a>在 F# 代码中使用 `fsi` 对象

F# 脚本有权访问表示 F# 交互窗口会话的自定义 `fsi` 对象。 这样，就可以自定义输出格式等。 这也是访问命令行参数的方式。

下面的示例展示了如何获取和使用命令行参数：

```fsharp
let args = fsi.CommandLineArgs

for arg in args do
    printfn $"{arg}"
```

计算后，它会打印出所有参数。 第一个参数始终是要计算的脚本的名称：

```dotnet
dotnet fsi Script1.fsx hello world from fsi
Script1.fsx
hello
world
from
fsi
```

也可以使用 `System.Environment.GetCommandLineArgs()` 访问相同的参数。

## <a name="f-interactive-directive-reference"></a>F# 交互窗口指令参考

前面所示的 `#r` 和 `#load` 指令只在 F# 交互窗口中可用。 有几个指令只在 F# 交互窗口可用：

|指令|描述|
|---------|-----------|
|`#r "nuget:..."`|通过 NuGet 引用包|
|`#r "assembly-name.dll"`|引用磁盘上的程序集|
|`#load "file-name.fsx"`|读取、编译并运行源文件。|
|`#help`|显示有关可用指令的信息。|
|`#I`|指定程序集搜索路径并用引号引起来。|
|`#quit`|终止 F# Interactive 会话。|
|`#time "on"` 或 `#time "off"`|`#time` 本身可以切换是否显示性能信息。 当它为 `"on"` 时，F# 交互窗口度量所解释并执行的代码的每个部分的实际时间、CPU 时间和垃圾回收信息。|

当在 F# Interactive 中指定文件或路径时，应指定字符串文本。 因此，文件和路径必须用引号引起来，也可以使用常见的转义符。 可以使用 `@` 字符让 F# 交互窗口将包含路径的字符串解释为逐字字符串。 这会导致 F# Interactive 忽略转义符。

## <a name="interactive-and-compiled-preprocessor-directives"></a>交互和编译的预处理器指令

在 F# 交互窗口中编译代码时，无论是以交互方式还是直接运行脚本，都会定义 INTERACTIVE 符号。 在编译器中编译代码时，则会定义 COMPILED 符号。 因此，如果代码需要在编译模式和交互模式下不同，可以使用这些预处理器指令进行条件编译，以确定使用哪个。 例如：

```fsharp
#if INTERACTIVE
// Some code that executes only in FSI
// ...
#endif
```

## <a name="using-f-interactive-in-visual-studio"></a>在 Visual Studio 中使用 F# 交互窗口

若要通过 Visual Studio 运行 F# Interactive，可以单击标记为“F# Interactive”的相应工具栏按钮，或使用组合键 **Ctrl+Alt+F**。 执行此操作将打开交互式窗口，该窗口是运行 F# Interactive 会话的工具窗口。 还可以选择一些你希望在交互式窗口中运行的代码，然后点击组合键 Alt+Enter。 F# Interactive 在标记为“F# Interactive”的工具窗口中启动。 当您使用此组合键时，请确保焦点位于编辑器窗口内。

无论您使用的是控制台还是 Visual Studio，都会出现命令提示符，并且解释器会等待您输入代码。 你可以像在代码文件中一样输入代码。 若要编译和执行代码，请输入两个分号 (**;;**) 以终止一行或几行输入。

F# Interactive 试图编译代码，如果成功，它将执行代码并打印其所编译类型和值的签名。 如果发生错误，解释器将打印错误消息。

在同一个会话中输入的代码可以访问之前输入的任何构造，以便你可以生成程序。 工具窗口中的大容量缓存允许你在需要时将代码复制到文件中。

当在 Visual Studio 中运行时，F# Interactive 将独立于你的项目运行，因此，你不能在 F# Interactive 中使用在项目中定义的构造，除非你将函数的代码复制到交互式窗口中。

可以通过调整设置来控制 F# 交互窗口命令行参数（选项）。 在“工具”菜单上，选择“选项...”，然后展开“F# 工具”。 可以更改的两种设置是 F# Interactive 选项和“64 位F# Interactive”，只有在 64 位计算机上运行 F# Interactive 时，更改才有意义。 此设置确定是要运行 fsi.exe 还是 fsianycpu.exe 的专用 64 位版本，它使用计算机体系结构来确定是作为 32 位还是 64 位进程来运行。

## <a name="related-articles"></a>相关文章

|Title|描述|
|-----|-----------|
|[F# Interactive 选项](../../language-reference/fsharp-interactive-options.md)|描述 F# Interactive (fsi.exe) 的命令行语法和选项。|

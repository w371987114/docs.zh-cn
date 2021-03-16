---
title: 如何：使用跟踪和调试执行有条件编译
description: 了解如何在编译 .NET 应用程序时使用 TRACE 和 DEBUG 条件属性进行条件编译。
ms.date: 03/30/2017
helpviewer_keywords:
- trace compiler options
- trace statements
- compiling source code, trace statements
- tracing [.NET Framework], enabling or disabling
- tracing [.NET Framework], compiling conditionally
- TRACE directive
- conditional compilation, tracing code
ms.assetid: 56d051c3-012c-42c1-9a58-7270edc624aa
ms.openlocfilehash: 90c989bcf21e24cf7ccf410c9a18a44cde81233e
ms.sourcegitcommit: 0bb8074d524e0dcf165430b744bb143461f17026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2021
ms.locfileid: "103478213"
---
# <a name="how-to-compile-conditionally-with-trace-and-debug"></a>如何：使用跟踪和调试执行有条件编译

在开发过程中调试应用程序时，跟踪和调试输出都会出现在 Visual Studio 的“输出”窗口中。 但是，若要在已部署的应用程序中包含跟踪功能，则必须在 TRACE 编译器指令处于启动状态下编译已检测应用程序。 这样就可以将跟踪代码编译成应用程序的发布版本。 如果未启用 TRACE 指令，将在编译过程中忽略所有跟踪代码，并且不会将其包含在将部署的可执行代码中。  
  
 跟踪方法和调试方法都具有关联的条件属性。 例如，如果跟踪的条件属性为 true，则所有跟踪语句都将包含在一个程序集（经过编译的 .exe 文件或 .dll）中；如果 Trace 条件属性为 false，则不会包括跟踪语句。  
  
 可以启用 Trace 或 Debug 条件属性作为一种生成，或启用这两者，或两者都不启用。 这样就会有四种类型的生成：Debug、Trace、二者都启用，或二者都不启用。 某些用于生产部署的发布版本可能不包含这两种属性；大多数调试版本会同时包含这两种属性。  
  
 可以通过几种方式来指定应用程序的编译器设置：  
  
- 属性页  
  
- 命令行  
  
- #CONST（适用于 Visual Basic）和 #define（适用于 C#）  
  
### <a name="to-change-compile-settings-from-the-property-pages-dialog-box"></a>从属性页对话框中更改编译设置  
  
1. 右键单击“解决方案资源管理器”中的项目节点。  
  
2. 从快捷菜单中选择“属性”。  
  
    - 在 Visual Basic 中，单击属性页左窗格中的“编译”选项卡，再单击“高级编译选项”按钮，以显示“高级编译器设置”对话框。 选中想要启用的编译器设置对应的复选框。 清除要禁用的设置的复选框。  
  
    - 在 C# 中，单击属性页左窗格中的“生成”选项卡，然后选中要启用的编译器设置对应的复选框。 清除要禁用的设置的复选框。  
  
### <a name="to-compile-instrumented-code-using-the-command-line"></a>使用命令行编译已插入检测点的代码  
  
1. 在命令行上设置条件编译器开关。 编译器将在可执行文件中包括跟踪或调试代码。  
  
     例如，在命令行上输入的以下编译器指令将在经过编译的可执行文件中包含跟踪代码：  
  
     对于 Visual Basic： **vbc -r:System.dll-d:TRACE = TRUE-d:DEBUG = FALSE MyApplication**  
  
     对于 c #： **csc -r:System.dll-d:DEBUG = FALSE MyApplication.cs**  
  
    > [!TIP]
    > 若要编译多个应用程序文件，请在文件名之间留一个空格，例如 MyApplication1.vb MyApplication2.vb MyApplication3.vb 或 MyApplication1.cs MyApplication2.cs MyApplication3.cs。  
  
     以上示例所使用的条件编译指令具有如下含义：  
  
    |指令|含义|  
    |---------------|-------------|  
    |`vbc`|Visual Basic 编译器|  
    |`csc`|C# 编译器|  
    |`-r:`|引用外部程序集（EXE 或 DLL）|  
    |`-d:`|定义条件编译符号|  
  
    > [!NOTE]
    > 必须用大写字母来拼写 TRACE 或 DEBUG。 有关条件编译命令的详细信息，请在命令提示处输入 `vbc /?`（对于 Visual Basic）或 `csc /?`（对于 C#）。 有关详细信息，请参阅[从命令行生成](../../csharp/language-reference/compiler-options/index.md) (C#) 或[调用命令行编译器](../../visual-basic/reference/command-line-compiler/how-to-invoke-the-command-line-compiler.md) (Visual Basic)。  
  
### <a name="to-perform-conditional-compilation-using-const-or-define"></a>使用 #CONST 或 #define 执行条件编译  
  
1. 在源代码文件的顶部键入对应于编程语言的适当语句。  
  
    |语言|语句|结果|  
    |--------------|---------------|------------|  
    |**Visual Basic**|#CONST TRACE = true|启用跟踪|  
    ||#CONST TRACE = false|禁用跟踪|  
    ||#CONST DEBUG = true|启用调试|  
    ||#CONST DEBUG = false|禁用调试|  
    |**C#**|#define TRACE|启用跟踪|  
    ||#undef TRACE|禁用跟踪|  
    ||#define DEBUG|启用调试|  
    ||#undef DEBUG|禁用调试|  
  
### <a name="to-disable-tracing-or-debugging"></a>禁用跟踪或调试  
  
从源代码中删除编译器指令。  
  
\- 或 -  
  
注释掉编译器指令。  
  
> [!NOTE]
> 准备进行编译时，可从“生成”菜单中选择“生成”，也可使用命令行方法但不键入 d:，以定义条件编译符号。  
  
## <a name="see-also"></a>另请参阅

- [跟踪应用程序和在应用程序中插入检测点](tracing-and-instrumenting-applications.md)
- [如何：创建、初始化和配置跟踪开关](how-to-create-initialize-and-configure-trace-switches.md)
- [跟踪开关](trace-switches.md)
- [跟踪侦听器](trace-listeners.md)
- [如何：向应用程序代码添加跟踪语句](how-to-add-trace-statements-to-application-code.md)
- [如何为 Visual Studio 命令行设置环境变量](../../csharp/language-reference/compiler-options/index.md)
- [如何：调用命令行编译器](../../visual-basic/reference/command-line-compiler/how-to-invoke-the-command-line-compiler.md)

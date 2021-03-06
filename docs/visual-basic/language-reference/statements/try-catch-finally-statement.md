---
title: "Try...Catch...Finally 语句 (Visual Basic)"
ms.date: 07/20/2015
ms.prod: .net
ms.reviewer: 
ms.suite: 
ms.technology: devlang-visual-basic
ms.topic: article
f1_keywords:
- vb.Try...Catch...Finally
- vb.when
- vb.Finally
- vb.Catch
- vb.Try
helpviewer_keywords:
- Try...Catch...Finally statements
- Try statement [Visual Basic]
- try-catch exception handling, Try...Catch...Finally statements
- error handling, while running code
- Try statement [Visual Basic], Try...Catch...Finally
- Finally keyword [Visual Basic], Try...Catch...Finally
- Catch statement [Visual Basic]
- When keyword [Visual Basic]
- Visual Basic code, handling errors while running
- structured exception handling, Try...Catch...Finally statements
ms.assetid: d6488026-ccb3-42b8-a810-0d97b9d6472b
caps.latest.revision: "69"
author: dotnet-bot
ms.author: dotnetcontent
ms.openlocfilehash: 08de4939960d8297269c82b1b040537dd43f3038
ms.sourcegitcommit: 4f3fef493080a43e70e951223894768d36ce430a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2017
---
# <a name="trycatchfinally-statement-visual-basic"></a>Try...Catch...Finally 语句 (Visual Basic)
提供用于处理部分或全部错误的给定块中的代码，同时仍在运行代码的方法。  
  
## <a name="syntax"></a>语法  
  
```  
Try  
    [ tryStatements ]  
    [ Exit Try ]  
[ Catch [ exception [ As type ] ] [ When expression ]  
    [ catchStatements ]  
    [ Exit Try ] ]  
[ Catch ... ]  
[ Finally  
    [ finallyStatements ] ]  
End Try  
```  
  
## <a name="parts"></a>部件  
  
|术语|定义|  
|---|---|  
|`tryStatements`|可选。 可能发生错误的语句。 可以是复合语句。|  
|`Catch`|可选。 多个`Catch`允许的块。 如果在处理时，则会发生异常`Try`块，每个`Catch`按文本顺序来确定是否将它与处理异常，检查语句`exception`表示已引发的异常。|  
|`exception`|可选。 任何变量名称。 `exception` 的初始值是引发的错误的值。 与使用`Catch`指定错误捕获。 如果省略，`Catch`语句将捕获所有异常。|  
|`type`|可选。 指定类筛选器的类型。 如果值`exception`由指定的类型的`type`或派生类型，该标识符将绑定到的异常对象。|  
|`When`|可选。 A`Catch`语句`When`子句捕获异常时，才`expression`计算结果为`True`。 A`When`子句应用只在检查的异常，类型之后和`expression`可能指向表示异常的标识符。|  
|`expression`|可选。 必须是隐式转换为`Boolean`。 描述泛型的筛选器的任何表达式。 通常用于筛选的错误号。 与使用`When`关键字来指定在其下捕获该错误的情况。|  
|`catchStatements`|可选。 语句来处理发生在关联的错误`Try`块。 可以是复合语句。|  
|`Exit Try`|可选。 关键字会中断外`Try...Catch...Finally`结构。 执行恢复并且立即后面的代码`End Try`语句。 `Finally`仍将继续执行语句。 中不允许`Finally`块。|  
|`Finally`|可选。 A`Finally`当执行离开的任何部分，则始终执行块`Try...Catch`语句。|  
|`finallyStatements`|可选。 发生所有其他错误处理后执行的语句。|  
|`End Try`|终止`Try...Catch...Finally`结构。|  
  
## <a name="remarks"></a>备注  
 如果你预计特定异常可能发生在特定的代码部分的过程，将代码放入`Try`块并使用`Catch`块保留控件和处理异常发生时。  
  
 A`Try…Catch`语句组成`Try`块后跟一个或多个`Catch`子句，指定各种异常的处理程序。 当中引发了异常`Try`块，[!INCLUDE[vbprvb](~/includes/vbprvb-md.md)]查找`Catch`处理异常的语句。 如果匹配`Catch`未找到语句，[!INCLUDE[vbprvb](~/includes/vbprvb-md.md)]检查调用当前的方法，并以此类推遍历调用堆栈的方法。 如果没有`Catch`找到该块，[!INCLUDE[vbprvb](~/includes/vbprvb-md.md)]向用户显示一条未处理的异常消息并停止执行程序。  
  
 你可以使用多个`Catch`中的语句`Try…Catch`语句。 如果你这样做，请的顺序`Catch`子句很重要，因为它们按顺序检查。 在使用更笼统的子句之前获取特定性更强的异常。  
  
 以下`Catch`语句条件将最不具体，并将捕获所有派生自异常<xref:System.Exception>类。 应与上一通常使用一种这些变体`Catch`中阻止`Try...Catch...Finally`结构之后捕获您期望的所有特定异常。 控制流可以永远不会到达`Catch`遵循以下任一这些变体的块。  
  
-   `type`是`Exception`，例如：`Catch ex As Exception`  
  
-   语句未包含任何`exception`变量，例如：`Catch`  
  
 当`Try…Catch…Finally`语句嵌套在另一个`Try`块，[!INCLUDE[vbprvb](~/includes/vbprvb-md.md)]首先检查每个`Catch`中最内层语句`Try`块。 如果没有任何匹配`Catch`语句找不到，则继续搜索`Catch`语句的外部`Try…Catch…Finally`块。  
  
 从本地变量`Try`块中不可用`Catch`进行阻止，因为它们是单独的块。 如果你想要在多个块中使用变量，声明外部变量`Try...Catch...Finally`结构。  
  
> [!TIP]
>  `Try…Catch…Finally`语句是可用作 IntelliSense 代码段。 在代码片段管理器中，展开**代码模式-如果，对于每个，Try Catch、 属性，等**，，然后**错误处理 （异常）**。 有关详细信息，请参阅[代码片段](/visualstudio/ide/code-snippets)。  
  
## <a name="finally-block"></a>Finally 块  
 如果你有在退出之前必须运行的一个或多个语句`Try`结构，请使用`Finally`块。 控制权将传递给`Finally`阻止它传递出之前`Try…Catch`结构。 即使内的任何位置发生异常，这是如此`Try`结构。  
  
 A`Finally`块可用于运行任何代码，必须执行，即使出现异常。 控制权将传递给`Finally`块而不考虑如何`Try...Catch`阻止退出。  
  
 中的代码`Finally`阻止运行，即使你的代码遇到`Return`中的语句`Try`或`Catch`块。 控件不能通过从`Try`或`Catch`阻止对相应`Finally`块中，在以下情况下：  
  
-   [End 语句](../../../visual-basic/language-reference/statements/end-statement.md)中遇到`Try`或`Catch`块。  
  
-   A<xref:System.StackOverflowException>中引发`Try`或`Catch`块。  
  
 它不是有效显式将执行到传输`Finally`块。 传输外的执行`Finally`块不是有效的除非通过异常。  
  
 如果`Try`语句未包含至少一个`Catch`块，它必须包含`Finally`块。  
  
> [!TIP]
>  如果不需要捕获特定异常`Using`语句的行为类似`Try…Finally`块中，且保证会处置的资源，而不考虑如何退出块。 这是 true 即使使用未经处理的异常。 有关详细信息，请参阅 [Using 语句](../../../visual-basic/language-reference/statements/using-statement.md)。  
  
## <a name="exception-argument"></a>异常自变量  
 `Catch`块`exception`自变量是的一个实例<xref:System.Exception>类或派生自类`Exception`类。 `Exception`类实例对应于中发生的错误`Try`块。  
  
 属性`Exception`对象帮助以确定原因和处理的异常的位置。 例如，<xref:System.Exception.StackTrace%2A>属性列表导致异常，帮助您找到代码中发生错误的调用的方法。 <xref:System.Exception.Message%2A>返回描述异常的消息。 <xref:System.Exception.HelpLink%2A>返回与关联的帮助文件链接。 <xref:System.Exception.InnerException%2A>返回`Exception`导致当前异常，或它的对象返回`Nothing`如果没有原始`Exception`。  
  
## <a name="considerations-when-using-a-trycatch-statement"></a>使用 Try 时的注意事项...Catch 语句  
 使用`Try…Catch`语句只是为了指示不寻常或意外程序事件的匹配项。 此错误的原因包括：  
  
-   在运行时捕获异常创建额外的开销，并可能速度要慢于预检查以避免异常。  
  
-   如果`Catch`块不能正确处理，异常可能不会正确报告给用户。  
  
-   异常处理会使程序更复杂。  
  
 不始终需要`Try…Catch`语句以检查可能发生的条件。 下面的示例检查文件是否存在之前尝试打开它。 这减少了捕捉引发的异常需要<xref:System.IO.File.OpenText%2A>方法。  
  
 [!code-vb[VbVbalrStatements#94](../../../visual-basic/language-reference/error-messages/codesnippet/VisualBasic/try-catch-finally-statement_1.vb)]  
  
 确保在该代码`Catch`块可以异常正确报告给用户，无论是通过线程安全日志记录还是相应的消息。 否则，异常可能会保持未知。  
  
## <a name="async-methods"></a>异步方法  
 如果你将具有的方法标记[异步](../../../visual-basic/language-reference/modifiers/async.md)修饰符，你可以使用[Await](../../../visual-basic/language-reference/operators/await-operator.md)方法中的运算符。 具有的语句`Await`运算符将暂停执行方法，直到等待的任务完成。 任务表示正在进行的工作。 时与关联的任务`Await`运算符完成后，执行将继续在同一方法中。 有关详细信息，请参阅[异步程序中的控制流](../../../visual-basic/programming-guide/concepts/async/control-flow-in-async-programs.md)。  
  
 任务返回异步方法可能最后处于错误状态，指示它由于未经处理的异常的原因而完成。 任务可能还处于已取消状态，这会导致最后`OperationCanceledException`引发外 await 表达式。 若要捕获任一类型的异常，将`Await`与中的任务关联的表达式`Try`块，并捕获中的异常`Catch`块。 本主题中后面提供了一个示例。  
  
 任务可能处于错误状态，原因是多个异常不负责其出错。 例如，任务可能是对 <xref:System.Threading.Tasks.Task.WhenAll%2A?displayProperty=nameWithType> 调用的结果。 当等待此类任务时，捕获的异常都只有一个异常，而且你无法预测将捕获到哪个异常。 本主题中后面提供了一个示例。  
  
 `Await`表达式不能包含在内`Catch`块或`Finally`块。  
  
## <a name="iterators"></a>迭代器  
 迭代器函数或`Get`访问器对集合执行自定义迭代。 迭代器使用[产生](../../../visual-basic/language-reference/statements/yield-statement.md)语句以返回一次的集合的每个元素。 使用调用迭代器函数[每个...下一条语句](../../../visual-basic/language-reference/statements/for-each-next-statement.md)。  
  
 A`Yield`语句可以是内部`Try`块。 A`Try`包含块`Yield`语句可以有`Catch`阻止，并且可以`Finally`块。 请参阅的"重试块在 Visual Basic"部分[迭代器](http://msdn.microsoft.com/library/f45331db-d595-46ec-9142-551d3d1eb1a7)有关示例。  
  
 A`Yield`语句不能包含在内`Catch`块或`Finally`块。  
  
 如果`For Each`（之外的迭代器函数） 的正文引发异常，`Catch`未执行迭代器函数中的块，但`Finally`执行迭代器函数中的块。 A`Catch`块中嵌套的迭代器函数捕获仅出现在迭代器函数的异常。  
  
## <a name="partial-trust-situations"></a>部分信任情况下  
 在部分信任情况下，例如网络共享上托管的应用程序`Try...Catch...Finally`不会捕获包含调用的方法调用之前发生的安全异常。 以下示例中，则将其置于服务器共享和运行在这里时, 将产生错误"System.Security.SecurityException： 请求失败。" 有关安全异常的详细信息，请参阅<xref:System.Security.SecurityException>类。  
  
 [!code-vb[VbVbalrStatements#85](../../../visual-basic/language-reference/error-messages/codesnippet/VisualBasic/try-catch-finally-statement_2.vb)]  
  
 在此类的部分信任情况下，你需要将`Process.Start`在单独的语句`Sub`。 首次调用`Sub`将失败。 这使`Try...Catch`可以捕捉到它之前`Sub`包含`Process.Start`启动而且产生有效的安全异常。  
  
## <a name="example"></a>示例  
 下面的示例演示的结构`Try...Catch...Finally`语句。  
  
 [!code-vb[VbVbalrStatements#86](../../../visual-basic/language-reference/error-messages/codesnippet/VisualBasic/try-catch-finally-statement_3.vb)]  
  
## <a name="example"></a>示例  
 在下面的示例中，`CreateException`方法抛出异常`NullReferenceException`。 生成异常的代码将不在`Try`块。 因此，`CreateException`方法并不处理异常。 `RunSample`方法未处理该异常，因为调用`CreateException`方法处于`Try`块。  
  
 该示例包含`Catch`几种类型的异常的语句按从最具体到最常见。  
  
 [!code-vb[VbVbalrStatements#91](../../../visual-basic/language-reference/error-messages/codesnippet/VisualBasic/try-catch-finally-statement_4.vb)]  
  
## <a name="example"></a>示例  
 下面的示例演示如何使用`Catch When`语句要作为筛选依据的条件表达式。 如果条件表达式的计算结果为`True`中的代码`Catch`块将运行。  
  
 [!code-vb[VbVbalrStatements#92](../../../visual-basic/language-reference/error-messages/codesnippet/VisualBasic/try-catch-finally-statement_5.vb)]  
  
## <a name="example"></a>示例  
 下面的示例有`Try…Catch`中包含的语句`Try`块。 内部`Catch`块中引发异常有其`InnerException`属性设置为原始异常。 外部`Catch`块都会报告自己的异常和内部异常。  
  
 [!code-vb[VbVbalrStatements#93](../../../visual-basic/language-reference/error-messages/codesnippet/VisualBasic/try-catch-finally-statement_6.vb)]  
  
## <a name="example"></a>示例  
 下面的示例阐释异步方法的异常处理。 若要捕获的适用于异步任务，异常`Await`表达式处于`Try`中捕获的调用方和异常块`Catch`块。  
  
 在示例中取消注释 `Throw New Exception` 行以演示异常处理。 在捕获该异常`Catch`块，该任务的`IsFaulted`属性设置为`True`，和任务的`Exception.InnerException`属性设置为异常。  
  
 取消注释 `Throw New OperationCancelledException` 行以演示在取消异步进程时发生的情况。 在捕获该异常`Catch`块和任务的`IsCanceled`属性设置为`True`。 但是，在不能应用于此示例中，某些情况下`IsFaulted`设置为`True`和`IsCanceled`设置为`False`。  
  
 [!code-vb[csAsyncExceptions#1](../../../csharp/language-reference/keywords/codesnippet/VisualBasic/try-catch-finally-statement_7.vb)]  
  
## <a name="example"></a>示例  
 下面的示例阐释了在多个任务可能导致多个异常的情况中的异常处理。 `Try`块都有`Await`任务的表达式，<xref:System.Threading.Tasks.Task.WhenAll%2A?displayProperty=nameWithType>返回。 任务已完成时的三项任务到<xref:System.Threading.Tasks.Task.WhenAll%2A?displayProperty=nameWithType>应用都已完成。  
  
 三个任务中的每一个都会导致异常。 `Catch`块循环访问异常，异常中找到`Exception.InnerExceptions`任务属性，`Task.WhenAll`返回。  
  
 [!code-vb[csAsyncExceptions#3](../../../csharp/language-reference/keywords/codesnippet/VisualBasic/try-catch-finally-statement_8.vb)]  
  
## <a name="see-also"></a>另请参阅  
 <xref:Microsoft.VisualBasic.Information.Err%2A>  
 <xref:System.Exception>  
 [Exit 语句](../../../visual-basic/language-reference/statements/exit-statement.md)  
 [On Error 语句](../../../visual-basic/language-reference/statements/on-error-statement.md)  
 [有关使用代码片段的最佳做法](/visualstudio/ide/best-practices-for-using-code-snippets)  
 [异常处理](https://msdn.microsoft.com/library/dd997415)  
 [Throw 语句](../../../visual-basic/language-reference/statements/throw-statement.md)

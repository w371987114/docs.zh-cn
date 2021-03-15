---
description: 详细了解：Visual Basic 的新增功能
title: 新增功能
ms.date: 10/24/2018
f1_keywords:
- VB.StartPage.WhatsNew
helpviewer_keywords:
- new features, Visual Basic
- what's new [Visual Basic]
- Visual Basic, what's new
ms.assetid: d7e97396-7f42-4873-a81c-4ebcc4b6ca02
ms.openlocfilehash: 1e5f7dec0476d0b7fbf09fa3b253d3d5dfedf426
ms.sourcegitcommit: e3cf8227573e13b8e1f4e3dc007404881cdafe47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103190367"
---
# <a name="whats-new-for-visual-basic"></a>Visual Basic 的新增功能

本主题列出每个 Visual Basic 版本的重要功能名以及该语言最新版本中的新功能和增强功能的详细说明。

## <a name="current-version"></a>当前版本

Visual Basic 16.9/Visual Studio 2019 版本 16.9\
有关新功能，请参阅 [Visual Basic 16.9](#visual-basic-169)。

可以从 [.NET 下载页](https://dotnet.microsoft.com/download)下载最新 .NET SDK。

## <a name="previous-versions"></a>早期版本

Visual Basic 16.0/Visual Studio 2019 版本 16.0\
有关新功能，请参阅 [Visual Basic 16.0](#visual-basic-160)。

Visual Basic 15.5/Visual Studio 2017 版本 15.5\
有关新功能，请参阅 [Visual Basic 15.5](#visual-basic-155)。

Visual Basic 15.3/Visual Studio 2017 版本 15.3\
有关新功能，请参阅 [Visual Basic 15.3](#visual-basic-153)。

Visual Basic 15 / Visual Studio 2017\
有关新功能，请参阅 [Visual Basic 2017](#visual-basic-15)。

Visual Basic/Visual Studio 2015\
有关新功能，请参阅 [Visual Basic 14](#visual-basic-14)。

Visual Basic/Visual Studio 2013\
.NET Compiler Platform（“Roslyn”）的技术预览

Visual Basic/Visual Studio 2012\
`Async` 和 `await` 关键字、迭代器、调用方信息特性

Visual Basic、Visual Studio 2010\
自动实现的属性、集合初始值设定项、隐式行继续符、动态、泛型协变/逆变、全局命名空间访问

Visual Basic/Visual Studio 2008\
语言集成查询 (LINQ)、XML 文本、本地类型推断、对象初始值设定项、匿名类型、扩展方法、本地 `var` 类型推断、lambda 表达式、`if` 运算符、分部方法、可以为 null 的值类型

Visual Basic/Visual Studio 2005\
`My` 类型和帮助程序类型（对应用、计算机、文件系统、网络的访问）

Visual Basic/Visual Studio .NET 2003\
移位运算符、循环变量声明

Visual Basic/Visual Studio .NET 2002\
Visual Basic.NET 的首次发布

## <a name="visual-basic-169"></a>Visual Basic 16.9

Visual Basic 16.9 允许使用仅限 init 属性。

## <a name="visual-basic-160"></a>Visual Basic 16.0

Visual Basic 16.0 侧重于向 .NET Core 提供 Visual Basic 运行时 (microsoft.visualbasic.dll) 的更多功能，这是 Visual Basic 侧重于 .NET Core 的第一个版本。 Visual Basic 运行时的许多部分都依赖于 WinForms，它们将添加到 Visual Basic 的更高版本中。

**允许在语句中的更多位置进行注释**

在 Visual Basic 15.8 及更早版本中，只允许在空行上、语句末尾或在语句中允许使用隐式行继续符的特定位置进行注释。 从 Visual Basic 16.0 开始，还允许在显式行继续符之后以及以空格开头后跟下划线的行上的语句中进行注释。

```vb
Public Sub Main()
    cmd.CommandText = ' Comment is allowed here without _
        "SELECT * FROM Titles JOIN Publishers " _ ' This is a comment
        & "ON Publishers.PubId = Titles.PubID " _
 _ ' This is a comment on a line without code
        & "WHERE Publishers.State = 'CA'"
End Sub
```

**优化的浮点到整数转换**

在以前版本的 Visual Basic 中，[双精度](../language-reference/data-types/double-data-type.md)和[单精度](../language-reference/data-types/single-data-type.md)值到整数的转换的性能相对较差。 Visual Basic 15.8 显著增强了以下情况下的浮点到整数转换的性能：将以下任何方法返回的值传递到一个 [Visual Basic 内部整数转换函数](../language-reference/functions/type-conversion-functions.md)（CByte、CShort、CInt、CLng、CSByte、CUShort、CUInt、CULng）时，或是在 [Option Strict](../language-reference/statements/option-strict-statement.md) 设置为 `Off` 的情况下将以下任何方法返回的值隐式强制转换为整型类型时：

- <xref:Microsoft.VisualBasic.Conversion.Fix(System.Double)?displayProperty=nameWithType>
- <xref:Microsoft.VisualBasic.Conversion.Fix(System.Object)?displayProperty=nameWithType>
- <xref:Microsoft.VisualBasic.Conversion.Fix(System.Single)?displayProperty=nameWithType>
- <xref:Microsoft.VisualBasic.Conversion.Int(System.Double)?displayProperty=nameWithType>
- <xref:Microsoft.VisualBasic.Conversion.Int(System.Object)?displayProperty=nameWithType>
- <xref:Microsoft.VisualBasic.Conversion.Int(System.Single)?displayProperty=nameWithType>
- <xref:System.Math.Ceiling(System.Double)?displayProperty=nameWithType>
- <xref:System.Math.Floor(System.Double)?displayProperty=nameWithType>
- <xref:System.Math.Round(System.Double)?displayProperty=nameWithType>
- <xref:System.Math.Truncate(System.Double)?displayProperty=nameWithType>

此优化使代码可以更快运行 -- 对于进行大量到整型类型的转换的代码，可快达两倍。 下面的示例说明了受此优化影响的一些简单方法调用：

```vb
Dim s As Single = 173.7619
Dim d As Double = s

Dim i1 As Integer = CInt(Fix(s))               ' Result: 173
Dim b1 As Byte = CByte(Int(d))                 ' Result: 173
Dim s1 AS Short = CShort(Math.Truncate(s))     ' Result: 173
Dim i2 As Integer = CInt(Math.Ceiling(d))      ' Result: 174
Dim i3 As Integer = CInt(Math.Round(s))        ' Result: 174
```

请注意，这会截断而不是舍入浮点值。

## <a name="visual-basic-155"></a>Visual Basic 15.5

[非尾随命名参数](../programming-guide/language-features/procedures/passing-arguments-by-position-and-by-name.md#mixing-arguments-by-position-and-by-name)

在 Visual Basic 15.3 和更早版本中，当方法通过位置和名称调用包含的参数时，位置参数必须位于命名参数之前。 从 Visual Basic 15.5 开始，只要到最后一个位置参数的所有参数都处于正确的位置，位置参数和命名参数就可以以任何顺序出现。 当使用命名参数提高代码的可读性时，此方法特别有用。

例如，以下方法调用在命名参数之间有两个位置参数。 命名参数清楚地表明值 19 代表年龄。

```vb
StudentInfo.Display("Mary", age:=19, #9/21/1998#)
```

[`Private Protected` 成员访问修饰符](../language-reference/modifiers/private-protected.md)

这个新的关键字组合定义了一个成员，该成员可以由其包含的类中的所有成员访问，也可以由从包含的类派生的类型访问，但前提是它们也可以在包含的程序集中找到。 由于无法继承结构，因此 `Private Protected` 仅适用于类的成员。

**前导十六进制/二进制/八进制分隔符**

Visual Basic 2017 新增支持下划线字符 (`_`) 作为数字分隔符。 从 Visual Basic 15.5 开始，可以使用下划线字符作为前缀和十六进制、二进制或八进制数字之间的前导分隔符。 以下示例使用前导数字分隔符将 3,271,948,384 定义为十六进制数字：

```vb
Dim number As Integer = &H_C305_F860
```

若要使用下划线字符作为前导分隔符，必须将以下元素添加到 Visual Basic 项目 (\*.vbproj) 文件中：

```xml
<PropertyGroup>
  <LangVersion>15.5</LangVersion>
</PropertyGroup>
```

## <a name="visual-basic-153"></a>Visual Basic 15.3

[**命名元组推理**](../programming-guide/language-features/data-types/tuples.md#inferred-tuple-element-names)

从变量分配元组元素的值时，Visual Basic 会从相应的变量名推断元组元素名；因此无需显式命名元组元素。 以下示例使用推理创建元组，其中包含三个命名元素`state`、`stateName` 和 `capital`。

[!code-vb[Inferred tuple names](../../../samples/snippets/visualbasic/programming-guide/language-features/data-types/named-tuples/program.vb#2)]

**其他编译器开关**

Visual Basic 命令行编译器现在支持 [-refout](../reference/command-line-compiler/refout-compiler-option.md) 和 [-refonly](../reference/command-line-compiler/refonly-compiler-option.md) 编译器选项，可控制引用程序集的输出 。 -refout 定义引用程序集的输出目录，-refonly 指定只通过编译输出一个引用程序集 。

## <a name="visual-basic-15"></a>Visual Basic 15

[**元祖**](../programming-guide/language-features/data-types/tuples.md)

元组是一种轻量级数据结构，通常用于从单个方法调用返回多个值。 一般情况下，若要从方法返回多个值，必须执行以下操作之一：

- 定义自定义类型（`Class` 或 `Structure`）。 这是重量级解决方案。

- 定义一个或多个 `ByRef` 参数，以及从方法返回一个值。

通过 Visual Basic 对元组的支持，可快速定义元组、为其值分配语义名称（可选），并快速检索其值。 以下示例包装对 <xref:System.Int32.TryParse%2A> 方法的调用，并返回一个元组。

[!code-vb[Tuple](../../../samples/snippets/visualbasic/programming-guide/language-features/data-types/tuple-returns.vb#2)]

然后，可以调用该方法并使用如下所示的代码处理返回的元组。

[!code-vb[ReturnTuple](../../../samples/snippets/visualbasic/programming-guide/language-features/data-types/tuple-returns.vb#3)]

**二进制文本和数字分隔符**

可通过使用前缀 `&B` 或 `&b` 定义二进制文本。 此外，可以将下划线字符 `_` 用作数字分隔符，以增强可读性。 以下示例使用这两项功能分配 `Byte` 值，并将其显示为十进制、十六进制和二进制数字。

[!code-vb[Binary](../../../samples/snippets/visualbasic/getting-started/bin-example.vb#1)]

有关详细信息，请参阅 [Byte](../language-reference/data-types/byte-data-type.md#literal-assignments)、[Integer](../language-reference/data-types/integer-data-type.md#literal-assignments)、[Long](../language-reference/data-types/long-data-type.md#literal-assignments)、[Short](../language-reference/data-types/short-data-type.md#literal-assignments)、[SByte](../language-reference/data-types/sbyte-data-type.md#literal-assignments)、[UInteger](../language-reference/data-types/uinteger-data-type.md#literal-assignments)、[ULong](../language-reference/data-types/ulong-data-type.md#literal-assignments) 和 [UShort](../language-reference/data-types/ushort-data-type.md#literal-assignments) 数据类型的“文本分配”部分。

[支持 C# 引用返回值](../programming-guide/language-features/procedures/ref-return-values.md)

从 C# 7.0 开始，C# 支持引用返回值。 也就是说，当调用方法收到引用返回的值时，可以更改引用的值。 Visual Basic 不允许使用引用返回值创建方法，但允许使用和修改引用返回值。

例如，用 C# 编写的以下 `Sentence` 类包括 `FindNext` 方法，该方法查找句子中以指定的子字符串开头的下一个单词。 该字符串作为引用返回值返回，方法引用传递的 `Boolean` 变量指示搜索是否成功。 这意味着，除了读取返回值外，调用方还可以对其进行修改，并且该修改会反映在 `Sentence` 类中。

[!code-csharp[Ref-Return](../../../samples/snippets/visualbasic/getting-started/ref-returns.cs)]

在最简单的形式中，可以使用如下所示的代码修改句子中找到的单词。 请注意，不是将值分配到方法，而是将值分配到方法返回的表达式（即引用返回值）。

[!code-vb[Ref-Return](../../../samples/snippets/visualbasic/getting-started/ref-return.vb#1)]

但是，此代码的问题是，如果找不到匹配项，方法将返回第一个单词。 由于该示例不检查 `Boolean` 参数的值以确定是否找到匹配项，所以如果没有匹配项，则修改第一个单词。 以下示例对此进行了更正：如果没有匹配项，则将第一个单词替换为自身。

[!code-vb[Ref-Return](../../../samples/snippets/visualbasic/getting-started/ref-return.vb#2)]

更好的解决方案是使用引用将引用返回值传递到的帮助程序方法。 然后，帮助程序方法可以修改引用传递给其的参数。 以下示例执行该操作。

[!code-vb[Ref-Return](../../../samples/snippets/visualbasic/getting-started/ref-return-helper.vb#1)]

有关详细信息，请参阅[引用返回值](../programming-guide/language-features/procedures/ref-return-values.md)。

## <a name="visual-basic-14"></a>Visual Basic 14

[NameOf](../language-reference/operators/nameof.md)

可以在错误消息中使用类型或成员的非限定字符串名，而无需对字符串进行硬编码。  这使代码可以在重构时保持正确。  此功能也可用于挂接“模型-视图-控制器”MVC 链接并触发属性更改事件。

[字符串内插](../programming-guide/language-features/strings/interpolated-strings.md)

可以使用字符串内插表达式构造字符串。  内插字符串表达式类似于包含表达式的模板字符串。  与[复合格式设置](../../standard/base-types/composite-formatting.md)相比，内插字符串在自变量方面更易于理解。

[NULL 条件成员访问和索引](../language-reference/operators/null-conditional-operators.md)

可以在执行成员访问 (`?.`) 或索引 (`?[]`) 操作之前以非常轻量的语法方式测试是否存在 null。  这些运算符可帮助编写更少的代码来处理 null 检查，尤其是对于下降到数据结构。  如果左操作数或对象引用为 null，则操作会返回 null。

[多行字符串文本](../programming-guide/language-features/strings/string-basics.md)

字符串可以包含换行符序列。  不再需要使用 `<xml><![CDATA[...text with newlines...]]></xml>.Value` 这种旧的解决方法

**注释**

可以在隐式行继续符之后、初始值设定项表达式内部以及 LINQ 表达式项之间放置注释。

**更智能的完全限定名称解析**

如果提供了 `Threading.Thread.Sleep(1000)` 这类代码，Visual Basic 过去会查找命名空间“Threading”，发现它在 System.Threading 与 System.Windows.Threading 混淆，然后报告错误。  Visual Basic 现在将这两个可能的命名空间结合在一起进行考虑。  如果显示完成列表，则 Visual Studio 编辑器会在完成列表中列出这两种类型中的成员。

**以年份开头的日期文本**

可以使用 yyyy-mm-dd 格式的日期文本 `#2015-03-17 16:10 PM#`。

**只读接口属性**

可以使用读写属性实现只读接口属性。 该接口可保证最小功能，不会阻止实现类允许设置属性。

[TypeOf \<expr> IsNot \<type>](../language-reference/operators/typeof-operator.md)

为提高代码的可读性，现在可以将 `TypeOf` 与 `IsNot` 一起使用。

[#Disable Warning \<ID> 和 #Enable Warning \<ID>](../language-reference/directives/disable-enable.md)

可以对源文件中的区域禁用和启用特定警告。

**XML 文档注释改进**

编写文档注释时，可获取智能编辑器和生成支持，以用于验证参数名、正确处理 `crefs`（泛型、运算符等）、着色和重构。

[部分模块和接口定义](../language-reference/modifiers/partial.md)

除了类和结构之外，还可以声明部分模块和接口。

[方法体中的 #Region 指令](../language-reference/directives/region-directive.md)

可以将 #Region…#End Region 限定符放置在文件中、函数内部甚至是跨越函数体的任何位置。

[替代定义是隐式重载](../language-reference/modifiers/overrides.md)

如果你向定义添加 `Overrides` 修饰符，则编译器会隐式添加 `Overloads`，以便你可以在常见情况下输入更少的代码。

**属性参数中允许使用 CObj**

当 CObj(...) 在特性构造中使用时，编译器过去会发出错误，指出它不是常量。

**从不同接口声明和使用不明确的方法**

以下代码以前会生成错误，从而阻止你声明 `IMock` 或调用 `GetDetails`（如果这些内容已在 C# 中声明）：

```vb
Interface ICustomer
  Sub GetDetails(x As Integer)
End Interface

Interface ITime
  Sub GetDetails(x As String)
End Interface

Interface IMock : Inherits ICustomer, ITime
  Overloads Sub GetDetails(x As Char)
End Interface

Interface IMock2 : Inherits ICustomer, ITime
End Interface
```

现在，编译器会使用正常的重载决策规则来选择最合适的 `GetDetails` 进行调用，你可以在 Visual Basic 中声明接口关系（如示例中所示的这些关系）。

## <a name="see-also"></a>请参阅

- [Visual Studio 2017 中的新增功能](/visualstudio/ide/whats-new-visual-studio-2017)
- [Visual Studio 2019 中的新增功能](/visualstudio/ide/whats-new-visual-studio-2019)

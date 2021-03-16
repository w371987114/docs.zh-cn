---
title: 中断性变更：StringInfo 和 TextElementEnumerator 现在与 UAX29 兼容
description: 了解 .NET 5 中的全球化中断性变更：StringInfo 和 TextElementEnumerator 将根据最新版 Unicode 标准处理字形群集。
ms.date: 04/07/2020
ms.openlocfilehash: cf770e30bfadf1973bbe018cc9d2783ed6234723
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256700"
---
# <a name="stringinfo-and-textelementenumerator-are-now-uax29-compliant"></a>StringInfo 和 TextElementEnumerator 现在与 UAX29 兼容

在此更改之前，<xref:System.Globalization.StringInfo?displayProperty=fullName> 和 <xref:System.Globalization.TextElementEnumerator?displayProperty=fullName> 无法正确处理所有字形群集。 某些字形已拆分为其构成部分，而不是合并在一起。 现在，<xref:System.Globalization.StringInfo> 和 <xref:System.Globalization.TextElementEnumerator> 将根据 Unicode 标准的最新版本处理字形群集。

此外，<xref:Microsoft.VisualBasic.Strings.StrReverse%2A?displayProperty=fullName> 方法（用于反转 Visual Basic 中的字符串）现在也遵循 Unicode 标准来处理字形群集。

## <a name="change-description"></a>更改描述

[字形](https://www.unicode.org/glossary/#grapheme)或[扩展的字形群集](https://www.unicode.org/glossary/#extended_grapheme_cluster)是一个可以由多个 Unicode 码位组成的用户感知字符。 例如，包含泰文字符“kam”的字符串（:::no-loc text="กำ":::）由以下两个字符组成：

- :::no-loc text="ก"::: (= '\u0e01') THAI CHARACTER KO KAI
- :::no-loc text=" ำ"::: (= '\u0e33') THAI CHARACTER SARA AM

当向用户显示时，操作系统会将这两个字符组合在一起以形成单个显示字符（或字形）“kam”或 :::no-loc text="กำ":::。 表情符号也可以包含以这种类似方式显示的多个组合字符。

> [!TIP]
> 引用字形时，.NET 文档有时会使用术语“文本元素”。

<xref:System.Globalization.StringInfo> 和 <xref:System.Globalization.TextElementEnumerator> 类检查字符串并返回有关它们包含的字形信息。 在 .NET Framework（所有版本）和 .NET Core 3.x 及更早版本中，这两个类将使用自定义逻辑，这种逻辑可处理一些合并类但并不完全符合 [Unicode 标准](https://www.unicode.org/reports/tr29/tr29-35.html#Grapheme_Cluster_Boundaries)。 例如，<xref:System.Globalization.StringInfo> 和 <xref:System.Globalization.TextElementEnumerator> 类会将单个“kam”错误地拆分回其构成部分，而不是将它们合并在一起。 这些类还会将表情符号“🤷🏽♀️”错误地拆分为四个群集（人耸肩、肤色调整、性别调整和不可见组合部分），而不是将它们合并为单个字形群集。

从 .NET 5 开始，<xref:System.Globalization.StringInfo> 和 <xref:System.Globalization.TextElementEnumerator> 类可实现 Unicode 标准，如 [Unicode 标准附录 \#29（修订号 35）第 3 章节所述](https://www.unicode.org/reports/tr29/tr29-35.html)。 特别是，它们现在将对所有包含类返回[扩展的字形群集](https://www.unicode.org/glossary/#extended_grapheme_cluster)。

考虑下列 C# 代码：

```csharp
using System.Globalization;

static void Main(string[] args)
{
    PrintGraphemes("กำ");
    PrintGraphemes("🤷🏽‍♀️");
}

static void PrintGraphemes(string str)
{
    Console.WriteLine($"Printing graphemes of \"{str}\"...");
    int i = 0;

    TextElementEnumerator enumerator = StringInfo.GetTextElementEnumerator(str);
    while (enumerator.MoveNext())
    {
        Console.WriteLine($"Grapheme {++i}: \"{enumerator.Current}\"");
    }

    Console.WriteLine($"({i} grapheme(s) total.)");
    Console.WriteLine();
}
```

在 .NET Framework 和 .NET Core 3.x 及更早版本中，字形是拆分的，控制台输出如下所示：

```txt
Printing graphemes of "กำ"...
Grapheme 1: "ก"
Grapheme 2: "ำ"
(2 grapheme(s) total.)

Printing graphemes of "🤷🏽‍♀️"...
Grapheme 1: "🤷"
Grapheme 2: "🏽"
Grapheme 3: "‍"
Grapheme 4: "♀️"
(4 grapheme(s) total.)
```

在 .NET 5 及更高版本中，字形是合并在一起的，控制台输出如下所示：

```txt
Printing graphemes of "กำ"...
Grapheme 1: "กำ"
(1 grapheme(s) total.)

Printing graphemes of "🤷🏽‍♀️"...
Grapheme 1: "🤷🏽‍♀️"
(1 grapheme(s) total.)
```

此外，从 .NET 5 开始，<xref:Microsoft.VisualBasic.Strings.StrReverse%2A?displayProperty=fullName> 方法（用于反转 Visual Basic 中的字符串）现在也遵循 Unicode 标准来处理字形群集。

这些更改是 .NET 中更广泛的一组 Unicode 和 UTF-8 改进的一部分，包括扩展的字形群集枚举 API，用于补充在 .NET Core 3.0 中随 <xref:System.Text.Rune?displayProperty=fullName> 类型引入的 Unicode 标量值枚举 API。

## <a name="version-introduced"></a>引入的版本

.NET 5.0

## <a name="recommended-action"></a>建议操作

你不必执行任何操作。 你的应用将在各种全球化相关场景中以更符合标准的方式自动运行。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Globalization.StringInfo?displayProperty=fullName>
- <xref:System.Globalization.TextElementEnumerator?displayProperty=fullName>
- <xref:Microsoft.VisualBasic.Strings.StrReverse%2A?displayProperty=fullName>

<!--

### Affected APIs

- `T:System.Globalization.StringInfo`
- `T:System.Globalization.TextElementEnumerator`
- `Overload:Microsoft.VisualBasic.Strings.StrReverse`

### Category

Globalization

-->

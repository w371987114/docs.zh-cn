---
title: 中断性变更：标准数字格式分析精度
description: 了解核心 .NET 库中的 .NET 6 中断性变更：标准数字格式分析现可处理更高的精度。
ms.date: 02/26/2021
ms.openlocfilehash: ad1555493bbc6198541365132cc421db7c01a5a2
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256915"
---
# <a name="standard-numeric-format-parsing-precision"></a>标准数字格式分析精度

在使用 `ToString` 和 `TryFormat` 将数字格式化为字符串时，.NET 现支持使用更高的精度值。

## <a name="change-description"></a>更改描述

在将数字格式化为字符串，[格式字符串](../../../../standard/base-types/standard-numeric-format-strings.md)中的精度说明符表示所生成的字符串中的位数。 根据格式说明符（即[字符串开头处的字符](../../../../standard/base-types/standard-numeric-format-strings.md#standard-format-specifiers)），精度可表示总位数、有效位数或小数位数。

在之前的 .NET 版本中，标准数字格式分析逻辑限制为 99 或更低的精度。 某些数字类型具有更高的精度，但一些 `ToString(string format)` 不会正确公开它。 如果你指定高于 99 的精度（例如 `32.ToString("C100")`），格式字符串将被解释为[自定义数字格式字符串](../../../../standard/base-types/custom-numeric-format-strings.md)而不是“精度为 100 的货币”。 在自定义数字格式字符串中，字符将被解释为[字符文本](../../../../standard/base-types/custom-numeric-format-strings.md#character-literals)。 此外，根据精度值，包含无效格式说明符的格式字符串将以不同的方式解释。 `H99` 会对无效的格式说明符引发 <xref:System.FormatException>，而 `H100` 被解释为自定义数字格式字符串。

从 .NET 6 开始，.NET 支持高达 <xref:System.Int32.MaxValue?displayProperty=nameWithType> 的精度。 由带有任意位数的格式说明符组成的格式字符串会被解释为带有精度的标准数字格式字符串。 对于下述任一或两种情况，会引发 <xref:System.FormatException>：

- 格式说明符字符不是[标准格式说明符](../../../../standard/base-types/standard-numeric-format-strings.md#standard-format-specifiers)。
- 精度大于 <xref:System.Int32.MaxValue?displayProperty=nameWithType>。

此更改是在影响所有数字类型的分析逻辑中实现的。

下表显示了各种格式字符串的行为更改。

| 格式字符串 | 旧行为 | .NET 6 及更高版本中的行为 |
| - | - | - |
| `C2` | 表示带有两个小数位数的货币 | 表示带有两个小数位数的货币（无更改） |
| `C100` | 表示打印“C100”的自定义数字格式字符串 | 表示带有 100 个小数位数的货币 |
| `H99` | 由于存在无效的标准格式说明符“H”，引发 <xref:System.FormatException> | 由于存在无效的标准格式说明符“H”，引发 <xref:System.FormatException>（无更改） |
| `H100` | 表示自定义数字格式字符串 | 由于存在无效的标准格式说明符“H”，引发 <xref:System.FormatException> |

## <a name="version-introduced"></a>引入的版本

6.0 预览版 2

## <a name="reason-for-change"></a>更改原因

此更改会更正在对数字格式分析使用更高的精度时出现的意外行为。

## <a name="recommended-action"></a>建议的操作

在大多数情况下，无需执行任何操作，生成的字符串中会显示正确的精度。

不过，如果想要还原到之前的行为（即当精度高于 99 时，格式说明符被解释为文本字符），可将字符用单引号引起来或使用反斜杠对其进行转义。 例如，在之前的 .NET 版本中，`42.ToString("G999")` 会返回 `G999`。 若要保持该行为，请将格式字符串更改为 `"'G'999"` 或 `"\\G999"`。 这将适用于 .NET Framework、.NET Core 和 .NET 5（及更高版本）。

以下格式字符串将继续被解释为自定义数字格式字符串：

- 以任何非 ASCII 字母字符开头，例如 `$` 或 `è`。
- 以 ASCII 字母字符开头但后面不跟 ASCII 数字，例如 `A$`。
- 以 ASCII 字母字符开头，后跟 ASCII 数字序列，然后再跟任何非 ASCII 数字字符，例如 `A12A`。

## <a name="affected-apis"></a>受影响的 API

此更改是在影响所有数字类型的分析逻辑中实现的。

- <xref:System.Numerics.BigInteger.ToString(System.String)?displayProperty=fullName>
- <xref:System.Numerics.BigInteger.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Numerics.BigInteger.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Int32.ToString(System.String)?displayProperty=fullName>
- <xref:System.Int32.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Int32.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.UInt32.ToString(System.String)?displayProperty=fullName>
- <xref:System.UInt32.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.UInt32.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Byte.ToString(System.String)?displayProperty=fullName>
- <xref:System.Byte.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Byte.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.SByte.ToString(System.String)?displayProperty=fullName>
- <xref:System.SByte.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.SByte.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Int16.ToString(System.String)?displayProperty=fullName>
- <xref:System.Int16.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Int16.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.UInt16.ToString(System.String)?displayProperty=fullName>
- <xref:System.UInt16.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.UInt16.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Int64.ToString(System.String)?displayProperty=fullName>
- <xref:System.Int64.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Int64.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.UInt64.ToString(System.String)?displayProperty=fullName>
- <xref:System.UInt64.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.UInt64.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Half.ToString(System.String)?displayProperty=fullName>
- <xref:System.Half.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Half.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Single.ToString(System.String)?displayProperty=fullName>
- <xref:System.Single.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Single.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Double.ToString(System.String)?displayProperty=fullName>
- <xref:System.Double.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Double.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Decimal.ToString(System.String)?displayProperty=fullName>
- <xref:System.Decimal.ToString(System.String,System.IFormatProvider)?displayProperty=fullName>
- <xref:System.Decimal.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)?displayProperty=fullName>

## <a name="see-also"></a>另请参阅

- [标准数字格式字符串](../../../../standard/base-types/standard-numeric-format-strings.md)
- [自定义格式字符串中的字符文本](../../../../standard/base-types/custom-numeric-format-strings.md#character-literals)

<!--

### Category

Core .NET libraries

### Affected APIs

- `M:System.Numerics.BigInteger.ToString(System.String)`
- `M:System.Numerics.BigInteger.ToString(System.String,System.IFormatProvider)`
- `M:System.Numerics.BigInteger.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.Int32.ToString(System.String)`
- `M:System.Int32.ToString(System.String,System.IFormatProvider)`
- `M:System.Int32.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.UInt32.ToString(System.String)`
- `M:System.UInt32.ToString(System.String,System.IFormatProvider)`
- `M:System.UInt32.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.Byte.ToString(System.String)`
- `M:System.Byte.ToString(System.String,System.IFormatProvider)`
- `M:System.Byte.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.SByte.ToString(System.String)`
- `M:System.SByte.ToString(System.String,System.IFormatProvider)`
- `M:System.SByte.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.Int16.ToString(System.String)`
- `M:System.Int16.ToString(System.String,System.IFormatProvider)`
- `M:System.Int16.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.UInt16.ToString(System.String)`
- `M:System.UInt16.ToString(System.String,System.IFormatProvider)`
- `M:System.UInt16.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.Int64.ToString(System.String)`
- `M:System.Int64.ToString(System.String,System.IFormatProvider)`
- `M:System.Int64.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.UInt64.ToString(System.String)`
- `M:System.UInt64.ToString(System.String,System.IFormatProvider)`
- `M:System.UInt64.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.Half.ToString(System.String)`
- `M:System.Half.ToString(System.String,System.IFormatProvider)`
- `M:System.Half.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.Single.ToString(System.String)`
- `M:System.Single.ToString(System.String,System.IFormatProvider)`
- `M:System.Single.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.Double.ToString(System.String)`
- `M:System.Double.ToString(System.String,System.IFormatProvider)`
- `M:System.Double.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`
- `M:System.Decimal.ToString(System.String)`
- `M:System.Decimal.ToString(System.String,System.IFormatProvider)`
- `M:System.Decimal.TryFormat(System.Span{System.Char},System.Int32@,System.ReadOnlySpan{System.Char},System.IFormatProvider)`

-->

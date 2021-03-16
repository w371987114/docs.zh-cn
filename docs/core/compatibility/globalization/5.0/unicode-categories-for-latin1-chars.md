---
title: 中断性变更：某些 Latin-1 字符的 Unicode 类别已更改
description: 了解 .NET 5 中的全球化中断性变更：Char 方法现在为 Latin-1 范围内的字符返回正确的 Unicode 类别。
ms.date: 04/07/2020
ms.openlocfilehash: 03355c488d2bdae78f989e647c9b5b7913d73649
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256668"
---
# <a name="unicode-category-changed-for-some-latin-1-characters"></a>某些 Latin-1 字符的 Unicode 类别已更改

现在，<xref:System.Char> 方法为 Latin-1 范围内的字符返回正确的 Unicode 类别。 类别与 Unicode 标准的类别匹配。

## <a name="change-description"></a>更改说明

在以前的 .NET 版本中，<xref:System.Char> 方法对 Latin-1 范围内的字符使用 Unicode 类别的固定列表。 但是，自从实现这些 API 以来，Unicode 标准已更改其中某些字符的类别，从而造成了不一致。 此外，遵循 Unicode 标准的 <xref:System.Char> 和 <xref:System.Globalization.CharUnicodeInfo> API 之间也存在不一致。 在 .NET 5 及更高版本中，<xref:System.Char> 方法使用并返回与所有字符的 Unicode 标准匹配的 Unicode 类别。

下表显示了 .NET 5.0 中 Unicode 类别已更改的字符：

| 字符    | Unicode 类别<br>在以前的 .NET 版本中 | Unicode 类别<br>在 .NET 5 及更高版本中 |
|:------------:|:---------------------------------------------:|:--------------------------------------------------:|
| § (\u00a7)   | `OtherSymbol`                                 | `OtherPunctuation`                                 |
| ª (\u00aa)   | `LowercaseLetter`                             | `OtherLetter`                                      |
| SHY (\u00ad) | `DashPunctuation`                             | `Format`                                           |
| ¶ (\u00b6)   | `OtherSymbol`                                 | `OtherPunctuation`                                 |
| º (\u00ba)   | `LowercaseLetter`                             | `OtherLetter`                                      |

## <a name="version-introduced"></a>引入的版本

.NET 5.0

## <a name="recommended-action"></a>建议操作

如果你有任何使用 <xref:System.Char> 类获取 Unicode 字符类别的代码，并且假定该类别永远不会改变，则可能需要对其进行更新。

## <a name="reason-for-change"></a>更改原因

进行此更改是为了使 <xref:System.Char> 类型返回的类别与 Unicode 标准和 <xref:System.Globalization.CharUnicodeInfo> 类型保持一致。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Char.GetUnicodeCategory%2A?displayProperty=fullName>
- <xref:System.Char.IsLetter%2A?displayProperty=fullName>
- <xref:System.Char.IsPunctuation%2A?displayProperty=fullName>
- <xref:System.Char.IsSymbol%2A?displayProperty=fullName>
- <xref:System.Char.IsLower%2A?displayProperty=fullName>

此外，此更改会影响依赖于 <xref:System.Char> 来获取 Unicode 字符类别的任何类，例如 <xref:System.Text.RegularExpressions.Regex>。

<!--

### Affected APIs

- `Overload:System.Char.GetUnicodeCategory`
- `Overload:System.Char.IsLetter`
- `Overload:System.Char.IsPunctuation`
- `Overload:System.Char.IsSymbol`
- `Overload:System.Char.IsLower`

### Category

- Core .NET libraries
- Globalization
-
-->

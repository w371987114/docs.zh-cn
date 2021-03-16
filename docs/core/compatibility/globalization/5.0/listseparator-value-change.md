---
title: 中断性变更：TextInfo.ListSeparator 值更改
description: 了解 .NET 5 的中断性变更：版本 5.0 和 5.0.1 之间更改了 TextInfo.ListSeparator 的默认值。
ms.date: 12/10/2020
ms.openlocfilehash: 9a319da8ea8e3cbf62cbf4730e553b03f5bfdc89
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256720"
---
# <a name="textinfolistseparator-values-changed"></a>TextInfo.ListSeparator 值已更改

在所有操作系统上，不同区域性的默认 <xref:System.Globalization.TextInfo.ListSeparator?displayProperty=nameWithType> 值均已更改。

## <a name="change-description"></a>更改描述

在 .NET 5.0.0 中，作为[从 NLS 切换到 ICU 库](icu-globalization-api.md)的一部分，不同区域性的默认 <xref:System.Globalization.TextInfo.ListSeparator?displayProperty=nameWithType> 值在 Windows 上已更改。 从 International Components for Unicode (ICU) 获得的十进制分隔符值用作 <xref:System.Globalization.TextInfo.ListSeparator> 值。 在 Linux 和 macOS 上，<xref:System.Globalization.TextInfo.ListSeparator?displayProperty=nameWithType> 值没有变化，也就是说，它们继续使用十进制分隔符值。

对于使用 .NET 5.0.1 及更高版本的所有操作系统，<xref:System.Globalization.TextInfo.ListSeparator?displayProperty=nameWithType> 的值等于从 NLS 获得的值。 对于 Windows，这意味着这些值等于 .NET Framework 和 .NET Core 1.0-3.1 中的值。 对于 Linux 和 macOS，<xref:System.Globalization.TextInfo.ListSeparator?displayProperty=nameWithType> 值现在与 Windows 的 <xref:System.Globalization.TextInfo.ListSeparator?displayProperty=nameWithType> 值匹配。

下表总结了对 <xref:System.Globalization.TextInfo.ListSeparator?displayProperty=nameWithType> 值所做的更改。

| | .NET framework<br/>.NET Core 1.0-3.1 | .NET 5 | .NET 5.0.1 |
-|-|-|-
| Windows | 从 NLS 获取 | ICU 中的十进制分隔符。<br/>可切换回 NLS。 | 等于 NLS |
| **Linux 和 macOS** | ICU 中的十进制分隔符 | ICU 中的十进制分隔符 | 等于 NLS |

## <a name="version-introduced"></a>引入的版本

5.0.1

## <a name="reason-for-change"></a>更改原因

开发人员报告说，他们在分析逗号分隔值 (CSV) 文件时使用 <xref:System.Globalization.TextInfo.ListSeparator?displayProperty=nameWithType> 属性，而新的 <xref:System.Globalization.TextInfo.ListSeparator?displayProperty=nameWithType> 值破坏了该分析。

## <a name="recommended-action"></a>建议的操作

如果代码依赖于先前的十进制分隔符值，则可以对所需的 <xref:System.Globalization.TextInfo.ListSeparator?displayProperty=nameWithType> 值进行硬编码。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Globalization.TextInfo.ListSeparator?displayProperty=fullName>

<!--

#### Category

- Globalization

### Affected APIs

- `P:System.Globalization.TextInfo.ListSeparator`

-->

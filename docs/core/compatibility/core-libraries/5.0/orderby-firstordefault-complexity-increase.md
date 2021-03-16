---
title: 中断性变更：增加了 LINQ OrderBy.First{OrDefault} 的复杂度
description: 了解核心 .NET 库中的 .NET 5 中断性变更：OrderBy.First 的实现已更改。
ms.date: 11/01/2020
ms.openlocfilehash: 4cd2dda5f60976f935505d6a6cb1e4c23d150d09
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257266"
---
# <a name="complexity-of-linq-orderbyfirstordefault-increased"></a>增加了 LINQ OrderBy.First{OrDefault} 的复杂度

<xref:System.Linq.Enumerable.OrderBy%2A>`.`<xref:System.Linq.Enumerable.First%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})> 和 <xref:System.Linq.Enumerable.OrderBy%2A>`.`<xref:System.Linq.Enumerable.FirstOrDefault%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})> 的实现已更改，这导致操作复杂度增加。

## <a name="change-description"></a>更改描述

在 .NET Core 1.x 至 3.x 中，调用 <xref:System.Linq.Enumerable.OrderBy%2A> 或 <xref:System.Linq.Enumerable.OrderByDescending%2A>（后跟 <xref:System.Linq.Enumerable.First%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})> 或 <xref:System.Linq.Enumerable.FirstOrDefault%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})>）需处理 `O(N)` 复杂度。 由于只需要第一个（或默认）元素，因此要找到它，只需要一次枚举。 然而，会精确调用 `N` 次提供给 <xref:System.Linq.Enumerable.First%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})> 或 <xref:System.Linq.Enumerable.FirstOrDefault%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})> 的谓词，其中 `N` 是序列的长度。

在 .NET 5 及更高版本中，[进行了一项更改](https://github.com/dotnet/runtime/pull/36643)，以致于调用 <xref:System.Linq.Enumerable.OrderBy%2A> 或 <xref:System.Linq.Enumerable.OrderByDescending%2A>（后跟 <xref:System.Linq.Enumerable.First%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})> 或 <xref:System.Linq.Enumerable.FirstOrDefault%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})>）会处理 `O(N log N)` 复杂度，而不是 `O(N)` 复杂度。 但是，可调用提供给 <xref:System.Linq.Enumerable.First%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})> 或 <xref:System.Linq.Enumerable.FirstOrDefault%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})> 的谓词（不超过 `N` 次），这对整体性能来说更为重要。

> [!NOTE]
> 这项更改与 .NET Framework 中操作的实现和复杂性相契合。

## <a name="reason-for-change"></a>更改原因

减少谓词调用次数的好处大于降低整体复杂度的好处，因此 .NET Core 1.0 中引入的实现已被还原。 有关详细信息，请参阅[此 dotnet/runtime 问题](https://github.com/dotnet/runtime/issues/31554)。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

开发人员方面无需执行任何操作。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Linq.Enumerable.OrderBy%2A?displayProperty=fullName>
- <xref:System.Linq.Enumerable.OrderByDescending%2A?displayProperty=fullName>
- <xref:System.Linq.Enumerable.First%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})?displayProperty=fullName>
- <xref:System.Linq.Enumerable.FirstOrDefault%60%601(System.Collections.Generic.IEnumerable{%60%600},System.Func{%60%600,System.Boolean})?displayProperty=fullName>

<!--

### Category

Core .NET libraries

### Affected APIs

- `Overload:System.Linq.Enumerable.OrderBy`
- `Overload:System.Linq.Enumerable.OrderByDescending`
- `M:System.Linq.Enumerable.First``1(System.Collections.Generic.IEnumerable{``0},System.Func{``0,System.Boolean})`
- `M:System.Linq.Enumerable.FirstOrDefault``1(System.Collections.Generic.IEnumerable{``0},System.Func{``0,System.Boolean})`

-->

---
title: 中断性变更：LastIndexOf 改进了对空搜索字符串的处理
description: 了解核心 .NET 库中的 .NET 5 中断性变更：在搜索零长度子字符串时，LastIndexOf 和相关 API 现在返回正确的值。
ms.date: 11/01/2020
ms.openlocfilehash: 9dc34300d867fe1bb9264494b3f2261bad2c1eea
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257422"
---
# <a name="lastindexof-has-improved-handling-of-empty-search-strings"></a>LastIndexOf 改进了对空搜索字符串的处理

在较大字符串中搜索零长度（或零长度等效项）子字符串时，<xref:System.String.LastIndexOf%2A?displayProperty=nameWithType> 和相关 API 现在返回正确的值。

## <a name="change-description"></a>更改描述

在 .NET Framework 和 .NET Core 1.0-3.1 中，当调用方搜索零长度子字符串时，<xref:System.String.LastIndexOf%2A?displayProperty=nameWithType> 和相关 API 可能会返回不正确的值。

```csharp
Console.WriteLine("Hello".LastIndexOf("")); // prints '4' (incorrect)

ReadOnlySpan<char> span = "Hello";
Console.WriteLine(span.LastIndexOf("")); // prints '0' (incorrect)
```

从 .NET 5 开始，这些 API 将返回 `LastIndexOf` 的正确值。

```csharp
Console.WriteLine("Hello".LastIndexOf("")); // prints '5' (correct)

ReadOnlySpan<char> span = "Hello";
Console.WriteLine(span.LastIndexOf("")); // prints '5' (correct)
```

在这些示例中，`5` 为正确答案，因为 `"Hello".Substring(5)` 和 `"Hello".AsSpan().Slice(5)` 均生成一个空字符串，该字符串完全等同于所查找的空子字符串。

## <a name="reason-for-change"></a>更改原因

此更改是围绕 .NET 5 字符串处理的整体 bug 修复工作的一部分。 它还有助于统一 Windows 和非 Windows 平台的行为。 有关详细信息，请参阅 [dotnet/runtime#13383](https://github.com/dotnet/runtime/issues/13383) 和 [dotnet/runtime##13382](https://github.com/dotnet/runtime/issues/13382)。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

你不必执行任何操作。 .NET 5 运行时自动提供新行为。

没有用于还原旧行为的兼容性开关。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.String.LastIndexOf%2A?displayProperty=fullName>
- <xref:System.Globalization.CompareInfo.LastIndexOf%2A?displayProperty=fullName>
- <xref:System.MemoryExtensions.LastIndexOf%2A?displayProperty=fullName>

<!--

### Category

Core .NET libraries

### Affected APIs

- `Overload:System.String.LastIndexOf`
- `Overload:System.Globalization.CompareInfo.LastIndexOf`
- `Overload:System.MemoryExtensions.LastIndexOf`

-->

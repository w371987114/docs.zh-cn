---
title: 中断性变更：CA1831:为字符串使用 AsSpan 而不是基于范围的索引器
description: 了解 .NET 5 中因启用代码分析规则 CA1831 而导致的中断性变更。
ms.date: 08/21/2020
ms.openlocfilehash: 1ed4e2bdde9c3d525f95963f316551909ac3de7c
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257786"
---
# <a name="warning-ca1831-use-asspan-instead-of-range-based-indexers-for-string"></a>警告 CA1831：为字符串使用 AsSpan 而不是基于范围的索引器

默认情况下，从 .NET 5.0 开始启用 .NET 代码分析器规则 [CA1831](/visualstudio/code-quality/ca1831)。 对于在字符串中使用基于 <xref:System.Range> 的索引器，但不打算进行复制的任何代码，都将生成一个生成警告。

## <a name="change-description"></a>更改说明

从 .NET 5 开始，.NET SDK 包括 [.NET 源代码分析器](../../../../fundamentals/code-analysis/overview.md)。 默认情况下会启用其中一些规则，包括 [CA1831](/visualstudio/code-quality/ca1831)。 如果项目包含违反此规则的代码，并已被配置为将警告视为错误，则此更改可能会中断生成。

规则 CA1831 查找在字符串中使用基于 <xref:System.Range> 的索引器但不打算进行复制的实例。 如果直接在字符串中使用基于 <xref:System.Range> 的索引器来生成隐式强制转换，则会创建字符串请求部分的不必要副本。 例如：

```csharp
ReadOnlySpan<char> slice = str[1..3];
```

相反，CA1831 建议在字符串的“范围”上使用基于 <xref:System.Range> 的索引器。 例如：

```csharp
ReadOnlySpan<char> slice = str.AsSpan()[1..3];
```

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

- 若要更正代码并避免不必要的分配，请在使用基于 <xref:System.Range> 的索引器之前调用 <xref:System.MemoryExtensions.AsSpan(System.String)> 或 <xref:System.MemoryExtensions.AsMemory(System.String)>。 例如：

  ```csharp
  ReadOnlySpan<char> slice = str.AsSpan()[1..3];
  ```

- 如果不想更改代码，则可通过将规则的严重性设置为 `suggestion` 或 `none` 来禁用规则。 有关详细信息，请参阅[配置代码分析规则](../../../../fundamentals/code-analysis/configuration-options.md)。

- 若要完全禁用代码分析，请在项目文件中将 `EnableNETAnalyzers` 设置为 `false`。 有关详细信息，请参阅 [EnableNETAnalyzers](../../../project-sdk/msbuild-props.md#enablenetanalyzers)。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Range?displayProperty=fullName>

<!--

### Affected APIs

- `T:System.Range`

### Category

Code analysis

-->

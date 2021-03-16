---
title: 中断性变更：CA2013:请勿将 ReferenceEquals 与值类型结合使用
description: 了解 .NET 5 中因启用代码分析规则 CA2013 而导致的中断性变更。
ms.date: 09/03/2020
ms.openlocfilehash: fc68d9a3af0d629dc39aba0091d32b1982d6f2c5
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257760"
---
# <a name="warning-ca2013-do-not-use-referenceequals-with-value-types"></a>警告 CA2013：请勿将 ReferenceEquals 与值类型结合使用

从 .NET 5.0 开始，默认启用 .NET 代码分析器规则 [CA2013](/visualstudio/code-quality/ca2013)。 对于使用 <xref:System.Object.ReferenceEquals(System.Object,System.Object)> 比较一个或多个值类型的相等性的代码，它会生成任何生成警告。

## <a name="change-description"></a>更改说明

从 .NET 5 开始，.NET SDK 包括 [.NET 源代码分析器](../../../../fundamentals/code-analysis/overview.md)。 其中一些规则会默认启用，包括 [CA2013](/visualstudio/code-quality/ca2013)。 如果项目包含违反此规则的代码，并已被配置为将警告视为错误，则此更改可能会中断生成。

规则 CA2013 查找使用 <xref:System.Object.ReferenceEquals(System.Object,System.Object)> 比较一个或多个值类型的相等性的实例。 通过此方式比较值类型的相等性可能会导致错误的结果，因为会先限定这些值，然后再将其进行比较。 即使比较的值表示值类型的同一个实例，<xref:System.Object.ReferenceEquals(System.Object,System.Object)> 也会返回 `false`。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

- 更改代码以使用适当的相等运算符，例如 `==`。 不应禁止显示此警告。

- 若要完全禁用代码分析，请在项目文件中将 `EnableNETAnalyzers` 设置为 `false`。 有关详细信息，请参阅 [EnableNETAnalyzers](../../../project-sdk/msbuild-props.md#enablenetanalyzers)。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Object.ReferenceEquals(System.Object,System.Object)?displayProperty=fullName>

<!--

### Affected APIs

- `M:System.Object.ReferenceEquals(System.Object,System.Object)`

### Category

Code analysis

-->

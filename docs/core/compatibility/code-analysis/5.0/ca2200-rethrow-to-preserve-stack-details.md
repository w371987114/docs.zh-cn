---
title: 中断性变更：CA2200:再次引发以保留堆栈详细信息
description: 了解 .NET 5 中启用代码分析规则 CA2200 所致的中断性变更。
ms.date: 09/03/2020
ms.openlocfilehash: 776a1bcf16c19364017e4652837720080fb7ba72
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257721"
---
# <a name="warning-ca2200-rethrow-to-preserve-stack-details"></a>警告 CA2200：再次引发以保留堆栈详细信息

从 .NET 5.0 开始，默认启用 .NET 代码分析器规则 [CA2200](/visualstudio/code-quality/ca2200)。 它针对再次引发异常的任何 `catch` 块生成一个生成警告，并在 `throw` 语句中显式指定该异常。

## <a name="change-description"></a>更改说明

从 .NET 5 开始，.NET SDK 包括 [.NET 源代码分析器](../../../../fundamentals/code-analysis/overview.md)。 其中一些规则会默认启用，包括 [CA2200](/visualstudio/code-quality/ca2200)。 如果项目包含违反此规则的代码，并已被配置为将警告视为错误，则此更改可能会中断生成。

规则 CA2200 会标记代码，其中重新引发了异常，并且在 `throw` 语句中指定了异常变量。 引发异常时，它所包含的部分信息是堆栈跟踪。 堆栈跟踪是一个方法调用层次结构列表，它以引发异常的方法开头，以捕获异常的方法结尾。 如果通过在 `throw` 语句中指定异常来重新引发该异常，则将在当前方法处重启堆栈跟踪，并且引发该异常的原始方法与当前方法之间的方法调用的列表将丢失。 若要保留原始堆栈跟踪信息和异常信息，请在未指定异常的情况下使用 `throw` 语句。

以下代码片段不会针对规则 CA2200 生成警告。 但是，注释行将触发冲突。

```csharp
catch (ArithmeticException e)
{
    // throw e;
    throw;
}
```

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

- 在未显式指定异常的情况下再次引发异常。 有关详细信息，请参阅 [CA2200](/visualstudio/code-quality/ca2200)。

- 若要完全禁用代码分析，请在项目文件中将 `EnableNETAnalyzers` 设置为 `false`。 有关详细信息，请参阅 [EnableNETAnalyzers](../../../project-sdk/msbuild-props.md#enablenetanalyzers)。

## <a name="affected-apis"></a>受影响的 API

无法通过 API 分析检测到。

<!--

### Affected APIs

Not detectable via API analysis.

### Category

Code analysis

-->

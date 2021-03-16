---
title: 中断性变更：CA2247:TaskCompletionSource 构造函数的参数应为 TaskCreationOptions 值
description: 了解 .NET 5 中因启用代码分析规则 CA2247 而导致的中断性变更。
ms.date: 09/03/2020
ms.openlocfilehash: 6c7accaad312352a1448406f2bbf4189f3df1ee5
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257695"
---
# <a name="warning-ca2247-argument-to-taskcompletionsource-constructor-should-be-taskcreationoptions-value"></a>警告 CA2247：TaskCompletionSource 构造函数的参数应为 TaskCreationOptions 值

从 .NET 5.0 开始，默认启用 .NET 代码分析器规则 [CA2247](/visualstudio/code-quality/ca2247)。 关于对 <xref:System.Threading.Tasks.TaskCompletionSource%601> 构造函数的调用（该函数传递类型为 <xref:System.Threading.Tasks.TaskContinuationOptions> 的参数），它会生成一个生成警告。

## <a name="change-description"></a>更改说明

从 .NET 5 开始，.NET SDK 包括 [.NET 源代码分析器](../../../../fundamentals/code-analysis/overview.md)。 其中一些规则会默认启用，包括 [CA2247](/visualstudio/code-quality/ca2247)。 如果项目包含违反此规则的代码，并已被配置为将警告视为错误，则此更改可能会中断生成。

规则 CA2247 会查找对 <xref:System.Threading.Tasks.TaskCompletionSource%601> 构造函数的调用，这些调用传递类型为 <xref:System.Threading.Tasks.TaskContinuationOptions> 的参数。 <xref:System.Threading.Tasks.TaskCompletionSource%601> 类型具有一个接受 <xref:System.Threading.Tasks.TaskCreationOptions> 值的构造函数和一个接受 <xref:System.Object> 的构造函数。 如果意外传递了 <xref:System.Threading.Tasks.TaskContinuationOptions> 值（而不是 <xref:System.Threading.Tasks.TaskCreationOptions> 值），则在运行时调用带有 <xref:System.Object> 参数的构造函数。 代码将编译并运行，但没有预期行为。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

- 将 <xref:System.Threading.Tasks.TaskContinuationOptions> 参数替换为相应的 <xref:System.Threading.Tasks.TaskCreationOptions> 值。 请勿禁止显示此警告，因为它几乎总是突出显示代码中的 bug。 有关详细信息，请参阅 [CA2247](/visualstudio/code-quality/ca2247)。

- 若要完全禁用代码分析，请在项目文件中将 `EnableNETAnalyzers` 设置为 `false`。 有关详细信息，请参阅 [EnableNETAnalyzers](../../../project-sdk/msbuild-props.md#enablenetanalyzers)。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Threading.Tasks.TaskCompletionSource%601.%23ctor(System.Object)>

<!--

### Affected APIs

- ``M:System.Threading.Tasks.TaskCompletionSource`1.#ctor(System.Object)``

### Category

Code analysis

-->

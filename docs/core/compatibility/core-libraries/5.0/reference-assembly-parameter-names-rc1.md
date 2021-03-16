---
title: 中断性变更：RC2 中的参数名称已更改
description: 了解核心 .NET 库中的 .NET 5 中断性变更：某些引用程序集参数名称已从 .NET 5.0 的预览版本和候选发布版本更改。
ms.date: 11/01/2020
ms.openlocfilehash: caca9055bda50174b40d5675c6d34679df61deba
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257188"
---
# <a name="parameter-names-changed-in-rc2"></a>RC2 中的参数名称已更改

某些引用程序集参数名称已更改以匹配实现程序集中的参数名称。

## <a name="change-description"></a>更改描述

在之前的 .NET 5 预览版和 RC 版本中，某些[引用程序集](../../../../standard/assembly/reference-assemblies.md)参数名称与实现程序集中的对应参数不同。 使用命名参数和反射时，这可能会导致出现问题。

在 .NET 5 RC2 中，这些不匹配的参数名称在引用程序集中已更新，现与实现程序集中的相应参数名完全匹配。

下表显示了更改的 API 和参数名称。

| API | 旧参数名称 | 新参数名称 |
| - | - | - |
| <xref:System.Diagnostics.ActivityContext.%23ctor(System.Diagnostics.ActivityTraceId,System.Diagnostics.ActivitySpanId,System.Diagnostics.ActivityTraceFlags,System.String,System.Boolean)> | `traceOptions` | `traceFlags` |
| <xref:System.Globalization.CompareInfo.IsPrefix(System.ReadOnlySpan{System.Char},System.ReadOnlySpan{System.Char},System.Globalization.CompareOptions,System.Int32@)?displayProperty=nameWithType> | `suffix` | `prefix` |

## <a name="reason-for-change"></a>更改原因

更改参数名称以保持一致性，避免在使用命名参数和反射时出现故障。

## <a name="version-introduced"></a>引入的版本

5.0 RC2

## <a name="recommended-action"></a>建议操作

如果由于参数名称更改而遇到编译器错误，请相应地更新参数名称。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Diagnostics.ActivityContext.%23ctor(System.Diagnostics.ActivityTraceId,System.Diagnostics.ActivitySpanId,System.Diagnostics.ActivityTraceFlags,System.String,System.Boolean)>
- <xref:System.Globalization.CompareInfo.IsPrefix(System.ReadOnlySpan{System.Char},System.ReadOnlySpan{System.Char},System.Globalization.CompareOptions,System.Int32@)?displayProperty=fullName>

<!--

#### Category

Core .NET libraries

### Affected APIs

- `M:System.Diagnostics.ActivityContext.#ctor(System.Diagnostics.ActivityTraceId,System.Diagnostics.ActivitySpanId,System.Diagnostics.ActivityTraceFlags,System.String,System.Boolean)`
- `M:System.Globalization.CompareInfo.IsPrefix(System.ReadOnlySpan{System.Char},System.ReadOnlySpan{System.Char},System.Globalization.CompareOptions,System.Int32@)`

-->

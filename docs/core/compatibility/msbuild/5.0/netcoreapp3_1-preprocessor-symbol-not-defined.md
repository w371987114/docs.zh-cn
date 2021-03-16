---
title: 中断性变更：面向 .NET 5 时，未定义 NETCOREAPP3_1 预处理器符号
description: 了解 .NET 5 中的中断性变更：项目不再为早期版本定义预处理器符号。
ms.date: 09/17/2020
ms.openlocfilehash: 390c8f5af97510db4652f3f42db577e6de158020
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256525"
---
# <a name="netcoreapp3_1-preprocessor-symbol-is-not-defined-when-targeting-net-5"></a>面向 .NET 5 时，未定义 NETCOREAPP3_1 预处理器符号

在 .NET 5 RC2 及更高版本中，项目不再为早期版本（而仅为其目标版本）定义预处理器符号。 这与 .NET Core 1.0 - 3.1 的行为相同。

## <a name="version-introduced"></a>引入的版本

5.0 RC2

## <a name="change-description"></a>更改描述

在 .NET 5 预览版 7 至 RC1 中，面向 `net5.0` 的项目会定义 `NETCOREAPP3_1` 和 `NET5_0` 预处理器符号。 此行为更改的目的在于，从 .NET 5 开始，条件编译[符号将会累积](https://github.com/dotnet/designs/blob/main/accepted/2020/net5/net5.md#preprocessor-symbols)。

在 .NET 5 RC2 及更高版本中，项目仅为其面向的目标框架名字对象 (TFM) 定义符号，而不为任何早期版本进行定义。

## <a name="reason-for-change"></a>更改原因

由于客户反馈，已从预览版 7 开始恢复该更改。 为较早版本的客户定义符号令客户惊讶和困惑，还有人以为这是 C# 编译器中的 bug。

## <a name="recommended-action"></a>建议的操作

请确保你的 `#if` 逻辑在项目面向 `net5.0` 或更高版本时不假定已定义 `NETCOREAPP3_1`。 相反，仅在项目明确面向 `netcoreapp3.1` 时才假定已定义 `NETCOREAPP3_1`。

例如，如果你的项目同时以 .NET Core 2.1 和 .NET Core 3.1 为目标，并调用 .NET Core 3.1 中引入的 API，则 `#if` 逻辑应如下所示：

```csharp
#if NETCOREAPP2_1 || NETCOREAPP3_0
    // Fallback behavior for old versions.
#elif NETCOREAPP
    // Behavior for .NET Core 3.1 and later.
#endif
```

## <a name="affected-apis"></a>受影响的 API

不可用

<!--

### Affected APIs

Not detectable via API analysis.

### Category

MSBuild

-->

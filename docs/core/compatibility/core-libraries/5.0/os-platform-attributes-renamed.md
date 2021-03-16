---
title: 中断性变更：已重命名或已删除 OSPlatform 属性
description: 了解核心 .NET 库中的 .NET 5 中断性变更：预览版本中引入的 OS 平台属性已被删除或已被重命名。
ms.date: 11/01/2020
ms.openlocfilehash: 118c5360eb02c1b4d57fccbd3b2cfdeff0b9fe12
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257253"
---
# <a name="osplatform-attributes-renamed-or-removed"></a>已重命名或已删除 OSPlatform 属性

已删除或已重命名 .NET 5 预览版 8 中引入的以下属性：`MinimumOSPlatformAttribute`、`RemovedInOSPlatformAttribute` 和 `ObsoletedInOSPlatformAttribute`。

## <a name="change-description"></a>更改描述

.NET 5 预览版 8 在 <xref:System.Runtime.Versioning> 命名空间中引入了以下属性：

- `MinimumOSPlatformAttribute`
- `RemovedInOSPlatformAttribute`
- `ObsoletedInOSPlatformAttribute`

在 .NET 5 预览版 8 中，当某个项目通过使用[目标框架名字对象](../../../../standard/frameworks.md)（如 `net5.0-windows`）以特定于 OS 风格的 .NET 5 为目标时，生成将添加程序集级别的 `System.Runtime.Versioning.MinimumOSPlatformAttribute` 属性。

在 .NET 5 RC1 中，已删除 `ObsoletedInOSPlatformAttribute`，并已将 `MinimumOSPlatformAttribute` 和 `RemovedInOSPlatformAttribute` 重命名为以下内容：

| 预览版 8 名称 | RC1 及更高版本名称 |
| - | - |
| `MinimumOSPlatformAttribute` | <xref:System.Runtime.Versioning.SupportedOSPlatformAttribute> |
| `RemovedInOSPlatformAttribute` | <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> |

在 .NET 5 RC1 及更高版本中，当某个项目通过使用[目标框架名字对象](../../../../standard/frameworks.md)（如 `net5.0-windows`）以特定于 OS 风格的 .NET 5 为目标时，生成将添加程序集级别的 <xref:System.Runtime.Versioning.SupportedOSPlatformAttribute> 属性。

## <a name="reason-for-change"></a>更改原因

.NET 5 预览版 8 在 <xref:System.Runtime.Versioning> 中引入了多个属性，用于指定支持 API 的平台。 将特定于平台的 API 用于不支持这些 API 的平台时，[平台兼容性分析器](../../code-analysis/5.0/ca1416-platform-compatibility-analyzer.md)使用这些属性生成生成警告。

对于 .NET 5 RC1，向平台兼容性分析器添加了一项附加功能，用于排除平台。 此功能允许将 API 标记为在 OS 平台上完全不受支持。 此功能会提示更改属性，包括使用更合适的名称。 由于不再需要 `ObsoletedInOSPlatformAttribute`，已将其删除。

## <a name="version-introduced"></a>引入的版本

5.0 RC1

## <a name="recommended-action"></a>建议操作

将项目从 .NET 5 预览版 8 重定目标到 .NET 5 RC1 时，可能会遇到由于这些更改而导致的生成或运行时错误。 例如，重命名 `MinimumOSPlatformAttribute` 可能会产生错误，因为该属性在生成时会应用于特定于平台的程序集，而旧的生成项目仍将引用旧的 API 名称。

生成时错误示例：

- **错误 CS0246：找不到类型名称或命名空间名称“MinimumOSPlatformAttribute”（是否缺少 using 指令或程序集引用？）**
- **错误 CS0246：找不到类型名称或命名空间名称“RemovedInOSPlatformAttribute”（是否缺少 using 指令或程序集引用？）**
- **错误 CS0246：找不到类型名称或命名空间名称“ObsoletedInOSPlatformAttribute”（是否缺少 using 指令或程序集引用？）**

运行时错误示例：

**未经处理的异常。System.TypeLoadException 异常:无法从程序集“System.Runtime, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a”中找到加载类型“System.Runtime.Versioning.MinimumOSPlatformAttribute”。**

若要解决这些错误：

- 将 `MinimumOSPlatformAttribute` 的任何引用更新为 <xref:System.Runtime.Versioning.SupportedOSPlatformAttribute>。
- 将 `RemovedInOSPlatformAttribute` 的任何引用更新为 <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>。
- 删除 `ObsoletedInOSPlatformAttribute` 的任何引用。
- 重新生成项目（或执行“清理 + 生成”）以删除旧的生成项目。

## <a name="affected-apis"></a>受影响的 API

- `System.Runtime.Versioning.MinimumOSPlatformAttribute`
- `System.Runtime.Versioning.ObsoletedInOSPlatformAttribute`
- `System.Runtime.Versioning.RemovedInOSPlatformAttribute`

<!--

### Category

Core .NET libraries

### Affected APIs

- `T:System.Runtime.Versioning.MinimumOSPlatformAttribute`
- `T:System.Runtime.Versioning.ObsoletedInOSPlatformAttribute`
- `T:System.Runtime.Versioning.RemovedInOSPlatformAttribute`

-->

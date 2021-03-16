---
title: 中断性变更：Environment.OSVersion 返回正确的操作系统版本
description: 了解核心 .NET 库中的 .NET 5 中断性变更：Environment.OSVersion 返回操作系统的实际版本而不是为应用程序兼容性选择的 OS。
ms.date: 11/01/2020
ms.openlocfilehash: 05ec886061263ea43a2d956b8ce0ecc06e2bf3ad
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257526"
---
# <a name="environmentosversion-returns-the-correct-operating-system-version"></a>Environment.OSVersion 返回正确的操作系统版本

<xref:System.Environment.OSVersion?displayProperty=nameWithType> 返回操作系统 (OS) 的实际版本，而不是为应用程序兼容性而选择的 OS。

## <a name="change-description"></a>更改描述

在以前的 .NET 版本中，当应用程序在 Windows 兼容模式下运行时，<xref:System.Environment.OSVersion?displayProperty=nameWithType> 返回的 OS 版本可能不正确。 有关详细信息，请参阅 [GetVersionExA 函数备注](/windows/win32/api/sysinfoapi/nf-sysinfoapi-getversionexa#remarks)。 在 macOS 上，<xref:System.Environment.OSVersion?displayProperty=nameWithType> 返回基础 Darwin 内核版本。

从 .NET 5 开始，<xref:System.Environment.OSVersion?displayProperty=nameWithType> 返回适用于 Windows 和 macOS 的操作系统的实际版本。

下表显示了行为差异。

|  | 以前的 .NET 版本 | .NET 5+ |
|--|------------------------|---------|
| Windows | 6.2.9200.0 | 10.0.19042.0 |
| macOS | 19.6.0.0 | 10.15.7 |

## <a name="reason-for-change"></a>更改原因

此属性的用户希望它返回操作系统的实际版本。 大多数 .NET 应用并未在其应用程序清单中指定其支持的版本，因此会从 dotnet 主机获取默认的支持版本。 因此，兼容性填充码对于正在运行的应用没有什么意义。 当 Windows 发布新版本时，如果较旧的 dotnet 主机仍在使用，这些应用可能会收到错误的 OS 版本。 返回实际版本更符合开发人员对此 API 的期望。

随着在 .NET 5 中引入 <xref:System.OperatingSystem.IsWindowsVersionAtLeast%2A?displayProperty=nameWithType>、<xref:System.OperatingSystem.IsMacOSVersionAtLeast%2A?displayProperty=nameWithType> 和 <xref:System.Runtime.Versioning.SupportedOSPlatformAttribute?displayProperty=nameWithType>，<xref:System.Environment.OSVersion?displayProperty=nameWithType> 已更改为与 Windows 和 macOS 一致。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

查看和测试使用 <xref:System.Environment.OSVersion?displayProperty=nameWithType> 的任何代码，以确保其行为符合预期。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Environment.OSVersion?displayProperty=fullName>

<!--

### Category

Core .NET libraries

### Affected APIs

- `P:System.Environment.OSVersion`

-->

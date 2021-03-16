---
title: 中断性变更：Microsoft.DotNet.PlatformAbstractions 包已删除
description: 了解核心 .NET 库中的 .NET 5 中断性变更：Microsoft.DotNet.PlatformAbstractions 包已删除。
ms.date: 11/01/2020
ms.openlocfilehash: aff7be816815b016e3ce694c4e9a97410538c08d
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257240"
---
# <a name="microsoftdotnetplatformabstractions-package-removed"></a>Microsoft.DotNet.PlatformAbstractions 包已删除

将不生成新版本的 [Microsoft.DotNet.PlatformAbstractions NuGet 包](https://www.nuget.org/packages/Microsoft.DotNet.PlatformAbstractions/)。

## <a name="change-description"></a>更改描述

以前，新版本的 <xref:Microsoft.DotNet.PlatformAbstractions?displayProperty=fullName> 库随新版本的 .NET Core 一起生成。 今后，将不会向库中添加新功能，也不会发布新的主版本。 但是，现有版本的库将继续有效并受到维护。

<xref:Microsoft.DotNet.PlatformAbstractions?displayProperty=fullName> 库与 System.\* 命名空间中已建立的 API 重叠。 此外，某些 <xref:Microsoft.DotNet.PlatformAbstractions> API 并未设计成具有与 System.\* 的其余部分相同的审查和长期可支持性。访问。 例如，<xref:Microsoft.DotNet.PlatformAbstractions> 使用 `Platform` 枚举来描述当前的操作系统平台。 如果设计了 <xref:System.Runtime.InteropServices.RuntimeInformation.IsOSPlatform(System.Runtime.InteropServices.OSPlatform)?displayProperty=nameWithType> API，则会明确拒绝此枚举设计，以支持新的平台和未来的灵活性。

现在，<xref:Microsoft.DotNet.PlatformAbstractions?displayProperty=fullName> 库启用的方案可以在没有它的情况下使用。 现有版本即使在 .NET 5 和更高版本中也将继续有效，并将与以前版本的 .NET Core 一起受到维护。 但是，新功能将不会添加到该库中， 而是将添加到其他库和 API 中。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

- 如果较旧版本的库可满足需求，则可以继续使用它们。

- 如果较旧版本无法满足需求，请使用建议的替换项替换 `PlatformAbstractions` API 的用法。

  | `PlatformAbstractions` API | 推荐的替换控件 |
  |-|-|
  | `ApplicationEnvironment.ApplicationBasePath` | <xref:System.AppContext.BaseDirectory?displayProperty=nameWithType> |
  | <xref:Microsoft.DotNet.PlatformAbstractions.HashCodeCombiner> | <xref:System.HashCode?displayProperty=nameWithType> |
  | `RuntimeEnvironment.GetRuntimeIdentifier()` | <xref:System.Runtime.InteropServices.RuntimeInformation.RuntimeIdentifier?displayProperty=nameWithType> |
  | `RuntimeEnvironment.OperatingSystemPlatform` | <xref:System.Runtime.InteropServices.RuntimeInformation.IsOSPlatform(System.Runtime.InteropServices.OSPlatform)?displayProperty=nameWithType> |
  | `RuntimeEnvironment.RuntimeArchitecture` | <xref:System.Runtime.InteropServices.RuntimeInformation.ProcessArchitecture?displayProperty=nameWithType> |
  | `RuntimeEnvironment.OperatingSystem` | <xref:System.Runtime.InteropServices.RuntimeInformation.OSDescription?displayProperty=nameWithType> |
  | `RuntimeEnvironment.OperatingSystemVersion` | <xref:System.Runtime.InteropServices.RuntimeInformation.OSDescription?displayProperty=nameWithType> 和 <xref:System.Environment.OSVersion?displayProperty=nameWithType> |

  > [!NOTE]
  > `RuntimeEnvironment.OperatingSystem` 和 `RuntimeEnvironment.OperatingSystemVersion` 的大多数用例都用于显示目的，例如，向用户、日志记录和遥测显示。 不建议根据操作系统 (OS) 版本做出运行时决策。 现在，<xref:System.Environment.OSVersion?displayProperty=nameWithType> 返回适用于 Windows 和 macOS 操作系统的[正确版本](environment-osversion-returns-correct-version.md)。 但是，对于大多数 Unix 发行版，所谓的“OS 版本”并不是那么简单。 例如，它可以是 Linux 内核版本，也可以是发行版本。 对于大多数 Unix 平台，<xref:System.Environment.OSVersion?displayProperty=nameWithType> 和 <xref:System.Runtime.InteropServices.RuntimeInformation.OSDescription?displayProperty=nameWithType> 返回 `uname` 返回的版本。 要获取 Linux 发行版的名称和版本信息，建议的方法是读取 /etc/os-release 文件。

## <a name="affected-apis"></a>受影响的 API

- `Microsoft.DotNet.PlatformAbstractions.ApplicationEnvironment.ApplicationBasePath`
- <xref:Microsoft.DotNet.PlatformAbstractions.HashCodeCombiner?displayProperty=fullName>
- `Microsoft.DotNet.PlatformAbstractions.RuntimeEnvironment.GetRuntimeIdentifier()`
- `Microsoft.DotNet.PlatformAbstractions.RuntimeEnvironment.OperatingSystem`
- `Microsoft.DotNet.PlatformAbstractions.RuntimeEnvironment.OperatingSystemPlatform`
- `Microsoft.DotNet.PlatformAbstractions.RuntimeEnvironment.OperatingSystemVersion`
- `Microsoft.DotNet.PlatformAbstractions.RuntimeEnvironment.RuntimeArchitecture`

<!--

### Category

Core .NET libraries

### Affected APIs

- `P:Microsoft.DotNet.PlatformAbstractions.ApplicationEnvironment.ApplicationBasePath`
- `T:Microsoft.DotNet.PlatformAbstractions.HashCodeCombiner`
- `M:Microsoft.DotNet.PlatformAbstractions.RuntimeEnvironment.GetRuntimeIdentifier`
- `P:Microsoft.DotNet.PlatformAbstractions.RuntimeEnvironment.OperatingSystem`
- `P:Microsoft.DotNet.PlatformAbstractions.RuntimeEnvironment.OperatingSystemPlatform`
- `P:Microsoft.DotNet.PlatformAbstractions.RuntimeEnvironment.OperatingSystemVersion`
- `P:Microsoft.DotNet.PlatformAbstractions.RuntimeEnvironment.RuntimeArchitecture`

-->

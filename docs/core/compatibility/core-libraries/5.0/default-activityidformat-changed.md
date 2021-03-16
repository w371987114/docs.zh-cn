---
title: 中断性变更：默认 ActivityIdFormat 为 W3C
description: 了解核心 .NET 库中的 .NET 5 中断性变更：默认 ActivityIdFormat 现在为 W3C。
ms.date: 11/01/2020
ms.openlocfilehash: f15c2d61443117cfbcb2be7de9561fecbff9a1d9
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257500"
---
# <a name="default-activityidformat-is-w3c"></a>默认 ActivityIdFormat 为 W3C

活动 (<xref:System.Diagnostics.Activity.DefaultIdFormat?displayProperty=nameWithType>) 的默认标识符格式现在为 <xref:System.Diagnostics.ActivityIdFormat.W3C?displayProperty=nameWithType>。

## <a name="change-description"></a>更改描述

.NET Core 3.0 中引入了 W3C 活动 ID 格式，作为分层 ID 格式的替代格式。 但是，为了保持兼容性，直到 .NET 5.0 才将 W3C 格式设置为默认格式。 .NET 5 中更改了默认格式，原因是 [W3C 格式已获得批准](https://www.w3.org/TR/trace-context/)，并广泛用于多种语言实现。

如果应用面向除 .NET 5 或更高版本之外的平台，那么它会使用旧行为，其中的默认格式为 <xref:System.Diagnostics.ActivityIdFormat.Hierarchical>。 此默认格式适用于平台 net45+、netstandard1.1+ 和 netcoreapp （1.x、2.x 和 3.x）。 在 .NET 5 及更高版本中，<xref:System.Diagnostics.Activity.DefaultIdFormat?displayProperty=nameWithType> 设置为 <xref:System.Diagnostics.ActivityIdFormat.W3C?displayProperty=nameWithType>。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

如果应用程序与用于分布式跟踪的标识符无关，则无需执行任何操作。 ASP.NET Core 和 <xref:System.Net.Http.HttpClient> 等库可以使用或传播两个版本的 <xref:System.Diagnostics.ActivityIdFormat>。

如果需要与现有系统的互操作性，或当前系统依赖于标识符的格式，则可以通过将 <xref:System.Diagnostics.Activity.DefaultIdFormat> 设置为 <xref:System.Diagnostics.ActivityIdFormat.Hierarchical?displayProperty=nameWithType> 来保留旧行为。 或者，可以通过以下三种方式之一来设置 AppContext 开关：

- 在项目文件中。

  ```xml
  <ItemGroup>
    <RuntimeHostConfigurationOption Include="System.Diagnostics.DefaultActivityIdFormatIsHierarchial" Value="true" />
  </ItemGroup>
  ```

- 在 runtimeconfig.json 文件中。

  ```json
  {
      "runtimeOptions": {
          "configProperties": {
              "System.Diagnostics.DefaultActivityIdFormatIsHierarchial": true
          }
      }
  }
  ```

- 通过环境变量。

  将 `DOTNET_SYSTEM_DIAGNOSTICS_DEFAULTACTIVITYIDFORMATISHIERARCHIAL` 设置为 `true` 或 1。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Diagnostics.Activity.DefaultIdFormat?displayProperty=fullName>

<!--

### Category

Core .NET libraries

### Affected APIs

- `P:System.Diagnostics.Activity.DefaultIdFormat`

-->

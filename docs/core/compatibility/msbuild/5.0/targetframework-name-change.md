---
title: 中断性变更：TargetFramework 从 netcoreapp 更改为 net
description: 了解 .NET 5 中的中断性变更：MSBuild TargetFramework 属性的值已从 netcoreapp3.1 更改为 net5.0。
ms.date: 10/17/2020
ms.openlocfilehash: 88bfe7602c83f61b56f11c4e0336702571369e3c
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256486"
---
# <a name="targetframework-change-from-netcoreapp-to-net"></a>TargetFramework 从 netcoreapp 更改为 net

MSBuild `TargetFramework` 属性的值已从 `netcoreapp3.1` 更改为 `net5.0`。 这可能会破坏依赖于分析 `TargetFramework` 的值的代码。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="change-description"></a>更改描述

在 .NET Core 1.0 - 3.1 中，MSBuild `TargetFramework` 属性的值以 `netcoreapp` 开头（例如，对于面向 .NET Core 3.1 的应用，为 `netcoreapp3.1`）。 从 .NET 5 开始，此值简化为仅以 `net` 开头（例如，对于 .NET 5.0，为 `net5.0`）。

有关详细信息，请参阅 [.NET Standard 的未来](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/)和 [.NET 5 中的目标框架名称](https://github.com/dotnet/designs/blob/main/accepted/2020/net5/net5.md)。

## <a name="reason-for-change"></a>更改原因

- 简化 `TargetFramework` 值。
- 使项目能够在 `TargetFramework` 属性中包含 `TargetPlatform`。

## <a name="recommended-action"></a>建议的操作

如果你有用于分析 `TargetFramework` 的值的逻辑，则需要对其进行更新。 例如，以下 MSBuild 条件依赖于 `TargetFramework` 的值。

```xml
<PropertyGroup Condition="$(TargetFramework.StartsWith('netcoreapp'))">
```

为满足此要求，可更新代码，改为比较目标框架标识符。

```xml
<PropertyGroup Condition="'$([MSBuild]::GetTargetFrameworkIdentifier('$(TargetFramework)'))' == '.NETCoreApp'">
```

## <a name="affected-apis"></a>受影响的 API

不可用

<!--

### Affected APIs

Not detectable via API analysis.

### Category

MSBuild

-->

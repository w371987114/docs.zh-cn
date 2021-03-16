---
title: 中断性变更：远程处理 API 已过时
description: 了解核心 .NET 库中的 .NET 5 中断性变更：某些与远程处理相关的 API 标记为已过时，并使用自定义诊断 ID 生成警告。
ms.date: 11/01/2020
ms.openlocfilehash: 3c4f7cd200cadd11321da60f2b4a0d191497aae8
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257163"
---
# <a name="remoting-apis-are-obsolete"></a>远程处理 API 已过时

某些与远程处理相关的 API 标记为已过时，并在编译时生成 `SYSLIB0010` 警告。 .NET 的未来版本中将删除这些 API。

## <a name="change-description"></a>更改描述

以下远程处理 API 标记为已过时。

| API | 在以下版本中标记为已过时… |
| - | - |
| <xref:System.MarshalByRefObject.GetLifetimeService?displayProperty=nameWithType> | 5.0 RC1 |
| <xref:System.MarshalByRefObject.InitializeLifetimeService?displayProperty=nameWithType> | 5.0 RC1 |

在 .NET Framework 2.x - 4.x 中，<xref:System.MarshalByRefObject.GetLifetimeService> 和 <xref:System.MarshalByRefObject.InitializeLifetimeService> 方法控制与 .NET 远程处理有关的实例的生存期。 在 .NET Core 2.x- 3.x 中，这些方法始终会在运行时引发 <xref:System.PlatformNotSupportedException>。

在 .NET 5 及更高版本中，<xref:System.MarshalByRefObject.GetLifetimeService> 和 <xref:System.MarshalByRefObject.InitializeLifetimeService> 方法以警告的形式标记为已过时，但仍在运行时引发 <xref:System.PlatformNotSupportedException>。

```csharp
// MemoryStream, like all Stream instances, subclasses MarshalByRefObject.
MemoryStream stream = new MemoryStream();
// Throws PlatformNotSupportedException; also produces warning SYSLIB0010.
obj.InitializeLifetimeService();
```

这是仅在编译时进行的更改。 以前版本的 .NET Core 没有运行时更改。

## <a name="reason-for-change"></a>更改原因

[.NET 远程处理](/previous-versions/dotnet/netframework-1.1/kwdt6w2k(v=vs.71))是一项传统技术。 它允许实例化另一个进程中的对象（甚至可能在不同的计算机上），并与该对象进行交互，就好像它是普通的进程内 .NET 对象实例一样。 .NET 远程处理基础结构仅存在于 .NET Framework 2.x - 4.x 中。 .NET Core 和 .NET 5 及更高版本不支持 .NET 远程处理，远程处理 API 要么不存在，要么始终在这些运行时引发异常。

为帮助开发人员摒弃这些 API，我们正在淘汰选定的远程处理相关 API。 .NET 的未来版本中可能完全删除这些 API。

## <a name="version-introduced"></a>引入的版本

.NET 5.0

## <a name="recommended-action"></a>建议操作

- 请考虑使用 WCF 或基于 HTTP 的 REST 服务与其他应用程序的对象或跨计算机进行通信。 有关详细信息，请参阅 [.NET Framework 技术在 .NET Core 上不可用](../../../porting/net-framework-tech-unavailable.md)。

- 如果必须继续使用已过时的 API，可在代码中取消 `SYSLIB0010` 警告。

  ```csharp
  MarshalByRefObject obj = GetMarshalByRefObj();
  #pragma warning disable SYSLIB0010 // Disable the warning.
  obj.InitializeLifetimeService(); // Still throws PNSE.
  obj.GetLifetimeService(); // Still throws PNSE.
  #pragma warning restore SYSLIB0010 // Reenable the warning.
  ```

  还可以在项目文件中取消警告，这将对项目中的所有源文件禁用警告。

  ```xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
     <TargetFramework>net5.0</TargetFramework>
     <!-- NoWarn below will suppress SYSLIB0010 project-wide -->
     <NoWarn>$(NoWarn);SYSLIB0010</NoWarn>
    </PropertyGroup>
  </Project>
  ```

  取消 `SYSLIB0010` 仅禁用远程处理 API 过时警告。 它不会禁用任何其他警告。 此外，它不会更改此硬编码运行时行为（即始终引发 <xref:System.PlatformNotSupportedException>）。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.MarshalByRefObject.GetLifetimeService?displayProperty=fullName>
- <xref:System.MarshalByRefObject.InitializeLifetimeService?displayProperty=fullName>

<!--

#### Category

Core .NET libraries

### Affected APIs

- `M:System.MarshalByRefObject.GetLifetimeService`
- `M:System.MarshalByRefObject.InitializeLifetimeService`

-->

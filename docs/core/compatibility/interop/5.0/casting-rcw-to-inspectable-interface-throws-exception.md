---
title: 中断性变更：将 RCW 强制转换为 `InterfaceIsIInspectable` 会引发异常
description: 了解 .NET 5 中的互操作中断性变更：将 RCW 强制转换为 `InterfaceIsIInspectable` 接口会引发 PlatformNotSupportedException。
ms.date: 09/13/2020
ms.openlocfilehash: 9f777ee9396f7822c9ff6bf5209021c07b8b618a
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256629"
---
# <a name="casting-rcw-to-an-interfaceisiinspectable-interface-throws-platformnotsupportedexception"></a>将 RCW 强制转换为 `InterfaceIsIInspectable` 接口会引发 PlatformNotSupportedException

将运行时可调用包装器 (RCW) 强制转换为标记为 <xref:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIInspectable> 的接口现在会引发 <xref:System.PlatformNotSupportedException>。 此更改是[从 .NET 删除 WinRT 支持](built-in-support-for-winrt-removed.md)的后续操作。

## <a name="version-introduced"></a>引入的版本

5.0 RC2

## <a name="change-description"></a>更改描述

在低于 .NET 5 预览版 6 的 .NET 版本中，将 RCW 强制转换为标记为 <xref:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIInspectable> 的接口的行为符合预期。 在 .NET 5 预览版 6-8 和 RC1 中，可将 RCW 成功转换为 <xref:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIInspectable> 接口。 但在对接口执行方法时，可能会出现访问冲突，因为[已在 .NET 5 预览版 6 中删除](built-in-support-for-winrt-removed.md)运行时中的基础支持。

在 .NET 5 RC2 及更高版本中，将 RCW 强制转换为标记为 <xref:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIInspectable> 的接口将在转换时引发 <xref:System.PlatformNotSupportedException>。

## <a name="reason-for-change"></a>更改原因

在[以前的 .NET 5 预览版](built-in-support-for-winrt-removed.md)中删除了对 <xref:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIInspectable> 的支持。 但是，意外忽视了强制转换到 <xref:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIInspectable> 接口的操作。 由于运行时中的基础支持不再存在，引发 <xref:System.PlatformNotSupportedException> 会启用正常故障路径。 引发异常还让你更容易发现已不再支持此功能。

## <a name="recommended-action"></a>建议的操作

- 如果可以在 Windows 运行时元数据 (WinMD) 文件中定义接口，请改用 C#/WinRT 工具。

- 否则，请将接口标记为 <xref:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIUnknown> 而不是 <xref:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIInspectable>，并向 <xref:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIInspectable> 方法接口的开头添加三个虚拟条目。 以下代码片段演示了一个示例。

  ```csharp
  [InterfaceType(ComInterfaceType.InterfaceIsIUnknown)]
  interface IMine
  {
      // Do not call these three methods.
      // They're exclusively to fill in the slots in the vtable.
      void GetIIdsSlot();
      void GetRuntimeClassNameSlot();
      void GetTrustLevelSlot();

      // The original members of the IMine interface go here.
      ...
  }
  ```

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIInspectable?displayProperty=fullName>

<!--

### Affected APIs

- `F:System.Runtime.InteropServices.ComInterfaceType.InterfaceIsIInspectable`

### Category

Interop

-->

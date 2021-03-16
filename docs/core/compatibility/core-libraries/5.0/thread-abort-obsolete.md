---
title: 中断性变更：Thread.Abort 已过时
description: 了解核心 .NET 库中的 .NET 5 中断性变更：Thread.Abort API 已过时。
ms.date: 11/01/2020
ms.openlocfilehash: 29c688250593801bab9b32b9e787911e561ec000
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257058"
---
# <a name="threadabort-is-obsolete"></a>Thread.Abort 已过时

<xref:System.Threading.Thread.Abort%2A?displayProperty=nameWithType> API 已过时。 如果调用这些方法，则面向 .NET 5 或更高版本的项目将遇到编译时警告 `SYSLIB0006`。

## <a name="change-description"></a>更改说明

以前，对 <xref:System.Threading.Thread.Abort%2A?displayProperty=nameWithType> 的调用不会生成编译时警告，但该方法确实会在运行时引发 <xref:System.PlatformNotSupportedException>。

从 .NET 5 开始，<xref:System.Threading.Thread.Abort%2A?displayProperty=nameWithType> 以警告的形式标记为已过时。 调用此方法将生成编译器警告 `SYSLIB0006`。 该方法的实现保持不变，并且继续引发 <xref:System.PlatformNotSupportedException>。

## <a name="reason-for-change"></a>更改原因

假定 <xref:System.Threading.Thread.Abort%2A?displayProperty=nameWithType> 在除 .NET Framework 以外的所有 .NET 实现中始终引发 <xref:System.PlatformNotSupportedException>，则向该方法添加 <xref:System.ObsoleteAttribute>，以引起人们对其调用位置的注意。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

- 使用 <xref:System.Threading.CancellationToken> 中止对工作单元的处理，而不是调用 <xref:System.Threading.Thread.Abort%2A?displayProperty=nameWithType>。 以下示例说明了 <xref:System.Threading.CancellationToken> 的用法。

  ```csharp
  void ProcessPendingWorkItemsNew(CancellationToken cancellationToken)
  {
      if (QueryIsMoreWorkPending())
      {
          // If the CancellationToken is marked as "needs to cancel",
          // this will throw the appropriate exception.
          cancellationToken.ThrowIfCancellationRequested();

          WorkItem work = DequeueWorkItem();
          ProcessWorkItem(work);
      }
  }
  ```

  有关详细信息，请参阅[托管线程中的取消](../../../../standard/threading/cancellation-in-managed-threads.md)。

- 若要禁止显示编译时警告，请禁止显示警告代码 `SYSLIB0006`。 警告代码特定于 <xref:System.Threading.Thread.Abort%2A?displayProperty=nameWithType>，并且禁止显示该代码不会禁止显示代码中的其他过时警告。 但是，建议删除对 <xref:System.Threading.Thread.Abort%2A?displayProperty=nameWithType> 的调用，而不是禁止显示该警告。

  ```csharp
  void MyMethod()
  {
  #pragma warning disable SYSLIB0006
      Thread.CurrentThread.Abort();
  #pragma warning restore SYSLIB0006
  }
  ```

  另外，还可以在项目文件中取消警告。

  ```xml
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net5.0</TargetFramework>
    <!-- Disable "Thread.Abort is obsolete" warnings for entire project. -->
    <NoWarn>$(NoWarn);SYSLIB0006</NoWarn>
  </PropertyGroup>
  ```

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Threading.Thread.Abort%2A?displayProperty=fullName>

<!--

#### Category

Core .NET libraries

### Affected APIs

- `Overload:System.Threading.Thread.Abort`

-->

---
title: 中断性变更：全局程序集缓存 API 已过时
description: 了解核心 .NET 库中的 .NET 5 中断性变更：处理 GAC 的 API 要么失败，要么不执行任何操作。
ms.date: 11/01/2020
ms.openlocfilehash: 39c7b092b06754a28723693c0147b7ec3a0b705e
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257474"
---
# <a name="global-assembly-cache-apis-are-obsolete"></a>全局程序集缓存 API 已过时

.NET Core 和 .NET 5 及更高版本消除了 .NET Framework 中存在的全局程序集缓存 (GAC) 这一概念。 因此，所有处理 GAC 的 .NETCore 和 .NET 5+ API 要么失败，要么不执行任何操作。

为帮助开发人员摒弃这些 API，一些 GAC 相关的 API 标记为已过时，并在编译时生成 `SYSLIB0005` 警告。 .NET 的未来版本中将删除这些 API。

## <a name="change-description"></a>更改描述

以下 API 标记为已过时。

| API | 在以下版本中标记为已过时… |
| - | - |
| <xref:System.Reflection.Assembly.GlobalAssemblyCache?displayProperty=nameWithType> | 5.0 RC1 |

在 .NET Framework 2.x - 4.x 中，如果从 GAC 加载了查询的程序集，则 <xref:System.Reflection.Assembly.GlobalAssemblyCache> 属性返回 `true`，如果从磁盘的不同位置加载了该程序集，则返回 `false`。 在 .NET Core 2.x - 3.x 中，<xref:System.Reflection.Assembly.GlobalAssemblyCache> 始终返回 `false`，这表明 GAC 在 .NET Core 中不存在。

```csharp
Assembly asm = typeof(object).Assembly;
// Prints 'True' on .NET Framework, 'False' on .NET Core.
Console.WriteLine(asm.GlobalAssemblyCache);
```

在 .NET 5 及更高版本中，<xref:System.Reflection.Assembly.GlobalAssemblyCache> 属性仍始终返回 `false`。 然而，属性 Getter 也标记为已过时，以指示调用方应停止访问该属性。 库和应用不得使用 <xref:System.Reflection.Assembly.GlobalAssemblyCache> API 来确定运行时行为，因为它在 .NET Core 和 .NET 5 及更高版本中始终返回 `false`。

```csharp
Assembly asm = typeof(object).Assembly;
// Prints 'False' on .NET 5.0+; also produces warning SYSLIB0005 at compile time.
Console.WriteLine(asm.GlobalAssemblyCache);
```

这是仅在编译时进行的更改。 以前版本的 .NET Core 没有运行时更改。

## <a name="reason-for-change"></a>更改原因

.NET Core 和 .NET 5 及更高版本中没有全局程序集缓存 (GAC) 这一概念。

## <a name="version-introduced"></a>引入的版本

.NET 5.0

## <a name="recommended-action"></a>建议操作

- 如果你的应用程序查询 <xref:System.Reflection.Assembly.GlobalAssemblyCache> 属性，请考虑删除该调用。 如果在运行时使用 <xref:System.Reflection.Assembly.GlobalAssemblyCache> 值在“GAC 中的程序集”流与“不在 GAC 中的程序集”流之间进行选择，请重新考虑流对于.NET Core 或 .NET 5.0+ 应用程序是否仍然有意义。

- 如果必须继续使用已过时的 API，可在代码中取消 `SYSLIB0005` 警告。

  ```csharp
  Assembly asm = typeof(object).Assembly;
  #pragma warning disable SYSLIB0005 // Disable the warning.
  // Prints 'False' on .NET 5.0+.
  Console.WriteLine(asm.GlobalAssemblyCache);
  #pragma warning restore SYSLIB0005 // Re-enable the warning.
  ```

  还可以在项目文件中取消警告，这将对项目中的所有源文件禁用警告。

  ```xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
     <TargetFramework>net5.0</TargetFramework>
     <!-- NoWarn below will suppress SYSLIB0005 project-wide -->
     <NoWarn>$(NoWarn);SYSLIB0005</NoWarn>
    </PropertyGroup>
  </Project>
  ```

  取消 `SYSLIB0005` 仅禁用 <xref:System.Reflection.Assembly.GlobalAssemblyCache> 过时警告。 它不会禁用任何其他警告。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Reflection.Assembly.GlobalAssemblyCache?displayProperty=fullName>

<!--

### Category

Core .NET libraries

### Affected APIs

- `P:System.Reflection.Assembly.GlobalAssemblyCache`

-->

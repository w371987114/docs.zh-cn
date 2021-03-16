---
title: .NET 5+ 中已过时的功能
description: 了解在 .NET 5 及更高版本中标记为已过时并生成 SYSLIB 编译器警告的 API。
ms.date: 10/20/2020
ms.openlocfilehash: d6563f21624456d74801242268ecf72652fc4f88
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256278"
---
# <a name="obsolete-features-in-net-5"></a>.NET 5 中已过时的功能

从 .NET 5 开始，一些新标记为已过时的 API 使用 <xref:System.ObsoleteAttribute> 上的两个新属性。

- <xref:System.ObsoleteAttribute.DiagnosticId?displayProperty=nameWithType> 属性指示编译器使用自定义诊断 ID 产生生成警告。 通过自定义 ID 可专门、单独地取消过时警告。 对于 .NET 5+ 过时，自定义诊断 ID 的格式为 `SYSLIBxxxx`。

- <xref:System.ObsoleteAttribute.UrlFormat?displayProperty=nameWithType> 属性指示编译器包含一个 URL 链接，可了解有关过时的详细信息。

如果由于使用过时的 API 而遇到生成警告或错误，请遵循[参考](#reference)部分中列出的诊断 ID 所提供的特定指导。 不能使用过时类型或成员的[标准诊断 ID (CS0618)](../../csharp/language-reference/compiler-messages/cs0618.md) 取消有关这些过时类型或成员的警告或错误；请改用自定义 `SYSLIBxxxx` 诊断 ID 值。 有关详细信息，请参阅[取消警告](#suppress-warnings)。

## <a name="reference"></a>参考

下表提供了 .NET 5+ 中 `SYSLIBxxxx` 过时的索引。

| 诊断 ID | 说明 |
| - | - |
| [SYSLIB0001](syslib-warnings/syslib0001.md) | UTF-7 编码不安全，因此不应使用。 请考虑改用 UTF-8。 |
| [SYSLIB0002](syslib-warnings/syslib0002.md) | <xref:System.Security.Permissions.PrincipalPermissionAttribute> 不受运行时支持，不得使用。 |
| [SYSLIB0003](syslib-warnings/syslib0003.md) | 运行时不支持或不接受代码访问安全性 (CAS)。 |
| [SYSLIB0004](syslib-warnings/syslib0004.md) | 不支持受约束的执行区域 (CER) 功能。 |
| [SYSLIB0005](syslib-warnings/syslib0005.md) | 不支持全局程序集缓存 (GAC)。 |
| [SYSLIB0006](syslib-warnings/syslib0006.md) | <xref:System.Threading.Thread.Abort?displayProperty=nameWithType> 不受支持并会引发 <xref:System.PlatformNotSupportedException>。 |
| [SYSLIB0007](syslib-warnings/syslib0007.md) | 不支持此加密算法的默认实现。 |
| [SYSLIB0008](syslib-warnings/syslib0008.md) | <xref:System.Runtime.CompilerServices.DebugInfoGenerator.CreatePdbGenerator> API 不受支持并会引发 <xref:System.PlatformNotSupportedException>。 |
| [SYSLIB0009](syslib-warnings/syslib0009.md) | <xref:System.Net.AuthenticationManager.Authenticate%2A?displayProperty=nameWithType> 和 <xref:System.Net.AuthenticationManager.PreAuthenticate%2A?displayProperty=nameWithType> 方法都不受支持并会引发 <xref:System.PlatformNotSupportedException>。 |
| [SYSLIB0010](syslib-warnings/syslib0010.md) | 某些远程处理 API 不受支持并会引发 <xref:System.PlatformNotSupportedException>。 |
| [SYSLIB0011](syslib-warnings/syslib0011.md) | <xref:System.Runtime.Serialization.Formatters.Binary.BinaryFormatter> 序列化已过时，不应使用。 |
| [SYSLIB0012](syslib-warnings/syslib0012.md) | 包含 <xref:System.Reflection.Assembly.CodeBase?displayProperty=nameWithType> 和 <xref:System.Reflection.Assembly.EscapedCodeBase?displayProperty=nameWithType> 只是为了实现 .NET Framework 兼容性。 请改用 <xref:System.Reflection.Assembly.Location?displayProperty=nameWithType>。 |

## <a name="suppress-warnings"></a>禁止显示警告

如果必须使用过时 API，并且 `SYSLIBxxxx` 诊断没有显示为错误，则可以在代码或项目文件中取消该警告。

在代码中：

```csharp
// Disable the warning.
#pragma warning disable SYSLIB0001
// Code that uses obsolete API.
...
// Re-enable the warning.
#pragma warning restore SYSLIB0001
```

项目文件：

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
   <TargetFramework>net5.0</TargetFramework>
   <!-- NoWarn below suppresses SYSLIB0001 project-wide -->
   <NoWarn>$(NoWarn);SYSLIB0001</NoWarn>
   <!-- To suppress multiple warnings, you can use multiple NoWarn elements -->
   <NoWarn>$(NoWarn);SYSLIB0002</NoWarn>
   <NoWarn>$(NoWarn);SYSLIB0003</NoWarn>
   <!-- Alternatively, you can suppress multiple warnings by using a semicolon-delimited list -->
   <NoWarn>$(NoWarn);SYSLIB0001;SYSLIB0002;SYSLIB0003</NoWarn>
  </PropertyGroup>
</Project>
```

> [!NOTE]
> 以这种方式取消警告只会禁用指定的过时警告。 它不会禁用任何其他警告，包括具有不同诊断 ID 的其他过时警告。

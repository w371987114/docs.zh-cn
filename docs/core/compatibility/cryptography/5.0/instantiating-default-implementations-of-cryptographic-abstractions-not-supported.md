---
title: 中断性变更：不支持对加密抽象的默认实现进行实例化
description: 了解 .NET 5 中的中断性变更：对加密抽象的无参数 Create() 重载已过时。
ms.date: 10/16/2020
ms.openlocfilehash: b75f3568317d1db8ae1bb629f760aaec7e69776a
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256785"
---
# <a name="instantiating-default-implementations-of-cryptographic-abstractions-is-not-supported"></a>不支持对加密抽象的默认实现进行实例化

从 .NET 5.0 开始，对加密抽象的无参数 `Create()` 重载已过时，不再作为警告显示。

## <a name="change-description"></a>更改描述

在 .NET Framework 2.0 - 4.8 中，可以将抽象加密基元工厂（如 <xref:System.Security.Cryptography.HashAlgorithm.Create?displayProperty=nameWithType>）配置为返回不同的算法。 例如，在默认安装 .NET Framework 4.8 时，无参数的静态方法 <xref:System.Security.Cryptography.HashAlgorithm.Create?displayProperty=nameWithType> 返回 SHA1 算法的实例，如以下代码片段所示。

**仅限 .NET Framework**

```csharp
// Return an instance of the default hash algorithm (SHA1).
HashAlgorithm alg = HashAlgorithm.Create();
// Prints 'System.Security.Cryptography.SHA1CryptoServiceProvider'.
Console.WriteLine(alg.GetType());

// Change the default algorithm to be SHA256, not SHA1.
CryptoConfig.AddAlgorithm(typeof(SHA256CryptoServiceProvider), typeof(HashAlgorithm).FullName);
alg = HashAlgorithm.Create();
// Prints 'System.Security.Cryptography.SHA256CryptoServiceProvider'.
Console.WriteLine(alg.GetType());
```

此外，你还可以使用[计算机范围的配置](../../../../framework/configure-apps/map-algorithm-names-to-cryptography-classes.md)来更改默认算法，而无需以编程方式调用 `CryptoConfig`。

在 .NET Core 2.0 - 3.1 中，抽象加密基元工厂（例如 <xref:System.Security.Cryptography.HashAlgorithm.Create?displayProperty=nameWithType>）始终引发 <xref:System.PlatformNotSupportedException>。

```csharp
// Throws PlatformNotSupportedException on .NET Core.
HashAlgorithm alg = HashAlgorithm.Create();
```

在 .NET 5 及更高版本中，抽象加密基元工厂（例如 <xref:System.Security.Cryptography.HashAlgorithm.Create?displayProperty=nameWithType>）被标记为已过时，并生成 ID 为 `SYSLIB0007` 的编译时警告。 在运行时，这些方法会继续引发 <xref:System.PlatformNotSupportedException>。

```csharp
// Throws PlatformNotSupportedException.
// Also produces compile-time warning SYSLIB0007 on .NET 5+.
HashAlgorithm alg = HashAlgorithm.Create();
```

这是仅在编译时进行的更改。 以前版本的 .NET Core 没有运行时更改。

> [!NOTE]
>
> - 仅 `Create()` 方法的无参数重载已过时。 参数化重载未过时，并且仍可按预期方式工作。
>
>   ```csharp
>   // Call Create(string), providing an explicit algorithm family name.
>   // Works in .NET Framework, .NET Core, and .NET 5.0+.
>   HashAlgorithm hashAlg = HashAlgorithm.Create("SHA256");
>   ```
>
> - 特定算法系列（而非抽象）的无参数重载未过时，并将继续按预期方式工作。
>
>   ```csharp
>   // Call a specific algorithm family's parameterless Create() ctor.
>   // Works in .NET Framework, .NET Core, and .NET 5.0+.
>   Aes aesAlg = Aes.Create();
>   ```

## <a name="reason-for-change"></a>更改原因

.NET Framework 中的加密配置系统不再存在于 .NET Core 和 .NET 5.0+ 中，因为该旧系统不允许适当的加密灵活性。 .NET 的后向兼容性要求也禁止框架更新某些加密 API 以跟上加密技术的发展。 例如，当 SHA-1 哈希算法是顶尖的算法时，.NET Framework 1.0 中引入了 <xref:System.Security.Cryptography.HashAlgorithm.Create?displayProperty=nameWithType> 方法。 20 年过去了，现在人们认为 SHA-1 已无效，但我们无法更改 <xref:System.Security.Cryptography.HashAlgorithm.Create?displayProperty=nameWithType> 来返回不同的算法。 这样做会在使用的应用程序中引入不可接受的中断性变更。

最佳做法表明，使用加密基元（例如 AES、SHA-* 和 RSA）的库应完全控制它们使用这些基元的方式。 需要面向未来的应用程序应利用较高级别的库，这些库包装这些基元并添加密钥管理和加密灵活性功能。 这些库通常由托管环境提供。 例如，[ASP.NET 的数据保护库](/aspnet/core/security/data-protection/)，该库代表调用应用程序来处理这些问题。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

- 建议采取的操作是用对特定算法（例如 <xref:System.Security.Cryptography.Aes.Create?displayProperty=nameWithType>）的工厂方法的调用替换对现已过时的 API 的调用。 这样，便可以完全控制要实例化哪些算法。

- 如果需要保持与使用现已过时的 API 的 .NET Framework 应用生成的现有有效负载的兼容性，请使用下表中建议的替换项。 该表提供了从 .NET Framework 默认算法到其 .NET 5+ 等效项的映射。

  | .NET framework | .NET Core/.NET 5.0+ 兼容替换项 | 备注 |
  | - | - | - |
  | <xref:System.Security.Cryptography.AsymmetricAlgorithm.Create?displayProperty=nameWithType> | <xref:System.Security.Cryptography.RSA.Create?displayProperty=nameWithType> | |
  | <xref:System.Security.Cryptography.HashAlgorithm.Create?displayProperty=nameWithType> | <xref:System.Security.Cryptography.SHA1.Create?displayProperty=nameWithType> | SHA-1 算法被认为已无效。 如果可能，请考虑使用更强大的算法。 请咨询安全顾问以获取进一步的指导。 |
  | <xref:System.Security.Cryptography.HMAC.Create?displayProperty=nameWithType> | <xref:System.Security.Cryptography.HMACSHA1.%23ctor> | 对于大多数新式应用程序，不建议使用 HMACSHA1 算法。 如果可能，请考虑使用更强大的算法。 请咨询安全顾问以获取进一步的指导。 |
  | <xref:System.Security.Cryptography.KeyedHashAlgorithm.Create?displayProperty=nameWithType> | <xref:System.Security.Cryptography.HMACSHA1.%23ctor> | 对于大多数新式应用程序，不建议使用 HMACSHA1 算法。 如果可能，请考虑使用更强大的算法。 请咨询安全顾问以获取进一步的指导。 |
  | <xref:System.Security.Cryptography.SymmetricAlgorithm.Create?displayProperty=nameWithType> | <xref:System.Security.Cryptography.Aes.Create?displayProperty=nameWithType> |

- 如果必须继续调用已过时的无参数 `Create()` 重载，则可以在代码中取消 `SYSLIB0007` 警告。

  ```csharp
  #pragma warning disable SYSLIB0007 // Disable the warning.
  HashAlgorithm alg = HashAlgorithm.Create(); // Still throws PNSE.
  #pragma warning restore SYSLIB0007 // Re-enable the warning.
  ```

  另外，还可以在项目文件中取消该警告。 这样做会对项目中所有源文件禁用该警告。

  ```xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
     <TargetFramework>net5.0</TargetFramework>
     <!-- NoWarn below suppresses SYSLIB0007 project-wide -->
     <NoWarn>$(NoWarn);SYSLIB0007</NoWarn>
    </PropertyGroup>
  </Project>
  ```

  > [!NOTE]
  > 取消 `SYSLIB0007` 仅禁用此处列出的加密 API 的过时警告。 它不会禁用任何其他警告。 此外，即使取消该警告，这些已过时的 API 仍会在运行时引发 <xref:System.PlatformNotSupportedException>。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Security.Cryptography.AsymmetricAlgorithm.Create?displayProperty=fullName>
- <xref:System.Security.Cryptography.HashAlgorithm.Create?displayProperty=fullName>
- <xref:System.Security.Cryptography.HMAC.Create?displayProperty=fullName>
- <xref:System.Security.Cryptography.KeyedHashAlgorithm.Create?displayProperty=fullName>
- <xref:System.Security.Cryptography.SymmetricAlgorithm.Create?displayProperty=fullName>

<!--

### Affected APIs

- `M:System.Security.Cryptography.AsymmetricAlgorithm.Create`
- `M:System.Security.Cryptography.HashAlgorithm.Create`
- `M:System.Security.Cryptography.HMAC.Create`
- `M:System.Security.Cryptography.KeyedHashAlgorithm.Create`
- `M:System.Security.Cryptography.SymmetricAlgorithm.Create`

### Category

- Cryptography

-->

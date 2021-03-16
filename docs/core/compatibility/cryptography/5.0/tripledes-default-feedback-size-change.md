---
title: 中断性变更：TripleDES.Create 所创建实例的默认 FeedbackSize 值已更改
description: 了解 .NET 5 中的中断性变更：从 TripleDES.Create() 返回的 TripleDES 实例的 FeedbackSize 属性默认值已从 64 更改为 8。
ms.date: 10/16/2020
ms.openlocfilehash: 9d3259da30cce84e83a3f13c610dad5884b445b8
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256759"
---
# <a name="default-feedbacksize-value-for-instances-created-by-tripledescreate-changed"></a>TripleDES.Create 所创建实例的默认 FeedbackSize 值已更改

从 <xref:System.Security.Cryptography.TripleDES.Create?displayProperty=nameWithType> 返回的 <xref:System.Security.Cryptography.TripleDES> 实例的 <xref:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize?displayProperty=nameWithType> 属性默认值从 64 改为 8，以便更轻松地从 .NET Framework 迁移。 仅当 <xref:System.Security.Cryptography.SymmetricAlgorithm.Mode> 属性为 <xref:System.Security.Cryptography.CipherMode.CFB?displayProperty=nameWithType> 时才使用此属性（除非直接在调用方代码中使用）。

向 .NET 5.0 RC1 版本中首次添加了对 <xref:System.Security.Cryptography.CipherMode.CFB> 模式的支持，因此只有 .NET 5 RC1 和 .NET 5 RC2 应用程序会受到此更改的影响。

## <a name="change-description"></a>更改描述

在 .NET Core 和早期的 .NET 5 预发行版中，`TripleDES.Create().FeedbackSize` 的默认值为 64。 从 .NET 5 的 RTM 版本开始，`TripleDES.Create().FeedbackSize` 的默认值为 8。

## <a name="reason-for-change"></a>更改原因

在 .NET Framework 中，<xref:System.Security.Cryptography.TripleDES> 基类将 <xref:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize> 的值默认为 64，但 <xref:System.Security.Cryptography.TripleDESCryptoServiceProvider> 类将默认值覆盖为 8。 将 <xref:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize> 属性引入到版本 2.0 的 .NET Core 时，将保留此相同行为。 但是，在 .NET Framework 中，<xref:System.Security.Cryptography.TripleDES.Create?displayProperty=nameWithType> 返回 <xref:System.Security.Cryptography.TripleDESCryptoServiceProvider> 的实例，因此算法中心中的默认值为 8。 对于 .NET Core 和 .NET 5 +，算法中心返回非公共实现，在此之前，其默认值为 64。

如果将 <xref:System.Security.Cryptography.TripleDES> 实现类的 <xref:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize> 值更改为 8，允许为将密码模式指定为 <xref:System.Security.Cryptography.CipherMode.CFB> 但未显式分配 <xref:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize> 属性的 .NET Framework 编写的应用程序继续在 .NET 5 上运行。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

当满足以下条件时，对 RC1 或 RC2 版本的 .NET 5 中的数据进行加密或解密的应用程序使用 CFB64 执行此操作：

- 具有 <xref:System.Security.Cryptography.TripleDES.Create?displayProperty=nameWithType> 的 <xref:System.Security.Cryptography.TripleDES> 实例。
- 使用 <xref:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize> 的默认值。
- <xref:System.Security.Cryptography.SymmetricAlgorithm.Mode> 属性设置为 <xref:System.Security.Cryptography.CipherMode.CFB?displayProperty=nameWithType>。

若要保持此行为，请将 <xref:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize> 属性分配为 `64`。

并非所有 `TripleDES` 实现对 <xref:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize> 都使用相同的默认值。 如果对 <xref:System.Security.Cryptography.TripleDES> 实例使用 <xref:System.Security.Cryptography.CipherMode.CFB> 密码模式，则建议你始终显式分配 <xref:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize> 属性值。

```csharp
TripleDES cipher = TripleDES.Create();
cipher.Mode = CipherMode.CFB;
// Explicitly set the FeedbackSize for CFB to control between CFB8 and CFB64.
cipher.FeedbackSize = 8;
```

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Security.Cryptography.TripleDES.Create?displayProperty=fullName>
- <xref:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize?displayProperty=fullName>

<!--

### Affected APIs

- `M:System.Security.Cryptography.TripleDES.Create`
- `P:System.Security.Cryptography.SymmetricAlgorithm.FeedbackSize`

### Category

- Cryptography

-->

---
title: 中断性变更：NegotiateStream 和 SslStream 允许连续的“开始”操作
description: 了解 .NET 5 中的中断性变更：关于安全流的错误案例会得到不同的处理，对 BeginAuthenticateAsServer 或 BeginAuthenticateAsClient 的后续调用可能不会再失败。
ms.date: 10/18/2020
ms.openlocfilehash: 5c042be01873849cc154111a31fc007521508c7b
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256434"
---
# <a name="negotiatestream-and-sslstream-allow-successive-begin-operations"></a>NegotiateStream 和 SslStream 允许连续的“开始”操作

关于安全流的错误案例会得到不同的处理，对 `BeginAuthenticateAsServer` 或 `BeginAuthenticateAsClient` 的后续调用可能不会再失败。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="change-description"></a>更改描述

在以前的 .NET 版本中，若在未先调用 `EndAuthenticateAsServer` 或 `EndAuthenticateAsClient` 的情况下就连续调用 `BeginAuthenticateAsServer` 或 `BeginAuthenticateAsClient`，这会导致 <xref:System.NotSupportedException>。 从 .NET 5 开始，对 `BeginAuthenticateAsServer` 或 `BeginAuthenticateAsClient` 的连续调用不会再导致 <xref:System.NotSupportedException>，因为这些 API 由基于 <xref:System.Threading.Tasks.Task> 的实现支持。

## <a name="reason-for-change"></a>更改原因

将内部实现从基于异步编程模型 (APM) 切换到基于 <xref:System.Threading.Tasks.Task> 会提高性能并降低代码复杂性。

## <a name="recommended-action"></a>建议操作

开发人员一方不需要执行任何操作。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Net.Security.SslStream.BeginAuthenticateAsServer%2A?displayProperty=fullName>
- <xref:System.Net.Security.SslStream.BeginAuthenticateAsClient%2A?displayProperty=fullName>
- <xref:System.Net.Security.NegotiateStream.BeginAuthenticateAsServer%2A?displayProperty=fullName>
- <xref:System.Net.Security.NegotiateStream.BeginAuthenticateAsClient%2A?displayProperty=fullName>

<!--

### Affected APIs

- `Overload:M:System.Net.Security.SslStream.BeginAuthenticateAsServer`
- `Overload:M:System.Net.Security.SslStream.BeginAuthenticateAsClient`
- `Overload:M:System.Net.Security.NegotiateStream.BeginAuthenticateAsServer`
- `Overload:M:System.Net.Security.NegotiateStream.BeginAuthenticateAsClient`

### Category

Networking

-->

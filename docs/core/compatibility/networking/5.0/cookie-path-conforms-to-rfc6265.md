---
title: 中断性变更：Cookie 路径处理现在符合 RFC 6265
description: 了解 .NET 5 中的中断性变更：RFC 6265 中定义的路径处理算法之前针对 Cookie 和 CookieContainer 类而实现。
ms.date: 08/18/2020
ms.openlocfilehash: 5ee1bccc79a5ac271904dd3223b58cc168f18cfa
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256460"
---
# <a name="cookie-path-handling-now-conforms-to-rfc-6265"></a>Cookie 路径处理现在符合 RFC 6265

[RFC 6265](https://tools.ietf.org/html/rfc6265) 中定义的路径处理算法是针对 <xref:System.Net.Cookie> 和 <xref:System.Net.CookieContainer> 类实现的。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="change-description"></a>更改描述

- 无需再将 <xref:System.Net.Cookie.Path> 属性作为请求路径的前缀。
- <xref:System.Net.Cookie.Path> 的默认值的计算和路径匹配算法是按照 RFC 6265 的[第 5.1.4 节](https://tools.ietf.org/html/rfc6265#section-5.1.4)中的定义实现的。

## <a name="recommended-action"></a>建议操作

在大多数情况下，无需执行任何操作。 但是，如果代码依赖于 <xref:System.Net.Cookie.Path> 验证，则需要在代码中实现所需的验证。 如果代码依赖于 <xref:System.Net.Cookie.Path> 的默认值计算，请考虑手动提供 <xref:System.Net.Cookie.Path> 值，而不使用默认值。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Net.Cookie?displayProperty=fullName>
- <xref:System.Net.CookieContainer?displayProperty=fullName>

<!--

### Affected APIs

- `T:System.Net.Cookie`
- `T:System.Net.CookieContainer`

### Category

Networking

-->

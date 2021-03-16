---
title: 中断性变更：Cryptography.Oid 在功能上仅用于初始化
description: 了解 .NET 5 中的中断性变更：当尝试更改某个值时，Cryptography.Oid 类的属性资源库现在会引发异常。
ms.date: 08/16/2020
ms.openlocfilehash: aa1e72adcda61f2292574729e36cdc578bf907d2
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256863"
---
# <a name="systemsecuritycryptographyoid-is-functionally-init-only"></a>System.Security.Cryptography.Oid 在功能上仅用于初始化

用于表示 ASN.1 对象标识符值及其易记名称的 <xref:System.Security.Cryptography.Oid?displayProperty=fullName> 类以前是完全可变的。 此可变性经常被忽视或意外出现。 现在，当你在分配值后尝试更改该值时，属性资源库会引发 <xref:System.PlatformNotSupportedException>。

## <a name="change-description"></a>更改描述

在以前的版本中，<xref:System.Security.Cryptography.Oid> 上的属性资源库可用于更改 <xref:System.Security.Cryptography.Oid.FriendlyName> 和 <xref:System.Security.Cryptography.Oid.Value> 属性的值。

在 .NET 5 及更高版本中，属性资源库仅可用于初始化值。 属性具有值后，无论是从构造函数还是以前版本调用属性资源库，属性资源库始终引发 <xref:System.PlatformNotSupportedException>。

## <a name="reason-for-change"></a>更改原因

通过此更改，可将 <xref:System.Security.Cryptography.Oid> 对象作为公共 API 中的返回值的一部分重用，以减少对象分配配置文件。 当 <xref:System.Security.Cryptography.Oid> 值用作输入时，则无需创建临时的“防御”副本。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

除了用于对象初始化之外，请避免使用 <xref:System.Security.Cryptography.Oid> 属性资源库。 若要表示新值，请使用新的实例，而不是更改现有对象上的值。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Security.Cryptography.Oid.FriendlyName?displayProperty=fullName>
- <xref:System.Security.Cryptography.Oid.Value?displayProperty=fullName>

<!--

### Affected APIs

- `P:System.Security.Cryptography.Oid.FriendlyName`
- `P:System.Security.Cryptography.Oid.Value`

### Category

Cryptography

-->

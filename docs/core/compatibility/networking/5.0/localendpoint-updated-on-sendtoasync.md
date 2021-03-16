---
title: 中断性变更：调用 SendToAsync 后更新 Socket.LocalEndPoint
description: 了解 .NET 5 中的中断性变更：SendToAsync 现在将本地终结点属性的值更新为套接字的本地地址。
ms.date: 10/18/2020
ms.openlocfilehash: 4be62f8bf6596cc3531d59f3e65eda005bff778a
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256421"
---
# <a name="socketlocalendpoint-is-updated-after-calling-sendtoasync"></a>调用 SendToAsync 后更新 Socket.LocalEndPoint

<xref:System.Net.Sockets.Socket.SendToAsync(System.Net.Sockets.SocketAsyncEventArgs)?displayProperty=nameWithType> 现将 <xref:System.Net.Sockets.Socket.LocalEndPoint?displayProperty=nameWithType> 属性的值更新为套接字的本地地址。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="change-description"></a>更改描述

在以前的 .NET 版本中，<xref:System.Net.Sockets.Socket.SendToAsync(System.Net.Sockets.SocketAsyncEventArgs)?displayProperty=nameWithType> 不会更改套接字实例上 <xref:System.Net.Sockets.Socket.LocalEndPoint?displayProperty=nameWithType> 属性的值。 从 .NET 5 开始，<xref:System.Net.Sockets.Socket.SendToAsync(System.Net.Sockets.SocketAsyncEventArgs)> 成功完成后，<xref:System.Net.Sockets.Socket.LocalEndPoint?displayProperty=nameWithType> 的值为隐式绑定的套接字的本地地址。 这一新行为与 <xref:System.Net.Sockets.Socket.SendTo(System.Byte[],System.Net.EndPoint)> 和 <xref:System.Net.Sockets.Socket.BeginSendTo(System.Byte[],System.Int32,System.Int32,System.Net.Sockets.SocketFlags,System.Net.EndPoint,System.AsyncCallback,System.Object)>/<xref:System.Net.Sockets.Socket.EndSendTo(System.IAsyncResult)> 的行为一致。

## <a name="reason-for-change"></a>更改原因

此更改会[修复一个 bug](https://github.com/dotnet/runtime/issues/915)，并使该行为在 `SendTo` 变体中保持一致。

## <a name="recommended-action"></a>建议的操作

更改所有假定 <xref:System.Net.Sockets.Socket.SendToAsync(System.Net.Sockets.SocketAsyncEventArgs)> 不会更改 <xref:System.Net.Sockets.Socket.LocalEndPoint?displayProperty=nameWithType> 的值的代码。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Net.Sockets.Socket.SendToAsync(System.Net.Sockets.SocketAsyncEventArgs)?displayProperty=fullName>

<!--

### Affected APIs

- `M:System.Net.Sockets.Socket.SendToAsync(System.Net.Sockets.SocketAsyncEventArgs)`

### Category

Networking

-->

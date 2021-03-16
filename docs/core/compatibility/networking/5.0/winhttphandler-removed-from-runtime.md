---
title: 中断性变更：已从 .NET 运行时中删除 WinHttpHandler
description: 了解 .NET 5 中的中断性变更：WinHttpHandler 已从 .NET 运行时中删除。
ms.date: 10/18/2020
ms.openlocfilehash: 95abcfb4d7670be035bd640dbb85458406c1b0e0
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256408"
---
# <a name="winhttphandler-removed-from-net-runtime"></a>已从 .NET 运行时中删除 WinHttpHandler

`WinHttpHandler` 类已从 System.Net.Http.dll 程序集删除。 它现在仅作为带外 (OOB) [NuGet 包](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/)提供。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="change-description"></a>更改描述

在以前的 .NET 版本中，<xref:System.Net.Http.WinHttpHandler> 类作为核心 .NET 库的一部分提供。 从 .NET 5 开始，<xref:System.Net.Http.WinHttpHandler> 类仅作为单独安装的 [NuGet 包](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/)提供。

## <a name="recommended-action"></a>建议操作

安装 [System.Net.Http.WinHttpHandler NuGet 包](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/)。 或者，如果不需要特定于 WinHTTP 的功能，请改用 <xref:System.Net.Http.SocketsHttpHandler>。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Net.Http.WinHttpHandler?displayProperty=fullName>

<!--

### Affected APIs

- `T:System.Net.Http.WinHttpHandler`

### Category

Networking

-->

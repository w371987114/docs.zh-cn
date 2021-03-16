---
title: 中断性变更：增加了 NotifyIcon.Text 最大文本长度
description: 了解 .NET 6 中的中断性变更：NotifyIcon.Text 属性的最大文本长度有所增加。
ms.date: 01/19/2021
ms.openlocfilehash: f87b98dd852ce202689ae9360bee9b6cfbf01794
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102255771"
---
# <a name="notifyicontext-maximum-text-length-increased"></a>增加了 NotifyIcon.Text 最大文本长度

<xref:System.Windows.Forms.NotifyIcon.Text?displayProperty=nameWithType> 属性允许的最大文本长度从 63 增加到了 127。

## <a name="change-description"></a>更改描述

在以前的 .NET 版本中，<xref:System.Windows.Forms.NotifyIcon.Text?displayProperty=nameWithType> 属性允许的最大文本长度为 63 个字符。 从 .NET 6 开始，允许的最大文本长度为 127 个字符。 在任何版本中，如果试图设置超过该限制的值，则都会引发 <xref:System.ArgumentException>。

## <a name="reason-for-change"></a>更改原因

允许的最大文本长度已增加到与[基础 Windows API](/windows/win32/api/shellapi/ns-shellapi-notifyicondataw#nif_showtip-0x00000080) 一致。 Windows API 在 Windows 2000 中已更新，但是由于兼容性原因，.NET Framework 中未更新此属性的大小限制。

## <a name="version-introduced"></a>引入的版本

.NET 6 预览版 1

## <a name="recommended-action"></a>建议的操作

查看代码，并在必要时放宽任何现有的保护条件。

## <a name="affected-apis"></a>受影响的 API

<xref:System.Windows.Forms.NotifyIcon.Text?displayProperty=fullName>

<!--

### Affected APIs

- `P:System.Windows.Forms.NotifyIcon.Text`

### Category

Windows Forms

-->

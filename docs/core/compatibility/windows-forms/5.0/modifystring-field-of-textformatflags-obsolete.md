---
title: 中断性变更：TextFormatFlags.ModifyString 已过时
description: 了解 .NET 5 中的中断性变更：TextFormatFlags.ModifyString 字段已过时，显示警告。
ms.date: 11/05/2020
ms.openlocfilehash: 9fe1e04b1ad36070b08af2affdca1e058ec74bb8
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256161"
---
# <a name="textformatflagsmodifystring-is-obsolete"></a>TextFormatFlags.ModifyString 已过时

<xref:System.Windows.Forms.TextFormatFlags.ModifyString?displayProperty=nameWithType> 字段已过时（警告），可能会在将来的 .NET 版本中删除。

## <a name="change-description"></a>更改描述

在以前的 .NET 版本中，<xref:System.Windows.Forms.TextFormatFlags.ModifyString?displayProperty=nameWithType> 枚举字段未被标记为已过时。 在 .NET 5 及更高版本中，它被标记为已过时（警告）。 此字段可能会在将来的 .NET 版本中删除。

## <a name="reason-for-change"></a>更改原因

在某些情况下，将字符串传递到 <xref:System.Windows.Forms.TextRenderer.MeasureText%2A?displayProperty=nameWithType> 和 <xref:System.Windows.Forms.TextFormatFlags.ModifyString?displayProperty=nameWithType> 会更改字符串。 此行为会中断字符串不可变性承诺，并可能导致致命的 .NET 运行时状态损坏。

## <a name="version-introduced"></a>引入的版本

.NET 5.0

## <a name="recommended-action"></a>建议操作

更新依赖于 <xref:System.Windows.Forms.TextFormatFlags.ModifyString?displayProperty=nameWithType> 的任何代码。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Windows.Forms.TextFormatFlags.ModifyString?displayProperty=fullName>

<!--

### Affected APIs

- `F:System.Windows.Forms.TextFormatFlags.ModifyString`

### Category

Windows Forms

-->

---
title: 中断性变更：某些 TableLayoutSettings 属性会引发 InvalidEnumArgumentException
description: 了解 .NET 6 中的中断性变更：某些 TableLayoutSettings API 现在将针对无效参数引发 InvalidEnumArgumentException。
ms.date: 01/18/2021
ms.openlocfilehash: 2da097122b935ec3a60c2bb009cc8ebbcff6468e
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102255719"
---
# <a name="selected-tablelayoutsettings-properties-throw-invalidenumargumentexception"></a>所选的 TableLayoutSettings 属性会引发 InvalidEnumArgumentException

如果尝试分配不正确的值，则所选的 <xref:System.Windows.Forms.TableLayoutSettings> 属性现在会引发 <xref:System.ComponentModel.InvalidEnumArgumentException>。

## <a name="change-description"></a>更改描述

在以前的 .NET 版本中，如果尝试分配不正确的值，这些属性会引发 <xref:System.ArgumentOutOfRangeException>。 从 .NET 6 开始，这些属性在此情况下会引发 <xref:System.ComponentModel.InvalidEnumArgumentException>。

## <a name="reason-for-change"></a>更改原因

在类似情况下，与现有的 Windows Forms API 一致，会引发 <xref:System.ComponentModel.InvalidEnumArgumentException>。 引发此异常还会为开发人员提供更好的调试体验。

## <a name="version-introduced"></a>引入的版本

.NET 6.0

## <a name="recommended-action"></a>建议的操作

- 更新代码以防止分配不正确的值。
- 如有必要，请在访问这些 API 时处理 <xref:System.ComponentModel.InvalidEnumArgumentException>。

## <a name="affected-apis"></a>受影响的 API

下表列出了受影响的属性：

| properties | 版本已更改 |
|-|-|-|-|
| <xref:System.Windows.Forms.TableLayoutPanel.CellBorderStyle?displayProperty=fullName> | 预览版 1 |
| <xref:System.Windows.Forms.TableLayoutPanel.GrowStyle?displayProperty=fullName> | 预览版 1 |

<!--

### Affected APIs

- `P:System.Windows.Forms.TableLayoutPanel.CellBorderStyle`
- `P:System.Windows.Forms.TableLayoutPanel.GrowStyle`

### Category

Windows Forms

-->

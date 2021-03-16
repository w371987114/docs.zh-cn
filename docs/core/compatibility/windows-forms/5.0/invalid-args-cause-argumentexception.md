---
title: 中断性变更：WinForms 方法现在会引发 ArgumentException
description: 了解 .NET 5 中的中断性变更：某些 Windows 窗体方法现在将针对无效参数引发 ArgumentException。
ms.date: 07/18/2020
ms.openlocfilehash: 9823e9162a562081cdd64346a502ca136b51fa75
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256131"
---
# <a name="winforms-methods-now-throw-argumentexception"></a>WinForms 方法现在会引发 ArgumentException

某些 Windows 窗体方法现在将针对无效参数引发 <xref:System.ArgumentException>，之前不会这样。

## <a name="change-description"></a>更改描述

以前，如果将异常类型或错误类型的参数传递给某些 Windows 窗体方法，会导致不确定的状态。 从 .NET 5 开始，在传递无效参数后，这些方法现会引发 <xref:System.ArgumentException>。

引发 <xref:System.ArgumentException> 符合 .NET 运行时的行为。 它还通过清楚地指示具体的无效参数来改进调试体验。

## <a name="version-introduced"></a>引入的版本

.NET 5.0

## <a name="recommended-action"></a>建议操作

- 更新代码以防止传递无效参数。
- 如有必要，请在调用方法时处理 <xref:System.ArgumentException>。

## <a name="affected-apis"></a>受影响的 API

下表列出了受影响的方法和参数：

| 方法 | 参数名称 | 条件 | 新增的版本 |
|-|-|-|-|
| <xref:System.Windows.Forms.TabControl.GetToolTipText(System.Object)?displayProperty=fullName> | `item` | 参数不属于 <xref:System.Windows.Forms.TabPage> 类型。 | 预览版 1 |
| <xref:System.Windows.Forms.DataFormats.GetFormat(System.String)?displayProperty=fullName> | `format` | 参数为 `null`、<xref:System.String.Empty?displayProperty=nameWithType> 或空格。 | 预览版 5 |
| <xref:System.Windows.Forms.InputLanguageChangedEventArgs.%23ctor(System.Globalization.CultureInfo,System.Byte)> | `culture` | 无法检索指定区域性的 `InputLanguage`。 | 预览版 7 |

<!--

### Affected APIs

- `M:System.Windows.Forms.TabControl.GetToolTipText(System.Object)`
- `M:System.Windows.Forms.DataFormats.GetFormat(System.String)`
- `M:System.Windows.Forms.InputLanguageChangedEventArgs.%23ctor(System.Globalization.CultureInfo,System.Byte)`

### Category

Windows Forms

-->

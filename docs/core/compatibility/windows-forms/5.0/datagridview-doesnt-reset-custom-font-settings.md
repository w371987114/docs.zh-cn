---
title: 中断性变更：DataGridView 不再重置自定义单元格样式的字体
description: 了解 .NET 5 中的中断性变更：如果已自定义单元格样式字体，则 DataGridView 不再重置默认单元格样式字体，以使其与环境字体一致。
ms.date: 10/18/2020
ms.openlocfilehash: fd28fb5a0b508157289dde1b720522ed49163e31
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256122"
---
# <a name="datagridview-no-longer-resets-fonts-for-customized-cell-styles"></a>DataGridView 不再重置自定义单元格样式的字体

当环境字体更改时，如果已自定义单元格样式字体，则 <xref:System.Windows.Forms.DataGridViewElement.DataGridView> 不再重置默认单元格样式字体，以让其与环境字体一致。

## <a name="change-description"></a>更改描述

在以前的 .NET 版本中，如果环境字体发生更改，<xref:System.Windows.Forms.DataGridViewElement.DataGridView> 将重置并覆盖 <xref:System.Windows.Forms.DataGridView.DefaultCellStyle>、<xref:System.Windows.Forms.DataGridView.ColumnHeadersDefaultCellStyle> 和 <xref:System.Windows.Forms.DataGridView.RowHeadersDefaultCellStyle> 属性中的用户定义的字体。

从 .NET 5 开始，如果在 <xref:System.Windows.Forms.DataGridView.DefaultCellStyle>、<xref:System.Windows.Forms.DataGridView.ColumnHeadersDefaultCellStyle> 或 <xref:System.Windows.Forms.DataGridView.RowHeadersDefaultCellStyle> 属性中配置了字体设置，那么即使环境字体发生更改，也会保留这些设置。 对于任何未自定义字体的属性，字体都将被更改为与环境字体设置一致。

## <a name="reason-for-change"></a>更改原因

[.NET Core 3.0](../../winforms.md#default-control-font-changed-to-segoe-ui-9-pt) 中的默认字体更改时，各单元格样式的默认字体设置也会更改。 如果应用程序依赖其 <xref:System.Windows.Forms.DataGridViewElement.DataGridView> 控件中的自定义样式，妨碍这些应用从 .NET Framework 迁移至 .NET 5.0，则不需要这种行为。

## <a name="version-introduced"></a>引入的版本

.NET 5.0

## <a name="recommended-action"></a>建议的操作

你无需执行任何操作。 但如果已自定义 <xref:System.Windows.Forms.DataGridView.DefaultCellStyle>、<xref:System.Windows.Forms.DataGridView.ColumnHeadersDefaultCellStyle> 或 <xref:System.Windows.Forms.DataGridView.RowHeadersDefaultCellStyle> 属性中的字体，并希望字体与环境字体一致，请将每个属性的 <xref:System.Windows.Forms.DataGridViewCellStyle.Font?displayProperty=nameWithType> 设置为 `null`。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Windows.Forms.DataGridView.DefaultCellStyle?displayProperty=fullName>
- <xref:System.Windows.Forms.DataGridView.ColumnHeadersDefaultCellStyle?displayProperty=fullName>
- <xref:System.Windows.Forms.DataGridView.RowHeadersDefaultCellStyle?displayProperty=fullName>

<!--

### Affected APIs

- `P:System.Windows.Forms.DataGridView.DefaultCellStyle`
- `P:System.Windows.Forms.DataGridView.ColumnHeadersDefaultCellStyle`
- `P:System.Windows.Forms.DataGridView.RowHeadersDefaultCellStyle`

### Category

- Windows Forms

-->

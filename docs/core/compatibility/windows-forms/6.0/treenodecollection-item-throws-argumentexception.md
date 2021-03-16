---
title: 中断性变更：TreeNodeCollection.Item(Int32) 为正在使用的节点抛出 ArgumentException
description: 了解 .NET 6 中的中断性变更：如果要分配的节点已分配给 TreeView，TreeNodeCollection.Item(Int32) 现在会抛出 ArgumentException。
ms.date: 01/19/2021
ms.openlocfilehash: 29727da2e4bc3d6ac89ed88819bf03d058603f77
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102255682"
---
# <a name="treenodecollectionitem-throws-exception-if-node-is-assigned-elsewhere"></a>如果节点被分配到其他地方，则 TreeNodeCollection.Item 抛出异常

如果要分配的节点已绑定到另一个 <xref:System.Windows.Forms.TreeView> 或位于不同索引上的此 <xref:System.Windows.Forms.TreeView>，则 <xref:System.Windows.Forms.TreeNodeCollection.Item(System.Int32)?displayProperty=nameWithType> 会抛出 <xref:System.ArgumentException>。

## <a name="change-description"></a>更改描述

在旧版 .NET 中，可以将树节点分配到集合，即使它已绑定到 <xref:System.Windows.Forms.TreeView>。 这可能会导致重复的节点。 自 .NET 6 起，如果要分配的节点已绑定到另一个 <xref:System.Windows.Forms.TreeView> 或位于不同索引上的此 <xref:System.Windows.Forms.TreeView>，则 <xref:System.Windows.Forms.TreeNodeCollection.Item(System.Int32)?displayProperty=nameWithType> 会引发 <xref:System.ArgumentException>。

## <a name="reason-for-change"></a>更改原因

验证输入参数是否与其他 `TreeNodeCollection` API 的行为一致。

## <a name="version-introduced"></a>引入的版本

.NET 6 预览版 1

## <a name="recommended-action"></a>建议的操作

在将 `TreeNode` 分配到集合之前，请务必先取消绑定它。

## <a name="affected-apis"></a>受影响的 API

<xref:System.Windows.Forms.TreeNodeCollection.Item(System.Int32)?displayProperty=fullName>

<!--

### Affected APIs

- `P:System.Windows.Forms.TreeNodeCollection.Item(System.Int32)`

### Category

Windows Forms

-->

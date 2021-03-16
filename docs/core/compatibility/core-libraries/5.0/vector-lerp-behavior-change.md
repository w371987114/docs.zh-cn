---
title: 中断性变更：Vector2.Lerp 和 Vector4.Lerp 的行为变更
description: 了解核心 .NET 库中的 .NET 5 中断性变更：Vector2.Lerp 和 Vector4.Lerp 的实现已更改，现正确考虑浮点数舍入误差。
ms.date: 11/01/2020
ms.openlocfilehash: a7014c30f2b102dc9a19e9a58f97b7c0ed8cd648
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256993"
---
# <a name="behavior-change-for-vector2lerp-and-vector4lerp"></a>Vector2.Lerp 和 Vector4.Lerp 的行为变更

<xref:System.Numerics.Vector2.Lerp(System.Numerics.Vector2,System.Numerics.Vector2,System.Single)?displayProperty=nameWithType> 和 <xref:System.Numerics.Vector4.Lerp(System.Numerics.Vector4,System.Numerics.Vector4,System.Single)?displayProperty=nameWithType> 的实现已更改，以正确考虑浮点数舍入误差。

## <a name="change-description"></a>更改描述

以前，<xref:System.Numerics.Vector2.Lerp(System.Numerics.Vector2,System.Numerics.Vector2,System.Single)?displayProperty=nameWithType> 和 <xref:System.Numerics.Vector4.Lerp(System.Numerics.Vector4,System.Numerics.Vector4,System.Single)?displayProperty=nameWithType> 实现为 `value1 + (value2 - value1) * amount`。 但是，由于浮点数舍入误差，在 `amount` 为 `1.0f` 时，此算法不会始终返回 `value2`。

在 .NET 5 及更高版本中，该实现使用与 <xref:System.Numerics.Vector3.Lerp(System.Numerics.Vector3,System.Numerics.Vector3,System.Single)?displayProperty=nameWithType> 相同的算法，即 `(value1 * (1.0f - amount)) + (value2 * amount)`。 此算法可正确考虑舍入误差。 现在，当 `amount` 为 `1.0f` 时，结果为精确的 `value2`。 更新的算法还允许使用 <xref:System.MathF.FusedMultiplyAdd%2A?displayProperty=nameWithType>（如果可用）自由地优化算法。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

无需执行任何操作。 但是，如果你想要保留旧行为，则可以实现自己的 `Lerp` 函数，使用先前的算法 `value1 + (value2 - value1) * amount`。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Numerics.Vector2.Lerp(System.Numerics.Vector2,System.Numerics.Vector2,System.Single)?displayProperty=fullName>
- <xref:System.Numerics.Vector4.Lerp(System.Numerics.Vector4,System.Numerics.Vector4,System.Single)?displayProperty=fullName>

<!--

#### Category

Core .NET libraries

### Affected APIs

- `M:System.Numerics.Vector2.Lerp(System.Numerics.Vector2,System.Numerics.Vector2,System.Single)`
- `M:System.Numerics.Vector4.Lerp(System.Numerics.Vector4,System.Numerics.Vector4,System.Single)`

-->

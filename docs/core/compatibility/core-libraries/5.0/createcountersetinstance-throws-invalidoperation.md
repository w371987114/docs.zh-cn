---
title: 中断性变更：如果实例已存在，CreateCounterSetInstance 将引发 InvalidOperationException
description: 了解核心 .NET 库中的 .NET 5 中断性变更：如果计数器已存在，CounterSet.CreateCounterSetInstance 将引发不同的异常。
ms.date: 11/01/2020
ms.openlocfilehash: bf59677a85538bc4992c589f8642ab4a0fce54ac
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257565"
---
# <a name="countersetcreatecountersetinstance-now-throws-invalidoperationexception-if-instance-already-exists"></a>如果实例已存在，CounterSet.CreateCounterSetInstance 现在将引发 InvalidOperationException

从 .NET 5 开始，如果计数器集已存在，<xref:System.Diagnostics.PerformanceData.CounterSet.CreateCounterSetInstance(System.String)?displayProperty=nameWithType> 将引发 <xref:System.InvalidOperationException> 而不是 <xref:System.ArgumentException>。

## <a name="change-description"></a>更改描述

在 .NET Framework 和 .NET Core 1.0 到 3.1 中，可以通过调用 <xref:System.Diagnostics.PerformanceData.CounterSet.CreateCounterSetInstance%2A> 创建计数器集的实例。 但是，如果计数器集已存在，则该方法将引发 <xref:System.ArgumentException> 异常。

在 .NET 5 及更高版本中，当调用 <xref:System.Diagnostics.PerformanceData.CounterSet.CreateCounterSetInstance%2A> 且计数器集存在时，将引发 <xref:System.InvalidOperationException> 异常。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

如果在调用 <xref:System.Diagnostics.PerformanceData.CounterSet.CreateCounterSetInstance%2A> 时捕获应用中的 <xref:System.ArgumentException> 异常，还应考虑捕获 <xref:System.InvalidOperationException> 异常。

> [!NOTE]
> 不建议捕获 <xref:System.ArgumentException> 异常。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Diagnostics.PerformanceData.CounterSet.CreateCounterSetInstance%2A?displayProperty=fullName>

<!--

### Category

Core .NET libraries

### Affected APIs

- `M:System.Diagnostics.PerformanceData.CounterSet.CreateCounterSetInstance(System.String)`

-->

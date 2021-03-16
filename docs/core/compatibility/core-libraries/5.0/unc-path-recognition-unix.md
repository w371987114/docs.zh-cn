---
title: 中断性变更：Unix 上 UNC 路径的 URI 识别
description: 了解核心 .NET 库中的 .NET 5 中断性变更：Uri 类现将以两个正斜杠开头的字符串识别为 Unix 上的 UNC 路径。
ms.date: 11/01/2020
ms.openlocfilehash: 65baaad5e1be7a8f61e3e62c976fd7e28f48730f
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257019"
---
# <a name="uri-recognition-of-unc-paths-on-unix"></a>Unix 上 UNC 路径的 URI 识别

现在，<xref:System.Uri> 类将以两个正斜杠 (`//`) 开头的字符串识别为 Unix 操作系统上的通用命名约定 (UNC) 路径。 此更改使此类字符串的行为在所有平台中保持一致。

## <a name="change-description"></a>更改描述

在早期版本的 .NET 中，<xref:System.Uri> 类将以两个正斜杠开头的字符串（例如 `//contoso`）识别为 Unix 操作系统上的绝对文件路径。 但是，在 Windows 上，此类字符串被识别为 UNC 路径。

从 .NET 5 开始，<xref:System.Uri> 类将以两个正斜杠开头的字符串识别为所有平台（包括 Unix）上的 UNC 路径。 此外，属性的行为遵循 UNC 语义：

- <xref:System.Uri.IsUnc?displayProperty=nameWithType> 返回 `true`。
- 路径中的反斜杠替换为正斜杠。 例如，`//first\second` 重命名为 `//first/second`。
- <xref:System.Uri.LocalPath?displayProperty=nameWithType> 不对字符进行百分号编码。 例如，`//first/\uFFF0` 不转换为 `//first/%EF%BF%B0`。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

开发人员一方不需要执行任何操作。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Uri?displayProperty=fullName>

<!--

#### Category

Core .NET libraries

### Affected APIs

- `T:System.Uri`

-->

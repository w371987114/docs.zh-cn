---
title: "不支持的功能"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-ado
ms.tgt_pltfrm: 
ms.topic: article
ms.assetid: e480cfb5-697e-42c8-bed5-9264c945c4f9
caps.latest.revision: "2"
author: JennieHubbard
ms.author: jhubbard
manager: jhubbard
ms.openlocfilehash: 6dd8ccebd278fdc36c536c49f7f1d4262b2de8c1
ms.sourcegitcommit: bd1ef61f4bb794b25383d3d72e71041a5ced172e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="unsupported-functionality"></a>不支持的功能
在 LINQ to SQL 中，无法通过转换现有的公共语言运行库 (CLR) 和 .NET Framework 构造公开以下 SQL 功能：  
  
-   `STDDEV`  
  
-   `LIKE`  
  
     尽管通过直接转换，不支持 `LIKE`，但是 <xref:System.Data.Linq.SqlClient.SqlMethods> 类中仍存在相似的功能。 有关详细信息，请参阅<xref:System.Data.Linq.SqlClient.SqlMethods.Like%2A?displayProperty=nameWithType>。  
  
-   `DATEDIFF`  
  
     LINQ to SQL 具有对 `DATEDIFF` 的有限支持。 <xref:System.Data.Linq.SqlClient.SqlMethods> 类中存在相似的功能。  
  
-   `ROUND`  
  
     LINQ to SQL 具有对 `ROUND` 的有限支持。 有关详细信息，请参阅[System.Math 方法](system-math-methods.md)。  
  
## <a name="see-also"></a>另请参阅  
 [数据类型和函数](data-types-and-functions.md)

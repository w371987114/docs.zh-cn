---
title: "变量 (Entity SQL)"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-ado
ms.tgt_pltfrm: 
ms.topic: article
ms.assetid: 3eed222a-f8f6-46b6-9cd5-220cc0e4e5d8
caps.latest.revision: "2"
author: JennieHubbard
ms.author: jhubbard
manager: jhubbard
ms.openlocfilehash: 551b6d6feab6829c9a2f6f2e89e1918acf463411
ms.sourcegitcommit: 4f3fef493080a43e70e951223894768d36ce430a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2017
---
# <a name="variables-entity-sql"></a>变量 (Entity SQL)
## <a name="variable"></a>变量  
 变量表达式是对当前作用域中定义的命名表达式的引用。 变量的引用必须是有效[!INCLUDE[esql](../../../../../../includes/esql-md.md)]标识符中, 定义[标识符](../../../../../../docs/framework/data/adonet/ef/language-reference/identifiers-entity-sql.md)。  
  
 以下示例演示如何在表达式中使用变量。 FROM 子句中的 `c` 是变量定义。 在 SELECT 子句中使用的 `c` 表示变量引用。  
  
```  
select c   
from LOB.customers as c  
```  
  
## <a name="see-also"></a>另请参阅  
 [标识符](../../../../../../docs/framework/data/adonet/ef/language-reference/identifiers-entity-sql.md)  
 [参数](../../../../../../docs/framework/data/adonet/ef/language-reference/parameters-entity-sql.md)  
 [Entity SQL 概述](../../../../../../docs/framework/data/adonet/ef/language-reference/entity-sql-overview.md)

---
title: "实体数据模型：命名空间"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-ado
ms.tgt_pltfrm: 
ms.topic: article
ms.assetid: 98ab4226-bb9f-44e7-af46-61a9b1a4e47c
caps.latest.revision: "4"
author: JennieHubbard
ms.author: jhubbard
manager: jhubbard
ms.openlocfilehash: 3e473453576f04e89b58806c5140e29855d7d022
ms.sourcegitcommit: 4f3fef493080a43e70e951223894768d36ce430a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2017
---
# <a name="entity-data-model-namespaces"></a>实体数据模型：命名空间
实体数据模型 (EDM) 中的命名空间是用于的抽象容器[实体类型](../../../../docs/framework/data/adonet/entity-type.md)，[复杂类型](../../../../docs/framework/data/adonet/complex-type.md)，和[关联](../../../../docs/framework/data/adonet/association-type.md)。 EDM 中的命名空间类似于编程语言中的命名空间：它们为自己所包含的对象提供了上下文，并为消除具有相同名称（但包含在不同的命名空间中）的对象的歧义提供了一种方式。  
  
## <a name="example"></a>示例  
 [ADO.NET 实体框架](../../../../docs/framework/data/adonet/ef/index.md)使用域特定语言 (DSL) 称为概念架构定义语言 ([CSDL](../../../../docs/framework/data/adonet/ef/language-reference/csdl-specification.md)) 来定义概念模型。 下面的 CSDL 代码使用命名空间来标识一个在不同概念模型中定义的类型。 该示例定义了一个实体类型 (`Publisher`)，它有一个从 `Address` 命名空间导入的复杂类型属性 (`ExtendedBooksModel`)。 请注意，`Using` 元素表明导入了一个命名空间。 还要注意的是，`Address` 属性的类型是使用其完全限定名称 (`ExtendedBooksModel.Address`) 定义的，表明该类型是在 `ExtendedBooksModel` 命名空间中定义的。  
  
 [!code-xml[EDM_Example_Model#ImportedNamespace](../../../../samples/snippets/xml/VS_Snippets_Data/edm_example_model/xml/books6.edmx#importednamespace)]  
  
## <a name="see-also"></a>另请参阅  
 [实体数据模型关键概念](../../../../docs/framework/data/adonet/entity-data-model-key-concepts.md)  
 [实体数据模型](../../../../docs/framework/data/adonet/entity-data-model.md)

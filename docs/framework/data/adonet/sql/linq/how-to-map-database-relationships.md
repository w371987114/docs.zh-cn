---
title: "如何：映射数据库关系"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-ado
ms.tgt_pltfrm: 
ms.topic: article
dev_langs:
- csharp
- vb
ms.assetid: 538def39-8399-46fb-b02d-60ede4e050af
caps.latest.revision: "3"
author: JennieHubbard
ms.author: jhubbard
manager: jhubbard
ms.openlocfilehash: aa771fbde889febb269f49603f7d2a2ac5c67784
ms.sourcegitcommit: 4f3fef493080a43e70e951223894768d36ce430a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2017
---
# <a name="how-to-map-database-relationships"></a>如何：映射数据库关系
可以在您的实体类中将始终相同的任何数据关系编码为属性引用。 例如，在 Northwind 示例数据库中，由于客户通常会下订单，因此在模型中客户与其订单之间始终存在关系。  
  
 [!INCLUDE[vbtecdlinq](../../../../../../includes/vbtecdlinq-md.md)] 定义了 <xref:System.Data.Linq.Mapping.AssociationAttribute> 属性来帮助表示此类关系。 此属性与 <xref:System.Data.Linq.EntitySet%601> 和 <xref:System.Data.Linq.EntityRef%601> 类型一起使用，来表示将作为数据库中的外键关系的内容。 有关详细信息，请参阅的关联属性部分[基于属性的映射](../../../../../../docs/framework/data/adonet/sql/linq/attribute-based-mapping.md)。  
  
> [!NOTE]
>  AssociationAttribute 和 ColumnAttribute Storage 属性值区分大小写。 例如，请确保 AssociationAttribute.Storage 属性 (Property) 的属性 (Attribute) 中使用的值与代码中其他位置使用的相应属性 (Property) 名称值的大小写相匹配。 这适用于所有 .NET 编程语言，即使是那些通常不区分大小写的编程语言，包括 [!INCLUDE[vb_current_short](../../../../../../includes/vb-current-short-md.md)]。 有关 Storage 属性的更多信息，请参见 <xref:System.Data.Linq.Mapping.DataAttribute.Storage%2A?displayProperty=nameWithType>。  
  
 大多数关系都是一对多关系，这一点在本主题后面部分的示例中会有所体现。 您还可以按如下方式来表示一对一和多对多关系：  
  
-   一对一：通过向双方添加 <xref:System.Data.Linq.EntitySet%601> 来表示此类关系。  
  
     例如，考虑`Customer` - `SecurityCode`关系，以便客户的安全代码将不会找到在创建`Customer`表，并可以仅得到授权的人才能访问。  
  
-   多对多： 在多对多关系中，链接表的主键 (也名为*接合*表) 通常由来自其他两个表的外键组合而成。  
  
     例如，考虑`Employee` - `Project`通过使用链接表形成多对多关系`EmployeeProject`。 [!INCLUDE[vbtecdlinq](../../../../../../includes/vbtecdlinq-md.md)] 要求使用以下三个类对这种关系进行建模：`Employee`、`Project` 和 `EmployeeProject`。 在这种情况下，更改 `Employee` 和 `Project` 之间的关系似乎需要更新主键 `EmployeeProject`。 但是，这种情况最好的模型化处理方法是删除现有 `EmployeeProject`，然后创建新的 `EmployeeProject`。  
  
    > [!NOTE]
    >  关系数据库中的关系通常模型化成引用其他表中主键的外键值。 若要在它们之间导航你显式将关联的两个表使用关系*联接*操作。  
    >   
    >  中的对象[!INCLUDE[vbtecdlinq](../../../../../../includes/vbtecdlinq-md.md)]、 一端另一方面，相互引用通过使用属性引用或使用导航的引用集合*点*表示法。  
  
## <a name="example"></a>示例  
 在下面的一对多示例中，`Customer` 类具有一个声明客户与其订单之间的关系的属性。  `Orders` 属性为 <xref:System.Data.Linq.EntitySet%601> 类型。 此类型表明这种关系是一对多的（一个客户对多个订单）。 <xref:System.Data.Linq.Mapping.AssociationAttribute.OtherKey%2A> 属性用来说明如何实现这种关联，即通过指定相关类中要与此属性比较的属性的名称。 在此示例中，`CustomerID`属性进行比较，就像数据库*联接*将比较该列值。  
  
> [!NOTE]
>  如果您使用的是 [!INCLUDE[vs_current_short](../../../../../../includes/vs-current-short-md.md)]，则可以使用 [!INCLUDE[vs_ordesigner_long](../../../../../../includes/vs-ordesigner-long-md.md)] 来创建类之间的关联。  
  
 [!code-csharp[DlinqCustomize#3](../../../../../../samples/snippets/csharp/VS_Snippets_Data/DLinqCustomize/cs/Program.cs#3)]
 [!code-vb[DlinqCustomize#3](../../../../../../samples/snippets/visualbasic/VS_Snippets_Data/DLinqCustomize/vb/Module1.vb#3)]  
  
## <a name="example"></a>示例  
 这种情况也可以反过来处理。 您可以不使用 `Customer` 类来说明客户与订单之间的关联，而改用 `Order` 类。 `Order` 类使用 <xref:System.Data.Linq.EntityRef%601> 类型反向说明与客户的关系，如下面的代码示例所示。  
  
> [!NOTE]
>  <xref:System.Data.Linq.EntityRef%601>类支持*延迟加载*。 有关详细信息，*请参阅*[延迟执行与立即加载](../../../../../../docs/framework/data/adonet/sql/linq/deferred-versus-immediate-loading.md)。  
  
 [!code-csharp[DLinqCustomize#5](../../../../../../samples/snippets/csharp/VS_Snippets_Data/DLinqCustomize/cs/Program.cs#5)]
 [!code-vb[DLinqCustomize#5](../../../../../../samples/snippets/visualbasic/VS_Snippets_Data/DLinqCustomize/vb/Module1.vb#5)]  
  
## <a name="see-also"></a>另请参阅  
 [如何： 通过使用代码编辑器自定义实体类](../../../../../../docs/framework/data/adonet/sql/linq/how-to-customize-entity-classes-by-using-the-code-editor.md)  
 [LINQ to SQL 对象模型](../../../../../../docs/framework/data/adonet/sql/linq/the-linq-to-sql-object-model.md)

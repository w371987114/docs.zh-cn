---
title: 泛型接口 - C# 编程指南
description: 了解如何通过 C# 使用泛型接口。 查看代码示例和其他可用资源。
ms.date: 07/20/2015
helpviewer_keywords:
- C# language, generic interfaces
- generics [C#], interfaces
ms.assetid: a8fa49a1-6e78-4a09-87e5-84a0b9f5ffbe
ms.openlocfilehash: 6089e14bd2b13268a03d3600ef8bf78f9afa1c6d
ms.sourcegitcommit: bdbf6472de867a0a11aaa5b9384a2506c24f27d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102206734"
---
# <a name="generic-interfaces-c-programming-guide"></a>泛型接口（C# 编程指南）

为泛型集合类或表示集合中的项的泛型类定义接口通常很有用处。 为避免对值类型的装箱和取消装箱操作，泛型类的首选项使用泛型接口，例如 <xref:System.IComparable%601>而不是 <xref:System.IComparable>。 .NET 类库定义多个泛型接口，以便用于 <xref:System.Collections.Generic> 命名空间中的集合类。  
  
 接口被指定为类型参数上的约束时，仅可使用实现接口的类型。 如下代码示例演示一个派生自 `GenericList<T>` 类的 `SortedList<T>` 类。 有关详细信息，请参阅[泛型介绍](./index.md)。 `SortedList<T>` 添加约束 `where T : IComparable<T>`。 这可使 `SortedList<T>` 中的 `BubbleSort` 方法在列表元素上使用泛型 <xref:System.IComparable%601.CompareTo%2A> 方法。 在此示例中，列表元素是一个实现 `IComparable<Person>` 的简单类 `Person`。  
  
 [!code-csharp[csProgGuideGenerics#29](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideGenerics/CS/Generics2.cs#29)]  
  
 可将多个接口指定为单个类型上的约束，如下所示：  
  
 [!code-csharp[csProgGuideGenerics#30](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideGenerics/CS/Generics.cs#30)]  
  
 一个接口可定义多个类型参数，如下所示：  
  
 [!code-csharp[csProgGuideGenerics#31](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideGenerics/CS/Generics.cs#31)]  
  
 适用于类的继承规则也适用于接口：  
  
 [!code-csharp[csProgGuideGenerics#32](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideGenerics/CS/Generics.cs#32)]  
  
 如果泛型接口是协变的（即，仅使用自身的类型参数作为返回值），那么这些接口可继承自非泛型接口。 在 .NET 类库中，<xref:System.Collections.Generic.IEnumerable%601> 继承自 <xref:System.Collections.IEnumerable>，因为 <xref:System.Collections.Generic.IEnumerable%601> 在 <xref:System.Collections.Generic.IEnumerable%601.GetEnumerator%2A> 的返回值和 <xref:System.Collections.Generic.IEnumerator%601.Current%2A> 属性 Getter 中仅使用 `T`。  
  
 具体类可实现封闭式构造接口，如下所示：  
  
 [!code-csharp[csProgGuideGenerics#33](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideGenerics/CS/Generics.cs#33)]  
  
 只要类形参列表提供接口所需的所有实参，泛型类即可实现泛型接口或封闭式构造接口，如下所示：  
  
 [!code-csharp[csProgGuideGenerics#34](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideGenerics/CS/Generics.cs#34)]  
  
 控制方法重载的规则对泛型类、泛型结构或泛型接口内的方法一样。 有关详细信息，请参阅[泛型方法](./generic-methods.md)。  
  
## <a name="see-also"></a>另请参阅

- [C# 编程指南](../index.md)
- [泛型介绍](./index.md)
- [interface](../../language-reference/keywords/interface.md)
- [泛型](../../../standard/generics/index.md)

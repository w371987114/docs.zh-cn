---
title: 相等性比较 - C# 编程指南
description: 了解相等性比较。 查看“值相等性”和“引用相等性”的说明，并查看其他资源。
ms.date: 07/20/2015
helpviewer_keywords:
- object equality [C#]
ms.assetid: 10b865ea-4e7b-4127-9242-c9b8f57d9f04
ms.openlocfilehash: 3bc41e9adeff23dc385d0888163f9edf04772595
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102259612"
---
# <a name="equality-comparisons-c-programming-guide"></a>相等性比较（C# 编程指南）

有时需要比较两个值是否相等。 在某些情况下，测试的是“值相等性”，也称为“等效性”，这意味着两个变量包含的值相等。 在其他情况下，必须确定两个变量是否引用内存中的同一基础对象。 此类型的相等性称为“引用相等性”或“标识”。 本主题介绍这两种相等性，并提供指向其他主题的链接，供用户了解详细信息。  
  
## <a name="reference-equality"></a>引用相等性

 引用相等性指两个对象引用均引用同一基础对象。 这可以通过简单的赋值来实现，如下面的示例所示。  
  
 [!code-csharp[csProgGuideStatements#18](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideStatements/CS/Statements.cs#18)]  
  
 在此代码中，创建了两个对象，但在赋值语句后，这两个引用所引用的是同一对象。 因此，它们具有引用相等性。 使用 <xref:System.Object.ReferenceEquals%2A> 方法确定两个引用是否引用同一对象。  
  
引用相等性的概念仅适用于引用类型。 由于在将值类型的实例赋给变量时将产生值的副本，因此值类型对象无法具有引用相等性。 因此，永远不会有两个未装箱结构引用内存中的同一位置。 此外，如果使用 <xref:System.Object.ReferenceEquals%2A> 比较两个值类型，结果将始终为 `false`，即使对象中包含的值都相同也是如此。 这是因为会将每个变量装箱到单独的对象实例中。 有关详细信息，请参阅[如何测试引用相等性（标识）](./how-to-test-for-reference-equality-identity.md)。

## <a name="value-equality"></a>值相等性

 值相等性指两个对象包含相同的一个或多个值。 对于基元值类型（例如 [int](../../language-reference/builtin-types/integral-numeric-types.md) 或 [bool](../../language-reference/builtin-types/bool.md)），针对值相等性的测试简单明了。 可以使用 [==](../../language-reference/operators/equality-operators.md#equality-operator-) 运算符，如下面的示例所示。  
  
```csharp  
int a = GetOriginalValue();  
int b = GetCurrentValue();  
  
// Test for value equality.
if (b == a)
{  
    // The two integers are equal.  
}  
```  
  
 对于大多数其他类型，针对值相等性的测试较为复杂，因为它需要用户了解类型对值相等性的定义方式。 对于具有多个字段或属性的类和结构，值相等性的定义通常指所有字段或属性都具有相同的值。 例如，如果 pointA.X 等于 pointB.X，并且 pointA.Y 等于 pointB.Y，则可以将两个 `Point` 对象定义为相等。 对记录来说，值相等性是指如果记录类型的两个变量类型相匹配，且所有属性和字段值都一致，那么记录类型的两个变量是相等的。  
  
但是，并不要求类型中的所有字段均相等。 只需子集相等即可。 比较不具所有权的类型时，应确保明确了解相等性对于该类型是如何定义的。 若要详细了解如何在自己的类和结构中定义值相等性，请参阅[如何为类型定义值相等性](./how-to-define-value-equality-for-a-type.md)。
  
### <a name="value-equality-for-floating-point-values"></a>浮点值的值相等性

 由于二进制计算机上的浮点算法不精确，因此浮点值（[double](../../language-reference/builtin-types/floating-point-numeric-types.md) 和 [float](../../language-reference/builtin-types/floating-point-numeric-types.md)）的相等比较会出现问题。 有关更多信息，请参阅 <xref:System.Double?displayProperty=nameWithType> 主题中的备注部分。  
  
## <a name="related-topics"></a>相关主题  
  
|Title|描述|  
|-----------|-----------------|
|[如何测试引用相等性（标识）](./how-to-test-for-reference-equality-identity.md)|介绍如何确定两个变量是否具有引用相等性。|
|[如何为类型定义值相等性](./how-to-define-value-equality-for-a-type.md)|介绍如何为类型提供值相等性的自定义定义。|
|[C# 编程指南](../index.md)|提供一些链接，这些链接指向重要 C# 语言功能以及通过 .NET 提供给 C# 的功能的相关详细信息。|
|[类型](../types/index.md)|提供有关 C# 类型系统的信息以及指向附加信息的链接。|
|[记录](../classes-and-structs/records.md)|提供有关记录类型的信息，默认情况下，记录类型会测试值相等性。|

## <a name="see-also"></a>另请参阅

- [C# 编程指南](../index.md)

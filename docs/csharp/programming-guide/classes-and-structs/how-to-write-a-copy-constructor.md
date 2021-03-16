---
title: 如何编写复制构造函数（C# 编程指南）
description: 了解如何使用 C# 编写复制构造函数，该构造函数采用类的实例并返回具有输入值的新实例。
ms.date: 07/20/2015
helpviewer_keywords:
- C# Language, copy constructor
- copy constructor [C#]
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.assetid: fba899b5-fc41-428e-a745-3ebdbf37990a
ms.openlocfilehash: c61018444dd600d6f33765b104034355d0301667
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102255231"
---
# <a name="how-to-write-a-copy-constructor-c-programming-guide"></a>如何编写复制构造函数（C# 编程指南）

C # [记录](records.md)为对象提供复制构造函数，但对于类，你必须自行编写。  
  
## <a name="example"></a>示例  

 在下面的示例中，`Person`[类](../../language-reference/keywords/class.md)定义一个复制构造函数，该函数使用 `Person` 的实例作为其参数。 该参数的属性值分配给 `Person` 的新实例的属性。 该代码包含一个备用复制构造函数，该函数发送要复制到该类的实例构造函数的实例的 `Name` 和 `Age` 属性。  
  
 [!code-csharp[CopyConstructor](snippets/how-to-write-a-copy-constructor/Program.cs)]

## <a name="see-also"></a>请参阅

- <xref:System.ICloneable>
- [记录](records.md)
- [C# 编程指南](../index.md)
- [类和结构](./index.md)
- [构造函数](./constructors.md)
- [终结器](./destructors.md)

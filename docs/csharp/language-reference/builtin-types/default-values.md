---
title: C# 类型的默认值 - C# 参考
description: 了解 C# 类型的默认值，例如 bool、char、int、float 和 double 等。
ms.date: 12/18/2019
helpviewer_keywords:
- default [C#]
- parameterless constructor [C#]
ms.openlocfilehash: 72d6249ed56ae6ae34a6f51102e1e7bbd3f64ab7
ms.sourcegitcommit: 42d436ebc2a7ee02fc1848c7742bc7d80e13fc2f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102104110"
---
# <a name="default-values-of-c-types-c-reference"></a>C# 类型的默认值（C# 参考）

下表显示 C# 类型的默认值：

|类型|默认值|
|---------|------------------|
|任何[引用类型](../keywords/reference-types.md)|`null`|
|任何[内置整数数值类型](integral-numeric-types.md)|0（零）|
|任何[内置浮点型数值类型](floating-point-numeric-types.md)|0（零）|
|[bool](bool.md)|`false`|
|[char](char.md)|`'\0'` (U + 0000)|
|[enum](enum.md)|表达式 `(E)0` 生成的值，其中 `E` 是枚举标识符。|
|[struct](struct.md)|通过如下设置生成的值：将所有值类型的字段设置为其默认值，将所有引用类型的字段设置为 `null`。|
|任何[可以为 null 的值类型](nullable-value-types.md)|<xref:System.Nullable%601.HasValue%2A> 属性为 `false` 且 <xref:System.Nullable%601.Value%2A> 属性未定义的实例。 该默认值也称为可以为 null 的值类型的“null”  值。|

使用 [`default` 运算符](../operators/default.md#default-operator)生成默认类型值，如下面的示例所示：

```csharp
int a = default(int);
```

从 C# 7.1 开始，可使用[`default`文本](../operators/default.md#default-literal)来初始化变量，使其具有其类型的默认值：

```csharp
int a = default;
```

对于值类型，隐式无参数构造函数还可生成类型的默认值，如以下示例所示：

```csharp-interactive
var n = new System.Numerics.Complex();
Console.WriteLine(n);  // output: (0, 0)
```

在运行时，如果 <xref:System.Type?displayProperty=nameWithType> 实例表示一个值类型，则可以使用 <xref:System.Activator.CreateInstance(System.Type)?displayProperty=nameWithType> 方法来调用无参数构造函数，以获取该类型的默认值。

## <a name="c-language-specification"></a>C# 语言规范

有关更多信息，请参阅 [C# 语言规范](~/_csharplang/spec/introduction.md)的以下部分：

- [默认值](~/_csharplang/spec/variables.md#default-values)
- [默认构造函数](~/_csharplang/spec/types.md#default-constructors)

## <a name="see-also"></a>请参阅

- [C# 参考](../index.md)
- [构造函数](../../programming-guide/classes-and-structs/constructors.md)

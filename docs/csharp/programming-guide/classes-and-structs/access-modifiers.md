---
title: 访问修饰符 - C# 编程指南
description: C# 中的所有类型和类型成员都具有可访问性级别，该级别可以控制是否可以从其他代码中使用它们。 查看此访问修饰符列表。
ms.date: 03/08/2020
helpviewer_keywords:
- C# Language, access modifiers
- access modifiers [C#], about
ms.assetid: 6e81ee82-224f-4a12-9baf-a0dca2656c5b
ms.openlocfilehash: 168965a3d7f5c3d2436bfdc25edb6c78cdabbc05
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102258333"
---
# <a name="access-modifiers-c-programming-guide"></a>访问修饰符（C# 编程指南）

所有类型和类型成员都具有可访问性级别。 该级别可以控制是否可以从你的程序集或其他程序集中的其他代码中使用它们。 可以使用以下访问修饰符在进行声明时指定类型或成员的可访问性：

- [public](../../language-reference/keywords/public.md)：同一程序集中的任何其他代码或引用该程序集的其他程序集都可以访问该类型或成员。
- [private](../../language-reference/keywords/private.md)：只有同一 `class` 或 `struct` 中的代码可以访问该类型或成员。
- [protected](../../language-reference/keywords/protected.md)：只有同一 `class` 或者从该 `class` 派生的 `class` 中的代码可以访问该类型或成员。
- [internal](../../language-reference/keywords/internal.md)：同一程序集中的任何代码都可以访问该类型或成员，但其他程序集中的代码不可以。
- [protected internal](../../language-reference/keywords/protected-internal.md)：该类型或成员可由对其进行声明的程序集或另一程序集中的派生 `class` 中的任何代码访问。
- [private protected](../../language-reference/keywords/private-protected.md)：只有在其声明程序集内，通过相同 `class` 中的代码或派生自该 `class` 的类型，才能访问类型或成员。

下面的示例演示如何在类型和成员上指定访问修饰符：

[!code-csharp[PublicAccess](~/samples/snippets/csharp/objectoriented/accessmodifiers.cs#PublicAccess)]

不是所有访问修饰符都可以在所有上下文中由所有类型或成员使用。 在某些情况下，类型成员的可访问性受到其包含类型的可访问性的限制。

## <a name="class-record-and-struct-accessibility"></a>类、记录和结构可访问性  

直接在命名空间中声明的类、记录和结构（即，没有嵌套在其他类或结构中的类、记录和结构）可以为 `public` 或 `internal`。 如果未指定任何访问修饰符，则默认设置为 `internal`。

结构成员（包括嵌套的类和结构）可以声明为 `public`、`internal` 或 `private`。 类成员（包括嵌套的类和结构）可以声明为 `public`、`protected internal`、`protected`、`internal`、`private protected` 或 `private`。 默认情况下，类成员和结构成员（包括嵌套的类和结构）的访问级别为 `private`。 不能从包含类型的外部访问私有嵌套类型。

派生类和派生记录不能具有高于其基类型的可访问性。 不能声明派生自内部类 `A` 的公共类 `B`。 如果允许这样，则它将具有使 `A` 公开的效果，因为可从派生类访问 `A` 的所有 `protected` 或 `internal` 成员。

可以通过使用 `InternalsVisibleToAttribute` 启用特定的其他程序集访问内部类型。 有关详细信息，请参阅[友元程序集](../../../standard/assembly/friend.md)。

## <a name="class-record-and-struct-member-accessibility"></a>类、记录和结构成员可访问性  

可以使用六种访问类型中的任意一种声明类和记录成员（包括嵌套的类、记录和结构）。 结构成员无法声明为 `protected`、`protected internal` 或 `private protected`，因为结构不支持继承。

通常情况下，成员的可访问性不大于包含该成员的类型的可访问性。 但是，如果内部类的 `public` 成员实现了接口方法或替代了在公共基类中定义的虚拟方法，则可从该程序集的外部访问该成员。

为字段、属性或事件的任何成员的类型必须至少与该成员本身具有相同的可访问性。 同样，任何方法、索引器或委托的返回类型和参数类型必须至少与该成员本身具有相同的可访问性。 例如，除非 `C` 也是 `public`，否则不能具有返回类 `C` 的 `public` 方法 `M`。 同样，如果 `A` 声明为 `private`，则不能具有类型 `A` 的 `protected` 属性。

用户定义的运算符始终必须声明为 `public` 和 `static`。 有关详细信息，请参阅[运算符重载](../../language-reference/operators/operator-overloading.md)。

终结器不能具有可访问性修饰符。

若要设置 `class`、`record` 或 `struct` 成员的访问级别，请向成员声明添加适当的关键字，如以下示例中所示。

[!code-csharp[MethodAccess](~/samples/snippets/csharp/objectoriented/accessmodifiers.cs#MethodAccess)]

## <a name="other-types"></a>其他类型

在命名空间内直接声明的接口可以声明为 `public` 或 `internal`，就像类和结构一样，接口默认设置为 `internal` 访问级别。 接口成员默认为 `public`，因为接口的用途是启用其他类型以访问类或结构。 接口成员声明可以包含任何访问修饰符。 这最适用于静态方法，以提供类的所有实现器需要的常见实现。

枚举成员始终为 `public`，并且不能应用任何访问修饰符。

委托类似于类和结构。 默认情况下，当在命名空间内直接声明它们时，它们具有 `internal` 访问级别，当将它们嵌套在命名空间内时，它们具有 `private` 访问级别。

## <a name="c-language-specification"></a>C# 语言规范

[!INCLUDE[CSharplangspec](~/includes/csharplangspec-md.md)]  

## <a name="see-also"></a>请参阅

- [C# 编程指南](../index.md)
- [类和结构](./index.md)
- [接口](../interfaces/index.md)
- [private](../../language-reference/keywords/private.md)
- [public](../../language-reference/keywords/public.md)
- [internal](../../language-reference/keywords/internal.md)
- [受保护](../../language-reference/keywords/protected.md)
- [protected internal](../../language-reference/keywords/protected-internal.md)
- [private protected](../../language-reference/keywords/private-protected.md)
- [class](../../language-reference/keywords/class.md)
- [struct](../../language-reference/builtin-types/struct.md)
- [interface](../../language-reference/keywords/interface.md)

---
description: 详细了解：语言独立性和与语言无关的组件
title: 语言独立性和与语言无关的组件
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- language interoperability
- Common Language Specification
- cross-language interoperability
- CLS
- runtime, language interoperability
- common language runtime, language interoperability
ms.assetid: 4f0b77d0-4844-464f-af73-6e06bedeafc6
ms.openlocfilehash: 4080cb38ca3b7d79c0ad4291198b13567511093c
ms.sourcegitcommit: 0bb8074d524e0dcf165430b744bb143461f17026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2021
ms.locfileid: "103480844"
---
# <a name="language-independence-and-language-independent-components"></a>语言独立性和与语言无关的组件

.NET 是独立的语言。 这意味着，作为开发人员，你可以使用面向 .NET 的多种语言（例如，C#、C++/CLI、Eiffel、F#、IronPython、IronRuby、PowerBuilder、Visual Basic、Visual COBOL 以及 Windows PowerShell）之一进行开发。 可访问针对 .NET 开发的类库的类型和成员，而不必了解它们的初始编写语言，也不必遵循任何原始语言的约定。 如果你是组件开发人员，无论组件采用哪种语言，均可由任何 .NET 应用访问。

> [!NOTE]
> 本文的第一部分讨论如何创建独立于语言的组件（即以任何语言编写的应用均可以使用的组件）。 还可以从用多种语言编写的源代码创建单个组件或应用；请参阅本文第二部分的[跨语言互操作性](#CrossLang)。

若要与使用任何语言编写的其他对象完全交互，对象必须只向调用方公开那些所有语言共有的功能。 此组通用功能由公共语言规范 (CLS) 定义，后者是一组适用于生成的程序集的规则。 公共语言规范在 [ECMA-335 标准：公共语言基础结构](https://www.ecma-international.org/publications-and-standards/standards/ecma-335/)的第 I 部分的第 7 条至第 11 条中进行了定义。

如果你的组件符合公共语言规范，则保证其符合 CLS 并可通过支持 CLS 的任何编程语言编写的程序集中的代码对其进行访问。 你可以通过将 <xref:System.CLSCompliantAttribute> 特性应用于源代码来确定自己的组件在编译时是否符合公共语言规范。 有关详细信息，请参阅 [CLSCompliantAttribute 特性](#CLSAttribute)。

本文内容：

- [CLS 符合性规则](#Rules)

  - [类型和类型成员签名](#Types)

  - [命名约定](#naming)

  - [类型转换](#conversion)

  - [数组](#arrays)

  - [接口](#Interfaces)

  - [枚举](#enums)

  - [类型成员概述](#members)

  - [成员可访问性](#MemberAccess)

  - [泛型类型和成员](#Generics)

  - [构造函数](#ctors)

  - [属性](#properties)

  - [事件](#events)

  - [重载](#overloads)

  - [异常](#exceptions)

  - [特性](#attributes)

- [CLSCompliantAttribute 特性](#CLSAttribute)

- [跨语言互操作性](#CrossLang)

<a name="Rules"></a>

## <a name="cls-compliance-rules"></a>CLS 遵从性规则

本节讨论用于创建符合 CLS 的组件的规则。 有关规则的完整列表，请参阅 [ECMA-335 标准：公共语言基础结构](https://www.ecma-international.org/publications-and-standards/standards/ecma-335/)的第 I 部分的第 7 条至第 11 条中进行了定义。

> [!NOTE]
> 公共语言规范讨论 CLS 遵从性的每个规则，因为它应用于使用者（以编程方式访问符合 CLS 的组件的开发人员）、框架（使用语言编译器创建符合 CLS 的库的开发人员）和扩展人员（创建可创建符合 CLS 的组件的语言编译器或代码分析器等工具的开发人员）。 本文重点介绍适用于框架的规则。 但请注意，一些适用于扩展程序的规则也适用于使用 Reflection.Emit 创建的程序集。

若要设计独立于语言的组件，您只需将 CLS 遵从性的规则应用于组件的公共接口即可。 您的私有实现不必符合规范。

> [!IMPORTANT]
> CLS 遵从性的规则仅适用于组件的公共接口，而非其私有实现。

例如，<xref:System.Byte> 之外的无符号整数不符合 CLS。 由于以下示例中的 `Person` 类公开了 `Age` 类型的 <xref:System.UInt16> 属性，因此以下代码显示编译器警告。

[!code-csharp[Conceptual.CLSCompliant#1](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/public1.cs#1)]
[!code-vb[Conceptual.CLSCompliant#1](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/public1.vb#1)]

您可以通过将 `Person` 属性的类型从 `Age` 更改为 <xref:System.UInt16> 来将 <xref:System.Int16> 类设置为符合 CLS，即一个符合 CLS 的 16 位带符号整数。 您无需更改私有 `personAge` 字段的类型。

[!code-csharp[Conceptual.CLSCompliant#2](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/public2.cs#2)]
[!code-vb[Conceptual.CLSCompliant#2](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/public2.vb#2)]

库的公共接口包括：

- 公共类的定义。

- 公共类中公共成员的定义，以及派生类可以访问的成员（即受保护的成员）的定义。

- 公共类中公共方法的参数和返回类型，以及派生类可以访问的方法的参数和返回类型。

下表中将列出 CLS 遵从性规则。 规则的文本摘自 [ECMA-335 标准：公共语言基础结构](https://www.ecma-international.org/publications-and-standards/standards/ecma-335/)（版权所有 2012，Ecma International）。 有关这些规则的详细信息，请参阅以下各节。

|类别|查看|规则|规则编号|
|--------------|---------|----------|-----------------|
|可访问性|[成员可访问性](#MemberAccess)|重写继承的方法时，可访问性应不会更改（重写一个继承自其他具有可访问性 `family-or-assembly` 的程序集的方法除外）。 在此情况下，重写应具有可访问性 `family`。|10|
|可访问性|[成员可访问性](#MemberAccess)|类型和成员的可见性和可访问性应是这样的：只要任何成员是可见的且可访问的，则该成员签名中的类型应是可见且可访问的。 例如，在其程序集外部可见的公共方法不应具有其类型仅在程序集内可见的参数。 只要任何成员是可见且可访问的，则构成该成员签名中使用的实例化泛型类型的类型应是可见且可访问的。 例如，一个在其程序集外部可见的成员签名中出现的实例化泛型类型不应具有其类型仅在程序集内可见的泛型实参。|12|
|数组|[数组](#arrays)|数组应具有符合 CLS 的类型的元素，且数组的所有维度的下限应为零。 各重载间只需区别：项是数组还是数组的元素类型。 当重载基于两个或更多数组类型时，元素类型应为命名类型。|16|
|特性|[特性](#attributes)|特性应是 <xref:System.Attribute?displayProperty=nameWithType> 类型或从该类型继承的类型。|41|
|特性|[特性](#attributes)|CLS 只允许自定义特性编码的子集。 这些编码中仅应显示的类型（请参阅第 IV 部分）：<xref:System.Type?displayProperty=nameWithType>、<xref:System.String?displayProperty=nameWithType>、<xref:System.Char?displayProperty=nameWithType>、<xref:System.Boolean?displayProperty=nameWithType>、<xref:System.Byte?displayProperty=nameWithType>、<xref:System.Int16?displayProperty=nameWithType>、<xref:System.Int32?displayProperty=nameWithType>、<xref:System.Int64?displayProperty=nameWithType>、<xref:System.Single?displayProperty=nameWithType>、<xref:System.Double?displayProperty=nameWithType> 以及基于符合 CLS 的基整数类型的任何枚举类型。|34|
|特性|[特性](#attributes)|CLS 不允许公开可见的所需修饰符（`modreq`，请参阅第 II 部分)，但允许其不了解的可选修饰符（`modopt`，请参阅第 II 部分）。|35|
|构造函数|[构造函数](#ctors)|在访问继承的实例数据之前，对象构造函数应调用其基类的某个实例构造函数。 （这不适用于不需要构造函数的值类型。)|21|
|构造函数|[构造函数](#ctors)|对象构造函数只能在创建对象的过程中调用，并且不应将对象初始化两次。|22|
|枚举|[枚举](#enums)|枚举的基础类型应是内置的 CLS 整数类型，字段的名称应为“value__”，并且应将该字段标记为 `RTSpecialName`。|7|
|枚举|[枚举](#enums)|有两种不同的枚举，二者由是否存在 <xref:System.FlagsAttribute?displayProperty=nameWithType>（请参阅第 IV 部分库）自定义特性指示。 一个表示命名的整数值；另一个表示命名的位标记（可合并以生成一个未命名的值）。 `enum` 的值不仅限于指定的值。|8|
|枚举|[枚举](#enums)|枚举的文本静态字段需具有枚举本身的类型。|9|
|事件|[事件](#events)|实现事件的方法将在元数据中标记为 `SpecialName`。|29|
|事件|[事件](#events)|事件及其访问器的可访问性应相同。|30|
|事件|[事件](#events)|事件的 `add` 和 `remove` 方法应同时存在或不存在。|31|
|事件|[事件](#events)|事件的 `add` 和 `remove` 方法均应采用一个参数，该参数的类型定义了事件的类型，且应派生自 <xref:System.Delegate?displayProperty=nameWithType>。|32|
|事件|[事件](#events)|事件应遵循特定的命名模式。 CLS 规则 29 中引用的 `SpecialName` 特性应在适当的名称比较中被忽略，且应遵循标识符规则。|33|
|异常|[异常](#exceptions)|引发的对象应为 <xref:System.Exception?displayProperty=nameWithType> 类型或从该类型继承的类型。 但是，阻止其他类型的异常的传播无需符合 CLS 的方法。|40|
|常规|[CLS 符合性：规则](#Rules)|CLS 规则仅适用于类型中在定义程序集之外可访问或可显示的部分。|1|
|常规|[CLS 符合性：规则](#Rules)|不应将不符合 CLS 的类型的成员标记为符合 CLS。|2|
|泛型|[泛型类型和成员](#Generics)|嵌套类型拥有的泛型形参的数目至少应与封闭类型的一样多。 嵌套类型中的泛型参数在位置上与其封闭类型中的泛型参数对应。|42|
|泛型|[泛型类型和成员](#Generics)|根据上面定义的规则，泛型类型的名称将对非嵌套类型上声明的或新引入到类型（如果嵌套）中的类型参数的数量进行编码。|43|
|泛型|[泛型类型和成员](#Generics)|泛型类型应该重新声明足够多的约束，以确保对基类型或接口的任何约束能够满足泛型类型约束的需要。|4444|
|泛型|[泛型类型和成员](#Generics)|用作对泛型参数的约束的类型本身应符合 CLS。|45|
|泛型|[泛型类型和成员](#Generics)|实例化泛型类型中的成员（包括嵌套类型）的可见性和可访问性应被认为限制在特定的实例化中，而不是限制在整个泛型类型声明中。 作出以下假定：CLS 规则 12 的可见性和可访问性仍适用。|46|
|泛型|[泛型类型和成员](#Generics)|对于每个抽象或虚拟泛型方法，应该有默认的具体（非抽象）实现。|47|
|接口|[接口](#Interfaces)|符合 CLS 的接口不需要不符合 CLS 的方法的定义即可实现这些方法。|18|
|接口|[接口](#Interfaces)|符合 CLS 的接口不应定义静态方法，也不应定义字段。|19|
|成员|[类型成员概述](#members)|全局静态字段和方法不符合 CLS。|36|
|成员|--|通过使用字段初始化元数据指定文本静态值。 符合 CLS 的文本必须在类型与文本（或基本类型，如果文本为 `enum`）完全相同的字段初始化元数据中指定值。|13|
|成员|[类型成员概述](#members)|vararg 约束不属于 CLS，并且 CLS 支持的唯一调用约定是标准托管调用约定。|15|
|命名约定|[命名约定](#naming)|针对用于管理允许启用且包含在标识符中的字符集的 Unicode Standard3.0 ，程序集应遵守其技术报告 15 的附件 7（可通过访问 <https://www.unicode.org/reports/tr15/tr15-18.html> 在线获得）。 标识符应是由 Unicode 范式 C 定义的规范格式。对于 CLS，如果两个标识符的小写映射（由不区分区域设置的 Unicode、一对一小写映射指定）相同，则它们也相同。 也就是说，对于要在 CLS 下视为不同的两个标识符，它们应以大小写之外的差别进行区分。 但是，若要重写继承的定义，CLI 需要对使用的原始声明进行准确编码。|4|
|重载|[命名约定](#naming)|在符合 CLS 的范围中引入的所有名称都应是明显独立的类型，除非名称完全相同且通过重载解析。 也就是说，CTS 允许单个类型对方法和字段使用相同的名称，但 CLS 不允许。|5|
|重载|[命名约定](#naming)|即使 CTS 允许区分不同的签名，但字段和嵌套类型只能由标识符比较区分。 （标识符比较后）具有相同名称的方法、属性和事件应在除返回类型不同之外还具有其他差异，CLS 规则 39 中指定的差异除外。|6|
|重载|[重载](#overloads)|只可重载属性和方法。|37|
|重载|[重载](#overloads)|属性和方法仅可基于其参数的数目和类型进行重载，名为 `op_Implicit` 和 `op_Explicit` 的转换运算符除外，这两种转换运算符也可基于其返回类型进行重载。|38|
|重载|--|如果类型中声明的两种或更多符合 CLS 的方法都具有相同的名称，并且对于特定的类型实例化集而言，它们具有相同的参数和返回类型，则所有这些方法在这些类型实例化时都应在语义上保持等效。|48|
|类型|[类型和类型成员签名](#Types)|<xref:System.Object?displayProperty=nameWithType> 符合 CLS。 任何其他符合 CLS 的类应从符合 CLS 的类继承。|23|
|属性|[属性](#properties)|实现属性的 getter 和 setter 方法的方法应在元数据中标记为 `SpecialName`。|24|
|属性|[属性](#properties)|属性的访问器必须同为静态、同为虚拟或同为实例。|26|
|属性|[属性](#properties)|属性的类型应该是 getter 的返回类型和 setter 的最后一个自变量的类型。 属性参数的类型应是对应于 getter 的参数的类型和 setter 的除最后一个参数之外所有参数的类型。 所有这些类型都应该符合 CLS，并且不应是托管指针（也就是说，不应该被引用传递）。|27|
|属性|[属性](#properties)|属性应遵循特定的命名模式。 CLS 规则 24 中引用的 `SpecialName` 特性将在适当名称比较中被忽略，且应遵循标识符规则。 属性应具有 getter 和/或 setter 方法。|28|
|类型转换|[类型转换](#conversion)|如果提供 `op_Implicit` 或 `op_Explicit`，则必须提供实现强制转换的替代方法。|39|
|类型|[类型和类型成员签名](#Types)|装箱的值类型不符合 CLS。|3|
|类型|[类型和类型成员签名](#Types)|显示在签名中的所有类型应符合 CLS。 构成实例化泛型类型的所有类型应符合 CLS。|11|
|类型|[类型和类型成员签名](#Types)|类型化的引用是不符合 CLS 的。|14|
|类型|[类型和类型成员签名](#Types)|非托管的指针类型不符合 CLS。|17|
|类型|[类型和类型成员签名](#Types)|符合 CLS 的类、值类型和接口不应要求实现不符合 CLS 的成员。|20|

<a name="Types"></a>

### <a name="types-and-type-member-signatures"></a>类型和类型成员签名

<xref:System.Object?displayProperty=nameWithType> 类型符合 CLS，并且是 .NET 类型系统中所有对象类型的基础类型。 .NET 中的继承要么是隐式的（例如，<xref:System.String> 类从 <xref:System.Object> 类隐式继承），要么是显式的（例如，<xref:System.Globalization.CultureNotFoundException> 类从 <xref:System.ArgumentException> 类显式继承，其中后者从 <xref:System.SystemException> 类显式继承，而该类又从 <xref:System.Exception> 类显式继承）。 对于要符合 CLS 的派生类型，其基本类型也必须符合 CLS。

下面的示例显示基本类型不符合 CLS 的派生类型。 它定义使用无符号 32 位整数作为计数器的基本 `Counter` 类。 由于类通过对无符号整数进行换行来提供计数器功能，因此类标记为不符合 CLS。 因此，派生类 `NonZeroCounter` 也不符合 CLS。

[!code-csharp[Conceptual.CLSCompliant#12](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/type3.cs#12)]
[!code-vb[Conceptual.CLSCompliant#12](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/type3.vb#12)]

成员签名中出现的所有类型（包括方法的返回类型或属性类型）必须符合 CLS。 此外，对于泛型类型：

- 构成实例化泛型类型的所有类型必须符合 CLS。

- 所有用作针对泛型参数的约束的类型必须符合 CLS。

.NET [通用类型系统](base-types/common-type-system.md)包括大量直接受公共语言运行时支持且专以程序集的元数据编码的内置类型。 在这些内部类型中，下表中所列的类型都符合 CLS。

|符合 CLS 的类型|描述|
|-------------------------|-----------------|
|<xref:System.Byte>|8 位无符号整数|
|<xref:System.Int16>|16 位带符号整数|
|<xref:System.Int32>|32 位带符号整数|
|<xref:System.Int64>|64 位带符号整数|
|<xref:System.Single>|单精度浮点值|
|<xref:System.Double>|双精度浮点值|
|<xref:System.Boolean>|`true` 或 `false` 值类型|
|<xref:System.Char>|UTF 16 编码单元|
|<xref:System.Decimal>|非浮点十进制数字|
|<xref:System.IntPtr>|平台定义的大小的指针或句柄|
|<xref:System.String>|包含零个、一个或多个 <xref:System.Char> 对象的集合|

下表中所列的内部类型不符合 CLS。

|不符合类型|描述|符合 CLS 的替代方法|
|-------------------------|-----------------|--------------------------------|
|<xref:System.SByte>|8 位带符号整数数据类型|<xref:System.Int16>|
|<xref:System.TypedReference>|指向对象及其运行时类型的指针|None|
|<xref:System.UInt16>|16 位无符号整数|<xref:System.Int32>|
|<xref:System.UInt32>|32 位无符号整数|<xref:System.Int64>|
|<xref:System.UInt64>|64 位无符号整数|<xref:System.Int64>（可能溢出）、<xref:System.Numerics.BigInteger> 或 <xref:System.Double>|
|<xref:System.UIntPtr>|未签名的指针或句柄|<xref:System.IntPtr>|

.NET 类库或任何其他类库可能包含不符合 CLS 的其他类型；例如：

- 装箱的值类型。 下面的 C# 示例创建一个具有名为 `int*` 的 `Value` 类型的公共属性的类。 由于 `int*` 是一个装箱的值类型，因此编译器将其标记为不符合 CLS。

  [!code-csharp[Conceptual.CLSCompliant#26](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/box2.cs#26)]

- 类型化的引用是一个特殊构造，它包含一个对对象的引用和一个对类型的引用。 类型化的引用由 <xref:System.TypedReference> 类显示在 .NET 中。

如果类型不符合 CLS，则需对其应用 <xref:System.CLSCompliantAttribute> 值为 `isCompliant` 的 `false` 特性。 有关更多信息，请参阅 [CLSCompliantAttribute 特性](#CLSAttribute)部分。

下面的示例说明了方法签名和泛型类型实例化中 CLS 遵从性的问题。 它定义一个具有类型为 `InvoiceItem` 的属性和类型为 <xref:System.UInt32> 的属性的 `Nullable(Of UInt32)` 类，以及一个具有类型为 <xref:System.UInt32> 和 `Nullable(Of UInt32)` 的参数的构造函数。 您尝试编译此示例时会收到四个编译器警告。

[!code-csharp[Conceptual.CLSCompliant#3](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/type1.cs#3)]
[!code-vb[Conceptual.CLSCompliant#3](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/type1.vb#3)]

若要消除编译器警告，请将 `InvoiceItem` 公共接口中不符合 CLS 的类型替换为符合 CLS 的类型：

[!code-csharp[Conceptual.CLSCompliant#4](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/type2.cs#4)]
[!code-vb[Conceptual.CLSCompliant#4](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/type2.vb#4)]

除了列出的特定类型之外，某些类型的类别也不符合 CLS。 其中包括非托管指针类型和函数指针类型。 下面的示例将生成一个编译器警告，因为它使用指向整数的指针来创建整数数组。

[!code-csharp[Conceptual.CLSCompliant#5](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/unmanagedptr1.cs#5)]

对于符合 CLS 的抽象类（即在 C# 中标记为 `abstract` 的累或在 Visual Basic 中标记为 `MustInherit` 的类），该类的所有成员也必须符合 CLS。

<a name="naming"></a>

### <a name="naming-conventions"></a>命名约定

由于一些编程语言不区分大小写，因此标识符（例如，命名空间、类型和成员的名称）必须不仅限于大小写不同。 如果两个标识符的小写映射是相同的，则这两个标识符被视为等效。 下面的 C# 示例定义两个公共类：`Person` 和 `person`。 由于它们只是大小写不同，因此 C# 编译器会将其标记为不符合 CLS。

[!code-csharp[Conceptual.CLSCompliant#16](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/naming1.cs#16)]

编程语言识别符（例如，命名空间、类型和成员的名称）必须遵照 [Unicode 标准 3.0、技术报告 15 和附件 7](https://www.unicode.org/reports/tr15/tr15-18.html)。 这表示：

- 标识符的第一个字符可以是任何 Unicode 大写字母、小写字母、标题大小写字母、修饰符字母、其他字母或字母数字。 有关 Unicode 字符类别的信息，请参阅 <xref:System.Globalization.UnicodeCategory?displayProperty=nameWithType> 枚举。

- 后继字符可以作为第一个字符来自任何类别，还可以包含无间隔标记、间距组合标记、十进制数字、连接器标点符号以及格式设置代码。

在比较标识符之前，应筛选格式设置代码，并将标识符转换为 Unicode 范式 C，因为单个字符可由多个 UTF 16 编码的代码单位表示。 在 Unicode 范式 C 中生成相同代码单位的字符序列不符合 CLS。 下列示例定义了一个名为 `Å` 的属性（包含字符 ANGSTROM SIGN (U+212B)）和另一个名为 `Å` 的属性（包含字符 LATIN CAPITAL LETTER A WITH RING ABOVE (U+00C5)）。 C# 和 Visual Basic 编译器都将源代码标记为不符合 CLS。

[!code-csharp[Conceptual.CLSCompliant#17](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/naming1.cs#17)]
[!code-vb[Conceptual.CLSCompliant#17](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/naming1.vb#17)]

特定范围内的成员名称（如程序集中的命名空间、命名空间中的类型或某类型中的成员）必须是唯一的，通过重载解析的名称除外。 此要求比常规类型系统的要求更加严格，后者允许一个范围内的多个成员在作为不同类型的成员（例如，一个是方法，一个是字段时）时，可以具有相同的名称。 具体而言，对于类型成员：

- 字段和嵌套类型只能通过名称区分。

- 具有相同名称的方法、属性和事件必须具有除返回类型不同之外的其他不同之处。

下面的示例说明了成员名称在其范围内必须是唯一的要求。 它定义了一个名为 `Converter` 的类，该类包括四个名为 `Conversion` 的成员。 其中三个是方法，一个是属性。 包含 <xref:System.Int64> 参数的方法具有唯一名称，但是，具有 <xref:System.Int32> 参数的两个方法不是，因为返回值不被视为成员签名的一部分。 由于属性不能具有与重载方法相同的名称，因此 `Conversion` 属性也违反了此要求。

[!code-csharp[Conceptual.CLSCompliant#19](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/naming3.cs#19)]
[!code-vb[Conceptual.CLSCompliant#19](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/naming3.vb#19)]

各种语言都包含唯一关键字，因此面向公共语言运行时的语言还必须提供一些机制，以便引用与关键字相符的标识符（例如，类型名称）。 例如，`case` 是 C# 和 Visual Basic 两者中的关键字。 但是，下面的 Visual Basic 示例可以通过使用左大括号和右大括号将名为 `case` 的类从 `case` 关键字中消除。 否则，该示例将生成错误消息“关键字无法用作标识符”，并且编译将失败。

[!code-vb[Conceptual.CLSCompliant#22](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/keyword1.vb#22)]

下面的 C# 示例可以通过使用 `case` 符号从语言关键字中消除标识符来实例化 `@` 类。 如果没有该符号，C# 编译器会显示两条错误消息：“应为类型”和“无效表达式术语‘case’”。

[!code-csharp[Conceptual.CLSCompliant#23](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/keyword2.cs#23)]

<a name="conversion"></a>

### <a name="type-conversion"></a>类型转换

公共语言规范定义了两个转换运算符：

- `op_Implicit` 用于扩大转换，不会丢失数据或精度。 例如，<xref:System.Decimal> 结构包含一个已重载的 `op_Implicit` 运算符，以将整数类型值和 <xref:System.Char> 值转换为 <xref:System.Decimal> 值。

- `op_Explicit` 用于收缩可能导致丢失量级（将一个值转换为一个范围更小的值）或精度的转换。 例如，<xref:System.Decimal> 结构包含一个已重载的 `op_Explicit` 运算符，以将 <xref:System.Double> 和 <xref:System.Single> 值转换为 <xref:System.Decimal>，并将 <xref:System.Decimal> 值转换为整数值：<xref:System.Double>、<xref:System.Single> 和 <xref:System.Char>。

但是，并非所有语言都支持运算符重载或定义自定义运算符。 如果选择实现这些转换运算符，您还应提供另一种执行转换的方法。 我们建议提供 `From`*Xxx* 和 `To`*Xxx* 方法。

下面的示例定义符合 CLS 的隐式和显式转换。 它创建表示带符号的双精度浮点数字的 `UDouble` 类。 它提供从 `UDouble` 到 <xref:System.Double> 的隐式转换和从 `UDouble` 到 <xref:System.Single>、从 <xref:System.Double> 到 `UDouble` 以及从 <xref:System.Single> 到 `UDouble` 的显式转换。 它还将 `ToDouble` 方法定义为隐式转换运算符的替代方法，并将 `ToSingle`、`FromDouble` 和 `FromSingle` 方法定义为显式转换运算符的替代方法。

[!code-csharp[Conceptual.CLSCompliant#15](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/convert1.cs#15)]
[!code-vb[Conceptual.CLSCompliant#15](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/convert1.vb#15)]

<a name="arrays"></a>

### <a name="arrays"></a>数组

符合 CLS 的数组符合以下规则：

- 数组的所有维度必须具有零下限。 下面的示例创建一个不符合 CLS 的数组，其下限为 1。 请注意，无论是否存在 <xref:System.CLSCompliantAttribute> 特性，编译器都不检测由 `Numbers.GetTenPrimes` 方法返回的数组是否符合 CLS。

  [!code-csharp[Conceptual.CLSCompliant#8](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/array1.cs#8)]
  [!code-vb[Conceptual.CLSCompliant#8](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/array1.vb#8)]

- 所有数组元素必须包括符合 CLS 的类型。 下面的示例定义返回不符合 CLS 的数组的两种方法。 第一个返回 <xref:System.UInt32> 值的数组。 第二个返回包括 <xref:System.Object> 和 <xref:System.Int32> 值的 <xref:System.UInt32> 数组。 虽然编译器因第一个数组是 <xref:System.UInt32> 类型而将其标识为不合规，但无法识别第二个包含不符合 CLS 元素的数组。

  [!code-csharp[Conceptual.CLSCompliant#9](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/array2.cs#9)]
  [!code-vb[Conceptual.CLSCompliant#9](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/array2.vb#9)]

- 具有数组参数的方法的重载决策基于它们是否为数组及它们的元素类型。 因此，以下对重载的 `GetSquares` 方法的定义符合 CLS。

  [!code-csharp[Conceptual.CLSCompliant#10](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/array3.cs#10)]
  [!code-vb[Conceptual.CLSCompliant#10](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/array3.vb#10)]

<a name="Interfaces"></a>

### <a name="interfaces"></a>接口

符合 CLS 的接口可以定义属性、事件和虚拟方法（没有实现的方法）。 符合 CLS 的接口不能有：

- 静态方法或静态字段。 如果您在接口中定义静态成员，那么 C# 和 Visual Basic 编译器将生成编译器错误。

- 字段。 如果您在接口中定义字段，则 C# 和 Visual Basic 编译器将生成编译器错误。

- 不符合 CLS 的方法。 例如，下面的接口定义包括方法 `INumber.GetUnsigned`，该方法标记为不符合 CLS。 此示例生成编译器警告。

  [!code-csharp[Conceptual.CLSCompliant#6](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/interface2.cs#6)]
  [!code-vb[Conceptual.CLSCompliant#6](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/interface2.vb#6)]

  由于存在此规则，因此符合 CLS 的类型不需要实现不符合 CLS 的成员。 如果一个符合 CLS 的框架公开实现不符合 CLS 接口的类，则其还应提供所有不符合 CLS 的成员的具体实现。

符合 CLS 的语言编译器还必须允许类提供在多个接口中具有相同名称和签名的成员的单独实现。  C# 和 Visual Basic 都支持[显式接口实现](../csharp/programming-guide/interfaces/explicit-interface-implementation.md)以提供同名方法的不同实现。 Visual Basic 还支持 `Implements` 关键字，可让您显式指定特定成员要实现的接口和成员。 下面的示例通过定义一个将 `Temperature` 和 `ICelsius` 接口作为显式接口实现的 `IFahrenheit` 类来说明此方案。

[!code-csharp[Conceptual.CLSCompliant#24](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/eii1.cs#24)]
[!code-vb[Conceptual.CLSCompliant#24](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/eii1.vb#24)]

<a name="enums"></a>

### <a name="enumerations"></a>枚举

符合 CLS 的枚举必须遵循下列规则：

- 枚举的基础类型必须是符合 CLS 的内部整数（<xref:System.Byte>、<xref:System.Int16>、<xref:System.Int32> 或 <xref:System.Int64>）。 例如，下面的代码尝试定义基础类型为 <xref:System.UInt32> 的枚举并生成编译器警告。

  [!code-csharp[Conceptual.CLSCompliant#7](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/enum3.cs#7)]
  [!code-vb[Conceptual.CLSCompliant#7](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/enum3.vb#7)]

- 枚举类型必须具有名为 `Value__` 且标有 <xref:System.Reflection.FieldAttributes.RTSpecialName?displayProperty=nameWithType> 特性的单个实例字段。 这使得您可以隐式引用字段值。

- 枚举包括文本静态字段，该字段的类型与枚举本身的类型匹配。 例如，如果您用 `State` 和 `State.On` 的值定义 `State.Off` 枚举，则 `State.On` 和 `State.Off` 都是文本静态字段，其类型为 `State`。

- 有两种枚举：

  - 一种表示一组互斥的命名整数值的枚举。 这种类型的枚举由缺少 <xref:System.FlagsAttribute?displayProperty=nameWithType> 自定义特性表示。

  - 一种表示可结合用来生成未命名值的一组位标志的枚举。 这种类型的枚举由存在 <xref:System.FlagsAttribute?displayProperty=nameWithType> 自定义特性表示。

  有关详细信息，请参阅 <xref:System.Enum> 结构的文档。

- 枚举的值不限于其指定值的范围。 换言之，枚举中的值的范围是其基础值的范围。 您可以使用 <xref:System.Enum.IsDefined%2A?displayProperty=nameWithType> 方法来确定指定的值是否为枚举成员。

<a name="members"></a>

### <a name="type-members-in-general"></a>类型成员概述

公共语言规范要求将所有字段和方法作为特定类的成员进行访问。 因此，全局静态字段和方法（即，除类型外定义的静态字段或方法）不符合 CLS。 如果您尝试在您的源代码中包括全局字段或方法，则 C# 和 Visual Basic 编译器都会生成编译器错误。

公共语言规范仅支持标准托管调用约定。 它不支持非托管调用约定和带使用 `varargs` 关键字标记的变量参数列表的方法。 对于符合标准托管调用约定的变量自变量列表，请使用 <xref:System.ParamArrayAttribute> 特性或单个语言的实现，如 C# 中的 `params` 关键字和 Visual Basic 中的 `ParamArray` 关键字。

<a name="MemberAccess"></a>

### <a name="member-accessibility"></a>成员可访问性

重写继承成员不能更改该成员的可访问性。 例如，无法在派生类中通过私有方法重写基类中的公共方法。 有一个例外：由其他程序集中的类型重写的程序集中的 `protected internal`（在 C# 中）或 `Protected Friend`（在 Visual Basic 中）成员。 在该示例中，重写的可访问性是 `Protected`。

下面的示例说明了当 <xref:System.CLSCompliantAttribute> 特性设置为 `true`，并且 `Human`（它是派生自 `Animal` 的类）尝试将 `Species` 属性的可访问性从公开更改为私有时生成的错误。 如果该示例的可访问性更改为公共，则其编译成功。

[!code-csharp[Conceptual.CLSCompliant#28](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/accessibility1.cs#28)]
[!code-vb[Conceptual.CLSCompliant#28](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/accessibility1.vb#28)]

如果某个成员是可访问的，则该成员签名中的类型必须是可访问的。 例如，这意味着公共成员不能包含类型是私有的、受保护的或内部的参数。 下面的示例说明了当 `StringWrapper` 类构造函数公开一个用于确定如何包装字符串值的内部 `StringOperationType` 枚举值时出现的编译器错误。

[!code-csharp[Conceptual.CLSCompliant#27](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/accessibility3.cs#27)]
[!code-vb[Conceptual.CLSCompliant#27](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/accessibility3.vb#27)]

<a name="Generics"></a>

### <a name="generic-types-and-members"></a>泛型类型和成员

嵌套类型拥有的泛型参数数目总是至少与封闭类型的一样多。 它们按位置对应于封闭类型中的泛型参数。 泛型类型还可以包括新的泛型参数。

一个包含类型及其嵌套类型的泛型类型参数之间的关系可能由各种语言的语法隐藏。 在下面的示例中，泛型类型 `Outer<T>` 包含两个嵌套的类：`Inner1A` 和 `Inner1B<U>`。 对于每个类从 `ToString` 继承的 <xref:System.Object.ToString%2A?displayProperty=nameWithType> 方法的调用，表示每个嵌套的类包括其包含的类的类型参数。

[!code-csharp[Conceptual.CLSCompliant#29](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/nestedgenerics2.cs#29)]
[!code-vb[Conceptual.CLSCompliant#29](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/nestedgenerics2.vb#29)]

泛型类型名称采用 name\`n 格式进行编码，其中 name 是类型名称，\` 是字符文本，而 n 是针对类型声明的参数数目，或对于嵌套泛型类型为最近引入的类型参数的数目  。 此泛型类型名称的编码主要对使用反射来访问库中符合 CLS 的泛型类型的开发人员很有用。

如果将约束应用于泛型类型，则任何用作约束的类型也必须符合 CLS。 下面的示例定义一个名为 `BaseClass` 的不符合 CLS 的类和一个其类型参数必须派生自 `BaseCollection` 的名为 `BaseClass` 的泛型类。 但由于 `BaseClass` 不符合 CLS，因此编译器会发出警告。

[!code-csharp[Conceptual.CLSCompliant#34](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/generics5.cs#34)]
[!code-vb[Conceptual.CLSCompliant#34](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/generics5.vb#34)]

如果泛型类型派生自泛型基本类型，则其必须重新声明所有约束，以确保也满足对基本类型的约束。 下面的示例定义可表示任何数值类型的 `Number<T>`。 它还定义表示浮点值的 `FloatingPoint<T>` 类。 但是，源代码无法编译，因为它未将 `Number<T>` 上（T 必须是值类型）的约束应用于 `FloatingPoint<T>`。

[!code-csharp[Conceptual.CLSCompliant#30](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/generics2a.cs#30)]
[!code-vb[Conceptual.CLSCompliant#30](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/generics2a.vb#30)]

如果将此约束添加到 `FloatingPoint<T>` 类中，则该示例成功编译。

[!code-csharp[Conceptual.CLSCompliant#31](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/generics2.cs#31)]
[!code-vb[Conceptual.CLSCompliant#31](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/generics2.vb#31)]

公共语言规范对嵌套类型和受保护成员规定了一个保守的按实例化模型。 开放式泛型类型不能公开具有包含嵌套的、受保护的泛型类型的特定实例化的签名的字段或成员。 扩大泛型基类或接口的特定实例化的非泛型类型不能公开带签名的字段或成员，此类字段或成员包含嵌套的、受保护的泛型类型的不同实例化。

下面的示例定义一个泛型类型 `C1<T>`（或 Visual Basic 中的 `C1(Of T)`）和一个受保护的类 `C1<T>.N`（或 Visual Basic 中的 `C1(Of T).N`）。 `C1<T>` 有两个方法：`M1` 和 `M2`。 但是，`M1` 并不符合 CLS，因为它试图从 C1\<T>（或 `C1(Of T)`）返回一个 `C1<int>.N`（或 `C1(Of Integer).N`）对象。 另一个名为 `C2` 的类派生自 `C1<long>`（或 `C1(Of Long)`）。 它有两个方法：`M3` 和 `M4`。 `M3` 不符合 CLS，因为它尝试从 `C1<int>.N` 的子类中返回 `C1(Of Integer).N`（或 `C1<long>`）对象。 请注意，语言编译器可具有更高限制。 在此示例中，Visual Basic 在尝试编译 `M4` 时显示错误。

[!code-csharp[Conceptual.CLSCompliant#32](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/generics4.cs#32)]
[!code-vb[Conceptual.CLSCompliant#32](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/generics4.vb#32)]

<a name="ctors"></a>

### <a name="constructors"></a>构造函数

符合 CLS 的类和结构中的构造函数必须遵循下列规则：

- 派生类的构造函数必须先调用其基类的实例构造函数，然后才能访问继承的实例数据。 存在此要求是因为基类构造函数并不由它们的派生类继承。 此规则不适用于不支持直接继承的结构。

  通常，编译器独立地强制实施 CLS 遵从性的此规则，如下面的示例所示。 它创建派生自 `Doctor` 类的 `Person` 类，但 `Doctor` 类无法调用 `Person` 类构造函数来初始化继承的实例字段。

  [!code-csharp[Conceptual.CLSCompliant#11](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/ctor1.cs#11)]
  [!code-vb[Conceptual.CLSCompliant#11](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/ctor1.vb#11)]

- 只有在创建对象时才能调用对象构造函数。 此外，不能将一个对象初始化两次。 例如，这意味着 <xref:System.Object.MemberwiseClone%2A?displayProperty=nameWithType> 和反序列化方法（如 <xref:System.Runtime.Serialization.Formatters.Binary.BinaryFormatter.Deserialize%2A?displayProperty=nameWithType>）不得调用构造函数。

<a name="properties"></a>

### <a name="properties"></a>属性

符合 CLS 的类型的属性必须遵循下列规则：

- 属性必须具有 setter 和/或 getter。 在程序集中，这些作为特殊方法实现，这意味着它们将显示为单独的方法（getter 命名为 `get_`*propertyname*，setter 命名为 `set_`*propertyname*），且在程序集元数据中标记为 `SpecialName`。 C# 和 Visual Basic 编译器会自动执行此规则，而无需应用 <xref:System.CLSCompliantAttribute> 特性。

- 属性的类型是属性 getter 的返回类型和 setter 的最后一个自变量。 这些类型必须符合 CLS，并且不能通过引用将参数分配到属性中（即它们不能为托管指针）。

- 如果属性包含 getter 和 setter 两者，则它们必须都是虚拟的、静态的或实例。 C# 和 Visual Basic 编译器通过它们的属性定义语法自动执行此规则。

<a name="events"></a>

### <a name="events"></a>事件

事件由其名称和类型定义。 事件类型是用于指示事件的委托。 例如，<xref:System.AppDomain.AssemblyResolve?displayProperty=nameWithType> 事件的类型为 <xref:System.ResolveEventHandler>。 除事件本身外，带有基于事件名称的名称的三种方法提供事件的实现并在程序集的元数据中标记为 `SpecialName`：

- 用于添加事件处理程序的名为 `add_`*EventName* 的方法。 例如，<xref:System.AppDomain.AssemblyResolve?displayProperty=nameWithType> 事件的事件订阅方法名为 `add_AssemblyResolve`。

- 用于移除事件处理程序的名为 `remove_`*EventName* 的方法。 例如，<xref:System.AppDomain.AssemblyResolve?displayProperty=nameWithType> 事件的移除方法名为 `remove_AssemblyResolve`。

- 用于指示事件已发生的名为 `raise_`*EventName* 的方法。

> [!NOTE]
> 大多数关于事件的公共语言规范的规则都通过语言编译器实施，且对组件开发人员是透明的。

用于添加、移除和引发事件的方法必须拥有相同的可访问性。 它们还必须都为静态、实例或虚拟的。 用于添加和移除事件的方法具有一个类型为事件委托类型的参数。 添加和移除方法必须同时存在或同时不存在。

如果两个读取之间的温度更改等于或超过阈值，则下面的示例将定义一个名为 `Temperature` 的类，该类会引发 `TemperatureChanged` 事件且符合 CLS。 `Temperature` 类显式定义 `raise_TemperatureChanged` 方法，以便其可以有选择地执行事件处理程序。

[!code-csharp[Conceptual.CLSCompliant#20](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/event1.cs#20)]
[!code-vb[Conceptual.CLSCompliant#20](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/event1.vb#20)]

<a name="overloads"></a>

### <a name="overloads"></a>Overloads

公共语言规范对重载成员有下列要求：

- 成员可以根据参数数量和任何参数的类型进行重载。 在重载间进行区分时，不考虑应用于方法或其参数的调用约定、返回类型、自定义修饰符，也不考虑是按照值还是引用传递参数。 有关示例，请参阅[命名约定](#naming)部分中名称在范围内必须是唯一的代码需求。

- 只可重载属性和方法。 无法重载字段和事件。

- 泛型方法可以基于其泛型参数的数目进行重载。

> [!NOTE]
> `op_Explicit` 和 `op_Implicit` 运算符是返回值不被视为重载决策的方法签名的一部分的规则的例外情况。 可以基于这两个运算符的参数和返回值对其进行重载。

<a name="exceptions"></a>

### <a name="exceptions"></a>异常

异常对象必须从 <xref:System.Exception?displayProperty=nameWithType> 派生或从派生自 <xref:System.Exception?displayProperty=nameWithType> 的另一种类型派生。 下面的示例说明了当名为 `ErrorClass` 的自定义类用于异常处理时产生的编译器错误。

[!code-csharp[Conceptual.CLSCompliant#13](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/exceptions1.cs#13)]
[!code-vb[Conceptual.CLSCompliant#13](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/exceptions1.vb#13)]

若要更正此错误，`ErrorClass` 类必须继承自 <xref:System.Exception?displayProperty=nameWithType>。 此外，必须重写 `Message` 属性。 下面的示例更正这些错误以定义符合 CLS 的 `ErrorClass` 类。

[!code-csharp[Conceptual.CLSCompliant#14](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/exceptions2.cs#14)]
[!code-vb[Conceptual.CLSCompliant#14](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/exceptions2.vb#14)]

<a name="attributes"></a>

### <a name="attributes"></a>特性

在 .NET 程序集中，自定义特性提供了一个可扩展机制，用于存储自定义特性和检索有关编程对象（如程序集、类型、成员和方法参数）的元数据。 自定义特性必须从 <xref:System.Attribute?displayProperty=nameWithType> 派生或从派生自 <xref:System.Attribute?displayProperty=nameWithType> 的类型派生。

下面的示例与此规则冲突。 它定义了不是从 `NumericAttribute` 派生的 <xref:System.Attribute?displayProperty=nameWithType> 类。 请注意，编译器错误仅当应用不符合 CLS 的特性时会出现，而在定义类时不会出现。

[!code-csharp[Conceptual.CLSCompliant#18](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/attribute1.cs#18)]
[!code-vb[Conceptual.CLSCompliant#18](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/attribute1.vb#18)]

构造函数或符合 CLS 的特性的属性只能公开以下类型：

- <xref:System.Boolean>

- <xref:System.Byte>

- <xref:System.Char>

- <xref:System.Double>

- <xref:System.Int16>

- <xref:System.Int32>

- <xref:System.Int64>

- <xref:System.Single>

- <xref:System.String>

- <xref:System.Type>

- 基础类型为 <xref:System.Byte>、<xref:System.Int16>、<xref:System.Int32> 或 <xref:System.Int64> 的任意枚举类型。

下面的示例定义了从 `DescriptionAttribute` 派生的 <xref:System.Attribute> 类。 类构造函数具有 `Descriptor` 类型的参数，因此，该类不符合 CLS。 请注意，C# 编译器发出警告，但编译成功，而 Visual Basic 编译器既不发出警告也不报告错误。

[!code-csharp[Conceptual.CLSCompliant#33](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/attribute2.cs#33)]
[!code-vb[Conceptual.CLSCompliant#33](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/attribute2.vb#33)]

<a name="CLSAttribute"></a>

## <a name="the-clscompliantattribute-attribute"></a>CLSCompliantAttribute 特性

<xref:System.CLSCompliantAttribute> 特性用于指示程序元素是否使用公共语言规范进行编译。 <xref:System.CLSCompliantAttribute.%23ctor%28System.Boolean%29> 构造函数包含一个所需参数（`isCompliant`），此参数指示该程序元素是否符合 CLS。

编译时，编译器检测到假定为符合 CLS 的不合规元素，并发出警告。 编译器不会对显式声明为不符合标准的类型或成员发出警告。

组件开发人员可以通过两种方法使用 <xref:System.CLSCompliantAttribute> 特性：

- 定义由组件公开的公共接口的符合 CLS 的部件以及不符合 CLS 的部件。 如果特性用于将特定程序元素标记为符合 CLS，则使用该特性可保证能通过面向 .NET 的所有语言和工具访问这些元素。

- 确保组件库的公共接口仅公开符合 CLS 的程序元素。 如果元素不符合 CLS，则编译器通常会发出一个警告。

> [!WARNING]
> 在某些情况下，语言编译器执行符合 CLS 的规则，而不管是否使用 <xref:System.CLSCompliantAttribute> 特性。 例如，在接口中定义静态成员会违反 CLS 规则。 就这一点而言，如果在接口中定义 `static`（在 C# 中）或 `Shared`（在 Visual Basic 中）成员，C# 和 Visual Basic 编译器都会显示错误消息，且无法编译应用。

<xref:System.CLSCompliantAttribute> 特性标记为具有 <xref:System.AttributeUsageAttribute> 值的 <xref:System.AttributeTargets.All?displayProperty=nameWithType> 特性。 利用此值，您可以将 <xref:System.CLSCompliantAttribute> 特性应用于任何程序元素，包括程序集、模块、类型（类、结构、枚举、接口和委托）、类型成员（构造函数、方法、属性、字段和事件）、参数、泛型参数和返回值。 但实际上，您只应将该特性应用于程序集、类型和类型成员。 否则，编译器在库的公共接口中遇到不符合标准的参数、泛型参数或返回值时，将忽略此特性并继续生成编译器警告。

<xref:System.CLSCompliantAttribute> 特性的值由包含的程序元素继承。 例如，如果程序集标记为符合 CLS，则其类型也符合 CLS。 如果类型标记为符合 CLS，则其嵌套的类型和成员也符合 CLS。

您可以通过将 <xref:System.CLSCompliantAttribute> 特性应用到包含的编程元素来显式重写继承的遵从性。 例如，可以使用具有 <xref:System.CLSCompliantAttribute> 值为 `isCompliant` 的 `false` 特性来定义符合标准的程序集中的不符合标准的类型，还可以使用 `isCompliant` 值为 `true` 的特性来定义不符合标准的程序集中的符合标准的类型。 您还可以在符合标准的类型中定义不符合标准的成员。 但是，不符合标准的类型无法拥有符合标准的成员，因此您无法使用 `isCompliant` 值为 `true` 的特性从一个不符合标准的类型重写继承。

在开发组件时，应始终使用 <xref:System.CLSCompliantAttribute> 特性来指示您的程序集、其类型及其成员是否符合 CLS。

创建符合 CLS 的组件：

1. 使用 <xref:System.CLSCompliantAttribute> 将程序集标记为符合 CLS。

2. 将程序集中不符合 CLS 的所有公开的类型标记为不符合标准。

3. 将符合 CLS 的类型中的所有公开的成员标记为不符合标准。

4. 为不符合 CLS 的成员提供符合 CLS 的替代项。

如果已成功标记所有不符合标准的类型和成员，您的编译器不会发出任何不符合警告。 但是，您应指出哪些成员不符合 CLS 并在产品文档中列出其不符合 CLS 的替代项。

下面的示例使用 <xref:System.CLSCompliantAttribute> 特性定义符合 CLS 的程序集和类型 `CharacterUtilities`，该类型具有两个不符合 CLS 的成员。 由于这两个成员标记有 `CLSCompliant(false)` 特性，因此编译器不生成任何警告。 该类还为两种方法提供符合 CLS 的替代项。 通常，我们只向 `ToUTF16` 方法添加两个重载，以便提供符合 CLS 的替代项。 但是，由于无法基于返回值重载这些方法，因此符合 CLS 的方法的名称不同于不符合标准的方法的名称。

[!code-csharp[Conceptual.CLSCompliant#35](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.clscompliant/cs/indicator3.cs#35)]
[!code-vb[Conceptual.CLSCompliant#35](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.clscompliant/vb/indicator3.vb#35)]

如果您开发的是应用程序而不是库（即，如果不公开可由其他应用程序开发人员使用的类型或成员），则只有在您的语言不支持程序元素时，您的应用程序使用的程序元素的 CLS 遵从性才会引起关注。 在这种情况下，当您尝试使用不符合 CLS 的元素时，您的语言编译器将生成错误。

<a name="CrossLang"></a>

## <a name="cross-language-interoperability"></a>跨语言互操作性

语言独立性可能有许多含义。 [语言独立性和与语言无关的组件](language-independence-and-language-independent-components.md)一文讨论了其中一个含义，其涉及将用一种语言编写的类型无缝地用于用另一种语言编写的应用程序。 本文介绍第二个含义，涉及将用多种语言编写的代码组合到一个 .NET 程序集。

以下示例通过创建一个名为 Utilities.dll 的包含两个类（`NumericLib` 和 `StringLib`）的类库演示了跨语言互操作性。 `NumericLib` 类用 C# 编写类，`StringLib` 类用 Visual Basic 编写。 以下是 StringUtil.vb 的源代码，该源代码在其 `ToTitleCase` 类中包含一个成员`StringLib`。

[!code-vb[Conceptual.CrossLanguage#1](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.crosslanguage/vb/stringutil.vb#1)]

以下是 NumberUtil.cs 的源代码，定义具有 `NumericLib` 和 `IsEven` 两个成员的 `NearZero` 类。

[!code-csharp[Conceptual.CrossLanguage#2](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.crosslanguage/cs/numberutil.cs#2)]

若要将两个类打包到单个程序集中，必须将它们编译到模块中。 要将 Visual Basic 源代码文件编译到模块，请使用此命令：

```console
vbc /t:module StringUtil.vb
```

有关 Visual Basic 编译器的命令行语法的详细信息，请参阅[从命令行生成](../visual-basic/reference/command-line-compiler/building-from-the-command-line.md)。

要将 C# 源代码文件编译到模块，请使用此命令：

```console
csc /t:module NumberUtil.cs
```

然后，可以使用[链接器选项](/cpp/build/reference/linker-options)将两个模块编译到一个程序集：

```console
link numberutil.netmodule stringutil.netmodule /out:UtilityLib.dll /dll
```

然后以下实例调用 `NumericLib.NearZero` 和 `StringLib.ToTitleCase` 方法。 请注意 Visual Basic 代码和 C# 代码都能够访问这两个类中的方法。

[!code-csharp[Conceptual.CrossLanguage#3](../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.crosslanguage/cs/useutilities1.cs#3)]
[!code-vb[Conceptual.CrossLanguage#3](../../samples/snippets/visualbasic/VS_Snippets_CLR/conceptual.crosslanguage/vb/useutilities1.vb#3)]

要编译 Visual Basic 代码，请使用此命令：

```console
vbc example.vb /r:UtilityLib.dll
```

要使用 C# 进行编译，请将编译器的名称从 **vbc** 更改为 **csc**，将文件扩展名从 .vb 更改为 .cs：

```console
csc example.cs /r:UtilityLib.dll
```

## <a name="see-also"></a>请参阅

- <xref:System.CLSCompliantAttribute>

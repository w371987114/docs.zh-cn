---
title: 中断性变更：全球化 API 在 Windows 上使用 ICU 库
description: 了解 .NET 5 中的全球化中断性变更：ICU 库用于全球化功能而不是 NLS。
ms.date: 05/19/2020
ms.openlocfilehash: 4b8580fcb3ba3c9b95357a7922e3a3062ccd3728
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256746"
---
# <a name="globalization-apis-use-icu-libraries-on-windows"></a>全球化 API 在 Windows 上使用 ICU 库

当在 Windows 10 2019 年 5 月更新或更高版本上运行时，.NET 5 及更高版本使用 [Unicode 国际组件 (ICU)](http://site.icu-project.org/home) 库来实现全球化功能。

## <a name="change-description"></a>更改描述

在 .NET Core 1.0 - 3.1 和 .NET Framework 4 及更高版本中，.NET 库使用[本地语言支持 (NLS)](/windows/win32/intl/national-language-support) API 来实现 Windows 上的全球化功能。 例如，NLS 函数用于比较字符串，获取区域性信息，并在适当的区域中执行字符串大小写。

从 .NET 5 开始，如果应用在 Windows 10 2019 年 5 月更新或更高版本上运行，.NET 库将默认使用 [ICU](http://site.icu-project.org/home) 全球化 API。

> [!NOTE]
> Windows 10 2019 年 5 月更新及更高版本随 ICU 本机库一起提供。 如果 .NET 运行时无法加载 ICU，它将改用 NLS。

## <a name="behavioral-differences"></a>行为差异

即使你不知道正在使用全球化设施，你也可能会在应用中看到更改。 本部分列出了你可能会看到的一些行为更改，但还有其他一些行为更改。

### <a name="stringindexof"></a>String.IndexOf

请考虑使用以下代码，它调用 <xref:System.String.IndexOf(System.String)?displayProperty=nameWithType> 来查找字符串中的换行符索引。

```csharp
string s = "Hello\r\nworld!";
int idx = s.IndexOf("\n");
Console.WriteLine(idx);
```

- 在 Windows 上的早期版本的 .NET 中，代码片段打印 `6`。
- 在 Windows 19H1 和更高版本上的 .NET 5 及更高版本中，代码片段会打印 `-1`。

若要通过执行序号搜索而不是区分区域性的搜索来修复此代码，请调用 <xref:System.String.IndexOf(System.String,System.StringComparison)> 重载，并传入 <xref:System.StringComparison.Ordinal?displayProperty=nameWithType> 作为参数。

可以运行代码分析规则 [CA1307：为了清晰起见，请指定 StringComparison](../../../../fundamentals/code-analysis/quality-rules/ca1307.md) 和 [CA1309：使用序号 StringComparison](../../../../fundamentals/code-analysis/quality-rules/ca1309.md) 在代码中查找这些调用站点。

有关详细信息，请参阅[在 .NET 5 及更高版本中比较字符串时的行为更改](../../../../standard/base-types/string-comparison-net-5-plus.md)。

### <a name="currency-symbol"></a>货币符号

请考虑使用以下代码，它使用货币格式说明符 `C` 设置字符串格式。 当前线程的区域性设置为仅包括语言（而非国家/地区）的区域性。

```csharp
System.Threading.Thread.CurrentThread.CurrentCulture = new System.Globalization.CultureInfo("de");
string text = string.Format("{0:C}", 100);
```

- 在 Windows 上的早期版本的 .NET 中，文本值为 `"100,00 €"`。
- 在 Windows 19H1 和更高版本上的 .NET 5 及更高版本中，文本值为 `"100,00 ¤"`，它使用国际货币符号而不是欧元。 在 ICU 中，这种设计是指，货币是国家或地区的属性，而不是语言。

## <a name="reason-for-change"></a>更改原因

引入此更改是为了统一所有支持的操作系统上的 .NET 的全球化行为。 它还能让应用程序捆绑自己的全球化库，而不是依赖于操作系统的内置库。

## <a name="version-introduced"></a>引入的版本

.NET 5.0

## <a name="recommended-action"></a>建议操作

开发人员一方不需要执行任何操作。 但是，如果你想要继续使用 NLS 全球化 API，则可以将[运行时开关](../../../run-time-config/globalization.md#nls)设置为还原到该行为。 有关可用开关的详细信息，请参阅 [.NET 全球化和 ICU](../../../../standard/globalization-localization/globalization-icu.md) 一文。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Span%601?displayProperty=fullName>
- <xref:System.String?displayProperty=fullName>
- <xref:System.Globalization?displayProperty=fullName> 命名空间中的大多数类型
- <xref:System.Array.Sort%2A?displayProperty=fullName>（对字符串数组进行排序时）
- <xref:System.Collections.Generic.List%601.Sort?displayProperty=fullName>（当列表元素为字符串时）
- <xref:System.Collections.Generic.SortedDictionary%602?displayProperty=fullName>（当键为字符串时）
- <xref:System.Collections.Generic.SortedList%602?displayProperty=fullName>（当键为字符串时）
- <xref:System.Collections.Generic.SortedSet%601?displayProperty=fullName>（当集包含字符串时）

<!--

### Affected APIs

- ``T:System.Span`1``
- `T:System.String`
- `N:System.Globalization`
- `Overload:System.Array.Sort`
- ``M:System.Collections.Generic.List`1.Sort``
- ``T:System.Collections.Generic.SortedDictionary`2``
- ``T:System.Collections.Generic.SortedList`2``
- ``T:System.Collections.Generic.SortedSet`1``

### Category

- Core .NET libraries
- Globalization

-->

---
title: 中断性变更：可以为 Null 的引用类型注释的更改
description: 了解核心 .NET 库中的 .NET 6 中断性变更：一些可以为 null 的引用类型注释已更改。
ms.date: 02/11/2021
ms.openlocfilehash: fe56fd3006528238713bd08f353b5044092043a2
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256928"
---
# <a name="changes-to-nullable-reference-type-annotations"></a>对可以为 Null 的引用类型注释的更改

在 .NET 6 中，.NET 库中的一些“为 Null 性”注释已更改。

## <a name="change-description"></a>更改描述

在以前的 .NET 版本中，一些可以为 Null 的引用类型注释不正确，并且生成警告要么缺失要么不正确。 从 .NET 6 开始，之前应用的一些注释已更新。 对于受影响的 API，将会生成新的生成警告，并且不再生成错误的生成警告。

其中的某些更改被视为“中断性”变更，因为它们可能会导致新的生成时警告。 迁移到 .NET 6 时，需要更新引用这些 API 的代码。

此页还介绍了其他不被视为中断性变更的更改。 任何引用已更新的 API 的代码都可从删除不再必要的运算符或 pragma 中受益。

## <a name="version-introduced"></a>引入的版本

6.0

## <a name="reason-for-change"></a>更改原因

从 .NET Core 3.0 开始，已将“为 Null 性”注释应用于 .NET 库。 自这项工作伊始，已预料到这些注释中的错误。 通过反馈和进一步测试，已确定受影响 API 的“为 Null 性”注释是不准确的。 更新后的注释正确地表示了 API 的“为 Null 性”协定。

## <a name="recommended-action"></a>建议的操作

更新调用这些 API 的代码，以反映修订后的“为 Null 性”协定。

## <a name="affected-apis"></a>受影响的 API

下表列出了受影响的 API：

| API | 更改内容 | 中断性或非中断性 | 新增的版本 |
| - | - | - |
| <xref:System.ComponentModel.ISite.Container?displayProperty=nameWithType> | 属性类型可以为 Null | 重大 | 预览版 1 |
| <xref:System.Xml.Linq.XContainer.Add(System.Object[])?displayProperty=nameWithType> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XContainer.AddFirst(System.Object[])?displayProperty=nameWithType> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XContainer.ReplaceNodes(System.Object[])?displayProperty=nameWithType> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XDocument.%23ctor(System.Object[])> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XDocument.%23ctor(System.Xml.Linq.XDeclaration,System.Object[])> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XElement.%23ctor(System.Xml.Linq.XName,System.Object[])> | 第二个参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XElement.ReplaceAll(System.Object[])?displayProperty=nameWithType> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XElement.ReplaceAttributes(System.Object[])?displayProperty=nameWithType> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XNode.AddAfterSelf(System.Object[])?displayProperty=nameWithType> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XNode.AddBeforeSelf(System.Object[])?displayProperty=nameWithType> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XNode.ReplaceWith(System.Object[])?displayProperty=nameWithType> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XStreamingElement.%23ctor(System.Xml.Linq.XName,System.Object)> | 第二个参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XStreamingElement.%23ctor(System.Xml.Linq.XName,System.Object[])> | 第二个参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Xml.Linq.XStreamingElement.Add(System.Object[])?displayProperty=nameWithType> | 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Net.Http.HttpClient.PatchAsync%2A?displayProperty=nameWithType> | `content` 参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Net.Http.HttpClient.PostAsync%2A?displayProperty=nameWithType> | `content` 参数类型可以为 Null  | 非中断性 | 预览版 1 |
| <xref:System.Net.Http.HttpClient.PutAsync%2A?displayProperty=nameWithType> | `content` 参数类型可以为 Null  | 非中断性 | 预览版 1 |
| <xref:System.Linq.Expressions.MethodCallExpression.Update(System.Linq.Expressions.Expression,System.Collections.Generic.IEnumerable{System.Linq.Expressions.Expression})?displayProperty=nameWithType> | 第一个参数类型可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Linq.Expressions.Expression%601.Update(System.Linq.Expressions.Expression,System.Collections.Generic.IEnumerable{System.Linq.Expressions.ParameterExpression})?displayProperty=nameWithType> | 返回类型不可以为 Null | 非中断性 | 预览版 1 |
| <xref:System.Data.IDataRecord.GetBytes(System.Int32,System.Int64,System.Byte[],System.Int32,System.Int32)?displayProperty=nameWithType> | `buffer` 参数类型可以为 Null | 重大 | 预览版 1 |
| <xref:System.Data.IDataRecord.GetChars(System.Int32,System.Int64,System.Char[],System.Int32,System.Int32)?displayProperty=nameWithType> | `buffer` 参数类型可以为 Null | 重大 | 预览版 1 |
| <xref:System.Data.Common.DbDataRecord.GetBytes(System.Int32,System.Int64,System.Byte[],System.Int32,System.Int32)?displayProperty=nameWithType> | `buffer` 参数类型可以为 Null | 重大 | 预览版 1 |
| <xref:System.Data.Common.DbDataRecord.GetChars(System.Int32,System.Int64,System.Char[],System.Int32,System.Int32)?displayProperty=nameWithType> | `buffer` 参数类型可以为 Null | 重大 | 预览版 1 |
| <xref:System.Net.HttpListenerContext.AcceptWebSocketAsync%2A?displayProperty=fullName> | `subProtocol` 参数类型可以为 Null | 非中断性 | 预览版 2 |
| 替代 <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> 的方法和[其他很多返回 `bool` 的方法](https://github.com/dotnet/runtime/pull/47598/files) | `[NotNullWhen(true)]` 已添加到第一个可为 null 的参数中 | 重大 | 预览版 2 |

## <a name="see-also"></a>另请参阅

- [ASP.NET Core 中可以为 null 的引用类型注释的更改](../../aspnet-core/6.0/nullable-reference-type-annotations-changed.md)

<!--

### Category

Core .NET libraries

### Affected APIs

- `P:System.ComponentModel.ISite.Container`
- `M:System.Xml.Linq.XContainer.Add(System.Object[])`
- `M:System.Xml.Linq.XContainer.AddFirst(System.Object[])`
- `M:System.Xml.Linq.XContainer.ReplaceNodes(System.Object[])`
- `M:System.Xml.Linq.XDocument.#ctor(System.Object[])`
- `M:System.Xml.Linq.XDocument.#ctor(System.Xml.Linq.XDeclaration,System.Object[])`
- `M:System.Xml.Linq.XElement.#ctor(System.Xml.Linq.XName,System.Object[])`
- `M:System.Xml.Linq.XElement.ReplaceAll(System.Object[])`
- `M:System.Xml.Linq.XElement.ReplaceAttributes(System.Object[])`
- `M:System.Xml.Linq.XNode.AddAfterSelf(System.Object[])`
- `M:System.Xml.Linq.XNode.AddBeforeSelf(System.Object[])`
- `M:System.Xml.Linq.XNode.ReplaceWith(System.Object[])`
- `M:System.Xml.Linq.XStreamingElement.#ctor(System.Xml.Linq.XName,System.Object)`
- `M:System.Xml.Linq.XStreamingElement.#ctor(System.Xml.Linq.XName,System.Object[])`
- `M:System.Xml.Linq.XStreamingElement.Add(System.Object[])`
- `O:System.Net.Http.HttpClient.PatchAsync`
- `O:System.Net.Http.HttpClient.PostAsync`
- `O:System.Net.Http.HttpClient.PutAsync`
- `M:System.Linq.Expressions.MethodCallExpression.Update(System.Linq.Expressions.Expression,System.Collections.Generic.IEnumerable{System.Linq.Expressions.Expression})`
- `M:System.Linq.Expressions.Expression%601.Update(System.Linq.Expressions.Expression,System.Collections.Generic.IEnumerable{System.Linq.Expressions.ParameterExpression})`
- `M:System.Data.IDataRecord.GetBytes(System.Int32,System.Int64,System.Byte[],System.Int32,System.Int32)`
- `M:System.Data.IDataRecord.GetChars(System.Int32,System.Int64,System.Char[],System.Int32,System.Int32)`
- `M:System.Data.Common.DbDataRecord.GetBytes(System.Int32,System.Int64,System.Byte[],System.Int32,System.Int32)`
- `M:System.Data.Common.DbDataRecord.GetChars(System.Int32,System.Int64,System.Char[],System.Int32,System.Int32)`

-->

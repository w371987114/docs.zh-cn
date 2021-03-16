---
title: 中断性变更：反序列化 char 需要单字符的字符串
description: 了解 .NET 5 中的中断性变更：反序列化为 char 目标时，System.Text.Json 在 JSON 中需要单字符字符串。
ms.date: 12/15/2020
ms.openlocfilehash: e901f8ee7e7521af948a3bcde5cf969640436f7f
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256330"
---
# <a name="systemtextjson-requires-single-char-string-to-deserialize-a-char"></a>System.Text.Json 需要使用单字符字符串才能反序列化 char

若要成功使用 <xref:System.Text.Json> 反序列化 <xref:System.Char>，JSON 字符串必须包含单字符。

## <a name="change-description"></a>更改描述

在 .NET 早期版本中，JSON 中的多 `char` 字符串会被成功地反序列化为 `char` 属性或字段。 只使用字符串的第一个 `char`，如以下示例所示：

```csharp
// .NET Core 3.0 and 3.1: Returns the first char 'a'.
// .NET 5 and later: Throws JsonException because payload has more than one char.
char deserializedChar = JsonSerializer.Deserialize<char>("\"abc\"");
```

在 .NET 5 及更高版本中，当反序列化目标为 `char` 时，除单 `char` 字符串以外的任何内容都会导致引发 <xref:System.Text.Json.JsonException>。 以下示例字符串在所有 .NET 版本中都已成功反序列化：

```csharp
// Correct usage.
char deserializedChar = JsonSerializer.Deserialize<char>("\"a\"");
```

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="reason-for-change"></a>更改原因

仅在提供的有效负载对目标类型有效时，反序列化分析才会成功。 对于 `char` 类型，唯一有效的有效负载是单 `char` 字符串。

## <a name="recommended-action"></a>建议的操作

将 JSON 反序列化为 `char` 目标时，请确保字符串包含单 `char`。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Text.Json.JsonSerializer.Deserialize%2A?displayProperty=fullName>

<!--

### Affected APIs

- `Overload:System.Text.Json.JsonSerializer.Deserialize`

### Category

Serialization

-->

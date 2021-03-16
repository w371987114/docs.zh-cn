---
title: 中断性变更：将 PropertyNamingPolicy、PropertyNameCaseInsensitive 和 Encoder 选项用于键值对
description: 了解 .NET 5 中的中断性变更：在序列化和反序列化键值对实例的 Key 和 Value 属性名称时，将使用 PropertyNamingPolicy、PropertyNameCaseInsensitive 和 Encoder 选项。
ms.date: 10/18/2020
ms.openlocfilehash: fe6298a677488574fdd7bdc7e887ed3b244ba8d6
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256317"
---
# <a name="propertynamingpolicy-propertynamecaseinsensitive-and-encoder-options-are-honored-when-serializing-and-deserializing-key-value-pairs"></a>对键值对进行序列化和反序列化时，使用“PropertyNamingPolicy”、“PropertyNameCaseInsensitive”和“Encoder”选项

现在，在序列化 <xref:System.Collections.Generic.KeyValuePair%602> 实例的 <xref:System.Collections.Generic.KeyValuePair%602.Key> 和 <xref:System.Collections.Generic.KeyValuePair%602.Value> 属性名称时，<xref:System.Text.Json.JsonSerializer> 将使用 <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy> 和 <xref:System.Text.Json.JsonSerializerOptions.Encoder> 选项。 此外，在反序列化 <xref:System.Collections.Generic.KeyValuePair%602> 实例时，<xref:System.Text.Json.JsonSerializer> 将使用 <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy> 和 <xref:System.Text.Json.JsonSerializerOptions.PropertyNameCaseInsensitive> 选项。

## <a name="change-description"></a>更改描述

### <a name="serialization"></a>序列化

在 .NET Core 3.x 版本和 [System.Text.Json NuGet 包](https://www.nuget.org/packages/System.Text.Json)的 4.6.0 - 4.7.2 版本中，<xref:System.Collections.Generic.KeyValuePair%602> 实例的属性始终准确序列化为“Key”和“Value”，而不考虑任何 <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy?displayProperty=nameWithType> 和 <xref:System.Text.Json.JsonSerializerOptions.Encoder?displayProperty=nameWithType> 选项。 下面的代码示例演示在序列化后 <xref:System.Collections.Generic.KeyValuePair%602.Key> 和 <xref:System.Collections.Generic.KeyValuePair%602.Value> 属性如何不采用 camel 大小写，即使指定的属性命名策略要求这样做也是如此。

```csharp
var options = new JsonSerializerOptions { PropertyNamingPolicy = JsonNamingPolicy.CamelCase };
KeyValuePair<int, int> kvp = KeyValuePair.Create(1, 1);
Console.WriteLine(JsonSerializer.Serialize(kvp, options));
// Expected: {"key":1,"value":1}
// Actual: {"Key":1,"Value":1}
```

从 .NET 5 开始，序列化 <xref:System.Collections.Generic.KeyValuePair%602> 实例时，将使用 <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy> 和 <xref:System.Text.Json.JsonSerializerOptions.Encoder> 选项。 下面的代码示例演示在序列化后 <xref:System.Collections.Generic.KeyValuePair%602.Key> 和 <xref:System.Collections.Generic.KeyValuePair%602.Value> 属性如何按照指定的属性命名策略采用 camel 大小写。

```csharp
var options = new JsonSerializerOptions { PropertyNamingPolicy = JsonNamingPolicy.CamelCase };
KeyValuePair<int, int> kvp = KeyValuePair.Create(1, 1);
Console.WriteLine(JsonSerializer.Serialize(kvp, options));
// {"key":1,"value":1}
```

### <a name="deserialization"></a>反序列化

在 .NET Core 3.x 版本和 [System.Text.Json NuGet 包](https://www.nuget.org/packages/System.Text.Json)的 4.7.x 版本中，如果 JSON 属性名称不精确地表示为 `Key` 和 `Value`（例如，它们不以大写字母开头），则会引发 <xref:System.Text.Json.JsonException>。 即使指定的属性命名策略明确允许该操作，也会引发该异常。

```csharp
var options = new JsonSerializerOptions { PropertyNamingPolicy = JsonNamingPolicy.CamelCase };
string json = @"{""key"":1,""value"":1}";
// Throws JsonException.
JsonSerializer.Deserialize<KeyValuePair<int, int>>(json, options);
```

从 .NET 5 开始，使用 <xref:System.Text.Json.JsonSerializer> 进行反序列化时，将使用 <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy> 和 <xref:System.Text.Json.JsonSerializerOptions.PropertyNameCaseInsensitive> 选项。 例如，下面的代码片段演示了小写的 <xref:System.Collections.Generic.KeyValuePair%602.Key> 和 <xref:System.Collections.Generic.KeyValuePair%602.Value> 属性名称的成功反序列化，因为指定的属性命名策略允许这样做。

```csharp
var options = new JsonSerializerOptions { PropertyNamingPolicy = JsonNamingPolicy.CamelCase };
string json = @"{""key"":1,""value"":1}";

KeyValuePair<int, int> kvp = JsonSerializer.Deserialize<KeyValuePair<int, int>>(json);
Console.WriteLine(kvp.Key); // 1
Console.WriteLine(kvp.Value); // 1
```

为了适应在以前版本中序列化的有效负载，“Key”和“Value”采用特殊大小写，以在反序列化时匹配。 即使 <xref:System.Collections.Generic.KeyValuePair%602.Key> 和 <xref:System.Collections.Generic.KeyValuePair%602.Value> 属性名称未根据以下代码示例中的 <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy> 选项采用 camel 大小写，它们也会成功进行反序列化。

```csharp
var options = new JsonSerializerOptions { PropertyNamingPolicy = JsonNamingPolicy.CamelCase };
string json = @"{""Key"":1,""Value"":1}";

KeyValuePair<int, int> kvp = JsonSerializer.Deserialize<KeyValuePair<int, int>>(json);
Console.WriteLine(kvp.Key); // 1
Console.WriteLine(kvp.Value); // 1
```

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="reason-for-change"></a>更改原因

大量客户反馈表明应使用 <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy>。 出于完整性考虑，还使用了 <xref:System.Text.Json.JsonSerializerOptions.PropertyNameCaseInsensitive> 和 <xref:System.Text.Json.JsonSerializerOptions.Encoder> 选项，使 <xref:System.Collections.Generic.KeyValuePair%602> 实例与任何其他普通旧 CLR 对象 (POCO) 得到相同的处理。

## <a name="recommended-action"></a>建议的操作

如果此更改对你造成干扰，则可以使用[自定义转换器](../../../../standard/serialization/system-text-json-converters-how-to.md)来实现所需语义。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Text.Json.JsonSerializer.Serialize%2A?displayProperty=fullName>
- <xref:System.Text.Json.JsonSerializer.SerializeToUtf8Bytes%2A?displayProperty=fullName>
- <xref:System.Text.Json.JsonSerializer.SerializeAsync%2A?displayProperty=fullName>
- <xref:System.Text.Json.JsonSerializer.Deserialize%2A?displayProperty=fullName>
- <xref:System.Text.Json.JsonSerializer.DeserializeAsync%2A?displayProperty=fullName>

<!--

### Affected APIs

- `Overload:System.Text.Json.JsonSerializer.Serialize`
- `Overload:System.Text.Json.JsonSerializer.SerializeAsync`
- `Overload:System.Text.Json.JsonSerializer.SerializeToUtf8Bytes`
- `Overload:System.Text.Json.JsonSerializer.Deserialize`
- `Overload:System.Text.Json.JsonSerializer.DeserializeAsync`

### Category

Serialization

-->

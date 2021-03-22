---
title: 中断性变更：ASP.NET Core 应用允许反序列化带引号的数字
description: 了解 .NET 5 中的中断性变更：ASP.NET Core 应用将成功反序列化以 JSON 字符串表示的数字，而不会引发异常。
ms.date: 10/21/2020
ms.openlocfilehash: bc1531bb9b159bfd9e80eafacafb50aa509973cc
ms.sourcegitcommit: d623f686701b94bef905ec5e93d8b55d031c5d6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2021
ms.locfileid: "103624144"
---
# <a name="aspnet-core-apps-allow-deserializing-quoted-numbers"></a>ASP.NET Core 应用允许反序列化带引号的数字

从 .NET 5 开始，ASP.NET Core 应用使用 <xref:System.Text.Json.JsonSerializerDefaults.Web?displayProperty=nameWithType> 指定的默认反序列化选项。 <xref:System.Text.Json.JsonSerializerDefaults.Web> 选项集包括将 <xref:System.Text.Json.JsonSerializerOptions.NumberHandling> 设置为 <xref:System.Text.Json.Serialization.JsonNumberHandling.AllowReadingFromString?displayProperty=nameWithType>。 此更改意味着 ASP.NET Core 应用将成功反序列化以 JSON 字符串表示的数字，而不会引发异常。

## <a name="change-description"></a>更改描述

在 .NET Core 3.0 - 3.1 中，如果 <xref:System.Text.Json.JsonSerializer> 在 JSON 有效负载中遇到带引号的数字，则在反序列化过程中将引发 <xref:System.Text.Json.JsonException>。 带引号的数字用于映射对象图中的数字属性。 在 .NET Core 3.0 - 3.1 中，仅从 <xref:System.Text.Json.JsonTokenType.Number?displayProperty=nameWithType> 令牌中读取数字。

从 .NET 5 开始，对于 ASP.NET Core 应用，JSON 有效负载中带引号的数字被默认视为有效。 不会在反序列化带引号的数字期间引发异常。

> [!TIP]
>
> - 默认的独立 <xref:System.Text.Json.JsonSerializer> 或 <xref:System.Text.Json.JsonSerializerOptions> 没有任何行为更改。
> - 从技术上说，这并不是一项中断性变更，因为这样做会使方案更宽松，而不是更严格（也就是说，它会强制来自 JSON 字符串的数字，而不是引发异常）。 然而，由于这是一项重大的行为变更，会影响许多 ASP.NET Core 应用，因此在此处进行了说明。
> - <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A?displayProperty=nameWithType> 和 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A?displayProperty=nameWithType> 扩展方法还使用 <xref:System.Text.Json.JsonSerializerDefaults.Web> 序列化选项集。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="reason-for-change"></a>更改原因

多个用户已请求在 <xref:System.Text.Json.JsonSerializer> 中进行更宽松的数字处理的选项。 此反馈表明，许多 JSON 创建方（例如 Web 上的服务）发出带引号的数字。 通过允许读取带引号的数字（反序列化），.NET 应用可以在 Web 上下文中（默认情况下）成功解析这些有效负载。 该配置通过 <xref:System.Text.Json.JsonSerializerDefaults.Web?displayProperty=nameWithType> 公开，因此你可以在不同的应用程序层（例如客户端、服务器和共享）上指定相同的选项。

## <a name="recommended-action"></a>建议操作

如果此更改是中断性的，例如，如果依赖于验证的严格数字处理，则可以重新启用以前的行为。 将 <xref:System.Text.Json.JsonSerializerOptions.NumberHandling?displayProperty=nameWithType> 选项设置为 <xref:System.Text.Json.Serialization.JsonNumberHandling.Strict?displayProperty=nameWithType>。

对于 ASP.NET Core MVC 和 Web API 应用，可以使用以下代码在 `Startup` 中配置选项：

```csharp
services.AddControllers()
   .AddJsonOptions(options => options.JsonSerializerOptions.NumberHandling = JsonNumberHandling.Strict);
```

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Text.Json.JsonSerializer.Deserialize%2A?displayProperty=fullName>
- <xref:System.Text.Json.JsonSerializer.DeserializeAsync%2A?displayProperty=fullName>

<!--

### Affected APIs

- `Overload:System.Text.Json.JsonSerializer.Deserialize`
- `Overload:System.Text.Json.JsonSerializer.DeserializeAsync`

### Category

- ASP.NET Core
- Serialization

-->

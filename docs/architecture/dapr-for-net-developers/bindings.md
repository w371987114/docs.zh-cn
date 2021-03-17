---
title: Dapr 绑定构建基块
description: 绑定构建基块及其功能、优点和应用方法的说明
author: edwinvw
ms.date: 02/17/2021
ms.openlocfilehash: d6f8b2aa90b15e5b9cd7b5c29938660d1b2907e9
ms.sourcegitcommit: d623f686701b94bef905ec5e93d8b55d031c5d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2021
ms.locfileid: "103623949"
---
# <a name="the-dapr-bindings-building-block"></a>Dapr 绑定构建基块

基于云的 *无服务器* 产品（如 AZURE FUNCTIONS 和 AWS Lambda）已跨分布式体系结构空间获得广泛的采用。 在许多方面，它们使微服务能够 *处理来自* 外部系统的事件或在外部系统 *中调用事件* ，从而消除了底层复杂性和管道问题。 外部资源很多：它们包括跨不同平台和供应商的数据存储、消息系统和 web 资源。 [Dapr 绑定构建块](https://docs.dapr.io/developing-applications/building-blocks/bindings/bindings-overview/)将这些相同的资源绑定功能引入 Dapr 应用程序的 doorstep。

## <a name="what-it-solves"></a>解决方法

通过 Dapr 资源绑定，你的服务可以将业务运营与直接应用程序之外的外部资源集成。 来自外部系统的事件可能会触发服务中传递上下文信息的操作。 然后，你的服务可以通过在另一个外部系统中触发事件来展开操作，同时传递上下文有效负载信息。 你的服务不需要耦合或识别外部资源就进行通信。 该管道封装在预定义的 Dapr 组件中。 在运行时，可以轻松地在运行时交换 Dapr 组件，而无需更改代码。

例如，当用户推文关键字时，请考虑触发事件的 Twitter 帐户。 服务公开用于接收和处理推文的事件处理程序。 完成后，服务将触发调用外部 Twilio 服务的事件。 Twilio 发送包含推文的短信。 图8-1 显示了此操作的概念体系结构。

![输入绑定](media/bindings/bindings-architecture.png)

**图 8-1**。 Dapr 资源绑定的概念体系结构。

乍一看，资源绑定行为可能与本书前面介绍的 [发布/订阅模式](publish-subscribe.md) 类似。 尽管它们共享相似之处，但也有不同之处。 发布/订阅侧重于 Dapr services 之间的异步通信。 资源绑定具有更大的范围。 它侧重于软件平台之间的系统互操作性。 在不同的应用程序、数据存储和微服务应用程序之外的服务之间交换信息。

## <a name="how-it-works"></a>工作原理

Dapr 资源绑定以组件配置文件开头。 此 YAML 文件描述要与其配置设置一起绑定的资源类型。 配置后，你的服务可以接收来自资源的事件或触发事件的事件。

> [!NOTE]
> 稍后将在 " *组件* " 部分中详细介绍绑定配置。

### <a name="input-bindings"></a>输入绑定

输入绑定通过外部资源的传入事件触发代码。 若要接收事件和数据，请从服务中注册一个将成为 *事件处理程序* 的公共终结点。 图8-2 显示了流：

![Dapr 输入绑定流](media/bindings/input-binding-flow.png)

**图 8-2**。 Dapr 输入绑定流。

图8.2 介绍了从外部 Twitter 帐户接收事件的步骤：

1. Dapr 挎斗读取绑定配置文件并订阅为外部资源指定的事件。 在此示例中，事件源是一个 Twitter 帐户。
1. 当在 Twitter 上发布了匹配的推文时，在 Dapr 挎斗中运行的绑定组件会选取它并触发一个事件。
1. Dapr 挎斗调用 (的终结点，该终结点为绑定配置) 事件处理程序。 在此示例中，服务在 `/tweet` 端口6000上侦听终结点上的 HTTP POST。 由于它是 HTTP POST 操作，因此在请求正文中传递事件的 JSON 有效负载。
1. 处理事件后，服务将返回 HTTP 状态代码 `200 OK` 。

以下 ASP.NET Core 控制器提供了一个处理受 Twitter 绑定触发的事件的示例：

```csharp
[ApiController]
public class SomeController : ControllerBase
{
    public class TwitterTweet
    {
        [JsonPropertyName("id_str")]
        public string ID {get; set; }

        [JsonPropertyName("text")]
        public string Text {get; set; }
    }

    [HttpPost("/tweet")]
    public ActionResult Post(TwitterTweet tweet)
    {
        // Handle tweet
        Console.WriteLine("Tweet received: {0}: {1}", tweet.ID, tweet.Text);

        // ...

        // Acknowledge message
        return Ok();
    }
}
```

如果操作错误，则会返回相应的400或500级别的 HTTP 状态代码。 对于 *至少传递一次* 的绑定，Dapr 挎斗将重试触发器。 查看 [有关资源绑定的 Dapr 文档] [1]，以查看它们是否提供了 " *至少一次* *" 或 "一次性交付* 保证"。

### <a name="output-bindings"></a>输出绑定

Dapr 还包括 *输出绑定* 功能。 它们使服务能够触发调用外部资源的事件。 同样，您首先配置描述输出绑定的绑定配置 YAML 文件。 一旦准备好，就会触发一个事件，该事件在应用程序的 Dapr 挎斗上调用绑定 API。 图8-3 显示了输出绑定的流：

![Dapr 输出绑定流](media/bindings/output-binding-flow.png)

**图 8-3**。 Dapr 输出绑定流。

1. Dapr 挎斗读取绑定配置文件，其中包含有关如何连接到外部资源的信息。 在此示例中，外部资源是 Twilio 的 SMS 帐户。
1. 应用程序在 `/v1.0/bindings/sms` Dapr 挎斗上调用终结点。 在这种情况下，它使用 HTTP POST 来调用 API。 还可以使用 gRPC。
1. 在 Dapr 挎斗中运行的绑定组件会调用外部消息系统来发送消息。 消息将包含 POST 请求中传递的负载。

例如，你可以通过使用卷调用 Dapr API 来调用输出绑定：

```console
curl -X POST http://localhost:3500/v1.0/bindings/sms \
  -H "Content-Type: application/json" \
  -d '{
        "data": "Welcome to this awesome service",
        "metadata": {
          "toNumber": "555-3277"
        },
        "operation": "create"
      }'
```

请注意，HTTP 端口与 Dapr 挎斗 (（在本例中为默认 Dapr HTTP 端口) 使用的端口相同 `3500` 。

负载 (的结构是，发送) 的消息将因绑定而异。 在上面的示例中，负载包含包含 `data` 消息的元素。 与其他类型的外部资源的绑定可能不同，尤其是对于发送的元数据。 每个负载还必须包含一个 `operation` 字段，该字段定义绑定将执行的操作。 上面的示例指定了 `create` 创建 SMS 消息的操作。 常见操作包括：

- create
- get
- delete
- list

绑定由绑定支持的操作的作者。 每个绑定的文档描述了可用的操作以及调用方法。

## <a name="using-the-dapr-net-sdk"></a>使用 Dapr .NET SDK

Dapr .NET SDK 为 .NET Core 开发人员提供特定于语言的支持。 在下面的示例中，对的调用 `HttpClient.PostAsync()` 将替换为 `DaprClient.InvokeBindingAsync()` 方法。 此专用方法简化了调用已配置的输出绑定的操作：

```csharp
private async Task SendSMSAsync([FromServices] DaprClient daprClient)
{
    var message = "Welcome to this awesome service";
    var metadata = new Dictionary<string, string>
    {
      { "toNumber", "555-3277" }
    };
    await daprClient.InvokeBindingAsync("sms", "create", message, metadata);
}
```

此方法需要 `metadata` 和 `message` 值。

当用于调用绑定时，将 `DaprClient` 使用 gRPC 来调用 Dapr 挎斗上的 DAPR API。

## <a name="binding-components"></a>绑定组件

在后台，资源绑定是通过 Dapr 绑定组件实现的。 它们由社区提供，并写入。 如果需要将与不存在 Dapr 绑定的外部资源集成，可以自行创建。 查看 [Dapr 组件-contrib](https://github.com/dapr/components-contrib) 存储库，了解如何参与绑定。

> [!NOTE]
> Dapr 及其所有组件都是以 [Golang](https://golang.org/) () 语言编写的。 转向成为新式的云本机编程平台。

使用 YAML 配置文件配置绑定。 下面是用于 Twitter 绑定的示例配置：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: twitter-mention
  namespace: default
spec:
  type: bindings.twitter
  version: v1
  metadata:
  - name: consumerKey
    value: "****" # twitter api consumer key, required
  - name: consumerSecret
    value: "****" # twitter api consumer secret, required
  - name: accessToken
    value: "****" # twitter api access token, required
  - name: accessSecret
    value: "****" # twitter api access secret, required
  - name: query
    value: "dapr" # your search query, required
```

每个绑定配置都包含一个包含 `metadata` 和字段的常规元素 `name` `namespace` 。 Dapr 将根据配置的字段确定要调用的服务的终结点 `name` 。 在上面的示例中，Dapr 将在 `/twitter-mention` 事件发生时调用在服务中批注的方法。

在 `spec` 元素中，指定绑定的 `type` 以及绑定特定的 `metadata` 。 该示例指定了用于通过其 API 访问 Twitter 帐户的凭据。 元数据可在输入绑定和输出绑定之间有所不同。 例如，若要使用 Twitter 作为输入绑定，需要使用字段指定要在推文中搜索的文本 `query` 。 每次发送匹配的推文时，Dapr 挎斗将调用 `/twitter-mention` 服务上的终结点。 它还将提供推文的内容。

可以将绑定配置为输入和/或输出。 有趣的是，绑定未显式指定输入或输出配置。 相反，方向通过使用绑定以及配置值进行推断。

[资源绑定的 Dapr 文档] [1] 提供了可用绑定的完整列表及其特定的配置设置。

### <a name="cron-binding"></a>Cron 绑定

请密切注意 Dapr 的 Cron 绑定。 它不订阅外部系统中的事件。 相反，此绑定使用可配置的间隔计划来触发你的应用程序。 绑定提供了一种简单的方法来实现后台工作线程唤醒，并定期执行一些工作，而无需实现具有可配置延迟的无限循环。 下面是 Cron 绑定配置的示例：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: checkOrderBacklog
  namespace: default
spec:
  type: bindings.cron
  version: v1
  metadata:
  - name: schedule
    value: "@every 30m"
```

在此示例中，Dapr 通过 `/checkOrderBacklog` 每隔30分钟调用终结点来触发服务。 有多种可用于指定值的模式 `schedule` 。 有关详细信息，请参阅 [Cron 绑定文档](https://docs.dapr.io/operations/components/setup-bindings/supported-bindings/cron/)。

## <a name="reference-application-eshopondapr"></a>引用应用程序： eShopOnDapr

随附的 eShopOnDapr 引用应用程序实现了输出绑定示例。 它触发 Dapr [SendGrid](https://docs.dapr.io/operations/components/setup-bindings/supported-bindings/sendgrid/) 绑定，以便在发出新订单时向用户发送电子邮件。 可以在 "组件" 文件夹的文件中找到此绑定 `eshop-email.yaml` ：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: sendmail
  namespace: eshop
spec:
  type: bindings.twilio.sendgrid
  version: v1
  metadata:
  - name: apiKey
    secretKeyRef:
      name: sendGridAPIKey
auth:
  secretStore: eshop-secretstore
```

此配置使用 [Twilio SendGrid](https://github.com/dapr/components-contrib/tree/master/bindings/twilio) 绑定组件。 请注意，用于连接到服务的 API 密钥使用 Dapr 机密引用。 此方法保留配置文件之外的机密。 请参阅 [机密构建块一章](secrets.md) ，详细了解 Dapr 机密。

绑定配置指定一个绑定组件，该组件可使用 `/sendmail` Dapr 挎斗上的终结点进行调用。 以下代码片段将在每次启动订单时发送电子邮件：

```csharp
public Task Handle(OrderStartedDomainEvent notification, CancellationToken cancellationToken)
{
    var string message = CreateEmailBody(notification);
    var metadata = new Dictionary<string, string>
    {
        {"emailFrom", "eShopOn@dapr.io"},
        {"emailTo", notification.UserName},
        {"subject", $"Your eShopOnDapr order #{notification.Order.Id}"}
    };
    return _daprClient.InvokeBindingAsync("sendmail", "create", message, metadata, cancellationToken);
}
```

如本示例中所示， `message` 包含消息正文。 `CreateEmailBody`方法只是将字符串的格式设置为正文文本。 指定电子邮件 `metadata` 发件人、收件人和电子邮件的主题。 如果这些值是静态的，则也可以将它们包含在配置文件的元数据字段中。 要调用的绑定的名称为 `sendmail` ，并且操作为 `create` 。

## <a name="summary"></a>摘要

Dapr 资源绑定使你可以与不同的外部资源和系统集成，而无需依赖于其库或 Sdk。 这些外部系统不一定必须是消息传递系统，例如服务总线或消息代理。 数据存储和 web 资源（如 Twitter 或 SendGrid）还存在绑定。

输入绑定 (或触发器) 对外部系统中发生的事件做出响应。 它们调用在你的应用程序中预先配置的公共 HTTP 终结点。 Dapr 使用配置中的绑定名称来确定要在应用程序中调用的终结点。

输出绑定将消息发送到外部系统。 通过在 `/v1.0/bindings/<binding-name>` Dapr 挎斗上的终结点上执行 HTTP POST 来触发输出绑定。 还可以使用 gRPC 来调用绑定。 .NET SDK 提供 `InvokeBindingAsync` 使用 gRPC 调用 Dapr 绑定的方法。

使用 Dapr 组件实现绑定。 这些组件由社区提供。 每个绑定组件的配置都有特定于它所抽象的外部系统的元数据。 此外，它支持的命令和负载的结构将因绑定组件而异。

### <a name="references"></a>参考

- [资源绑定的 Dapr 文档](https://docs.dapr.io/operations/components/setup-bindings/supported-bindings/)

>[!div class="step-by-step"]
>[上一页](publish-subscribe.md)
>[下一页](observability.md)

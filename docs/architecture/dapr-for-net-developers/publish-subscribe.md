---
title: Dapr publish & 订阅构建基块
description: Dapr publish 的说明 & 订阅构造块以及如何应用它
author: edwinvw
ms.date: 02/07/2021
ms.openlocfilehash: 4cb249bafc8241eb54153be2605c33d0a3adb619
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102604679"
---
# <a name="the-dapr-publish--subscribe-building-block"></a>Dapr publish & 订阅构建基块

[发布-订阅模式](/azure/architecture/patterns/publisher-subscriber) (通常称为 "发布/订阅" ) 是众所周知且广泛使用的消息模式。 架构师通常在分布式应用程序中采用它。 但是，实现此方法的管道可能会很复杂。 在不同的消息传送产品中，通常会有细微的功能差异。 Dapr 提供了一个构建基块，可显著简化实现发布/订阅功能。

## <a name="what-it-solves"></a>解决方法

Publish-Subscribe 模式的主要优点是 **松散耦合**，有时称为 [临时分离](/azure/architecture/guide/technology-choices/messaging#decoupling)。 模式将发送消息的服务分离 (**发布** 服务器上的服务) 从使用 **订阅服务器**)  (消息。 发布服务器和订阅服务器都不能识别每个订阅服务器，两者都依赖于分散消息的集中式 **消息代理** 。

图7-1 显示了发布/订阅模式的高级体系结构。

![发布/订阅模式](./media/publish-subscribe/pub-sub-pattern.png)

图 7-1。 发布/订阅模式。

从上图中，记下模式的步骤：

1. 发布服务器将消息发送到消息代理。
1. 订阅服务器将绑定到消息代理上的订阅。
1. 消息代理将消息的副本转发给感兴趣的订阅。
1. 订阅服务器从其订阅使用消息。

大多数消息代理封装了一种在收到消息后可以持久保存消息的排队机制。 使用它，消息代理可通过存储消息来保证 **持久性** 。 当发布者发送消息时，订阅服务器无需立即可用，甚至不需要联机。 订阅者将接收并处理消息。  Dapr 为消息传递 **至少保证一次** 语义。 发布消息后，该消息将至少传递到任何相关订户。

 > 如果服务只能处理一次消息，则需要提供 [幂等性检查](/azure/architecture/microservices/design/api-design#idempotent-operations) ，确保不会多次处理同一消息。 虽然这种逻辑可以编码，但某些消息代理（如 Azure 服务总线）提供内置的 *重复检测* 消息传递功能。

有多种可用的消息代理产品-商业和开源。 每个都有优点和缺点。 你的工作是将系统要求与相应的 broker 匹配。 选择后，最佳做法是将应用程序与消息代理程序分离。 可以通过在 *抽象* 内包装代理来实现此功能。 抽象封装消息检测并向代码公开一般的发布/订阅操作。 你的代码与抽象而不是实际的消息代理进行通信。 在明智的决定下，你必须编写和维护抽象及其基础实现。 此方法需要自定义代码，这些代码可能会很复杂、重复性并且容易出错。

Dapr publish & 订阅构建基块提供现成的消息传递抽象和实现。 您必须编写的自定义代码在 Dapr 构建基块内预生成和封装。 绑定到它并使用它。 你和你的团队将重点放在创建可为客户带来价值的业务功能上，而不是编写消息传递管道代码。

## <a name="how-it-works"></a>工作原理

Dapr publish & 订阅构建基块提供了平台无关的 API 框架来发送和接收消息。 你的服务将消息发布到一个命名 [主题](/azure/service-bus-messaging/service-bus-queues-topics-subscriptions#topics-and-subscriptions)。 你的服务订阅主题来使用消息。

服务在 Dapr 挎斗上调用 pub/sub API。 然后，挎斗将调用一个预定义的 Dapr pub/sub 组件来封装特定的消息代理产品。 图7-2 显示了 Dapr pub/sub 消息传递堆栈。

![Pub/sub 堆栈](./media/publish-subscribe/pub-sub-buildingblock.png)

**图 7-2**。 Dapr pub/sub 堆栈。

可以通过多种方式调用 Dapr publish & 订阅构建基块。

在最低级别，任何编程平台均可使用 **Dapr 本机 API** 通过 HTTP 或 gRPC 调用构建基块。 若要发布消息，请执行以下 API 调用：

``` http
http://localhost:<dapr-port>/v1.0/publish/<pub-sub-name>/<topic>
```

以上调用中有几个 Dapr 特定的 URL 段：

- `<dapr-port>` 提供 Dapr 挎斗正在侦听的端口号。
- `<pub-sub-name>` 提供选定的 Dapr pub/sub 组件的名称。
- `<topic>` 提供消息要发布到的主题的名称。

使用 *卷曲* 的命令行工具发布消息，可以尝试一下：

``` curl
curl -X POST http://localhost:3500/v1.0/publish/pubsub/newOrder \
  -H "Content-Type: application/json" \
  -d '{ "orderId": "1234", "productId": "5678", "amount": 2 }'
```

通过订阅主题来接收消息。 在启动时，Dapr 运行时将调用已知终结点上的应用程序来识别和创建所需的订阅：

``` http
http://localhost:<appPort>/dapr/subscribe
```

- `<appPort>` 通知 Dapr 挎斗应用程序侦听的端口。

可以自行实现此终结点。 但 Dapr 提供了更直观的实现方法。 本章稍后将介绍此功能。

来自调用的响应包含应用程序将订阅的主题的列表。 每个都包括在主题收到消息时要调用的终结点。 下面是响应的示例：

```json
[
  {
    "pubsubname": "pubsub",
    "topic": "newOrder",
    "route": "/orders"
  },
  {
    "pubsubname": "pubsub",
    "topic": "newProduct",
    "route": "/productCatalog/products"
  }
]
```

在 JSON 响应中，可以看到应用程序要订阅主题 `newOrder` 和 `newProduct` 。 `/orders`分别为每个终结点注册终结点 `/productCatalog/products` 。 对于这两个订阅，应用程序将绑定到名为的 Dapr 组件 `pubsub` 。

图7-3 显示了该示例的流。

![包含 Dapr 的发布/订阅示例](media/publish-subscribe/pub-sub-flow.png)

**图 7-3**。 具有 Dapr 的发布/订阅流。

在上图中，请注意以下 flow：

1. Dapr 挎斗 for Service B `/dapr/subscribe` 从服务 b 调用终结点， (使用者) 。 服务会响应它要创建的订阅。
1. 服务 B 的 Dapr 挎斗在消息代理上创建请求的订阅。
1. 服务 A 在 `/v1.0/publish/<pub-sub-name>/<topic>` Dapr 服务的终结点上发布一条消息挎斗。
1. 服务 A 挎斗将消息发布到消息代理。
1. 消息代理将消息副本发送到服务 B 挎斗。
1. 服务 B 挎斗调用对应于订阅 (的终结点，在这种情况下 `/orders`) 服务 b 上。服务使用 HTTP 状态代码进行响应 `200 OK` ，以便挎斗将消息视为已成功处理。

在示例中，消息已成功处理。 但是，如果服务 B 处理请求时出现问题，则可以使用响应指定需要对消息执行的操作。 当它返回 HTTP 状态代码时 `404` ，将记录错误并删除消息。 对于任何其他状态代码 `200` `404` （或），将记录警告，然后重试消息。 或者，服务 B 可以通过在响应正文中包含 JSON 有效负载，显式指定需要对消息执行的操作。

```json
{
  "status": "<status>"
}
```

下表显示了可用的 `status` 值：

| 状态           | 操作                                                       |
| ---------------- | ------------------------------------------------------------ |
| 成功          | 消息被视为已成功处理和丢弃。 |
| 重试            | 重试消息。                                      |
| DROP             | 将记录警告，并删除消息。              |
| 任何其他状态 | 重试消息。                                      |

### <a name="competing-consumers"></a>竞争使用者

向外扩展订阅某个主题的应用程序时，必须处理竞争使用者。 只有一个应用程序实例应处理发送到主题的消息。 幸运的是，Dapr 处理这一问题。 当具有相同应用程序 id 的服务的多个实例订阅主题时，Dapr 仅将每条消息传递给其中的一个。

### <a name="sdks"></a>SDK

对本机 Dapr Api 进行 HTTP 调用非常耗时且更抽象。 你的调用是在 HTTP 级别进行定制的，你将需要处理诸如序列化和 HTTP 响应代码这样的管道问题。 幸运的是，有一种更直观的方式。 Dapr 为常用开发平台提供多种语言特定的 Sdk。 撰写本文时，可以使用 Node.js、Python、.NET、Java 和 JavaScript。

## <a name="use-the-dapr-net-sdk"></a>使用 Dapr .NET SDK

对于 .NET 开发人员而言， [Dapr .NET SDK](https://www.nuget.org/packages/Dapr.Client) 提供了更高效的 Dapr 处理方式。 SDK 公开了一个 `DaprClient` 类，通过该类可以直接调用 Dapr 功能。 它直观且易于使用。

若要发布消息，会 `DaprClient` 公开一个 `PublishEventAsync` 方法。

```csharp
var data = new OrderData
{
  orderId = "123456",
  productId = "67890",
  amount = 2
};

var daprClient = new DaprClientBuilder().Build();

await daprClient.PublishEventAsync<OrderData>("pubsub", "newOrder", data);
```

- 第一个参数 `pubsub` 是提供消息代理实现的 Dapr 组件的名称。 本章稍后将介绍这些组件。
- 第二个参数 `neworder` 提供要向其发送消息的主题的名称。
- 第三个参数是消息的负载。
- 您可以使用方法的泛型类型参数来指定消息的 .NET 类型。

若要接收消息，请将终结点绑定到已注册主题的订阅。 用于 Dapr 的 AspNetCore 库使此功能变得简单。 例如，假设你有一个名为的现有 ASP.NET WebAPI 操作方法 `CreateOrder` ：

```csharp
[HttpPost("/orders")]
public async Task<ActionResult> CreateOrder(Order order)
```

 > 必须在项目中添加对 [Dapr](https://www.nuget.org/packages/Dapr.AspNetCore) NuGet 包的引用，才能使用 Dapr ASP.NET Core 集成。

若要将此操作方法绑定到主题，请使用属性对其进行修饰 `Topic` ：

```csharp
[Topic("pubsub", "newOrder")]
[HttpPost("/orders")]
public async Task<ActionResult> CreateOrder(Order order)
```

可以指定具有此属性的两个关键元素：

- 在这种情况下，要 (目标的 Dapr pub/sub 组件 `pubsub`) 。
- 在此示例中订阅 (的主题 `newOrder`) 。

然后，Dapr 将调用该操作方法，因为它接收该主题的消息。

还需要启用 ASP.NET Core 才能使用 Dapr。 Dapr .NET SDK 提供了可在类中调用的多个扩展方法 `Startup` 。

在 `ConfigureServices` 方法中，必须添加以下扩展方法：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddControllers().AddDapr();
}
```

将 `AddDapr` extension 方法追加到 `AddControllers` 扩展方法会注册必要的服务，以将 Dapr 集成到 MVC 管道中。 它还将 `DaprClient` 实例注册到依赖关系注入容器，然后可以将该容器注入服务中的任何位置。

在 `Configure` 方法中，必须添加以下中间件组件来启用 Dapr：

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    // ...
    app.UseCloudEvents();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapSubscribeHandler();
        // ...
    });
}
```

用于 `UseCloudEvents` 将 **CloudEvents** 中间件添加到 ASP.NET Core 中间件管道的调用。 此中间件将解包使用 CloudEvents 结构化格式的请求，因此接收方法可以直接读取事件负载。

> [CloudEvents](https://cloudevents.io/) 是一种标准化的消息传递格式，提供跨平台描述事件信息的常用方法。 Dapr 的 CloudEvents。 有关 CloudEvents 的详细信息，请参阅 [CloudEvents 规范](https://github.com/cloudevents/spec/tree/v1.0)。

`MapSubscribeHandler`终结点路由配置中对的调用会将 Dapr 订阅终结点添加到应用程序。 此终结点将响应上的请求 `/dapr/subscribe` 。 调用此终结点时，它将自动查找用属性修饰的所有 WebAPI 操作方法， `Topic` 并指示 Dapr 为它们创建订阅。

## <a name="pubsub-components"></a>Pub/sub 组件

Dapr [pub/sub 组件](https://github.com/dapr/components-contrib/tree/master/pubsub) 处理消息的实际传输。 有多个可用。 每个都封装特定消息代理产品以实现发布/订阅功能。 撰写本文时，可以使用以下发布/订阅组件：

- Apache Kafka
- Azure 事件中心
- Azure 服务总线
- AWS SNS/SQS
- GCP Pub/Sub
- Hazelcast
- MQTT
- NAT
- Pulsar
- RabbitMQ
- Redis 流

> [!NOTE]
> Azure 云堆栈) 和事件流 (azure 事件中心) 可用性 (的消息传递功能。

这些组件由 [GitHub 上的 contrib 存储库](https://github.com/dapr/components-contrib/tree/master/pubsub)中的社区创建。 建议为尚不受支持的消息代理编写自己的 Dapr 组件。

### <a name="configure-pubsub-components"></a>配置发布/订阅组件

使用 Dapr 配置文件，您可以指定要使用)  (的发布/订阅组件。 此配置包含多个字段。 `name`字段指定要使用的发布/订阅组件。 发送或接收消息时，需要指定此名称， (如之前在方法签名) 中看到的那样 `PublishEventAsync` 。

下面你将看到一个用于配置 RabbitMQ 消息代理组件的 Dapr 配置文件示例：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: pubsub-rq
spec:
  type: pubsub.rabbitmq
  version: v1
  metadata:
  - name: host
    value: "amqp://localhost:5672"
  - name: durable
    value: true
```

在此示例中，可以看到，可以在块中指定任何特定于消息代理的配置 `metadata` 。 在这种情况下，RabbitMQ 配置为创建持久队列。 但 RabbitMQ 组件具有更多的配置选项。 每个组件的配置将有自己的一组可能的字段。 您可以阅读每个发布 [/订阅组件](https://docs.dapr.io/operations/components/setup-pubsub/supported-pubsub/)的文档中提供的字段。

在以编程方式从代码订阅主题时，Dapr pub/sub 还提供了一种声明方式来订阅主题。 此方法会从应用程序代码中删除 Dapr 依赖项。 因此，它还使现有应用程序能够订阅主题，而不会对代码进行任何更改。 以下示例演示了用于配置订阅的 Dapr 配置文件：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Subscription
metadata:
  name: newOrder-subscription
spec:
  pubsubname: pubsub
  topic: newOrder
  route: /orders
scopes:
- ServiceB
- ServiceC
```

必须为每个订阅指定多个元素：

- 在此情况下 (要使用的 Dapr pub/sub 组件的名称 `pubsub`) 。
- 在此示例中订阅 (的主题的名称 `newOrder`) 。
- 在此示例中，需要为本主题调用 API 操作 (`/orders`) 。
- [作用域](https://docs.dapr.io/developing-applications/building-blocks/pubsub/pubsub-scopes/)可以指定哪些服务可以发布和订阅主题。

## <a name="reference-application-eshopondapr"></a>引用应用程序： eShopOnDapr

随附的 [eShopOnDapr](https://github.com/dotnet-architecture/eShopOnDapr) 应用提供端到端参考体系结构，用于构造实现 Dapr 的微服务应用程序。 eShopOnDapr 是在几年前创建的广泛流行的 [eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers) 应用程序的演变。 这两个版本都使用发布/订阅模式来跨微服务传达 [集成事件](https://devblogs.microsoft.com/cesardelatorre/domain-events-vs-integration-events-in-domain-driven-design-and-microservices-architectures/#integration-events) 。 集成事件包括：

- 当用户签出购物篮时。
- 订单的付款已成功。
- 在一段时间内，购买的宽限期已过期。

EShopOnContainers 中的事件基于以下 `IEventBus` 接口：

```csharp
public interface IEventBus
{
    void Publish(IntegrationEvent integrationEvent);

    void Subscribe<T, THandler>()
        where TEvent : IntegrationEvent
        where THandler : IIntegrationEventHandler<T>;
}
```

此接口的具体实现在 RabbitMQ 和 Azure 服务总线的 eShopOnContainers 中存在。 每个实现都包含大量自定义的管道代码，这些代码非常复杂，难于理解和维护。

较新的 eShopOnDapr 通过使用 Dapr 大大简化了发布/订阅行为。 例如， `IEventBus` 接口被缩减为单一方法：

```csharp
public interface IEventBus
{
    Task PublishAsync(IntegrationEvent integrationEvent);
}
```

### <a name="publish-events"></a>发布事件

在更新的 eShopOnDapr 中，一个 `DaprEventBus` 实现可以支持任何 Dapr 支持的消息代理。 下面的代码块显示了简化的发布方法。 请注意该 `PublishAsync` 方法如何使用 Dapr 客户端来发布事件：

```csharp
public class DaprEventBus : IEventBus
{
    private const string PubSubName = "pubsub";

    private readonly DaprClient _daprClient;
    private readonly ILogger<DaprEventBus> _logger;

    public DaprEventBus(DaprClient daprClient, ILogger<DaprEventBus> logger)
    {
        _daprClient = daprClient ?? throw new ArgumentNullException(nameof(daprClient));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
    }

    public async Task PublishAsync(IntegrationEvent integrationEvent)
    {
        var topicName = integrationEvent.GetType().Name;

        // Dapr uses System.Text.Json which does not support serialization of a
        // polymorphic type hierarchy by default. Using object as the type
        // parameter causes all properties to be serialized.
        await _daprClient.PublishEventAsync<object>(PubSubName, topicName, integrationEvent);
    }
}
```

如代码片段中所示，主题名称是从事件类型的名称派生的。 因为所有 eShop services 都使用 `IEventBus` 抽象，所以现场修改 Dapr 需要 *完全不更改* 主线应用程序代码。

> [!IMPORTANT]
> Dapr SDK 使用 `System.Text.Json` 来序列化/反序列化消息。 但是， `System.Text.Json` 默认情况下不会序列化派生类的属性。 在 eShop 代码中，事件有时显式声明为 `IntegrationEvent` （集成事件的基类）。 这是因为具体事件类型是在运行时基于业务逻辑动态确定的。 因此，使用基类而不是派生类的类型信息对事件进行序列化。 若要 `System.Text.Json` 在这种情况下强制序列化派生类的所有属性，代码使用 `object` 作为泛型类型参数。 有关详细信息，请参阅 [.net 文档](../../standard/serialization/system-text-json-polymorphism.md)。

借助 Dapr，可 **大大简化** 基础结构代码。 不需要区分不同的消息代理。 Dapr 为你提供此抽象。 如果需要，可以轻松地交换消息代理或配置多个消息代理组件。

### <a name="subscribe-to-events"></a>订阅事件

前面的 eShopOnContainers 应用程序包含 *SubscriptionManagers* 来处理每个消息代理的订阅实现。 每个管理器都包含用于处理订阅事件的复杂消息代理特定代码。 若要接收事件，每个服务必须为每个事件类型显式注册一个处理程序。

eShopOnDapr 使用 Dapr ASP.NET Core 库优化了事件订阅的管道。 每个事件都由控制器中的操作方法处理。 `Topic`特性使用要订阅的相应主题的名称修饰操作方法。 下面是从中获取的代码片段 `PaymentService` ：

```csharp
[Route("api/v1/[controller]")]
[ApiController]
public class IntegrationEventController : ControllerBase
{
    private const string DAPR_PUBSUB_NAME = "pubsub";

    private readonly IServiceProvider _serviceProvider;

    public IntegrationEventController(IServiceProvider serviceProvider)
    {
        _serviceProvider = serviceProvider;
    }

    [HttpPost("OrderStatusChangedToValidated")]
    [Topic(DAPR_PUBSUB_NAME, "OrderStatusChangedToValidatedIntegrationEvent")]
    public async Task OrderStarted(OrderStatusChangedToValidatedIntegrationEvent integrationEvent)
    {
        var handler = _serviceProvider.GetRequiredService<OrderStatusChangedToValidatedIntegrationEventHandler>();
        await handler.Handle(integrationEvent);
    }
}
```

在 `Topic` 特性中，事件的 .net 类型的名称将用作主题名称。 在处理事件时，将调用以前的 eShopOnContainers 代码库中已经存在的事件处理程序。 在上面的示例中，从主题接收的消息 `OrderStatusChangedToValidatedIntegrationEvent` 调用了现有的 `OrderStatusChangedToValidatedIntegrationEventHandler` 事件处理程序。 由于 Dapr 实现了订阅和消息代理的基础管道，因此大量原始代码已过时，并已从代码库中删除。 很多代码对于理解和维护非常复杂。

### <a name="use-pubsub-components"></a>使用发布/订阅组件

在 eShopOnDapr 存储库中， `deployment` 文件夹包含使用不同部署模式部署应用程序的文件： `Docker Compose` 和 `Kubernetes` 。 `dapr`包含文件夹的每个文件夹中都存在一个文件夹 `components` 。 此文件夹包含一个文件，该文件 `eshop-pubsub.yaml` 包含应用程序将用于发布/订阅行为的 Dapr pub/sub 组件的配置。 正如您在前面的代码片段中所看到的那样，所使用的 pub/sub 组件的名称为 `pubsub` 。 下面是 `eshop-pubsub.yaml` 文件夹中文件的内容 `deployment/compose/dapr/components` ：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: pubsub
  namespace: default
spec:
  type: pubsub.nats
  version: v1
  metadata:
  - name: natsURL
    value: nats://demo.nats.io:4222
```

前面的配置指定此示例所需的 [nat 消息代理](https://nats.io/) 。 若要更改消息代理，只需配置一个不同的消息代理，如 RabbitMQ 或 Azure 服务总线，并更新 yaml 文件。 对于 Dapr，切换消息代理时，不会对主线服务代码进行任何更改。

最后，您可能会问： "为什么需要在一个应用程序中使用多个消息代理？"。 很多时候系统将处理具有不同特征的工作负荷。 一个事件可能一天发生10次，但另一个事件每秒发生5000次。 可以通过将消息传送流量分区给不同消息代理来受益。 使用 Dapr，可以添加多个 pub/sub 组件配置，每个配置都有不同的名称。

## <a name="summary"></a>总结

Pub/sub 模式可帮助你分离分布式应用程序中的服务。 Dapr publish & 订阅构建基块可简化在应用程序中实现此行为。

通过 Dapr pub/sub，你可以将消息发布到特定 *主题*。 同时，构建基块将查询你的服务，以确定) 订阅 (的主题。

你可以通过 HTTP 或使用特定于语言的 Sdk 之一（如 .NET SDK for Dapr）使用 Dapr pub/sub。 .NET SDK 与 ASP.NET core 平台紧密集成。

使用 Dapr，可以将受支持的消息代理产品插入应用程序。 然后，你可以在无需对应用程序进行代码更改的情况下交换消息代理。

> [!div class="step-by-step"]
> [上一页](service-invocation.md)
> [下一页](bindings.md)

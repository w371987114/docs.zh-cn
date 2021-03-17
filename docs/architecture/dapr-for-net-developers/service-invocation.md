---
title: Dapr 服务调用构建基块
description: 服务调用构建基块的说明、功能、优点以及应用方法
author: amolenk
ms.date: 02/17/2021
ms.openlocfilehash: f6d5f10ae476d85a9925c4fa387a16d575cacf6a
ms.sourcegitcommit: d623f686701b94bef905ec5e93d8b55d031c5d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2021
ms.locfileid: "103624092"
---
# <a name="the-dapr-service-invocation-building-block"></a>Dapr 服务调用构建基块

在分布式系统中，一项服务通常需要与其他服务进行通信才能完成业务运营。 [Dapr 服务调用构建块](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/service-invocation-overview/)可帮助简化服务之间的通信。

## <a name="what-it-solves"></a>解决方法

在分布式应用程序中的服务之间进行调用可能会很简单，但会涉及到许多挑战。 例如：

- 其他服务的位置。
- 在给定服务地址的情况下，如何安全地调用服务。
- 在发生短暂的 [暂时性错误](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) 时如何处理重试。

最后，随着分布式应用程序编写许多不同的服务，跨服务调用关系图捕获见解对于诊断生产问题至关重要。

服务调用构建块通过使用 Dapr 挎斗作为服务的 [反向代理](https://kemptechnologies.com/reverse-proxy/reverse-proxy/) 来解决这些难题。

## <a name="how-it-works"></a>工作原理

让我们从一个示例开始。 假设有两个服务： "服务 A" 和 "服务 B"。 服务 A 需要 `catalog/items` 在服务 B 上调用 API。尽管服务 A 可能会依赖于服务 B 并直接对其进行调用，但是服务 A 在 Dapr 挎斗上调用服务调用 API。 图6-1 显示了该操作。

![Dapr 服务调用的工作方式](./media/service-invocation/service-invocation-flow.png)

**图 6-1**. Dapr 服务调用的工作方式。

请注意上图中的步骤：

1. 服务 A `catalog/items` 通过在挎斗的服务上调用服务调用 API，在服务 B 中调用终结点。

    > [!NOTE]
    > 挎斗使用可插接式名称解析机制来解析服务 B 的地址。在自承载模式下，Dapr 使用 [mdn](https://www.ionos.com/digitalguide/server/know-how/multicast-dns/) 来查找它。 在 Kubernetes 模式下运行时，Kubernetes DNS 服务将确定地址。  

1. 服务 A 挎斗将请求转发到服务 B 挎斗。

1. 服务 B 挎斗对 `catalog/items` 服务 b API 发出实际请求。

1. 服务 B 执行请求，并将响应返回给其挎斗。

1. 服务 B 挎斗将响应转发回挎斗的服务。

1. 服务 A 挎斗将响应返回给服务 A。

由于调用流过分支，Dapr 可以注入一些有用的交叉切削行为：

- 失败时自动重试调用。
- 通过相互 (mTLS) 身份验证（包括自动证书滚动更新），在服务之间进行调用。
- 使用访问控制策略控制客户端可以执行的操作。
- 捕获服务间所有调用的跟踪和指标，以提供见解和诊断。

任何应用程序都可以通过使用 Dapr 中内置的本机 **调用** API 来调用 Dapr 挎斗。 可以通过 HTTP 或 gRPC 调用 API。 使用以下 URL 调用 HTTP API：

``` http
http://localhost:<dapr-port>/v1.0/invoke/<application-id>/method/<method-name>
```

- `<dapr-port>` Dapr 正在侦听的 HTTP 端口。
- `<application-id>` 要调用的服务的应用程序 ID。
- `<method-name>` 要在远程服务上调用的方法的名称。

在下面的示例中， 对的 `catalog/items` "GET" 终结点发出了一个卷调用 `Service B` ：

```console
curl http://localhost:3500/v1.0/invoke/serviceb/method/catalog/items
```

> [!NOTE]
> Dapr Api 启用支持 HTTP 或 gRPC 的任何应用程序堆栈以使用 Dapr 构建基块。 因此，服务调用构建块可充当协议之间的桥梁。 服务可以使用 HTTP、gRPC 或两者的组合互相通信。

在下一部分中，你将了解如何使用 .NET SDK 来简化服务调用调用。

## <a name="use-the-dapr-net-sdk"></a>使用 Dapr .NET SDK

Dapr [.NET SDK](https://github.com/dapr/dotnet-sdk) 为 .net 开发人员提供了直观的、特定于语言的方法来与 Dapr 交互。 SDK 为开发人员提供了三种建立远程服务调用调用的方法：

1. 使用 HttpClient 调用 HTTP 服务
1. 使用 DaprClient 调用 HTTP 服务
1. 使用 DaprClient 调用 gRPC 服务

### <a name="invoke-http-services-using-httpclient"></a>使用 HttpClient 调用 HTTP 服务

调用 HTTP 终结点的首选方法是使用 Dapr 与的丰富集成 `HttpClient` 。 下面的示例通过调用 `submit` 应用程序的方法来提交订单 `orderservice` ：

```csharp
var httpClient = DaprClient.CreateHttpClient();
await httpClient.PostAsJsonAsync("http://orderservice/submit", order);
```

在此示例中， `DaprClient.CreateHttpClient` 返回 `HttpClient` 用于执行 Dapr 服务调用的实例。 返回的 `HttpClient` 使用特殊的 Dapr 消息处理程序，该处理程序会重写传出请求的 uri。 主机名称被解释为要调用的服务的应用程序 ID。 实际调用的重写请求为：

```http
http://127.0.0.1:3500/v1/invoke/orderservice/method/submit
```

此示例使用 Dapr HTTP 终结点的默认值，即 `http://127.0.0.1:<dapr-http-port>/` 。 的值 `dapr-http-port` 取自 `DAPR_HTTP_PORT` 环境变量。 如果未设置，则使用默认端口号 `3500` 。

或者，你可以在调用中配置自定义终结点 `DaprClient.CreateHttpClient` ：

```csharp
var httpClient = DaprClient.CreateHttpClient(daprEndpoint = "localhost:4000");
```

还可以通过指定应用程序 ID 来直接设置基址。 这样就可以在进行调用时使用相对 Uri：

```csharp
var httpClient = DaprClient.CreateHttpClient("orderservice");
await httpClient.PostAsJsonAsync("/submit");
```

`HttpClient`对象旨在长时间生存期。 可以在 `HttpClient` 应用程序的生存期内重复使用单个实例。 下一个方案演示了 `OrderServiceClient` 类如何重用 Dapr `HttpClient` 实例：  

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddSingleton<IOrderServiceClient, OrderServiceClient>(
        _ => new OrderServiceClient(DaprClient.CreateInvokeHttpClient("orderservice")));
}
```

在上面的代码片段中， `OrderServiceClient` 使用 ASP.NET Core 依赖关系注入系统将注册为单一实例。 实现工厂通过调用创建一个新的 `HttpClient` 实例 `DaprClient.CreateInvokeHttpClient` 。 然后，它使用新创建的 `HttpClient` 来实例化 `OrderServiceClient` 对象。 通过将注册 `OrderServiceClient` 为单一实例，它将在应用程序的生存期内重复使用。

`OrderServiceClient`本身没有特定于 Dapr 的代码。 即使在后台使用 Dapr 服务调用，你也可以像对待任何其他 HttpClient 一样处理 Dapr HttpClient：

```csharp
public class OrderServiceClient : IOrderServiceClient
{
    private readonly HttpClient _httpClient;

    public OrderServiceClient(HttpClient httpClient)
    {
        _httpClient = httpClient ?? throw new ArgumentNullException(nameof(httpClient));
    }

    public async Task SubmitOrder(Order order)
    {
        var response = await _httpClient.PostAsJsonAsync("submit", order);
        response.EnsureSuccessStatusCode();
    }
}
```

将 HttpClient 类用于 Dapr 服务调用有很多好处：

- HttpClient 是许多开发人员已在其代码中使用的众所周知的类。 使用 HttpClient for Dapr 服务调用，开发人员可重复使用其现有技能。
- HttpClient 支持高级方案，如自定义标头，以及对请求和响应消息的完全控制。
- 在 .NET 5 中，HttpClient 支持使用上的 System.Text.Js自动进行序列化和反序列化。
- HttpClient 集成了许多现有框架和库 [，如重新调整](https://github.com/reactiveui/refit)、 [RestSharp](https://restsharp.dev/getting-started/getting-started.html#basic-usage)和 [Polly](https://github.com/App-vNext/Polly)。

### <a name="invoke-http-services-using-daprclient"></a>使用 DaprClient 调用 HTTP 服务

尽管 HttpClient 是使用 HTTP 语义调用服务的首选方法，但也可以使用 `DaprClient.InvokeMethodAsync` 方法系列。 下面的示例通过调用 `submit` 应用程序的方法来提交订单 `orderservice` ：

``` csharp
var daprClient = new DaprClientBuilder().Build();
try
{
    var confirmation =
        await daprClient.InvokeMethodAsync<Order, OrderConfirmation>(
            "orderservice", "submit", order);
}
catch (InvocationException ex)
{
    // Handle error
}
```

第三个参数（ `order` 对象）在内部序列化 (与 `System.Text.JsonSerializer`) 并作为请求负载发送。 .NET SDK 负责调用挎斗。 它还会对对象的响应进行反序列化 `OrderConfirmation` 。 由于未指定 HTTP 方法，因此将以 HTTP POST 的形式执行请求。

下一个示例演示如何通过指定以下内容来发出 HTTP GET 请求 `HttpMethod` ：

```csharp
var catalogItems = await daprClient.InvokeMethodAsync<IEnumerable<CatalogItem>>(HttpMethod.Get, "catalogservice", "items");
```

在某些情况下，可能需要对请求消息进行更多的控制。 例如，当你需要指定请求标头，或你想要将自定义序列化程序用于有效负载时。 `DaprClient.CreateInvokeMethodRequest` 创建一个 `HttpRequestMessage` 。 下面的示例演示如何将 HTTP 授权标头添加到请求消息：

```csharp
var request = daprClient.CreateInvokeMethodRequest("orderservice", "submit", order);
request.Headers.Authorization = new AuthenticationHeaderValue("bearer", token);
```

`HttpRequestMessage`现在具有以下属性集：

- Url = `http://127.0.0.1:3500/v1.0/invoke/orderservice/method/submit`
- HttpMethod = POST
- Content =  `JsonContent` 包含 JSON 序列化的对象 `order`
- 标头。 Authorization = "持有者 \<token> "

按所需方式设置请求后，请使用发送此请求 `DaprClient.InvokeMethodAsync` ：

```csharp
var orderConfirmation = await daprClient.InvokeMethodAsync<OrderConfirmation>(request);
```

`DaprClient.InvokeMethodAsync` 如果请求成功，则反序列化对象的响应 `OrderConfirmation` 。 或者，您可以使用 `DaprClient.InvokeMethodWithResponseAsync` 来获取对基础的完全访问权限 `HttpResponseMessage` ：

```csharp
var response = await daprClient.InvokeMethodWithResponseAsync(request);
response.EnsureSuccessStatusCode();

var orderConfirmation = response.Content.ReadFromJsonAsync<OrderConfirmation>();
```

> [!NOTE]
> 对于使用 HTTP 的服务调用调用，有必要考虑使用上一节中介绍的 Dapr HttpClient 集成。 使用 HttpClient 为你提供了更多好处，例如与现有框架和库集成。

### <a name="invoke-grpc-services-using-daprclient"></a>使用 DaprClient 调用 gRPC 服务

DaprClient 提供了一系列 `InvokeMethodGrpcAsync` 方法来调用 gRPC 终结点。 与 HTTP 方法的主要区别是使用 Protobuf 序列化程序，而不是 JSON。 下面的示例调用 `submitOrder` Over gRPC 的的方法 `orderservice` 。

```csharp
var daprClient = new DaprClientBuilder().Build();
try
{
    var confirmation = await daprClient.InvokeMethodGrpcAsync<Order, OrderConfirmation>("orderservice", "submitOrder", order);
}
catch (InvocationException ex)
{
    // Handle error
}
```

在上面的示例中，DaprClient `order` 使用 [Protobuf](https://developers.google.com/protocol-buffers) 序列化给定对象，并使用结果作为 gRPC 请求正文。 同样，响应正文 Protobuf 反序列化并返回给调用方。 与 HTTP 服务调用中使用的 JSON 有效负载相比，Protobuf 通常提供更好的性能。

## <a name="reference-application-eshopondapr"></a>引用应用程序： eShopOnDapr

Microsoft 的原始 [eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers) 微服务参考体系结构使用了混合使用 HTTP/REST 和 gRPC 服务。 使用 gRPC 仅限于 [聚合器服务](../cloud-native/service-to-service-communication.md#service-aggregator-pattern) 和核心后端服务之间的通信。 图6-2 显示体系结构：

![eShopOnContainers 中的 gRPC 和 HTTP/REST 调用](./media/service-invocation/eshop-on-containers.png)

**图 6-2**. eShopOnContainers 中的 gRPC 和 HTTP/REST 调用。

请注意上图中的步骤：

1. 前端使用 HTTP/REST 调用 [API 网关](/azure/architecture/microservices/design/gateway) 。

1. API 网关转发简单的 [CRUD](https://www.sumologic.com/glossary/crud/) (使用 HTTP/REST 将) 请求直接发送到核心后端服务。

1. API 网关将涉及多个后端服务的协调调用的复杂请求转发到 web 购物聚合器服务。

1. 聚合器服务使用 gRPC 来调用核心后端服务。

在最近更新的 eShopOnDapr 实现中，Dapr 分支将添加到服务和 API 网关。 图6-3 显示了更新后的体系结构：

![eShopOnContainers 中分支的 gRPC 和 HTTP/REST 调用](./media/service-invocation/eshop-on-dapr.png)

**图 6-3**。 使用 Dapr 更新 eShop 体系结构。

请注意上图中更新的步骤：

1. 前端仍使用 HTTP/REST 调用 API 网关。

1. API 网关将 HTTP 请求转发到其 Dapr 挎斗。

1. API 网关挎斗将请求发送到聚合器或后端服务的挎斗。

1. 聚合器服务使用 Dapr .NET SDK 通过其挎斗体系结构调用后端服务。

Dapr 实现分支和 gRPC 之间的调用。 因此，即使是使用 HTTP/REST 语义调用远程服务，也仍然使用 gRPC 实现传输的一部分。

EShopOnDapr reference 应用程序的优点在于 Dapr service 调用构建基块。 优点包括服务发现、自动 mTLS 和可观察性。

### <a name="forward-http-requests-using-envoy-and-dapr"></a>使用 Envoy 和 Dapr 转发 HTTP 请求

原始 eShop 应用程序和更新的应用程序都将 [Envoy 代理](https://www.envoyproxy.io/) 用作 API 网关。 Envoy 是一种开放源代理和跨新式分布式应用程序常用的通信总线。 源自 Lyft，Envoy 由 [云本机计算基础](https://www.cncf.io/)拥有和维护。

在原始 eShopOnContainers 实现中，Envoy API 网关会将传入的 HTTP 请求直接转发到聚合器或后端服务。 在 new eShopOnDapr 中，Envoy 代理将请求转发到 Dapr 挎斗。 挎斗提供服务调用、mTLS 和可观察性。

使用 YAML 定义文件对 Envoy 进行配置，以控制代理的行为。 为了使 Envoy 能够将 HTTP 请求转发到 Dapr 挎斗容器，会将一个 `dapr` 群集添加到配置中。 群集配置包含一个主机，该主机指向 Dapr 挎斗正在侦听的 HTTP 端口：

``` yaml
clusters:
- name: dapr
  connect_timeout: 0.25s
  type: strict_dns
  hosts:
  - socket_address:
    address: 127.0.0.1
    port_value: 3500
```

更新 Envoy 路由配置，以将传入请求重写为对 Dapr 挎斗的调用， (请注意 `prefix_rewrite` 键/值对) ：

``` yaml
- name: "c-short"
  match:
    prefix: "/c/"
  route:
    auto_host_rewrite: true
    prefix_rewrite: "/v1.0/invoke/catalog-api/method/"
    cluster: dapr
```

假设前端客户端要检索目录项列表。 目录 API 提供用于获取目录项的终结点：

``` csharp
[Route("api/v1/[controller]")]
[ApiController]
public class CatalogController : ControllerBase
{
    [HttpGet("items")]
    public async Task<IActionResult> ItemsAsync(
        [FromQuery] int pageSize = 10,
        [FromQuery] int pageIndex = 0)
    {
        // ...
    }
```

首先，前端直接调用 Envoy API 网关。

```
GET http://<api-gateway>/c/api/v1/catalog/items?pageSize=20
```

Envoy 代理匹配路由，重写 HTTP 请求，并将其转发到 `invoke` 其 Dapr 挎斗的 API：

```
GET http://127.0.0.1:3500/v1.0/invoke/catalog-api/method/api/v1/catalog/items?pageSize=20
```

挎斗处理服务发现并将请求路由到目录 API 挎斗。 最后，挎斗调用目录 API 来执行请求、提取目录项并返回响应：

```
GET http://localhost/api/v1/catalog/items?pageSize=20
```

### <a name="make-aggregated-service-calls-using-the-net-sdk"></a>使用 .NET SDK 进行聚合服务调用

EShop 前端的大多数调用都是简单的 CRUD 调用。 API 网关将它们转发给单个服务进行处理。 但在某些情况下，需要多个后端服务一起工作来完成请求。 对于更复杂的调用，eShop 使用 web 购物聚合器服务跨多个服务调解工作流。 图6-4 显示了将商品添加到购物篮的处理顺序：

![更新购物篮序列图](./media/service-invocation/complex-call.png)

图 6-4  。 更新购物篮序列。

聚合器服务首先从目录 API 中检索目录项。 然后，它将验证项目的可用性和定价。 最后，聚合器服务通过调用购物篮 API 来保存更新后的购物篮。

聚合器服务包含一个 `BasketController` ，它提供终结点用于更新购物篮：

``` csharp
[Route("api/v1/[controller]")]
[Authorize]
[ApiController]
public class BasketController : ControllerBase
{
    private readonly ICatalogService _catalog;
    private readonly IBasketService _basket;

    [HttpPost]
    [HttpPut]
    public async Task<ActionResult<BasketData>> UpdateAllBasketAsync(
        [FromBody] UpdateBasketRequest data, [FromHeader] string authorization)
    {
        // Get the item details from the catalog API.
        var catalogItems = await _catalog.GetCatalogItemsAsync(
            data.Items.Select(x => x.ProductId));

        // Check item availability and prices; store results in basket object.
        var basket = CreateValidatedBasket(data, catalogItems);

        // Save the shopping basket.
        await _basket.UpdateAsync(basket, authorization);

        return basket;
    }

    // ...
}
```

`UpdateAllBasketAsync`方法使用特性获取传入请求的 *Authorization* 标头 `FromHeader` 。 *Authorization* 标头包含调用受保护的后端服务所需的访问令牌。

收到更新购物篮的请求后，聚合器服务将调用目录 API 以获取项详细信息。 购物篮控制器使用注入 `ICatalogService` 的对象进行调用，并与目录 API 通信。 接口的原始实现使用 gRPC 进行调用。 更新的实现将 Dapr 服务调用与 HttpClient 支持结合使用：

``` csharp
public class CatalogService : ICatalogService
{
    private readonly HttpClient _httpClient;

    public CatalogService(HttpClient httpClient)
    {
        _httpClient = httpClient ?? throw new ArgumentNullException(nameof(httpClient));
    }

    public Task<IEnumerable<CatalogItem>> GetCatalogItemsAsync(IEnumerable<int> ids)
    {
        var requestUri = $"api/v1/catalog/items?ids={string.Join(",", ids)}";

        return _httpClient.GetFromJsonAsync<IEnumerable<CatalogItem>>(requestUri);
    }

    // ...
}
```

请注意，不需要 Dapr 特定的代码就可以调用服务调用。 所有通信都是使用标准 HttpClient 对象完成的。

将 Dapr HttpClient 注入 `CatalogService` 方法中的类 `Startup.ConfigureServices` ：

```csharp
services.AddSingleton<ICatalogService, CatalogService>(
    _ => new CatalogService(DaprClient.CreateInvokeHttpClient("catalog-api")));
```

聚合器服务所做的另一种调用是购物篮 API。 它只允许授权的请求。 在 *授权* 请求标头中传递访问令牌，以确保调用成功：

``` csharp
public class BasketService : IBasketService
{
    public Task UpdateAsync(BasketData currentBasket, string accessToken)
    {
        var request = new HttpRequestMessage(HttpMethod.Post, "api/v1/basket")
        {
            Content = JsonContent.Create(currentBasket)
        };
        request.Headers.Authorization = new AuthenticationHeaderValue(accessToken);

        var response = await _httpClient.SendAsync(request);
        response.EnsureSuccessStatusCode();
    }

    // ...
}
```

在此示例中，这种情况下，仅使用标准 HttpClient 功能来调用服务。 这样，已经熟悉 HttpClient 的开发人员就可以重复使用其现有技能。 它甚至使现有的 HttpClient 代码可以使用 Dapr 服务调用，而无需进行任何更改。

## <a name="summary"></a>摘要

本章介绍了服务调用构建基块。 你已了解如何通过直接 HTTP 调用 Dapr 挎斗并使用 Dapr .NET SDK 调用远程方法。

Dapr .NET SDK 提供了多种方法来调用远程方法。 HttpClient 支持对于需要重复使用现有技能的开发人员非常有用，并且与许多现有框架和库兼容。 DaprClient 提供使用 HTTP 或 gRPC 语义直接使用 Dapr 服务调用 API 的支持。

EShopOnDapr 参考体系结构显示了如何使用 Dapr 服务调用现代化原始 eShopOnContainers 解决方案。 将 Dapr 添加到 eShop 提供了一些优点，例如自动重试、使用 mTLS 进行消息加密，以及改进的可观察性。

### <a name="references"></a>参考

- [Dapr 服务调用构建基块](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/)

- [监视分布式云本机应用程序](../cloud-native/observability-patterns.md)

> [!div class="step-by-step"]
> [上一页](state-management.md)
> [下一页](publish-subscribe.md)

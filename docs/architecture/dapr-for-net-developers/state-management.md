---
title: Dapr 状态管理构建基块
description: 说明状态管理构建基块、功能、优点以及如何应用它。
author: amolenk
ms.date: 02/17/2021
ms.reviewer: robvet
ms.openlocfilehash: 67b7f839ccbe24752281fb537b0473d4984d9e37
ms.sourcegitcommit: d623f686701b94bef905ec5e93d8b55d031c5d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2021
ms.locfileid: "103623923"
---
# <a name="the-dapr-state-management-building-block"></a>Dapr 状态管理构建基块

分布式应用程序由独立的服务组成。 虽然每个服务都应该是无状态的，但某些服务必须跟踪状态才能完成业务运营。 请考虑电子商务网站的购物篮服务。 如果服务无法跟踪状态，则客户可能会通过离开网站使购物篮内容松动，导致销售丢失和客户不满。 对于这些情况，需要将状态保存到分布式状态存储中。 [Dapr 状态管理构建块](https://docs.dapr.io/developing-applications/building-blocks/state-management/)简化了状态跟踪，并提供了跨各种数据存储的高级功能。

若要试用状态管理构建基块，请参阅 [第3章中的计数器应用程序示例](getting-started.md)。

## <a name="what-it-solves"></a>解决方法

分布式应用程序中的跟踪状态可能具有挑战性。 例如：

- 应用程序可能需要不同类型的数据存储。
- 访问和更新数据时可能需要不同的一致性级别。
- 多个用户可以同时更新数据，需要冲突解决。
- 在与数据存储交互时，服务必须重试发生的任何短暂的 [暂时性错误](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) 。

Dapr 状态管理构建块解决了这些难题。 它简化了跟踪状态，而无需依赖关系或学习曲线在第三方存储 Sdk 上。

> [!IMPORTANT]
> Dapr 状态管理提供 [密钥/值](/azure/architecture/guide/technology-choices/data-store-overview#keyvalue-stores) API。 此功能不支持关系数据存储或图形数据存储。

## <a name="how-it-works"></a>工作原理

应用程序与 Dapr 挎斗交互，以存储和检索键/值数据。 在后台，挎斗 API 使用可配置的状态存储组件来持久保存数据。 开发人员可以从不断增长的 [支持状态存储](https://docs.dapr.io/operations/components/setup-state-store/supported-state-stores/) 集合中进行选择，其中包括 Azure Cosmos DB、SQL Server 和 Cassandra。

可以通过 HTTP 或 gRPC 调用 API。 使用以下 URL 调用 HTTP API：

```http
http://localhost:<dapr-port>/v1.0/state/<store-name>/
```

- `<dapr-port>`： Dapr 侦听的 HTTP 端口。
- `<store-name>`：要使用的状态存储组件的名称。

图5-1 显示了 Dapr 的购物篮服务如何使用名为的 Dapr 状态存储组件来存储键/值对 `statestore` 。

![在 Dapr 状态存储中存储键/值对的关系图。](media/state-management/state-management-flow.png)

**图 5-1**。 在 Dapr 状态存储中存储键/值对。

请注意上图中的步骤：

1. 购物篮服务在 Dapr 挎斗上调用状态管理 API。 请求正文包含一个可包含多个键/值对的 JSON 数组。
1. Dapr 挎斗基于组件配置文件确定状态存储。 在这种情况下，它是一个 Redis 缓存状态存储。
1. 挎斗将数据保留到 Redis 缓存。

检索存储的数据是类似的 API 调用。 在下面的示例中， *卷* 命令通过调用 DAPR 挎斗 API 来检索数据：

```console
curl http://localhost:3500/v1.0/state/statestore/basket1
```

此命令将在响应正文中返回已存储状态：

```json
{
  "items": [
    {
      "itemId": "DaprHoodie",
      "quantity": 1
    }
  ],
  "customerId": 1
}
```

以下各节介绍如何使用状态管理构建基块的更高级的功能。

### <a name="consistency"></a>一致性

[CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)是一组适用于存储状态的分布式系统的原则。 图5-2 显示了 CAP 定理的三个属性。

![CAP 定理。](media/state-management/cap-theorem.png)

**图 5-2**。 CAP 定理。

定理指出，分布式数据系统提供一致性、可用性和分区容差之间的权衡。 而且，任何数据存储只能 *保证三个属性中的两个*：

- *一致性* (**C**) 。 群集中的每个节点都将使用最新的数据做出响应，即使在所有副本更新之前，系统都必须阻止请求。 如果查询当前正在更新的项的 "一致性系统"，则在所有副本都成功更新之前，将不会收到响应。 但是，您将始终接收最新的数据。

- *可用性* (**)** 。 即使该响应不是最新的数据，每个节点都将返回立即响应。 如果您在 "可用系统" 中查询正在更新的项，则您将获得该服务在此时可以提供的最佳可能的答案。

- *分区容差* (**P**) 。 即使复制的数据节点发生故障或失去与其他复制的数据节点的连接，保证系统仍可继续运行。

分布式应用程序必须处理 **P** 属性。 随着服务彼此间的网络调用通信，会发生网络中断 (**P**) 。 考虑到这一点，分布式应用程序必须是 **AP** 或 **CP**。

**AP** 应用程序选择 "可用性一致性"。 Dapr 通过其 **最终一致性** 策略支持此选择。 请考虑使用基础数据存储（例如 Azure CosmosDB）将冗余数据存储在多个副本上。 对于最终一致性，状态存储会将更新写入一个副本并完成与客户端的写入请求。 此时间过后，存储将以异步方式更新其副本。 读取请求可以返回任何副本的数据，包括尚未收到最新更新的副本。

**CP** 应用程序选择一致性和可用性。 Dapr 通过其 **强一致性** 策略支持此选择。 在此方案中，状态存储将同步更新 *所有* (或在某些情况下，在完成写入请求 *之前*) 必需副本的 *仲裁*。 读取操作将跨副本持续返回最新数据。

通过将 *一致性提示* 附加到操作来指定状态操作的一致性级别。 以下 *卷* 命令 `Hello=World` 使用强一致性提示向状态存储写入一个键/值对：

```console
curl -X POST http://localhost:3500/v1.0/state/<store-name> \
  -H "Content-Type: application/json" \
  -d '[
        {
          "key": "Hello",
          "value": "World",
          "options": {
            "consistency": "strong"
          }
        }
      ]'
```

> [!IMPORTANT]
> 它用于满足附加到操作的一致性提示，由 Dapr 状态存储组件完成。 并非所有数据存储都支持这两种一致性级别。 如果未设置任何一致性提示，则默认行为为 **最终** 状态。

### <a name="concurrency"></a>并发

在多用户应用程序中，有可能多个用户同时更新同一时间)  (相同的数据。 Dapr 支持乐观并发控制 (OCC) 来管理冲突。 OCC 基于一个假设，因为用户处理数据的不同部分，所以更新冲突很少见。 更有效的方法是将更新成功，如果不成功，则重试。 实现悲观锁定的替代方法可能会影响长时间运行的锁定，导致数据争用。

Dapr 支持使用 Etag)  (OCC 的乐观并发控制。 ETag 是与存储的键/值对的特定版本相关联的值。 键/值对的每次更新时，ETag 值也会更新。 当客户端检索键/值对时，响应包括当前 ETag 值。 当客户端更新或删除键/值对时，它必须在请求正文中发送回该 ETag 值。 如果其他客户端同时更新了数据，则 Etag 不会匹配，请求将失败。 此时，客户端必须检索更新的数据，重新进行更改，然后重新提交更新。 此策略称为 **第一次写入-wins**。

Dapr 还支持 **最后写入 wins** 策略。 使用此方法时，客户端不会将 ETag 附加到写入请求。 状态存储组件将始终允许更新，即使基础值在会话期间已更改也是如此。 最后写入-wins 对于数据争用较少的高吞吐量写入方案非常有用。 同样，可以容忍偶尔的用户更新。

### <a name="transactions"></a>事务

Dapr 可以将 *多项更改* 作为一个作为事务实现的操作写入数据存储区。 此功能仅适用于支持 [ACID](https://en.wikipedia.org/wiki/ACID) 事务的数据存储。 撰写本文时，这些存储包括 Redis、MongoDB、PostgreSQL、SQL Server 和 Azure CosmosDB。

在下面的示例中，多项操作将发送到单个事务中的状态存储。 所有操作必须成功才能提交事务。 如果一个或多个操作失败，则将回滚整个事务。

```console
curl -X POST http://localhost:3500/v1.0/state/<store-name>/transaction \
  -H "Content-Type: application/json" \
  -d '{
        "operations": [
          {
            "operation": "upsert",
            "request": { "key": "Key1", "value": "Value1"
            }
          },
          {
            "operation": "delete",
            "request": { "key": "Key2" }
          }
        ]
      }'
```

对于不支持事务的数据存储，还可以将多个键作为单个请求发送。 下面的示例演示了一个 **大容量** 写入操作：

```console
curl -X POST http://localhost:3500/v1.0/state/<store-name> \
  -H "Content-Type: application/json" \
  -d '[
        { "key": "Key1", "value": "Value1" },
        { "key": "Key2", "value": "Value2" }
      ]'
```

对于大容量操作，Dapr 会将每个键/值对更新作为单独的请求提交到数据存储区。

## <a name="use-the-dapr-net-sdk"></a>使用 Dapr .NET SDK

Dapr .NET SDK 为 .NET Core 平台提供特定于语言的支持。 开发人员可以使用 `DaprClient` [第3章](getting-started.md) 中引入的类来读取和写入数据。 下面的示例演示如何使用 `DaprClient.GetStateAsync<TValue>` 方法从状态存储中读取数据。 此方法需要将存储名称、 `statestore` 和键 `AMS` 作为参数：

```csharp
var weatherForecast = await daprClient.GetStateAsync<WeatherForecast>("statestore", "AMS");
```

如果状态存储不包含键的任何数据 `AMS` ，则结果将为 `default(WeatherForecast)` 。

若要将数据写入数据存储，请使用 `DaprClient.SaveStateAsync<TValue>` 方法：

```csharp
daprClient.SaveStateAsync("statestore", "AMS", weatherForecast);
```

该示例使用 **最后一个写入 wins** 策略，因为 ETag 值不传递到状态存储组件。 若要将乐观并发控制 (使用 **第一写 wins** 策略的 OCC) ，请首先使用方法检索当前 ETag `DaprClient.GetStateAndETagAsync` 。 然后，使用方法编写更新的值并传递检索到的 ETag `DaprClient.TrySaveStateAsync` 。

```csharp
var (weatherForecast, etag) = await daprClient.GetStateAndETagAsync<WeatherForecast>("statestore", city);

// ... make some changes to the retrieved weather forecast

var result = await daprClient.TrySaveStateAsync("statestore", city, weatherForecast, etag);
```

`DaprClient.TrySaveStateAsync`如果在检索数据后状态存储中更改了数据 (和关联的 ETag) ，则该方法将失败。 方法返回一个布尔值，指示调用是否成功。 处理故障的策略是只需从状态存储重新加载已更新的数据，重新进行更改，然后重新提交更新。

如果你始终希望写入成功，而不考虑数据的其他更改，请使用 **最后一个写入 wins** 策略。

SDK 提供了用于批量检索数据、删除数据和执行事务的其他方法。 有关详细信息，请参阅 [Dapr .NET SDK 存储库](https://github.com/dapr/dotnet-sdk)。

### <a name="aspnet-core-integration"></a>ASP.NET Core 集成

Dapr 还支持 ASP.NET Core，这是一个跨平台框架，用于生成基于云的新式 web 应用程序。 Dapr SDK 直接将状态管理功能集成到 [ASP.NET Core 模型绑定](/aspnet/core/mvc/models/model-binding) 功能。 配置非常简单。 `IMVCBuilder.AddDapr`通过 `.AddDapr` 在类中追加扩展方法来添加， `Startup.cs` 如下面的示例中所示：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers().AddDapr();
}
```

配置后，Dapr 可以使用 ASP.NET Core 特性将键/值对直接注入控制器操作 `FromState` 。 `DaprClient`不再需要引用对象。 下一个示例显示了一个 Web API，该 API 返回给定城市的天气预报：

```csharp
[HttpGet("{city}")]
public ActionResult<WeatherForecast> Get([FromState("statestore", "city")] StateEntry<WeatherForecast> forecast)
{
    if (forecast.Value == null)
    {
      return NotFound();
    }

    return forecast.Value;
}
```

在此示例中，控制器使用属性加载天气预报 `FromState` 。 第一个属性参数是状态存储， `statestore` 。 第二个特性参数 `city` 是用于获取状态键的 [路由模板](/aspnet/core/mvc/controllers/routing#route-templates) 变量的名称。 如果省略第二个参数，则使用绑定方法参数的名称 (`forecast`) 用于查找路由模板变量。

`StateEntry`类包含为单个键/值对检索的所有信息的属性： `StoreName` 、 `Key` 、 `Value` 和 `ETag` 。 ETag 有助于实现 (OCC) 策略的开放式并发控制。 类还提供了删除或更新检索到的键/值数据的方法，无需使用 `DaprClient` 实例。 在下一个示例中， `TrySaveAsync` 方法用于使用 OCC 更新检索到的天气预报。

```csharp
[HttpPut("{city}")]
public async Task Put(WeatherForecast updatedForecast, [FromState("statestore", "city")] StateEntry<WeatherForecast> currentForecast)
{
    // update cached current forecast with updated forecast passed into service endpoint
    currentForecast.Value = updatedForecast;

    // update state store
    var success = await currentForecast.TrySaveAsync();

    // ... check result
}
```

## <a name="state-store-components"></a>状态存储组件

在撰写本文时，Dapr 为以下事务状态存储提供支持：

- Azure CosmosDB
- Azure SQL Server
- MongoDB
- PostgreSQL
- Redis

Dapr 还包括对支持 CRUD 操作的状态存储的支持，但不支持事务功能：

- Aerospike
- Azure Blob 存储
- Azure 表存储
- Cassandra
- Cloudstate
- Couchbase
- etcd
- Google Cloud Firestore
- Hashicorp Consul
- Hazelcast
- Memcached
- Zookeeper

### <a name="configuration"></a>Configuration

在为本地自承载开发初始化时，Dapr 将 Redis 注册为默认的状态存储。 下面是默认状态存储配置的示例。 请注意默认名称 `statestore` ：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  version: v1
  metadata:
  - name: redisHost
    value: localhost:6379
  - name: redisPassword
    value: ""
  - name: actorStateStore
    value: "true"
```

 > [!NOTE]
 > 许多状态存储可以注册到单个应用程序，每个应用程序都有不同的名称。

Redis 状态存储需要 `redisHost` 和 `redisPassword` 元数据来连接到 Redis 实例。 在上面的示例中，Redis 密码 (默认值为空字符串) 以纯字符串形式存储。 最佳做法是避免使用明文引用并始终使用机密引用。 若要了解有关机密管理的详细信息，请参阅第 [10 章](secrets.md)。

其他元数据字段指示执行组件 `actorStateStore` 生成块是否可以使用状态存储区。

### <a name="key-prefix-strategies"></a>密钥前缀策略

状态存储组件允许不同的策略在底层存储中存储键/值对。 回忆一项更早的示例，其中存储了客户希望购买的商品：

```console
curl -X POST http://localhost:3500/v1.0/state/statestore \
  -H "Content-Type: application/json" \
  -d '[{
        "key": "basket1",
        "value": {
          "customerId": 1,
          "items": [
            { "itemId": "DaprHoodie", "quantity": 1 }
          ]
        }
     }]'
```

使用 Redis 控制台工具，在 Redis 缓存中查看 Redis 状态存储组件如何保存数据：

```
127.0.0.1:6379> KEYS *
1) "basketservice||basket1"

127.0.0.1:6379> HGETALL basketservice||basket1
1) "data"
2) "{\"items\":[{\"itemId\":\"DaprHoodie\",\"quantity\":1}],\"customerId\":1}"
3) "version"
4) "1"
```

输出显示数据的完整 Redis **键** `basketservice||basket1` 。 默认情况下，Dapr 使用 `application id` Dapr 实例的 `basketservice` 作为密钥的前缀 () 。 此命名约定允许多个 Dapr 实例共享相同的数据存储，而不会发生键名称冲突。 对于开发人员来说， `application id` 在运行具有 Dapr 的应用程序时，始终必须指定相同的。 如果省略，则 Dapr 将生成唯一的应用程序 ID。 如果 `application id` 更改，应用程序将无法再访问使用上一个密钥前缀存储的状态。

也就是说，可以在状态存储组件文件的 "元数据" 字段中为密钥前缀配置 *常量值* `keyPrefix` 。 请看下面的示例：

```yaml
spec:
  metadata:
  - name: keyPrefix
  - value: MyPrefix
```

使用常量键前缀可以跨多个 Dapr 应用程序访问状态存储。 更多操作，将设置 `keyPrefix` 为 `none` 完全省略前缀。

## <a name="reference-application-eshopondapr"></a>引用应用程序： eShopOnDapr

本书包括一个名为的参考应用程序 `eShopOnDapr` 。 它从早期的 Microsoft 微服务引用应用程序进行建模 `eShopOnContainers` 。

原始 [eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers) 体系结构使用 `IBasketRepository` 接口来读取和写入购物篮服务的数据。 `RedisBasketRepository`类提供了使用 Redis 作为基础数据存储的实现：

```csharp
public class RedisBasketRepository : IBasketRepository
{
    private readonly ConnectionMultiplexer _redis;
    private readonly IDatabase _database;

    public RedisBasketRepository(ConnectionMultiplexer redis)
    {
        _redis = redis;
        _database = redis.GetDatabase();
    }

    public async Task<CustomerBasket> GetBasketAsync(string customerId)
    {
        var data = await _database.StringGetAsync(customerId);

        if (data.IsNullOrEmpty)
        {
            return null;
        }

        return JsonConvert.DeserializeObject<CustomerBasket>(data);
    }

    // ...
}
```

此代码使用第三方 `StackExchange.Redis` NuGet 包。 若要为给定客户加载购物篮，需执行以下步骤：

1. 将插入 `ConnectionMultiplexer` 到构造函数中。 `ConnectionMultiplexer`向文件中的依赖关系注入框架注册 `Startup.cs` ：

   ```csharp
   services.AddSingleton<ConnectionMultiplexer>(sp =>
   {
       var settings = sp.GetRequiredService<IOptions<BasketSettings>>().Value;
       var configuration = ConfigurationOptions.Parse(settings.ConnectionString, true);
       configuration.ResolveDns = true;
       return ConnectionMultiplexer.Connect(configuration);
   });
   ```

1. 使用在 `ConnectionMultiplexer` `IDatabase` 每个使用类中创建实例。

1. 使用 `IDatabase` 给定的作为键，使用实例执行 Redis StringGet 调用 `customerId` 。

1. 检查是否已从 Redis 加载数据;如果不是，则返回 `null` 。

1. 将 Redis 中的数据反序列化为 `CustomerBasket` 对象并返回结果。

在更新后的 [eShopOnDapr](https://github.com/dotnet-architecture/eShopOnDapr) 引用应用程序中，新 `DaprBasketRepository` 类将替换 `RedisBasketRepository` 类：

```csharp
public class DaprBasketRepository : IBasketRepository
{
    private const string StoreName = "eshop-basket-statestore";

    private readonly DaprClient _daprClient;

    public DaprBasketRepository(DaprClient daprClient)
    {
        _daprClient = daprClient ?? throw new ArgumentNullException(nameof(daprClient));;
    }

    public async Task<CustomerBasket> GetBasketAsync(string customerId)
    {
        return await _daprClient.GetStateAsync<CustomerBasket>(StoreName, customerId);
    }

    // ...
}
```

已更新的代码使用 Dapr .NET SDK，通过状态管理构建块读取和写入数据。 为客户加载购物篮的新步骤大大简化：

1. 将插入 `DaprClient` 到构造函数中。 `DaprClient`注册到文件中的依赖关系注入框架 `Startup.cs` 。
1. 使用 `DaprClient.GetStateAsync` 方法从已配置的状态存储中加载客户的购物篮项，并返回结果。

更新后的实现仍然使用 Redis 作为基础数据存储。 但是，Dapr 将 `StackExchange.Redis` 从应用程序中抽象化引用和复杂性。 Dapr 配置文件是必需的：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: eshop-basket-statestore
  namespace: eshop
spec:
  type: state.redis
  version: v1
  metadata:
  - name: redisHost
    value: redis:6379
  - name: redisPassword
    secretKeyRef:
      name: redisPassword
auth:
  secretStore: eshop-secretstore
```

Dapr 实现还简化了基础数据存储的更改。 例如，若要切换到 Azure 表存储，只需更改配置文件的内容。 不需要更改代码。

## <a name="summary"></a>摘要

Dapr 状态管理构建块提供了一个 API，用于在各种数据存储区中存储键/值数据。 API 为以下内容提供支持：

- 批量操作
- 强一致性和最终一致性
- 乐观并发控制
- 多项事务

.NET SDK 为 .NET Core 和 ASP.NET Core 提供特定于语言的支持。 模型绑定集成简化了访问和更新 ASP.NET Core 控制器操作方法的状态。

在 eShopOnDapr reference 应用程序中，转向 Dapr 状态管理的好处是显而易见的：

1. 新实现使用更少的代码行。
1. 它消除了第三方 API 的复杂性 `StackExchange.Redis` 。
1. 将基础 Redis 缓存替换为不同类型的数据存储现在只需要更改状态存储配置文件。

### <a name="references"></a>参考

- [Dapr 支持的状态存储](https://docs.dapr.io/operations/components/setup-state-store/supported-state-stores/)

> [!div class="step-by-step"]
> [上一页](reference-application.md)
> [下一页](service-invocation.md)

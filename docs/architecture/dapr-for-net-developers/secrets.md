---
title: Dapr 机密构建基块
description: 机密构建基块、功能、权益和应用方法的描述
author: edwinvw
ms.date: 02/17/2021
ms.openlocfilehash: 52b899b4d496aab6762f69bbee99faecfcd23d59
ms.sourcegitcommit: d623f686701b94bef905ec5e93d8b55d031c5d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2021
ms.locfileid: "103623741"
---
# <a name="the-dapr-secrets-building-block"></a>Dapr 机密构建基块

企业应用程序要求机密。 常见示例包括：

- 包含用户名和密码的数据库连接字符串。
- 用于调用外部 web API 的 API 密钥。
- 用于对外部系统进行身份验证的客户端证书。

必须谨慎管理机密，使其不会在应用程序外公开。

不久前，将应用程序机密存储在应用程序基本代码内的配置文件中是很常见的。 .NET 开发人员将 fondly 回忆 *web.config* 的文件。 虽然实现起来很简单，但将机密与代码集成在一起并不安全。 常见的失误是在推送到公共 GIT 存储库时包括文件，从而向世界公开机密。

用于构造新式分布式应用程序的一种广泛接受的方法是 [Twelve-Factor 应用程序](https://12factor.net/)。 其中介绍了一系列原则和最佳实践。 第三个因素规定， *配置和机密外部化在基本代码外。*

为了解决这一问题，.NET Core 平台包含一个 [密钥管理器](/aspnet/core/security/app-secrets#secret-manager) 功能，该功能将敏感数据存储在项目树之外的物理文件夹中。 虽然机密不受源代码管理，但此功能不会对数据进行加密。 它仅用于 **开发目的** 。

更新式、安全的做法是在 **Hashicorp 保管库** 或 **Azure Key Vault** 等机密管理工具中隔离机密。  这些工具使你能够将机密存储在外部，并跨环境改变凭据，并从应用程序代码中引用它们。 然而，每个工具都有其复杂性和学习曲线。

Dapr 提供简化密钥管理的构建基块。

## <a name="what-it-solves"></a>解决方法

Dapr [机密构建块](https://docs.dapr.io/developing-applications/building-blocks/secrets/secrets-overview/) 消除了使用机密和机密管理工具的复杂性。

- 它通过统一的接口隐藏基础管道。
- 它支持各种可 *插入* 的机密存储组件，这些组件在开发和生产之间有所不同。
- 应用程序不要求直接依赖于密钥存储库。
- 开发人员无需详细了解每个密钥存储。

Dapr 处理上述所有问题。

通过身份验证和授权来保护对机密的访问。 只有具有足够权限的应用程序可以访问机密。 在 Kubernetes 中运行的应用程序也可以使用其内置的机密管理机制。

## <a name="how-it-works"></a>工作原理

应用程序通过两种方式使用机密构建基块：

- 直接从应用程序块检索机密。
- 从 Dapr 组件配置中间接引用机密。

首先介绍如何直接检索机密。 在后面的部分中对从 Dapr 组件配置文件引用机密进行了说明。

使用机密构建块时，应用程序与 Dapr 挎斗交互。 挎斗公开了机密 API。 可以通过 HTTP 或 gRPC 调用 API。 使用以下 URL 调用 HTTP API：

```http
http://localhost:<dapr-port>/v1.0/secrets/<store-name>/<name>?<metadata>
```

URL 包含以下段：

- `<dapr-port>` 指定 Dapr 挎斗正在侦听的端口号。
- `<store-name>` 指定 Dapr 密钥存储的名称。
- `<name>` 指定要检索的机密的名称。
- `<metadata>` 提供机密的附加信息。 此段是可选的，每个密钥存储的元数据属性各不相同。 有关元数据属性的详细信息，请参阅 [机密 API 参考]([Secrets API reference | Dapr Docs](https://docs.dapr.io/reference/api/secrets_api/))。

 > [!NOTE]
 > 上述 URL 表示可用于支持 HTTP 或 gRPC 的任何开发平台的本机 Dapr API 调用。 .NET、Java 和中转等常用平台都有自己的自定义 Api。

JSON 响应包含密钥和机密的值。

图10-1 显示了 Dapr 如何处理机密 API 请求：

![使用 Dapr 机密 API 检索机密的示意图。](media/secrets/secrets-flow.png)

**图 10-1**。 使用 Dapr 机密 API 检索机密。

1. 服务调用 Dapr 机密 API 以及密钥存储的名称和要检索的机密。
1. Dapr 挎斗从机密存储中检索指定的机密。
1. Dapr 挎斗将机密信息返回给服务。

某些机密存储支持在单个机密中存储多个键/值对。 对于这些方案，响应会在单个 JSON 响应中包含多个键/值对，如以下示例中所示：

```http
GET http://localhost:3500/v1.0/secrets/secret-store/interestRates?metadata.version_id=3
```

```json
{
  "tier1-percentage": "2.5",
  "tier2-percentage": "3.8",
  "tier3-percentage": "5.1"
}
```

Dapr 机密 API 还提供了一个操作，用于检索应用程序有权访问的所有机密：

```http
http://localhost:<dapr-port>/v1.0/secrets/<store-name>/bulk
```

## <a name="use-the-dapr-net-sdk"></a>使用 Dapr .NET SDK

对于 .NET 开发人员而言，Dapr .NET SDK 精简了 Dapr 的密钥管理。 请考虑 `DaprClient.GetSecretAsync` 方法。 它使你能够以最小的努力直接从任何 Dapr 密钥存储中检索机密。 下面是一个获取 SQL Server 数据库的连接字符串机密的示例：

```csharp
var metadata = new Dictionary<string, string> { ["version_id"] = "3" };
Dictionary<string, string> secrets = await daprClient.GetSecretAsync("secret-store", "eshopsecrets", metadata);
string connectionString = secrets["customerdb"];
```

方法的参数 `GetSecretAsync` 包括：

-  ( "secret store" 的 Dapr 机密存储区组件的名称 ) 
- 要检索的机密 ( "eshopsecrets" ) 
- 可选的元数据键/值对 ( "version_id = 3" ) 

方法使用字典对象进行响应，因为机密可包含多个键/值对。 在上面的示例中，从集合引用名为的机密 `customerdb` 以返回连接字符串。

Dapr .NET SDK 还提供 .NET 配置提供程序。 它将指定的机密加载到基础 [.Net Core 配置 API](../../core/extensions/configuration.md)。 然后，正在运行的应用程序可以从 `IConfiguration` 在 ASP.NET Core 依赖关系注入中注册的字典中引用机密。

[Dapr.Extensions.Configu](https://www.nuget.org/packages/Dapr.Extensions.Configuration) NuGet 包中提供了机密配置提供程序。 可在 ASP.NET Web API 应用程序的中注册提供程序 `Program.cs` ：  

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration(config =>
        {
            var daprClient = new DaprClientBuilder().Build();
            var secretDescriptors = new List<DaprSecretDescriptor>
            {
                new DaprSecretDescriptor("eshopsecrets")
            };
            config.AddDaprSecretStore("secret-store", secretDescriptors, daprClient);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

上述示例 `eshopsecrets` 在启动时将机密集合加载到 .net 配置系统中。 注册提供程序需要的实例 `DaprClient` ，以在 Dapr 挎斗上调用密码 API。 其他参数包括密钥存储的名称，以及包含 `DaprSecretDescriptor` 机密名称的对象。

加载后，可以直接从应用程序代码中检索机密：

```csharp
public void GetCustomer(IConfiguration config)
{
    var connectionString = config["eshopsecrets"]["customerdb"];
}
```

## <a name="secret-store-components"></a>机密存储区组件

机密构建块支持多个机密存储区组件。 撰写本文时，可以使用以下密钥存储：

- 环境变量
- 本地文件
- Kubernetes 机密
- AWS 机密管理器
- Azure Key Vault
- GCP 密钥管理器
- HashiCorp 保管库

> [!IMPORTANT]
> 环境变量和本地文件组件仅适用于开发工作负荷。

以下部分说明如何配置密钥存储。

### <a name="configuration"></a>Configuration

使用 Dapr 组件配置文件配置密钥存储。 文件的典型结构如下所示：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: [component name]
  namespace: [namespace]
spec:
  type: secretstores.[secret store type]
  version: [secret store version]
  metadata:
  - name: [property name]
    value: [property value]
```

所有 Dapr 组件配置文件都需要 `name` 和可选 `namespace` 值。 另外， `type` 部分中的字段 `spec` 指定了机密存储区组件的类型。 部分中的属性 `metadata` 因机密存储区的不同而异。

### <a name="indirectly-consume-dapr-secrets"></a>间接使用 Dapr 机密

如本章前面所述，应用程序还可以通过在组件配置文件中引用机密来使用机密。 请考虑使用 Redis 缓存来存储状态的 [状态管理组件](state-management.md) ：

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
    value: localhost:6379
  - name: redisPassword
    value: e$h0p0nD@pr
```

上述配置文件包含用于连接到 Redis 服务器的 **明文** 密码。 **硬编码** 密码始终是一个不错的想法。 将此配置文件推送到公共存储库将公开密码。 在机密存储区中存储密码会显著改善这种情况。

下面的示例使用几个不同的机密存储区对此进行了演示。

### <a name="local-file"></a>本地文件

本地文件组件设计用于开发方案。 它将机密存储在 JSON 文件中的本地文件系统上。 下面是一个名为的示例 `eshop-secrets.json` 。 它包含单个机密-Redis 的密码：

```json
{
  "eShopRedisPassword": "e$h0p0nD@pr"
}
```

将此文件放置在 `components` 运行 Dapr 应用程序时指定的文件夹中。

以下密钥存储配置文件使用 JSON 文件作为密钥存储。 它还放置在文件夹中 `components` ：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: eshop-local-secret-store
  namespace: eshop
spec:
  type: secretstores.local.file
  version: v1
  metadata:
  - name: secretsFile
    value: ./components/eshop-secrets.json
  - name: nestedSeparator
    value: ":"
```

组件类型为 `secretstore.local.file` 。 `secretsFile`Metadata 元素指定密钥文件的路径。

> [!IMPORTANT]
> 机密文件的路径可以是绝对路径或相对路径。 相对路径基于应用程序的启动文件夹。 在此示例中， `components` 文件夹是包含 .net 应用程序的目录的子文件夹。

从应用程序文件夹中，启动 Dapr 应用程序， `components` 并将路径指定为命令行参数：

```console
dapr run --app-id basket-api --components-path ./components dotnet run
```

> [!NOTE]
> 上面的示例适用于在自承载模式下运行 Dapr。 对于 Kubernetes 托管，请考虑使用卷装入。

`nestedSeparator`Dapr 配置文件中的指定一个字符来 *平展* JSON 层次结构。 请考虑以下代码片段：

```json
{
    "redisPassword": "some password",
    "connectionStrings": {
        "customerdb": "some connection string",
        "productdb": "some connection string"
    }
}
```

使用冒号作为分隔符，可以 `customerdb` 使用键来检索连接字符串 `connectionStrings:customerdb` 。

> [!NOTE]
> 冒号 `:` 为默认分隔符值。

在下一个示例中，状态管理配置文件引用本地机密存储区组件以获取用于连接到 Redis 服务器的密码：

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
    value: localhost:6379
  - name: redisPassword
    secretKeyRef:
      name: eShopRedisPassword
      key: eShopRedisPassword
auth:
  secretStore: eshop-local-secret-store
```

`secretKeyRef`元素引用包含密码的机密。 它将替换前面的 *明文* 值。 机密名称和密钥名称 `eShopRedisPassword` 引用密钥。 机密管理组件的名称 `eshop-local-secret-store` 可在 metadata 元素中找到 `auth` 。

您可能想知道 `eShopRedisPassword` 密钥引用中名称和密钥的原因是相同的。 在本地文件密钥存储中，不会使用单独的名称标识机密。 在下一个示例中，该方案将不同于使用 Kubernetes 机密。

### <a name="kubernetes-secret"></a>Kubernetes 机密

此第二个示例重点介绍在 Kubernetes 中运行的 Dapr 应用程序。 它使用 Kubernetes 提供的标准机密机制。 使用 Kubernetes CLI (`kubectl`) 创建一个 `eshop-redis-secret` 包含密码的名为的机密：

```console
kubectl create secret generic eshopsecrets --from-literal=redisPassword=e$h0p0nD@pr -n eshop
```

创建后，可以在 "状态管理" 的组件配置文件中引用该机密：

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
      name: eshopsecrets
      key: redisPassword
auth:
  secretStore: kubernetes
```

`secretKeyRef`元素分别指定 Kubernetes 机密的名称和机密的密钥、 `eshopsecrets` 和 `redisPassword` 。 `auth`Metadata 节指示 Dapr 使用 Kubernetes 机密管理组件。

> [!NOTE]
> 身份验证是使用 Kubernetes 机密时的默认值，可以省略。

在生产设置中，通常会将机密作为自动 CI/CD 管道的一部分创建。 这样做可确保只有具有足够权限的人员才能访问和更改机密。 开发人员无需知道机密的实际值即可创建配置文件。

### <a name="azure-key-vault"></a>Azure Key Vault

下一个示例适用于实际的生产方案。 它使用 **Azure Key Vault** 作为密钥存储。 Azure Key Vault 是一种托管的 Azure 服务，可让机密安全地存储在云中。

要使此示例正常工作，必须满足以下先决条件：

- 已保护对 Azure 订阅的管理访问。
- 你预配了一个名为 `eshopkv` 的 Azure Key Vault，它包含一个名为 `redisPassword` 的机密，其中包含用于连接到 Redis 服务器的密码。
- 已在 Azure Active Directory 中创建 [服务主体](/azure/active-directory/develop/howto-create-service-principal-portal) 。
- 已为此服务主体安装了 X509 证书， (包含本地文件系统上的公钥和私钥) 。

> [!NOTE]
> 服务主体是应用程序可用于对 Azure 服务进行身份验证的标识。 服务主体使用 X509 证书。 应用程序使用此证书作为凭据对自己进行身份验证。

[Dapr Azure Key Vault 密钥存储文档](https://docs.dapr.io/operations/components/setup-secret-store/supported-secret-stores/azure-keyvault/)提供了创建和配置 Key Vault 环境的分步说明。

#### <a name="use-key-vault-when-running-in-self-hosted-mode"></a>在自承载模式下运行时使用 Key Vault

在 Dapr 自承载模式下使用 Azure Key Vault 需要以下配置文件：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: eshop-azurekv-secret-store
  namespace: eshop
spec:
  type: secretstores.azure.keyvault
  version: v1
  metadata:
  - name: vaultName
    value: eshopkv
  - name: spnTenantId
    value: "619926af-a7c3-4e95-93ed-4ecc4e3e652b"
  - name: spnClientId
    value: "6cf48032-6c38-43be-9d6f-2a43ce736b09"
  - name: spnCertificateFile
    value : "azurekv-spn-cert.pfx"
```

密钥存储类型为 `secretstores.azure.keyvault` 。 `metadata`用于配置对 Key Vault 的访问权限的元素需要以下属性：

- `vaultName`包含 Azure Key Vault 的名称。
- `spnTenantId`包含用于对 Key Vault 进行身份验证的服务主体的 *租户 ID* 。
- `spnClientId`包含用于对 Key Vault 进行身份验证的服务主体的 *应用程序 ID* 。
- `spnCertificateFile`包含服务主体的证书文件路径，以对 Key Vault 进行身份验证。

> [!TIP]
> 可以从 Azure 门户或 Azure CLI 复制服务主体信息。

现在，应用程序可以从 Azure Key Vault 中检索 Redis 密码。

#### <a name="use-key-vault-when-running-on-kubernetes"></a>在 Kubernetes 上运行时使用 Key Vault

使用 Dapr 和 Kubernetes 的 Azure Key Vault 也需要服务主体针对 Azure Key Vault 进行身份验证。

首先，使用 kubectl CLI 工具创建包含证书文件的 *Kubernetes 机密* ：

```console
kubectl create secret generic [k8s_spn_secret_name] --from-file=[pfx_certificate_file_local_path] -n eshop
```

此命令需要两个命令行参数：

- `[k8s_spn_secret_name]` Kubernetes 密钥存储中的机密名称。
- `[pfx_certificate_file_local_path]` 是 X509 证书文件的路径。

创建后，可以在密钥存储组件配置文件中引用 Kubernetes 机密：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: eshop-azurekv-secret-store
  namespace: eshop
spec:
  type: secretstores.azure.keyvault
  version: v1
  metadata:
  - name: vaultName
    value: [your_keyvault_name]
  - name: spnTenantId
    value: "619926af-a7c3-4e95-93ed-4ecc4e3e652b"
  - name: spnClientId
    value: "6cf48032-6c38-43be-9d6f-2a43ce736b09"
  - name: spnCertificate
    secretKeyRef:
      name: [k8s_spn_secret_name]
      key: [pfx_certificate_file_local_name]
auth:
    secretStore: kubernetes
```

此时，在 Kubernetes 中运行的应用程序可以从 Azure Key Vault 检索 Redis 密码。

> [!IMPORTANT]
> 将服务主体的 X509 证书文件保存在安全的位置至关重要。 最好将它放在源代码存储库外的已知文件夹中。 然后，配置文件可以引用此众所周知的文件夹中的证书文件。 在本地开发计算机上，您负责将证书复制到文件夹。 对于自动部署，管道会将证书复制到部署了该应用程序的计算机。 最佳做法是在每个环境中使用不同的服务主体。 这样做可以防止开发环境中的服务主体访问生产环境中的机密。

在 Azure Kubernetes Service (AKS) 中运行时，最好使用 [Azure 托管标识](/azure/active-directory/managed-identities-azure-resources/overview) 对 Azure Key Vault 进行身份验证。 托管标识超出了本书的范围，但在 [与托管标识](https://docs.dapr.io/operations/components/setup-secret-store/supported-secret-stores/azure-keyvault-managed-identity/) 相关的 Azure Key Vault 文档中进行了介绍。

### <a name="scope-secrets"></a>范围机密

机密作用域允许您控制您的应用程序可以访问的机密。 在 Dapr 挎斗配置文件中配置作用域。 [Dapr 配置文档](https://docs.dapr.io/operations/configuration/configuration-overview/)提供了有关如何对机密进行作用域的说明。

下面是包含机密作用域的 Dapr 挎斗配置文件的示例：

```yml
apiVersion: dapr.io/v1alpha1
kind: Configuration
metadata:
  name: dapr-config
  namespace: eshop
spec:
  tracing:
    samplingRate: "1"
  secrets:
    scopes:
      - storeName: eshop-azurekv-secret-store
        defaultAccess: allow
        deniedSecrets: ["redisPassword", "apiKey"]
```

为每个密钥存储指定作用域。 在上面的示例中，机密存储区命名为 `eshop-azurekv-secret-store` 。 使用以下属性配置对机密的访问：

| 属性         | 值               | 说明                                                                                                                       |
|------------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| `defaultAccess`  | `allow` 或 `deny`   | 允许或拒绝对指定密钥存储中 *所有* 机密的访问。 此属性是可选的，默认值为 `allow` 。 |
| `allowedSecrets` | 机密密钥列表 | 数组中指定的机密将可供访问。 此属性是可选的。                                                     |
| `deniedSecrets`  | 机密密钥列表 | 数组中指定的机密将不可访问。 此属性是可选的。                                                 |

`allowedSecrets`和 `deniedSecrets` 属性优先于 `defaultAccess` 属性。 假设指定 `defaultAccess: allowed` 和 `allowedSecrets` 列表。 在这种情况下， `allowedSecrets` 应用程序只能访问列表中的机密。

## <a name="reference-application-eshopondapr"></a>引用应用程序： eShopOnDapr

EShopOnDapr 参考应用程序对两个机密使用机密构建基块：

- 用于连接到 Redis 缓存的密码。
- 用于使用 Twilio Sendgrid API 的 API 密钥。 应用程序使用 Twillio 通过 Dapr 输出绑定 (发送电子邮件，如 [绑定构建块章节](bindings.md)) 中所述。

使用 Docker Compose 运行应用程序时，将使用 **本地文件** 密钥存储。 组件配置文件位于 `eshop-secretstore.yaml` `dapr/components` eShopOnDapr 存储库的文件夹中：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: eshop-secretstore
  namespace: eshop
spec:
  type: secretstores.local.file
  version: v1
  metadata:
  - name: secretsFile
    value: ./components/eshop-secretstore.json
```

配置文件引用 `eshop-secretstore.json` 位于同一文件夹中的本地存储文件：

```json
{
    "redisPassword": "**********",
    "sendgridAPIKey": "**********"
}
```

在 `components` 命令行中指定文件夹，并将其作为本地文件夹装载到 Dapr 挎斗容器中。 下面是 `docker-compose.override.yml` 存储库根目录中的文件中用于指定卷装入的代码段：

```yaml
  ordering-backgroundtasks-dapr:
    command: ["./daprd",
      "-app-id", "ordering-backgroundtasks",
      "-app-port", "80",
      "-dapr-grpc-port", "50004",
      "-components-path", "/components",
      "-config", "/configuration/eshop-config.yaml"
      ]
    volumes:
      - "./dapr/components/:/components"
      - "./dapr/configuration/:/configuration"
```

> [!NOTE]
> Docker Compose 重写文件包含特定于环境的配置值。

`/components`卷装入和 `--components-path` 命令行参数会传递到 `daprd` 启动命令。

配置后，其他组件配置文件也可以引用该机密。 下面是发布/订阅组件配置使用机密的示例：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: pubsub
  namespace: eshop
spec:
  type: pubsub.redis
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

在前面的示例中，本地 Redis 存储用于引用机密。

## <a name="summary"></a>摘要

Dapr 机密构建块提供了存储和检索敏感配置设置（例如密码和连接字符串）的功能。 它将机密保密，防止无意中泄露机密。

构建基块支持多个不同的机密存储，并通过 Dapr 机密 API 隐藏其复杂性。

Dapr .NET SDK 提供了一个 `DaprClient` 用于检索机密的对象。 它还包括将机密添加到 .NET Core 配置系统的 .NET 配置提供程序。 加载后，可以在 .NET 代码中使用这些机密。

可以使用机密范围来控制对特定机密的访问。

## <a name="references"></a>参考

- [超出 Twelve-Factor 应用程序](https://tanzu.vmware.com/content/blog/beyond-the-twelve-factor-app)

>[!div class="step-by-step"]
>[上一页](observability.md)
>[下一页](summary.md)

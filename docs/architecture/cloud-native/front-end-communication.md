---
title: 前端客户端通信
description: 了解前端客户端如何与云本机系统进行通信
author: robvet
ms.date: 01/19/2021
ms.openlocfilehash: b28fd05aded652057deecd6814199e0360202a07
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102604510"
---
# <a name="front-end-client-communication"></a>前端客户端通信

在云本机系统中，前端客户端 (移动、web 和桌面应用程序) 需要通信通道与独立后端微服务交互。  

有哪些选项？

为简单起见，前端客户端可以直接与后端微服务 *通信* ，如图4-2 所示。

![直接客户端到服务的通信](./media/direct-client-to-service-communication.png)

**图 4-2**。 直接客户端到服务的通信

使用此方法时，每个微服务都有一个公共终结点，可供前端客户端访问。 在生产环境中，可以将负载均衡器放在微服务的前面，按比例路由流量。

虽然实现简单，但仅对简单的微服务应用程序进行直接客户端通信。 此模式将前端客户端紧密耦合到核心后端服务，从而为一些问题打开门，其中包括：

- 对后端服务重构的客户端敏感。
- 因为核心后端服务直接公开，所以攻击面会更广泛。
- 跨每个微服务的交叉切削问题的重复。
- 过于复杂的客户端代码-客户端必须跟踪多个终结点并以弹性方式处理故障。

相反，广泛接受的云设计模式是在前端应用程序和后端服务之间实现 [API 网关服务](../microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern.md) 。 模式如图4-3 所示。

![API 网关模式](./media/api-gateway-pattern.png)

**图 4-3.** API 网关模式

在上图中，请注意 API 网关服务如何抽象后端核心微服务。 作为 web API 实现，它充当 *反向代理*，将传入流量路由到内部微服务。

网关将客户端与内部服务分区和重构隔离开来。 如果更改后端服务，则可以在网关上使用它，而无需中断客户端。 这也是你的第一道防线，用于进行交叉切削关注，如标识、缓存、复原、计量和限制。 许多这些交叉切削问题都可以从后端核心服务关闭到网关，简化后端服务。

必须小心，使 API 网关保持简单快捷。 通常，业务逻辑会在网关之外。 复杂的网关风险成为瓶颈，最终成为单体架构本身。 较大的系统通常会公开多个 API 网关，并按客户端类型 (移动、web、桌面) 或后端功能。 前端模式的 [后端](/azure/architecture/patterns/backends-for-frontends) 提供了实现多个网关的方向。 模式如图4-4 所示。

![前端模式](./media/backend-for-frontend-pattern.png)

**图 4-4.** 前端模式

请注意，在上图中，如何将传入流量发送到特定的 API 网关（基于客户端类型）： web、移动或桌面应用。 这种方法非常合理，因为每个设备的功能在外形规格、性能和显示限制方面的差异大不相同。 通常，移动应用程序的功能与浏览器或桌面应用程序的功能不同。 可以对每个网关进行优化，以匹配相应设备的功能和功能。

首先，你可以构建自己的 API 网关服务。 GitHub 快速搜索将提供许多示例。 但是，有几个框架和商业网关产品可用。

## <a name="ocelot-gateway"></a>Ocelot 关

对于简单的 .NET 云本机应用程序，你可能会考虑到 [Ocelot 网关](https://github.com/ThreeMammals/Ocelot)。 Ocelot 是为 .NET 微服务创建的开源 API 网关，需要在其系统中提供统一的入口点。 它是轻型、快速、可缩放的。

与任何 API 网关一样，其主要功能是将传入的 HTTP 请求转发到下游服务。 此外，它还支持 .NET 中间件管道中可配置的各种功能。 下表中提供了其功能集。

|Ocelot 功能  | |
| :-------- | :-------- |
| 路由 | 身份验证 |
| 请求聚合 | 授权 |
| 服务发现 (与 Consul 和 Eureka)  | 限制 |
| 负载平衡 | 日志记录、跟踪 |
| 缓存 | 标头/查询字符串转换 |
| 相关 Pass-Through | 自定义中间件 |
| 服务质量 | 重试策略 |

每个 Ocelot 网关指定 JSON 配置文件中的上游和下游地址和可配置功能。 客户端将 HTTP 请求发送到 Ocelot 网关。 接收后，Ocelot 通过其管道将 HttpRequest 对象传递到其配置所指定的状态。 管道结束时，Ocelot 会创建一个新的 HTTPResponseObject，并将其传递给下游服务。 对于响应，Ocelot 会反转管道，并将响应发送回客户端。

Ocelot 以 NuGet 包的形式提供。 它以 NET Standard 2.0 为目标，使其与 .NET Core 2.0 + 和 .NET Framework 4.6.1 + 运行时兼容。 Ocelot 与说 HTTP 的任何内容集成，并在 .NET Core 支持的平台上运行： Linux、macOS 和 Windows。 Ocelot 可扩展，并支持许多现代平台，包括 Docker 容器、Azure Kubernetes 服务或其他公有云。  Ocelot 与开源包（如 [Consul](https://www.consul.io)、 [GraphQL](https://graphql.org)和 Netflix 的 [Eureka](https://github.com/Netflix/eureka)）集成。

对于简单的云本机应用程序，请考虑使用 Ocelot，这些应用程序不需要商业 API 网关的丰富功能集。

## <a name="azure-application-gateway"></a>Azure 应用程序网关

对于简单的网关要求，你可以考虑 [Azure 应用程序网关](/azure/application-gateway/overview)。 它以 Azure [PaaS 服务](https://azure.microsoft.com/overview/what-is-paas/)的形式提供，包括 URL 路由、SSL 终止和 Web 应用程序防火墙等基本网关功能。 该服务支持 [第7层负载均衡](https://www.nginx.com/resources/glossary/layer-7-load-balancing/) 功能。 使用第7层时，可以根据 HTTP 消息的实际内容来路由请求，而不是只路由低级 TCP 网络数据包。

在本指南中，我们宣传在 [Kubernetes](https://www.infoworld.com/article/3268073/what-is-kubernetes-your-next-application-platform.html)中托管云本机系统。 容器 orchestrator Kubernetes 自动执行容器化工作负荷的部署、缩放和操作问题。 Azure 应用程序网关可配置为 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) 群集的 API 网关。

利用 [应用程序网关入口控制器](https://azure.github.io/application-gateway-kubernetes-ingress/) ，Azure 应用程序网关可以直接使用 [Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)。 图4.5 显示了此体系结构。

![应用程序网关入口控制器](./media/application-gateway-ingress-controller.png)

**图4-5。** 应用程序网关入口控制器

Kubernetes 包含支持 HTTP (级别 7) 负载平衡的内置功能，称为 [入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)。 入口定义一组规则，用于确定如何将 AKS 中的微服务实例公开给外界。 在上图中，入口控制器解释为群集配置的入口规则并自动配置 Azure 应用程序网关。 根据这些规则，应用程序网关将流量路由到在 AKS 内运行的微服务。 入口控制器侦听入站规则的更改，并对 Azure 应用程序网关进行适当的更改。

## <a name="azure-api-management"></a>Azure API 管理

对于适中到大规模的云本机系统，可以考虑 [AZURE API 管理](https://azure.microsoft.com/services/api-management/)。 这是一项基于云的服务，不仅能解决 API 网关需求，而且还提供全面的开发人员和管理体验。 API 管理如图4-6 所示。

![Azure API 管理](./media/azure-api-management.png)

**图 4-6.** Azure API 管理

首先，API 管理公开了一个网关服务器，该服务器允许根据可配置的规则和策略对后端服务进行受控访问。 这些服务可以位于 Azure 云中、本地的数据中心或其他公有云。 API 密钥和 JWT 令牌决定谁可以执行什么操作。 出于分析目的记录所有流量。

对于开发人员而言，API 管理提供了一个开发人员门户，该门户提供对服务、文档以及调用这些服务的示例代码的访问。 开发人员可以使用 Swagger/开放式 API 来检查服务终结点并分析其使用情况。 此服务适用于主要开发平台： .NET、Java、Golang 等。

发布者门户公开管理仪表板，管理员在此仪表板中公开 Api 并管理其行为。 可以授予服务访问权限、监视服务运行状况和收集服务遥测。 管理员将 *策略* 应用于每个终结点，以影响行为。 [策略](/azure/api-management/api-management-howto-policies) 是针对每个服务调用按顺序执行的预生成语句。  将策略配置为入站调用、出站调用，或在出现错误时调用。 策略可以在不同的服务范围内应用，以便在结合策略时启用确定性排序。 该产品附带了大量预先生成的 [策略](/azure/api-management/api-management-policies)。

以下示例说明了策略如何影响云本机服务的行为：  

- 限制服务访问。
- 强制执行身份验证。  
- 如果需要，限制来自单个源的调用。
- 启用缓存。
- 阻止从特定 IP 地址进行的调用。
- 控制服务的流。
- 将请求从 SOAP 转换为 REST 或不同数据格式（如从 XML 到 JSON）。

Azure API 管理可以公开托管在云或数据中心的任何位置托管的后端服务。 对于可以在云本机系统中公开的旧服务，它支持 REST 和 SOAP Api。 甚至可以通过 API 管理公开其他 Azure 服务。 可以在 azure [服务总线](https://azure.microsoft.com/services/service-bus/) 或 [azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)等 azure 支持服务的基础上放置托管 API。 Azure API 管理不包括内置负载平衡支持，应结合使用负载均衡服务。

Azure API 管理跨 [四个不同的层](https://azure.microsoft.com/pricing/details/api-management/)提供：

- 开发人员
- 基本
- 标准
- 高级

开发人员层适用于非生产工作负荷和评估。 其他层提供越来越多的功能、功能和更高的服务级别协议， (Sla) 。 高级层提供了 [Azure 虚拟网络](/azure/virtual-network/virtual-networks-overview) 和 [多区域支持](/azure/api-management/api-management-howto-deploy-multi-region)。 所有层都具有固定的每小时价格。

Azure 云还为 Azure API 管理提供 [无服务器层](https://azure.microsoft.com/blog/announcing-azure-api-management-for-serverless-architectures/) 。 作为 *消耗定价层*，该服务是围绕无服务器计算模型设计的 API 管理的一个变体。 与先前显示的 "预分配" 定价层不同，消耗层提供即时预配和按操作付费定价。

它在以下用例中启用 API 网关功能：

- 使用 [Azure Functions](/azure/azure-functions/functions-overview) 和 [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)等无服务器技术实现了微服务。
- Azure 后备服务资源（如服务总线队列和主题、Azure 存储等）。
- 微服务：流量偶尔出现大的峰值，但仍保持较低的时间。

消耗层使用相同的基础服务 API 管理组件，但基于动态分配的资源使用完全不同的体系结构。 它与无服务器计算模型完全一致：

- 没有要管理的基础结构。
- 无空闲容量。
- 高可用性。
- 自动缩放。
- 成本基于实际使用情况。
  
对于将无服务器资源作为 Api 公开的云本机系统，新的消耗层是一个不错的选择。

## <a name="real-time-communication"></a>实时通信

对于通过 HTTP 与后端云本机系统进行通信的前端应用程序，"实时" 或 "推送" 是一种选择。 应用程序（如财政股票、在线教育、游戏和作业进度更新）需要来自后端的即时实时响应。 对于普通的 HTTP 通信，客户端无法知道何时有新数据可用。 客户端必须持续 *轮询* 或向服务器发送请求。 通过 *实时* 通信，服务器可以随时将新数据推送到客户端。

实时系统通常由高频数据流和大量并发客户端连接来表征。 手动实现实时连接可能很快就会变得复杂，这需要不重要的基础结构来确保可伸缩性和可靠的消息传送到连接的客户端。 你可以自行管理 Azure Redis 缓存实例，以及一组配置了用于客户端关联的粘滞会话的负载均衡器。

[Azure SignalR 服务](https://azure.microsoft.com/services/signalr-service/) 是一项完全托管的 azure 服务，可简化云本机应用程序的实时通信。 精简的技术实现细节，如容量预配、缩放和持续连接。 使用99.9% 的服务级别协议处理它们。 你专注于应用程序功能，而不是基础结构管道。

启用后，基于云的 HTTP 服务可以直接将内容更新推送到连接的客户端，包括浏览器、移动和桌面应用程序。 无需轮询服务器就可以更新客户端。 Azure SignalR 对创建实时连接的传输技术（包括 Websocket、Server-Side 事件和长轮询）进行了抽象。 开发人员重点介绍如何将消息发送到连接的客户端的所有或特定子集。

图4-7 显示了一组 HTTP 客户端，这些客户端连接到启用了 Azure SignalR 的云本机应用程序。

![Azure SignalR](./media/azure-signalr-service.png)

**图 4-7.** Azure SignalR

Azure SignalR 服务的另一个优点是实现无服务器云本机服务。 或许你的代码是按需执行的，Azure Functions 触发器。 这种情况可能比较棘手，因为你的代码不会与客户端保持长时间的连接。 Azure SignalR 服务可以处理这种情况，因为该服务已为用户管理连接。

Azure SignalR 服务可与其他 Azure 服务（例如 Azure SQL 数据库、服务总线或 Redis 缓存）紧密集成，从而为你的云本机应用程序打开了许多可能性。

>[!div class="step-by-step"]
>[上一页](communication-patterns.md)
>[下一页](service-to-service-communication.md)

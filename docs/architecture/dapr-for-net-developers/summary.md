---
title: 摘要和前景
description: 概括介绍了重要的 Dapr 结论，并探讨了领先的路上。
ms.date: 02/17/2021
author: robvet
ms.openlocfilehash: fdbb92d067c29db56aa7449b8fe3c974c8c132b8
ms.sourcegitcommit: d623f686701b94bef905ec5e93d8b55d031c5d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2021
ms.locfileid: "103624066"
---
# <a name="summary-and-the-road-ahead"></a>摘要和前景

我们正在 Dapr 航班结束。 从 [第2章](dapr-at-20000-feet.md) 开始，从20000英尺开始的 jet 飞机是最终的方法，并是关于地面。

随着飞机出租车，让我们花点时间来回顾本指南中的一些重要结论：

- **Dapr** -Dapr 是一种 *分布式应用程序运行时* ，它可以简化生成分布式应用程序的方式。 它公开构建基块和可插入组件的体系结构。 Dapr 提供了一个 **动态胶水** ，它将应用程序与 Dapr 运行时中存在的基础结构功能进行绑定。 你和你的团队将精力集中于向客户提供业务功能，而不是构建基础结构管道。

- **开放源和跨平台** -本机 Dapr API 可由支持 HTTP 或 gRPC 的 *任何平台* 使用。 Dapr 还为常见开发平台提供特定于语言的 Sdk。 Dapr v1.0 支持中转、Python、.NET、Java、PHP 和 JavaScript。

- **构建基块** -Dapr 构建基块封装分布式应用程序功能。 撰写本文时，Dapr 支持图11-1 中所示的七个构造块。

![Dapr 构建基块](./media/dapr-at-20000-feet/building-blocks.png)

**图 11-1**。 Dapr 构建基块。

- **组件** -Dapr 组件提供每个 Dapr 构建块功能的具体实现。 它们公开一个公共接口，使开发人员能够在不更改应用程序代码的情况下交换组件实现。 图11-2 显示组件、构建基块和服务之间的关系。

![Dapr 构建基块集成](./media/dapr-at-20000-feet/building-blocks-integration.png)

图 11-2。 Dapr 构建基块集成。

- **分支** -Dapr 在挎斗体系结构中与应用程序一起运行，可以作为容器的单独进程。 应用程序通过 HTTP 和 gRPC 与 Dapr Api 通信。 分支提供隔离和封装，因为它们不是服务的一部分，而是连接到它。 图11-3 显示了挎斗的体系结构。

![挎斗体系结构](./media/dapr-at-20000-feet/sidecar-generic.png)

图 11-3。 挎斗体系结构。

- **宿主环境** Dapr 提供跨平台支持，可以在多个环境中运行。 撰写本文时，环境包含本地自承载模式和 Kubernetes。

- **eShopOnDapr** -此书籍包含一个名为 [eShopOnDapr](https://github.com/dotnet-architecture/eShopOnDapr)的附属引用应用程序。 使用常用的电子商务应用程序域，引用应用程序演示了每个构建基块的使用情况。 这是一种广泛使用的 [eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers)的演变。

## <a name="the-road-ahead"></a>领先的路上

期待着，Dapr 有可能对分布式应用程序开发产生深远的影响。 Dapr 团队及其开源参与者的预期情况如何？

撰写本文时，Dapr 的建议增强列表包括：

- 现有构建基块的功能增强：
  - 状态管理中的查询功能使你能够检索多个值。
  - 通过 "发布/订阅" 中的主题筛选，您可以基于内容筛选主题。
  - 可观察性中的应用程序跟踪 API，无需绑定到特定库即可直接在应用程序中提供跟踪。
  - 为参与者编程模型提供事件驱动功能的参与者的绑定和发布/订阅支持。 绑定组件将触发事件和消息调用执行组件中的方法。

- 新构建基块：
  - 用于读取和写入配置数据的配置 API 构建基块。 该块将绑定到包含 Azure Configuration Manager 或 GCP 配置管理的提供程序。
  - Http 缩放到零自动缩放。
  - 领导选举构建基块提供单一实例和锁定语义功能。
  - 用于服务调用的透明代理构建块，使你能够基于网络级别的 Url 或 DNS 地址来路由消息。
  - 复原构建块 (断路熔断器，隔舱 & 超时) 。

- 与框架和云本机技术的集成。 示例包括：
  - Django
  - Nodejs
  - Express
  - Kyma
  - 中途

- 新语言 Sdk：
  - JavaScript
  - RUST
  - C++

- 新的宿主平台：
  - VM
  - Azure IoT Edge
  - Azure Stack Edge
  - Azure Service Fabric

- 开发人员和操作员生产力工具：
  - VS Code 扩展。
  - 用于本地调试 DevOps 管道开发的远程开发人员容器。
  - Dapr 操作仪表板增强功能，可更深入地了解管理 Dapr 应用程序的操作问题。

Dapr 版本1.0 为开发人员提供了引人注目的工具箱来构建分布式应用程序。 如建议的增强列表所示，Dapr 处于积极开发下，具有许多新功能。 请继续关注 [Dapr 站点](https://dapr.io/) 和 [Dapr 公告博客](https://cloudblogs.microsoft.com/opensource/2019/10/16/announcing-dapr-open-source-project-build-microservice-applications/) ，以供将来更新。

>[!div class="step-by-step"]
>[上一页](secrets.md)

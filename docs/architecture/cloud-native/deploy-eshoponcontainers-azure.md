---
title: 将 eShopOnContainers 部署到 Azure
description: 使用 Azure Kubernetes 服务、Helm 和 DevSpaces 部署 eShopOnContainers 应用程序。
ms.date: 01/19/2021
ms.openlocfilehash: da68fa1510841b6cef9117b6d84e7ed30c565698
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102604562"
---
# <a name="deploying-eshoponcontainers-to-azure"></a>将 eShopOnContainers 部署到 Azure

可以将 eShopOnContainers 应用程序部署到各种 Azure 平台。 推荐的方法是将应用程序部署到 Azure Kubernetes Services (AKS) 。 Helm 是一种 Kubernetes 部署工具，可用于降低部署复杂性。 开发人员可以根据需要实现 Kubernetes 的 Azure Dev Spaces，以简化开发过程。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

若要将 eShop 托管在 AKS 中，第一步是创建 AKS 群集。 为此，你可以使用 Azure 门户，这将指导你完成所需的步骤。 你还可以从 Azure CLI 创建群集，注意启用 Role-Based Access Control (RBAC) 和应用程序路由。 EShopOnContainers 文档详细介绍了创建你自己的 AKS 群集的步骤。 创建后，可以从 Kubernetes 仪表板访问和管理群集。

你现在可以使用 Helm 将 eShop 应用程序部署到群集。

## <a name="deploying-to-azure-kubernetes-service-using-helm"></a>使用 Helm 部署到 Azure Kubernetes 服务

Helm 是直接与 Kubernetes 配合使用的应用程序包管理器工具。 它可帮助你定义、安装和升级 Kubernetes 应用程序。 虽然可通过自定义 CLI 脚本或简单的部署文件将简单应用部署到 AKS，但复杂应用可以包含许多 Kubernetes 对象，并受益于 Helm。

使用 Helm，应用程序包括基于文本的配置文件（称为 Helm 图），以声明方式描述 Helm 包中的应用程序和配置。 图表使用标准 YAML 格式的文件来描述一组相关的 Kubernetes 资源。 它们与它们所描述的应用程序代码一起版本化。 Helm 图的范围从简单到复杂，具体取决于它们所描述的安装的要求。

Helm 由命令行客户端工具组成，该工具使用 Helm 图表，并将命令启动到名为 Tiller 的服务器组件。 Tiller 与 Kubernetes API 通信，以确保正确预配容器化工作负荷。 Helm 由云本机计算基础维护。

以下 yaml 文件提供了 Helm 模板：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Values.app.svc.marketing }}
  labels:
    app: {{ template "marketing-api.name" . }}
    chart: {{ template "marketing-api.chart" . }}
    release: {{ .Release.Name }}
    heritage: {{ .Release.Service }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app: {{ template "marketing-api.name" . }}
    release: {{ .Release.Name }}
```

请注意模板如何描述一组动态的键/值对。 调用模板时，括在大括号中的值从其他基于 yaml 的配置文件中提取。

你将在/k8s/helm 文件夹中找到 eShopOnContainers helm 图。 图2-6 显示了如何将应用程序的不同组件组织到 helm 用于定义和管理部署的文件夹结构中。

![EShopOnContainers helm 文件夹 ](./media/eshoponcontainers-helm-folder.png)
 **图 2-6**。 EShopOnContainers helm 文件夹。

使用命令安装每个单独的组件 `helm install` 。 eShop 包括一个 "部署全部" 脚本，该脚本循环遍历并使用各自的 helm 图表安装组件。 结果就是一个可重复执行的过程，该过程与源代码管理中的应用程序进行了版本控制，团队中的任何人都可以使用单行脚本命令将其部署到 AKS 群集。

> 请注意，Helm 的版本3不需要 Tiller 服务器组件。 可在 [此处](https://medium.com/better-programming/why-is-tiller-missing-in-helm-3-2347c446714)找到有关此增强功能的详细信息。

## <a name="azure-dev-spaces"></a>Azure Dev Spaces

云本机应用程序可以快速增长大而复杂，需要大量计算资源才能运行。 在这些情况下，整个应用程序不能在开发计算机上承载 (尤其是便携式计算机) 。 Azure Dev Spaces 旨在使用 AKS 解决此问题。 它使开发人员能够在 AKS 开发群集中托管应用程序的其余部分，并使用其服务的本地版本。

开发人员在 AKS 群集中共享正在运行 (开发) 实例，其中包含整个容器化应用程序。 但它们使用在计算机上设置的个人空间来本地开发其服务。 准备就绪后，它们将在 AKS 群集中从端到端测试-无需复制依赖项。 Azure Dev Spaces 将本地计算机中的代码合并到 AKS 中的服务。 团队成员可以查看其更改在实际 AKS 环境中的行为方式。 开发人员可以使用 Visual Studio 2017 或 Visual Studio Code 直接在 Kubernetes 中快速循环访问和调试代码。

在图2-7 中，可以看到，开发人员 Susie 已将自行车微服务的更新版本部署到了其开发人员空间。 然后，她就可以使用自定义 URL 以其空间的名称开头 (susie.s.dev.myapp.eus.azds.io) 来测试她的更改。

![显示自行车微服务 ](./media/azure-devspaces-one.png)
 **图 2-7** 的 eShopOnContainers 体系结构。 开发人员 Susie 将部署自己的自行车微服务版本并对其进行测试。

同时，开发人员 John 正在自定义预订微服务，并需要对其更改进行测试。 他将其更改部署到自己的开发空间，而不会与 Susie 的更改发生冲突，如图2-8 所示。 John 然后使用自己的 URL 测试其更改，该 URL 以其空间的名称作为前缀 (john.s.dev.myapp.eus.azds.io) 。

![显示 John 的保留版本的 eShopOnContainers 体系结构微服务 ](./media/azure-devspaces-two.png)
 **图 2-8**。 开发人员 John 部署自己的保留版本微服务，并对其进行测试，而不会与其他开发人员发生冲突。

使用 Azure Dev Spaces，团队可以直接使用 AKS，同时对其更改进行单独的更改、部署和测试。 此方法减少了单独专用托管环境的需要，因为每个开发人员都有效地拥有自己的 AKS 环境。 开发人员可以使用其 CLI 来处理 Azure Dev Spaces 或启动其应用程序，以便直接从 Visual Studio Azure Dev Spaces。 [了解有关 Azure Dev Spaces 工作和配置的详细信息。](/azure/dev-spaces/how-dev-spaces-works)

## <a name="azure-functions-and-logic-apps-serverless"></a>Azure Functions 和逻辑应用 (无服务器) 

EShopOnContainers 示例包括对跟踪联机市场营销活动的支持。 Azure Function 用于跟踪给定市场活动 ID 的市场营销活动详细信息。 单个 Azure 功能更简单、更充分，而不是创建完整的微服务。 Azure Functions 具有一个简单的生成和部署模型，尤其是在配置为在 Kubernetes 中运行时。 部署函数使用 Azure 资源管理器 (ARM) 模板和 Azure CLI 来编写脚本。 此活动服务不是面向客户的，它调用单个操作，使其成为 Azure Functions 的理想候选项。 函数需要最少的配置，包括数据库连接字符串数据和映像基 URI 设置。 在 Azure 门户中配置 Azure Functions。

>[!div class="step-by-step"]
>[上一页](map-eshoponcontainers-azure-services.md)
>[下一页](centralized-configuration.md)

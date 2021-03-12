---
title: Dapr 可观察性构建基块
description: 说明可观察性构建基块及其功能、优点以及如何应用它
author: edwinvw
ms.date: 02/07/2021
ms.reviewer: robvet
ms.openlocfilehash: 6add36b2030c3061ee522604b2e07f05875b98a9
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102604705"
---
# <a name="the-dapr-observability-building-block"></a>Dapr 可观察性构建基块

新式分布式系统非常复杂。 首先，你可以通过独立的、松散耦合且可部署的服务。 这些服务跨进程和服务器边界。 然后，它们使用不同类型的基础结构支持服务 (数据库、消息代理、key vault) 。 最后，这些分散的部分组合在一起构成应用程序。

由于有许多不同的移动部件，您如何了解会发生什么情况呢？ 遗憾的是，过去的旧监视方法还不够。 相反，系统必须从端到端 **观察** 。 新式 [可观察性](../cloud-native/observability-patterns.md) 做法可随时了解应用程序的运行状况。 它们允许您通过观察输出来推断内部状态。 可观察性是监视分布式应用程序和排查其问题所必需的。

用于获取可观察性的系统信息称为 **遥测**。 它可以分为四大类：

1. **分布式跟踪** 提供有关分布式事务中所涉及服务和服务之间的流量的见解。
1. **度量值** 可让你深入了解服务的性能及其资源消耗情况。
1. **日志记录** 可提供代码的执行方式以及错误发生的情况。
1. **运行状况** 终结点可让你深入了解服务的可用性。

遥测的深度取决于应用程序平台的可观察性功能。 请考虑 Azure 云。 它提供丰富的遥测体验，其中包括所有遥测类别。 如果没有任何配置，大多数 Azure IaaS 和 PaaS 服务会将遥测传播和发布到 [Azure 应用程序 Insights](/azure/azure-monitor/app/app-insights-overview) 服务。 Application Insights 通过高度可视化的仪表板显示系统日志记录、跟踪和问题区域。 甚至可以呈现一个关系图，其中显示基于服务通信的服务之间的依赖关系。

但是，如果应用程序不能使用 Azure PaaS 和 IaaS 资源，该怎么办？ 是否仍可以利用 Application Insights 丰富的遥测体验？ 答案是肯定的。 非 Azure 应用程序可以导入库、添加配置和检测代码，以便将遥测发出到 Azure 应用程序 Insights。 但是，这种方法将应用程序 **紧密耦合** 到 Application Insights。 将应用移到其他监视平台可能涉及到昂贵的重构。 避免在代码外进行紧密耦合并使用可观察性是不是很好吗？

通过 Dapr，你可以。 让我们看看 Dapr 如何可以将可观察性添加到我们的分布式应用程序。

## <a name="what-it-solves"></a>解决方法

Dapr 可观察性构建基块将可观察性与应用程序分离。 它自动捕获由 Dapr 分支和 Dapr 系统服务生成的、构成 Dapr 控制平面的流量。 块将流量与跨多个服务的单个操作关联起来。 它还公开了系统的性能指标、资源使用情况和运行状况。 遥测以开放标准格式发布，使信息可以送入你选择的监视后端。 在这里，可以对信息进行可视化、查询和分析。

由于 Dapr 抽象掉了该管道，因此应用程序不知道如何实现可观察性。 无需引用库或实现自定义检测代码。 Dapr 使开发人员能够专注于构建业务逻辑，而不是可观察性的管道。 可观察性在 Dapr 级别配置，在服务之间保持一致，即使是由不同的团队创建，并使用不同的技术堆栈构建。

## <a name="how-it-works"></a>工作原理

Dapr 的 [挎斗体系结构](dapr-at-20000-feet.md#sidecar-architecture) 启用内置可观察性功能。 服务通信时，Dapr 分支会截获流量并提取跟踪、指标和日志记录信息。 遥测以开放标准格式发布。 默认情况下，Dapr 支持 [OpenTelemetry](https://opentelemetry.io/) 和 [Zipkin](https://zipkin.io/)。

Dapr 提供可将遥测发布到不同后端监视工具的 [收集](https://docs.dapr.io/operations/monitoring/tracing/open-telemetry-collector/) 器。 这些工具提供了 Dapr 遥测用于分析和查询。 图9-1 显示了 Dapr 可观察性体系结构：

![Dapr 可观察性体系结构](media/observability/observability-architecture.png)

**图 9-1**。 Dapr 可观察性体系结构。

1. 服务 A 调用服务 B 上的操作。调用将从服务 A 的 Dapr 挎斗路由到服务 B 的挎斗。
1. 当服务 B 完成操作时，响应将通过 Dapr 分支发送回服务 A。 它们收集并发布每个请求和响应的所有可用遥测数据。
1. 配置的收集器引入遥测数据，并将其发送到监视后端。  

作为开发人员，请记住，添加可观察性不同于配置其他 Dapr 构建基块，如发布/订阅或状态管理。 添加收集器和监视后端，而不是引用构建基块。 图9-1 显示了可以配置多个收集器，它与不同的监视后端集成。

在本章开头，已标识了四个分类的遥测。 以下各节将提供每个类别的详细信息。 其中介绍了如何配置与常用监视后端集成的收集器。

### <a name="distributed-tracing"></a>分布式跟踪

分布式跟踪提供了跨分布式应用程序中的服务进行通信的流量。 交换请求和响应消息的日志是用于解决问题的有用信息源。 硬部分是对来自相同操作的 *消息进行关联* 。

Dapr 使用 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context) 来关联相关消息。 它将相同的上下文信息注入到形成唯一操作的请求和响应中。 图9-2 显示了相关的工作方式：

![W3C 跟踪上下文示例](media/observability/w3c-trace-context.png)

**图 9-2**。 W3C 跟踪上下文示例。

1. 服务 A 在服务 B 上调用操作。当服务 A 启动调用时，Dapr 将创建一个唯一的跟踪上下文并将其注入到请求中。
1. 服务 B 接收请求，并对服务 C 调用操作。 Dapr 检测传入请求包含跟踪上下文，并通过将其注入到服务 C 的传出请求来传播。  
1. 服务 C 接收请求并对其进行处理。 Dapr 检测到传入请求包含跟踪上下文，并通过将其注入到服务 B 的传出响应来传播。
1. 服务 B 接收响应并对其进行处理。 然后，它会创建新的响应，并通过将跟踪上下文注入到服务 A 的传出响应来传播跟踪上下文。

一组共同的请求和响应称为 " *跟踪*"。 图9-3 显示了一个跟踪：

![跟踪和跨越](media/observability/traces-spans.png)

**图 9-3**。 跟踪和跨越。

在图中，请注意跟踪如何表示在多个服务之间发生的唯一应用程序事务。 跟踪是 *范围* 的集合。 每个范围都代表跟踪内完成的单个操作或工作单元。 跨越是在实现唯一事务的服务之间发送的请求和响应。

后续部分介绍如何通过将跟踪遥测数据发布到监视后端来检查跟踪遥测数据。

#### <a name="use-a-zipkin-monitoring-back-end"></a>使用 Zipkin 监视后端

[Zipkin](https://zipkin.io/) 是开源分布式跟踪系统。 它可以摄取和可视化遥测数据。 Dapr 提供对 Zipkin 的默认支持。 下面的示例演示如何将 Zipkin 配置为可视化 Dapr 遥测。

##### <a name="enable-and-configure-tracing"></a>启用和配置跟踪

若要启动，必须使用 Dapr 配置文件为 Dapr 运行时启用跟踪。 下面是名为的配置文件的示例 `tracing-config.yaml` ：  

```yaml
apiVersion: dapr.io/v1alpha1
kind: Configuration
metadata:
  name: tracing-config
  namespace: default
spec:
  tracing:
    samplingRate: "1"
    zipkin:
      endpointAddress: "http://zipkin.default.svc.cluster.local:9411/api/v2/spans"
```

`samplingRate`属性指定用于发布跟踪的时间间隔。 该值必须介于 `0` 禁用 (跟踪) 并 `1` (发布每个跟踪) 。 例如，如果值为 `0.5` ，则发布每个其他跟踪，大大减少已发布的流量。 指向在 `endpointAddress` Kubernetes 群集中运行的 Zipkin 服务器上的终结点。 Zipkin 的默认端口为 `9411` 。 必须使用 Kubernetes CLI 将配置应用到 Kubernetes 群集：

```console
kubectl apply -f tracing-config.yaml
```

##### <a name="install-the-zipkin-server"></a>安装 Zipkin 服务器

在自承载模式下安装 Dapr 时，会自动安装 Zipkin 服务器，并在位于 `$HOME/.dapr/config.yaml` 或 Windows 上的默认配置文件中启用跟踪 `%USERPROFILE%\.dapr\config.yaml` 。

但是，在 Kubernetes 群集上安装 Dapr 时，默认情况下不会添加 Zipkin。 以下名为的 Kubernetes 清单文件将 `zipkin.yaml` 标准 Zipkin 服务器部署到群集：

```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: zipkin
  namespace: eshop
  labels:
    service: zipkin
spec:
  replicas: 1
  selector:
    matchLabels:
      service: zipkin
  template:
    metadata:
      labels:
        service: zipkin
    spec:
      containers:
        - name: zipkin
          image: openzipkin/zipkin-slim
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 9411
              protocol: TCP

---

kind: Service
apiVersion: v1
metadata:
  name: zipkin
  namespace: eshop
  labels:
    service: zipkin
spec:
  type: NodePort
  ports:
    - port: 9411
      targetPort: 9411
      nodePort: 32411
      protocol: TCP
      name: zipkin
  selector:
    service: zipkin

```

部署使用标准 `openzipkin/zipkin-slim` 容器映像。 Zipkin 服务公开 Zipkin web 前端，你可以使用它来查看端口上的遥测数据 `32411` 。 使用 Kubernetes CLI 将 Zipkin 清单文件应用到 Kubernetes 群集并部署 Zipkin 服务器：

```console
kubectl apply -f zipkin.yaml
```

##### <a name="configure-the-services-to-use-the-tracing-configuration"></a>将服务配置为使用跟踪配置

现在，所有内容都已正确设置，可以开始发布遥测数据。 在应用程序中部署的每个 Dapr 挎斗都必须在启动时指示发出遥测数据。 为此，请将 `dapr.io/config` 引用配置的批注添加 `tracing-config` 到每个服务的部署中。 下面是包含批注的 eShop 排序 API 服务的清单文件的示例：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ordering-api
  namespace: eshop
  labels:
    app: eshop
spec:
  replicas: 1
  selector:
    matchLabels:
      app: eshop
  template:
    metadata:
      labels:
        app: simulation
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "ordering-api"
        dapr.io/config: "tracing-config"
    spec:
      containers:
      - name: simulation
        image: eshop/ordering.api:linux-latest
```

##### <a name="inspect-the-telemetry-in-zipkin"></a>检查 Zipkin 中的遥测数据

启动应用程序后，Dapr 分支将向 Zipkin 服务器发出遥测数据。 若要检查此遥测数据，请将 web 浏览器指向 <http://localhost:32411> 。 你将看到 Zipkin web 前端：

![Zipkin 起始页](media/observability/zipkin.png)

在 " *查找跟踪* " 选项卡上，可以查询跟踪。 按 " *运行查询* " 按钮而不指定任何限制将显示所有引入 *跟踪*：

![跟踪列表](media/observability/zipkin-traces-overview.png)

单击特定跟踪旁边的 " *显示* " 按钮将显示该跟踪的详细信息：

![跟踪的详细信息](media/observability/zipkin-trace-details.png)

详细信息页上的每一项都是一个范围，该范围表示作为选定跟踪的一部分的请求。

##### <a name="inspect-the-dependencies-between-services"></a>检查服务之间的依赖关系

由于 Dapr 分支处理服务之间的流量，因此 Zipkin 可以使用跟踪信息来确定服务之间的依赖关系。 若要查看其工作方式，请在 Zipkin 网页上，单击 " *依赖项* " 选项卡，然后选择带有放大镜的按钮。 Zipkin 将显示服务及其依赖项的概述：

![Zipkin 中的依赖项关系图](media/observability/zipkin-dependencies.png)

服务之间的线条上的动画点表示请求，并从源移动到目标。 红点表示失败的请求。

#### <a name="use-a-jaeger-or-new-relic-monitoring-back-end"></a>使用 Jaeger 或 New Relic 监视后端

除了 Zipkin 本身以外，其他监视后端软件还支持使用 Zipkin 格式的引入遥测。 [Jaeger](https://www.jaegertracing.io/) 是由 Uber 技术创建的开源跟踪系统。 它用于跟踪分布式服务之间的事务，并对复杂的微服务环境进行故障排除。 [New Relic](https://newrelic.com/) 是一个 *全堆栈* 可观察性平台。 它链接分布式应用程序中的相关数据，以提供完整的系统画面。 若要试用，请 `endpointAddress` 在 Dapr 配置文件中指定指向 Jaeger 或新 Relic 服务器的。 下面是配置文件的一个示例，它将 Dapr 配置为将遥测数据发送到 Jaeger 服务器。 Jaeger 的 URL 与 Zipkin 的 URL 相同。 唯一的区别是服务器运行的端口：

 ```yaml
 apiVersion: dapr.io/v1alpha1
 kind: Configuration
 metadata:
   name: tracing-config
   namespace: default
 spec:
   tracing:
     samplingRate: "1"
     zipkin:
       endpointAddress: "http://localhost:9415/api/v2/spans"
 ```

若要试用 New Relic，请指定新的 Relic API 的终结点。 下面是新 Relic 的配置文件示例：

 ```yaml
apiVersion: dapr.io/v1alpha1
 kind: Configuration
 metadata:
   name: tracing-config
   namespace: default
 spec:
   tracing:
     samplingRate: "1"
     zipkin:
       endpointAddress: "https://trace-api.newrelic.com/trace/v1?Api-Key=<NR-API-KEY>&Data-Format=zipkin&Data-Format-Version=2"
 ```

有关如何使用的详细信息，请查看 Jaeger 和新 Relic 网站。

### <a name="metrics"></a>指标

指标提供性能和资源使用情况的见解。 Dapr 会发出大量系统和运行时指标。 Dapr 使用 [Prometheus](https://prometheus.io/) 作为度量标准。 Dapr 分支和系统服务，会在端口上公开指标终结点 `9090` 。 *Prometheus scraper* 按预定义的间隔调用此终结点，以收集指标。 Scraper 将指标值发送到监视后端。 图9-4 显示了抓取过程：

![抓取 Prometheus 指标](media/observability/prometheus-scraper.png)

**图 9-4**。 抓取 Prometheus 指标。

在上图中，每个挎斗和系统服务都公开了侦听端口9090的指标终结点。 Prometheus 指标 Scrapper 从每个终结点捕获指标，并将信息发布到监视后端。  

#### <a name="service-discovery"></a>服务发现

您可能想知道指标 scraper 如何知道收集指标的位置。 Prometheus 可以与内置于目标部署环境中的发现机制集成。 例如，在 Kubernetes 中运行时，Prometheus 可与 Kubernetes API 集成，以查找在该环境中运行的所有可用 Kubernetes 资源。

#### <a name="metrics-list"></a>指标列表

Dapr 为 Dapr 系统服务及其运行时生成一组大量指标。 示例包括：

| 指标                                         | 源 | 说明                                                  |
| ---------------------------------------------- | :----: | ------------------------------------------------------------ |
| dapr_operator_service_created_total            | 系统 | Dapr Operator service 创建的 Dapr 服务总数。 |
| dapr_injector_sidecar_injection/requests_total | 系统 | Dapr Sidecar-Injector 服务收到的挎斗注入请求总数。 |
| dapr_placement_runtimes_total                  | 系统 | 向 Dapr 放置服务报告的主机总数。 |
| dapr_sentry_cert_sign_request_received_total   | 系统 | Dapr Sentry 服务)  (收到的证书签名请求数。 |
| dapr_runtime_component_loaded      | 运行时 | 已成功加载的 Dapr 组件数。           |
| dapr_grpc_io_server_completed_rpcs | 运行时 | 按方法和状态的 gRPC 调用的计数。                    |
| dapr_http_server_request_count     | 运行时 | HTTP 服务器中启动的 HTTP 请求数。           |
| dapr_http/client/sent_bytes        | 运行时 | 请求正文中发送的总字节数 (不包括 HTTP 客户端) 的标头。 |

有关可用指标的详细信息，请参阅 [Dapr 指标文档](https://docs.dapr.io/operations/monitoring/metrics/)。

#### <a name="configure-dapr-metrics"></a>配置 Dapr 指标

在运行时，可以通过在 `--enable-metrics=false` Dapr 命令中包含自变量来禁用指标集合终结点。 或者，还可以更改具有参数的终结点的默认端口 `--metrics-port 9090` 。

还可以使用 Dapr 配置文件来静态地启用或禁用运行时指标收集：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Configuration
metadata:
  name: dapr-config
  namespace: eshop
spec:
  tracing:
    samplingRate: "1"
  metric:
    enabled: false
```

#### <a name="visualize-dapr-metrics"></a>可视化 Dapr 指标

通过 Prometheus scraper 收集指标并将其发布到监视后端，你如何了解原始数据？ 用于分析指标的常用可视化工具是 [Grafana](https://grafana.com/grafana/)。 使用 Grafana 可以从可用指标创建仪表板。 下面是一个显示 Dapr 系统服务指标的仪表板示例：

![显示 Dapr 系统服务指标的 Grafana 仪表板](media/observability/grafana-sample.png)

Dapr 文档包含 [用于安装 Prometheus 和 Grafana 的教程](https://docs.dapr.io/operations/monitoring/metrics/grafana/)。

### <a name="logging"></a>Logging

日志记录提供了在运行时服务发生的情况。 运行应用程序时，Dapr 会自动从 Dapr 分支和 Dapr 系统服务发出日志条目。 但是，在应用程序代码中检测到的日志记录项 **不** 会自动包括在内。 若要从应用程序代码发出日志记录，可以导入特定 SDK，如 [OPENTELEMETRY sdk for .net](https://opentelemetry.io/docs/net/)。 日志记录应用程序代码将在本章后面的 *使用 Dapr .NET SDK* 部分中介绍。  

#### <a name="log-entry-structure"></a>日志项目结构

Dapr 发出结构化日志记录。 每个日志条目都具有以下格式：

| 字段    | 说明                                          | 示例                             |
| -------- | ---------------------------------------------------- | ----------------------------------- |
| time     | ISO8601 格式的时间戳                          | `2021-01-10T14:19:31.000Z`          |
| 级别    | 条目 (`debug` \| `info` \| `warn` \| `error`) 级别   | `info`                              |
| 类型     | 日志类型                                             | `log`                               |
| msg      | 日志消息                                          | `metrics server started on :62408/` |
| scope    | 日志范围                                        | `dapr.runtime`                      |
| instance | Dapr 运行的主机名                             | TSTSRV01                            |
| app_id   | Dapr 应用 ID                                          | ordering-api                        |
| ver      | Dapr 运行时版本                                 | `1.0.0`-rc. 2                        |

在故障排除方案中搜索日志记录条目时， `time` 和 `level` 字段特别有用。 时间字段对日志条目进行排序，以便您可以确定特定时间段。 进行故障排除时， *调试级别* 的日志项提供有关代码行为的详细信息。

#### <a name="plain-text-versus-json-format"></a>纯文本与 JSON 格式

默认情况下，Dapr 以纯文本格式发出结构化日志记录。 每个日志条目的格式为包含键/值对的字符串。 下面是以纯文本形式记录的示例：

```text
== DAPR == time="2021-01-12T16:11:39.4669323+01:00" level=info msg="starting Dapr Runtime -- version 1.0.0-rc.2 -- commit 196483d" app_id=ordering-api instance=TSTSRV03 scope=dapr.runtime type=log ver=1.0.0-rc.2
== DAPR == time="2021-01-12T16:11:39.467933+01:00" level=info msg="log level set to: info" app_id=ordering-api instance=TSTSRV03 scope=dapr.runtime type=log ver=1.0.0-rc.2
== DAPR == time="2021-01-12T16:11:39.467933+01:00" level=info msg="metrics server started on :62408/" app_id=ordering-api instance=TSTSRV03 scope=dapr.metrics type=log ver=1.0.0-rc.2
```

尽管简单，但难以分析这种格式。 如果使用监视工具查看日志条目，则需要启用 JSON 格式的日志记录。 使用 JSON 项，监视工具可以对各个字段进行索引和查询。 下面是 JSON 格式的相同日志条目：

```json
{"app_id": "ordering-api", "instance": "TSTSRV03", "level": "info", "msg": "starting Dapr Runtime -- version 1.0.0-rc.2 -- commit 196483d", "scope": "dapr.runtime", "time": "2021-01-12T16:11:39.4669323+01:00", "type": "log", "ver": "1.0.0-rc.2"}
{"app_id": "ordering-api", "instance": "TSTSRV03", "level": "info", "msg": "log level set to: info", "scope": "dapr.runtime", "type": "log", "time": "2021-01-12T16:11:39.467933+01:00", "ver": "1.0.0-rc.2"}
{"app_id": "ordering-api", "instance": "TSTSRV03", "level": "info", "msg": "metrics server started on :62408/", "scope": "dapr.metrics", "type": "log", "time": "2021-01-12T16:11:39.467933+01:00", "ver": "1.0.0-rc.2"}
```

若要启用 JSON 格式设置，需要配置每个 Dapr 挎斗。 在自承载模式下，可以在 `--log-as-json` 命令行上指定标志：

```console
dapr run --app-id ordering-api --log-level info --log-as-json dotnet run
```

在 Kubernetes 中，你可以 `dapr.io/log-as-json` 为应用程序的每个部署添加批注：

```yaml
annotations:
   dapr.io/enabled: "true"
   dapr.io/app-id: "ordering-api"
   dapr.io/app-port: "80"
   dapr.io/config: "dapr-config"
   dapr.io/log-as-json: "true"
```

使用 Helm 在 Kubernetes 群集中安装 Dapr 时，可以为所有 Dapr 系统服务启用 JSON 格式的日志记录：

```console
helm repo add dapr https://dapr.github.io/helm-charts/
helm repo update
kubectl create namespace dapr-system
helm install dapr dapr/dapr --namespace dapr-system --set global.logAsJson=true
```

#### <a name="collect-logs"></a>收集日志

Dapr 发出的日志可以送入监视后端进行分析。 日志收集器是一个组件，用于从系统收集日志并将其发送到监视后端。 常用日志收集器是 [Fluentd](https://www.fluentd.org/)。 请参阅 Dapr 文档中的 [操作方法：设置 Fluentd、弹性搜索和 Kibana In Kubernetes](https://docs.dapr.io/operations/monitoring/logging/fluentd/) 。 本文包含有关将 Fluentd 设置为日志收集器和 [ELK 堆栈](https://www.elastic.co/elastic-stack) (弹性搜索和 Kibana) 为监视后端的说明。

### <a name="health-status"></a>运行状况

服务的运行状况提供对可用性的深入了解。 每个 Dapr 挎斗公开一个可供宿主环境用来确定挎斗的运行状况的运行状况 API。 该 API 有一个操作：

```console
GET http://localhost:3500/v1.0/healthz
```

操作返回两个 HTTP 状态代码：

- 204：挎斗正常运行时
- 500：挎斗不正常时

在自承载模式下运行时，不会自动调用运行状况 API。 可以通过应用程序代码或运行状况监视工具调用 API。

在 Kubernetes 中运行时，Dapr 挎斗-注入器会自动将 Kubernetes 配置为使用运行状况 API 来执行 *活动探测* 和 *准备情况探测*。

Kubernetes 使用活动探测来确定容器是否已启动并正在运行。 如果活动探测返回失败代码，Kubernetes 将假定容器处于死锁，并自动重启它。 此功能提高了应用程序的总体可用性。

Kubernetes 使用就绪探测器来确定容器是否准备好开始接受流量。 当容器的所有容器都准备就绪时，它被视为已就绪。 准备情况确定 Kubernetes 服务是否可以将流量定向到负载平衡方案中的 pod。 未准备就绪的 pod 会自动从负载平衡器中删除。

活动和准备情况探测具有多个可配置参数。 这两者都在 pod 的清单文件的 "容器规范" 部分中进行配置。 默认情况下，Dapr 对每个挎斗容器使用以下配置：

```yaml
livenessProbe:
      httpGet:
        path: v1.0/healthz
        port: 3500
      initialDelaySeconds: 5
      periodSeconds: 10
      timeoutSeconds : 5
      failureThreshold : 3
readinessProbe:
      httpGet:
        path: v1.0/healthz
        port: 3500
      initialDelaySeconds: 5
      periodSeconds: 10
      timeoutSeconds : 5
      failureThreshold: 3
```

 以下参数可用于探测：

- `path`指定 Dapr HEALTH API 终结点。
- `port`指定 Dapr HEALTH API 端口。
- `initialDelaySeconds`指定 Kubernetes 第一次开始探测容器前等待的秒数。
- `periodSeconds`指定 Kubernetes 将在每个探测之间等待的秒数。
- `timeoutSeconds`指定 Kubernetes 将在超时前等待 API 响应的秒数。超时被解释为失败。
- `failureThreshold`指定在考虑容器处于不活动状态或尚未准备就绪之前，Kubernetes 将接受的失败状态代码的数目。

### <a name="dapr-dashboard"></a>Dapr 仪表板

Dapr 提供了一个仪表板，用于显示有关 Dapr 应用程序、组件和配置的状态信息。 使用 Dapr CLI 将仪表板作为本地计算机上的 web 应用程序在端口8080上启动：

```console
dapr dashboard
```

对于在 Kubernetes 中运行的 Dapr 应用程序，请使用以下命令：

```console
dapr dashboard -k
```

此时将打开仪表板，其中概述了应用程序中具有 Dapr 挎斗的所有服务。 以下屏幕截图显示了 Kubernetes 中运行的 eShopOnDapr 应用程序的 Dapr 仪表板：

![Dapr 仪表板概述页](media/observability/dapr-dashboard-overview.png)

在对 Dapr 应用程序进行故障排除时，Dapr 仪表板非常有用。 其中提供了有关 Dapr 分支和系统服务的信息。 可以向下钻取每个服务的配置，包括日志记录条目。

该仪表板还会显示应用程序的已配置组件 (及其配置) ：

![Dapr 仪表板组件页面](media/observability/dapr-dashboard-components.png)

可以通过仪表板提供大量的信息。 可以通过运行 Dapr 应用程序并浏览仪表板来发现它。 您可以使用附带的 eShopOnDapr 应用程序启动。

有关 Dapr 仪表板命令的详细信息，请参阅 Dapr 文档中的 [Dapr 仪表板 CLI 命令参考](https://docs.dapr.io/reference/cli/dapr-dashboard/) 。

## <a name="use-the-dapr-net-sdk"></a>使用 Dapr .NET SDK

Dapr .NET SDK 不包含任何特定的可观察性功能。 所有可观察性功能都在 Dapr 级别提供。

如果要从 .NET 应用程序代码发出遥测数据，则应考虑适用于 [.net 的 OPENTELEMETRY SDK](https://opentelemetry.io/docs/net/)。 开放式遥测项目是跨平台、开源和供应商不可知的项目。 它提供端到端的实现，以生成、发出、收集、处理和导出遥测数据。 每种语言都有一个检测库，支持自动和手动检测。 遥测使用开放式遥测标准进行发布。 该项目具有广泛的行业支持，并采用云提供商、供应商和最终用户。

## <a name="reference-application-eshopondapr"></a>引用应用程序： eShopOnDapr

随附的 eShopOnDapr 引用应用程序中的可观察性包含多个部分。 捕获所有分支的遥测数据。 此外，还有其他可观察性功能，这些功能继承自早期的 eShopOnContainers 示例。

### <a name="custom-health-dashboard"></a>自定义运行状况仪表板

EShopOnDapr 中的 **WebStatus** 项目是一个自定义运行状况仪表板，可让你深入了解 eShop 服务的运行状况。 此仪表板不使用 Dapr health API，但使用 ASP.NET Core 的内置 [运行状况检查机制](/aspnet/core/host-and-deploy/health-checks) 。 该仪表板不仅提供服务的运行状况状态，还提供服务依赖项的运行状况。 例如，使用数据库的服务还提供此数据库的运行状况状态，如以下屏幕截图所示：

![eShopOnDapr 自定义运行状况仪表板](media/observability/eshop-health-dashboard.png)

### <a name="seq-log-aggregator"></a>Seq 日志聚合器

[Seq](https://datalust.co/seq) 是在 eShopOnDapr 中用于聚合日志的常用日志聚合器服务器。 从应用程序服务中 Seq 引入日志记录，而不是从 Dapr 系统服务或分支。 Seq 索引应用程序日志记录，提供一个 web 前端用于分析和查询日志。 它还提供了用于构建监视仪表板的功能。

EShopOnDapr 应用程序服务使用 [SeriLog](https://serilog.net/) 日志记录库发出结构化日志记录。 Serilog 将日志事件发布到称为 **接收器** 的构造。 接收器只是 Serilog 写入其日志记录事件的目标平台。 [许多 Serilog 接收器都可用](https://github.com/serilog/serilog/wiki/Provided-Sinks)，包括用于 Seq 的接收器。 Seq 是 eShopOnDapr 中使用的 Serilog 接收器。

### <a name="application-insights"></a>Application Insights

eShopOnDapr services 还使用适用于 .NET Core 的 Microsoft Application Insights SDK 将遥测直接发送到 Azure 应用程序 Insights。 有关详细信息，请参阅 Microsoft 文档中的 [Azure 应用程序 Insights for ASP.NET Core 应用程序](/azure/azure-monitor/app/asp-net-core) 。

## <a name="summary"></a>总结

在生产环境中运行分布式系统时，良好的可观察性是至关重要的。

Dapr 提供不同类型的遥测，包括分布式跟踪、日志记录、指标和运行状况状态。

Dapr 仅生成 Dapr 系统服务和分支的遥测。 不会自动包含应用程序代码中的遥测数据。 不过，可以使用特定的 SDK （如 OpenTelemetry SDK for .NET）从应用程序代码发出遥测数据。

Dapr 遥测采用基于开放标准的格式生成，因此可通过大量可用的监视工具来引入。 一些示例包括： Zipkin、Azure 应用程序 Insights、ELK Stack、New Relic 和 Grafana。 有关如何监视具有特定监视后端的 Dapr 应用程序的教程，请参阅 Dapr 文档中的 [监视应用程序 Dapr](https://docs.dapr.io/operations/monitoring/) 。

需要引入遥测的遥测 scraper，并将其发布到监视后端。

可以将 Dapr 配置为发出结构化日志记录。 建议使用结构化日志记录，因为后端监视工具可以对其进行索引。 索引日志记录使用户能够在搜索日志记录时执行丰富的查询。

Dapr 提供了一个仪表板，其中提供了有关 Dapr 服务和配置的信息。

## <a name="references"></a>参考

- [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview/)
- [打开遥测](https://opentelemetry.io/)
- [Zipkin](https://zipkin.io/)
- [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)
- [Jaeger](https://www.jaegertracing.io/)
- [New Relic](https://newrelic.com/)
- [Prometheus](https://prometheus.io/)
- [Grafana](https://grafana.com/grafana/)
- [打开遥测 SDK for .NET](https://opentelemetry.io/docs/net/)
- [Fluentd](https://www.fluentd.org/)
- [ELK 堆栈](https://www.elastic.co/elastic-stack)
- [序列](https://datalust.co/seq)
- [Serilog](https://serilog.net/)

> [!div class="step-by-step"]
> [上一页](bindings.md)
> [下一页](secrets.md)

---
title: 部署承载于 Internet 信息服务中的 WCF 服务
description: 了解开发和部署在 IIS 中承载的 WCF 服务所需的任务（从验证组件安装开始）
ms.date: 03/30/2017
ms.assetid: 04ebd329-3fbd-44c3-b3ab-1de3517e27d7
ms.openlocfilehash: a51cf09309a5a5c9ac8c8a4cc4fecc0a203b7b3c
ms.sourcegitcommit: 1dbe25ff484a02025d5c34146e517c236f7161fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104653473"
---
# <a name="deploying-an-internet-information-services-hosted-wcf-service"></a>部署承载于 Internet 信息服务中的 WCF 服务

开发和部署 Windows Communication Foundation (承载于 Internet Information Services (IIS) 中的 WCF) 服务，其中包括以下任务：

- 确保正确安装并注册了 IIS、ASP.NET、WCF 和 WCF 激活组件。

- 创建新的 IIS 应用程序，或重复使用现有的 ASP.NET 应用程序。

- 为 WCF 服务创建 .svc 文件。

- 将服务实现部署到 IIS 应用程序。

- 配置 WCF 服务。

有关创建 IIS 承载的 WCF 服务的详细演练，请参阅 [如何：在 iis 中承载 WCF 服务](how-to-host-a-wcf-service-in-iis.md)。

## <a name="ensure-that-iis-aspnet-and-wcf-are-correctly-installed-and-registered"></a>确保已正确安装和注册 IIS、ASP.NET 和 WCF

为了使 IIS 承载的 WCF 服务正常工作，必须安装 WCF、IIS 和 ASP.NET。 将 WCF (作为 .NET Framework) 、ASP.NET 和 IIS 的一部分进行安装的过程因操作系统而异。 有关安装 WCF 和 .NET Framework 的详细信息，请参阅 [为开发人员安装 .NET Framework](../../install/guide-for-developers.md)。 若要在 Windows 10 上安装 IIS，请在 **"控制面板"** 中打开 "**程序和功能**"，然后选择 **"打开或关闭 Windows 功能**"。 在 **Windows 功能** 中，选择 " **Internet Information Services** "，然后选择 **"确定"**。

![突出显示了 IIS 的 Windows 功能](./media/windows-features-iis.png)

有关在其他操作系统上安装 IIS 的说明，请参阅在 [Windows Vista 和 windows 7 上安装 iis](/iis/install/installing-iis-7/installing-iis-on-windows-vista-and-windows-7) 和 [在 Windows Server 2012 R2 上安装 iis 8.5](/iis/install/installing-iis-85/installing-iis-85-on-windows-server-2012-r2)。

如果计算机上已存在 IIS，则 .NET Framework 的安装过程会自动将 WCF 注册到 IIS。 如果在 .NET Framework 后安装 IIS，则需要执行其他步骤以将 WCF 注册到 IIS 和 ASP.NET。 根据您的操作系统，可以按如下所述执行此操作：

- Windows 7 和 Windows Server 2003：使用 [ (ServiceModelReg.exe) ](../servicemodelreg-exe.md) 工具将 WCF 注册到 IIS。 若要使用此工具，请 `ServiceModelReg.exe /i /x` 在 [visual studio 开发人员命令提示或 Visual Studio 开发人员 PowerShell](/visualstudio/ide/reference/command-prompt-powershell)中输入。

- Windows 7：最后，必须验证是否已将 ASP.NET 配置为使用 .NET Framework 版本4或更高版本。 可以通过使用选项运行 ASPNET_Regiis 工具来执行此操作 `–i` 。 有关详细信息，请参阅 [ASP.NET IIS 注册工具](/previous-versions/dotnet/netframework-3.5/k6h9cz8h(v=vs.90))。

## <a name="create-a-new-iis-application-or-reuse-an-existing-aspnet-application"></a>创建新的 IIS 应用程序或重新使用现有的 ASP.NET 应用程序

IIS 承载的 WCF 服务必须驻留在 IIS 应用程序内。 你可以创建新的 IIS 应用程序以独占方式承载 WCF 服务。 或者，你可以将 WCF 服务部署到现有应用程序，该应用程序已在托管 ASP.NET 2.0 内容 (如 .aspx pages 和 ASP.NET Web services [.ASMX] ) 。 有关这些选项的详细信息，请参阅 [Wcf 服务和 ASP.NET](wcf-services-and-aspnet.md)中的 "在 ASP.NET 兼容模式下承载 wcf 并行" 和 "在兼容模式下承载 wcf 服务" 部分。

请注意，IIS 6.0 和更高版本定期重新启动独立的面向对象的编程应用程序。 默认值为 1740 分钟。 支持的最大值为 71,582 分钟。 可以禁用此重新启动。 有关此属性的详细信息，请参阅 [PeriodicRestartTime](/previous-versions/iis/6.0-sdk/ms525914(v=vs.90))。

## <a name="create-an-svc-file-for-the-wcf-service"></a>为 WCF 服务创建 .svc 文件

在 IIS 中承载的 WCF 服务以特殊内容文件的形式表示 ( .svc 文件) 在 IIS 应用程序内。 此模型与在 IIS 应用程序内将 ASMX 页表示为 .asmx 文件的方式类似。 .Svc 文件包含特定于 WCF 的处理指令 ([ \@ ServiceHost](../../configure-apps/file-schema/wcf-directive/servicehost.md)) ，它允许 WCF 宿主基础结构激活托管服务以响应传入消息。 .svc 文件的最常见语法如以下语句所示。

`<% @ServiceHost Service="MyNamespace.MyServiceImplementationTypeName" %>`

它由[ \@ ServiceHost](../../configure-apps/file-schema/wcf-directive/servicehost.md)指令和单个属性组成 `Service` 。 `Service` 属性的值是服务实现的公共语言运行库 (CLR) 类型名称。 使用此指令与使用以下代码创建服务主机基本等效。

```csharp
new ServiceHost( typeof( MyNamespace.MyServiceImplementationTypeName ) );
```

也可以执行其他承载配置，如创建服务的基址列表。 也可以使用自定义 <xref:System.ServiceModel.Activation.ServiceHostFactory> 扩展指令以用于自定义承载解决方案。 承载 WCF 服务的 IIS 应用程序不负责管理实例的创建和生存期 <xref:System.ServiceModel.ServiceHost> 。 <xref:System.ServiceModel.ServiceHost>当收到 .svc 文件的第一个请求时，托管 WCF 宿主基础结构会动态创建必要的实例。 在代码显式关闭该实例之前或回收应用程序时，不释放该实例。

有关 .svc 文件语法的详细信息，请参阅[ \@ ServiceHost](../../configure-apps/file-schema/wcf-directive/servicehost.md)。

## <a name="deploy-the-service-implementation-to-the-iis-application"></a>将服务实现部署到 IIS 应用程序

在 IIS 中托管的 WCF 服务使用与 ASP.NET 2.0 相同的动态编译模型。 与 ASP.NET 一样，你可以在不同位置按多种方式部署 IIS 承载的 WCF 服务的实现代码，如下所示：

- 作为全局程序集缓存 (GAC) 或应用程序的 \bin 目录中的预编译 .dll 文件。 在部署类库的新版本后，才更新预编译的二进制文件。

- 作为位于应用程序的 \ App_Code 目录中的未编译源文件。 处理应用程序的第一个请求时，动态需要位于此目录中的源文件。 对 \App_Code 目录中文件进行的任何更改都导致在收到下一个请求时回收和重新编译整个应用程序。

- 作为直接放置在 .svc 文件中的未编译代码。 实现代码还可以在服务的 .svc 文件中内联定位到 \@ ServiceHost 指令之后。 对内联代码进行的任何更改导致在收到下一个请求时回收和重新编译应用程序。

有关 ASP.NET 2.0 编译模型的详细信息，请参阅 [ASP.NET 编译概述](/previous-versions/aspnet/ms178466(v=vs.100))。

## <a name="configure-the-wcf-service"></a>配置 WCF 服务

IIS 承载的 WCF 服务将其配置存储在应用程序 Web.config 文件中。 承载于 iis 中的服务使用与在 IIS 外部承载的 WCF 服务相同的配置元素和语法。 但是，下面的约束对 IIS 承载环境是唯一的：

- 承载于 IIS 中的服务的基址。

- 在 IIS 外部承载 WCF 服务的应用程序可以通过将一组基址 Uri 传递到 <xref:System.ServiceModel.ServiceHost> 构造函数或 [\<host>](../../configure-apps/file-schema/wcf/host.md) 在服务配置中提供元素，来控制它们所承载服务的基址。 承载于 IIS 中的服务无法控制其基址；承载于 IIS 中的服务的基址是其 .svc 文件的地址。

### <a name="endpoint-addresses-for-iis-hosted-services"></a>承载于 IIS 中的服务的终结点地址

承载于 IIS 中时，任何终结点地址始终被认为相对于表示服务的 .svc 文件的地址。 例如，如果 WCF 服务的基址 `http://localhost/Application1/MyService.svc` 具有以下终结点配置：

```xml
<endpoint address="anotherEndpoint" />
```

这提供了一个可以在上访问的终结点 `http://localhost/Application1/MyService.svc/anotherEndpoint` 。

同样，使用空字符串作为相对地址的终结点配置元素提供了一个可到达的终结点 `http://localhost/Application1/MyService.svc` ，即基址。

```xml
<endpoint address="" />
```

对于承载于 IIS 中的服务的终结点，必须始终使用相对终结点地址。 提供完全限定的终结点地址 (例如， `http://localhost/MyService.svc` 如果终结点地址未指向承载公开终结点的服务的 IIS 应用程序，则) 可能导致服务部署中的错误。 对所承载的服务使用相对终结点地址避免了这些潜在冲突。

### <a name="available-transports"></a>可用传输

托管在 IIS 5.1 和 IIS 6.0 中的 WCF 服务限制为使用基于 HTTP 的通信。 在这些 IIS 平台上，将所承载的服务配置为使用非 HTTP 绑定会导致服务激活期间出错。 对于 IIS 7.0，支持的传输包括 HTTP、Net.tcp、Net.pipe、net.pipe 和 msmq.formatname，以便向后兼容现有 MSMQ 应用程序。

### <a name="http-transport-security"></a>HTTP 传输安全

IIS 承载的 WCF 服务可以利用 HTTP 传输安全 (例如，HTTPS 和 HTTP 身份验证方案，如基本、摘要式和 Windows 集成身份验证) ，前提是包含该服务的 IIS 虚拟目录支持这些设置。 所承载终结点的绑定上的 HTTP 传输安全设置必须与包含它的 IIS 虚拟目录上的传输安全设置匹配。

例如，配置为使用 HTTP 摘要式身份验证的 WCF 终结点必须驻留在也配置为允许 HTTP 摘要式身份验证的 IIS 虚拟目录中。 IIS 设置和 WCF 终结点设置的不匹配组合会导致服务激活期间出错。

## <a name="see-also"></a>请参阅

- [在 Internet 信息服务中承载](hosting-in-internet-information-services.md)
- [Internet 信息服务承载最佳实践](internet-information-services-hosting-best-practices.md)
- [Windows Server App Fabric 承载功能](/previous-versions/appfabric/ee677189(v=azure.10))

---
title: "在工作流解决方案中添加服务引用"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-clr
ms.tgt_pltfrm: 
ms.topic: article
ms.assetid: 83574cf3-9803-49bc-837f-432936dc9c76
caps.latest.revision: "5"
author: dotnet-bot
ms.author: dotnetcontent
manager: wpickett
ms.openlocfilehash: 9a80362f8cb7dce2853472b7f03c3586e33b8578
ms.sourcegitcommit: ce279f2d7fe2220e6ea0a25a8a7a5370ddf8d9f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="adding-a-service-reference-in-a-workflow-solution"></a>在工作流解决方案中添加服务引用
在工作流应用程序中添加服务引用后，其工作方式与常规 WCF 应用程序略有不同。 当选择“添加服务引用”并指定该服务的 URL 时，将下载元数据并生成自定义活动，从而允许调用添加的引用所指向的 WCF 服务或 WCF 工作流服务。 添加服务引用后，重新生成解决方案，以便构建生成的活动。 之后它们将出现在工作流设计器工具箱中。 但是请注意，仅当在工作流解决方案之内添加服务引用时，这才是可行的。 以下网络广播演示如何在其他类型的项目中添加服务引用：[从 Web 项目中的工作流中调用 WCF 服务](http://go.microsoft.com/fwlink/?LinkId=207725)。  
  
 添加对包含相同操作名称的服务的两个或多个服务引用会导致出现问题。 生成的活动将仅调用第一个服务操作。 要解决此问题，请重命名服务操作以使其各不相同，或在每个生成的活动中更改终结点配置名称。  
  
## <a name="see-also"></a>另请参阅  
 [工作流服务](../../../../docs/framework/wcf/feature-details/workflow-services.md)  
 [如何： 创建调用另一个工作流服务的工作流服务](../../../../docs/framework/wcf/feature-details/how-to-create-a-workflow-service-that-calls-another-workflow-service.md)  
 [从 Web 项目中的工作流中调用 WCF 服务](http://go.microsoft.com/fwlink/?LinkId=207725)

---
title: "疑难解答： 服务应用程序赢得 &#39; 无法安装"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-clr
ms.tgt_pltfrm: 
ms.topic: article
helpviewer_keywords:
- troubleshooting service applications
- services, troubleshooting
- services, debugging
- Windows NT services, troubleshooting
- troubleshooting NT services
- Windows Service applications, troubleshooting
ms.assetid: 45c48e2e-b97d-44bc-8896-14f328e0ce33
caps.latest.revision: "8"
author: ghogen
ms.author: ghogen
manager: douge
ms.openlocfilehash: 82eb870761a7865385631cd9961ce99e0b0d3502
ms.sourcegitcommit: bd1ef61f4bb794b25383d3d72e71041a5ced172e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="troubleshooting-service-application-won39t-install"></a>疑难解答： 服务应用程序赢得 &#39; 无法安装
如果服务应用程序将无法正确安装，请检查以确保<xref:System.ServiceProcess.ServiceBase.ServiceName%2A>为服务类的属性设置为相同的值，该服务的安装程序中所示。 值必须是为了使你的服务以正确安装两个实例中相同。  
  
> [!NOTE]
>  你还可以查看安装日志，以在安装过程中获得反馈。  
  
 你还应检查来确定是否有另一个服务具有相同的名称已安装。 服务名称必须唯一，安装才能成功。  
  
## <a name="see-also"></a>另请参阅  
 [Windows 服务应用程序简介](../../../docs/framework/windows-services/introduction-to-windows-service-applications.md)

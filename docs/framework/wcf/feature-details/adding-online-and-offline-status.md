---
title: "添加联机和脱机状态"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-clr
ms.tgt_pltfrm: 
ms.topic: article
ms.assetid: 05e5f51d-81b6-4c17-b364-9dda447a5fce
caps.latest.revision: "5"
author: dotnet-bot
ms.author: dotnetcontent
manager: wpickett
ms.openlocfilehash: 5d5b6c858b5aa5918498ba8fccee41f7392ac32e
ms.sourcegitcommit: ce279f2d7fe2220e6ea0a25a8a7a5370ddf8d9f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="adding-online-and-offline-status"></a>添加联机和脱机状态
在很多情况下，应用程序必须监控有关对等通道连接状态的特定详细信息。 通过对 `GetProperty` 接口的实现调用 <xref:System.ServiceModel.IOnlineStatus> 方法可以获取此信息。 实现此接口的对象可以监视连接状态或注册事件处理程序（如 `OnOnline` 和 `OnOffline`），并对联机状态的更改立即做出反应。  
  
 在对等通道基础结构中，如果客户端连接到至少一个其他对等客户端，则将此客户端视为联机；否则，视为脱机。 在调试开发应用程序或向最终用户显示详细信息时，此功能特别有用。  
  
> [!NOTE]
>  联机事件处理程序首先应确保节点在发送消息之前处于打开状态。  
  
## <a name="see-also"></a>另请参阅  
 [生成对等通道应用程序](../../../../docs/framework/wcf/feature-details/building-a-peer-channel-application.md)

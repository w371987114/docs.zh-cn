---
title: '&lt;protocolMapping&gt;'
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-clr
ms.tgt_pltfrm: 
ms.topic: article
ms.assetid: 5076644b-1f33-4f26-9488-87de9fcda04c
caps.latest.revision: "3"
author: dotnet-bot
ms.author: dotnetcontent
manager: wpickett
ms.openlocfilehash: 88eb76a5657bd4a83bebb32ce30f73d32693be9b
ms.sourcegitcommit: ce279f2d7fe2220e6ea0a25a8a7a5370ddf8d9f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="ltprotocolmappinggt"></a>&lt;protocolMapping&gt;
表示一个配置节，用于定义一组的传输协议方案 （例如，http、 net.tcp、 net.pipe 等） 和 WCF 绑定之间的默认协议映射。 当在运行时创建默认终结点时，[!INCLUDE[indigo1](../../../../../includes/indigo1-md.md)] 将查看已配置的映射，并确定要用于基于特定内容的地址的绑定。  
  
 \<system.serviceModel >  
\<protocolMapping >  
  
## <a name="syntax"></a>语法  
  
```xml
   <protocolMapping>    <add binding="String"         bindingConfiguration="String"         scheme="http/net.msmq/net.pipe/net.tcp"/></protocolMapping>  
```

## <a name="attributes-and-elements"></a>特性和元素  
 下列各节描述了特性、子元素和父元素。  
  
### <a name="attributes"></a>特性  
 无。  
  
### <a name="child-elements"></a>子元素  
  
|元素|描述|  
|-------------|-----------------|  
|[\<筛选器 >](../../../../../docs/framework/configure-apps/file-schema/wcf/filters-of-routing.md)|包含传输协议方案 （例如，http、 net.tcp、 net.pipe 等） 和 WCF 绑定之间的默认协议映射。|  
  
### <a name="parent-elements"></a>父元素  
  
|元素|描述|  
|-------------|-----------------|  
|system.ServiceModel|所有 WCF 配置元素的根元素。|  
  
## <a name="example"></a>示例  
 下面的配置示例演示 machine.config 文件中的默认协议映射。 您可以通过修改 machine.config 文件在计算机级别重写此默认映射。 或者，如果您只希望在应用程序范围内重写此映射，则可以在应用程序配置文件中重写此节，并为单独的协议方案更改映射。  
  
```xml  
<protocolMapping>  
        <add scheme="http" binding="basicHttpBinding"/>  
        <add scheme="net.tcp" binding="netTcpBinding"/>  
        <add scheme="net.pipe" binding="netNamedPipeBinding"/>  
        <add scheme="net.msmq" binding="netMsmqBinding"/>  
</protocolMapping>  
```  
  
## <a name="see-also"></a>另请参阅  
 <xref:System.ServiceModel.Configuration.ProtocolMappingSection?displayProperty=nameWithType>       
 <xref:System.ServiceModel.Configuration.ProtocolMappingElement?displayProperty=nameWithType>    

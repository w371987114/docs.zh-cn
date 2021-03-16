---
description: 了解详细信息： <supportPortability> 元素
title: <supportPortability> 元素
ms.date: 03/30/2017
helpviewer_keywords:
- supportPortability element
- <supportPortability> element
ms.assetid: 6453ef66-19b4-41f3-b712-52d0c2abc9ca
ms.openlocfilehash: 932e20c865c151880c1a49517451db71ecd08951
ms.sourcegitcommit: 0bb8074d524e0dcf165430b744bb143461f17026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2021
ms.locfileid: "103478901"
---
# <a name="supportportability-element"></a>\<supportPortability> 元素

通过禁用将程序集视为等效于应用程序可移植性用途的默认行为来指定应用程序可以在两种不同的 .NET Framework 实现中引用同一程序集。  
  
[**\<configuration>**](../configuration-element.md)\
&nbsp;&nbsp;[**\<runtime>**](runtime-element.md)\
&nbsp;&nbsp;&nbsp;&nbsp;[**\<assemblyBinding>**](assemblybinding-element-for-runtime.md)\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**\<supportPortability>**  
  
## <a name="syntax"></a>语法  
  
```xml  
<supportPortability PKT="public_key_token" enabled="true|false"/>  
```  
  
## <a name="attributes-and-elements"></a>特性和元素  

下列各节描述了特性、子元素和父元素。  
  
### <a name="attributes"></a>特性  
  
|属性|说明|  
|---------------|-----------------|  
|PKT|必需的特性。<br /><br /> 以字符串的形式指定受影响的程序集的公钥标记。|  
|enabled|可选特性。<br /><br /> 指定是否应启用指定 .NET Framework 程序集的实现之间的可移植性支持。|  
  
## <a name="enabled-attribute"></a>enabled 特性  
  
|值|说明|  
|-----------|-----------------|  
|是|支持指定 .NET Framework 程序集的实现之间的可移植性。 这是默认设置。|  
|false|禁用支持指定 .NET Framework 程序集的实现之间的可移植性。 这使应用程序可以引用指定程序集的多个实现。|  
  
### <a name="child-elements"></a>子元素  

无。  
  
### <a name="parent-elements"></a>父元素  
  
|元素|说明|  
|-------------|-----------------|  
|`configuration`|公共语言运行时和 .NET Framework 应用程序所使用的每个配置文件中的根元素。|  
|`runtime`|包含有关程序集绑定和垃圾回收的信息。|  
|`assemblyBinding`|包含有关程序集版本重定向和程序集位置的信息。|  
  
## <a name="remarks"></a>备注  

从 .NET Framework 4 开始，将自动为可以使用两种 .NET Framework 实现的应用程序（例如，.NET Framework 实现或 Silverlight 实现的 .NET Framework）提供支持。 特定 .NET Framework 程序集的两个实现被视为等效于程序集联编程序。 在少数情况下，此应用程序可移植性功能会导致问题。 在这些情况下， `<supportPortability>` 可以使用元素来禁用该功能。  
  
这种情况是必须同时引用特定引用程序集的 Silverlight 实现的 .NET Framework 实现和 .NET Framework 的程序集。 例如，在 Windows Presentation Foundation () WPF 中编写的 XAML 设计器可能需要引用 WPF 桌面实现、设计器的用户界面和 Silverlight 实现中包含的 WPF 子集。 默认情况下，单独引用会导致编译器错误，因为程序集绑定将这两个程序集视为等效。 此元素禁用默认行为，并允许编译成功。  
  
> [!IMPORTANT]
> 为了使编译器能够将信息传递到公共语言运行时的程序集绑定逻辑，必须使用 `/appconfig` 编译器选项来指定包含此元素的 app.config 文件的位置。  
  
## <a name="example"></a>示例  

下面的示例使应用程序能够同时引用在两个实现中都存在的任何 .NET Framework 程序集的 .NET Framework 实现和 Silverlight 实现 .NET Framework。 `/appconfig`必须使用编译器选项指定此 app.config 文件的位置。  
  
```xml  
<configuration>  
   <runtime>  
      <assemblyBinding>  
         <supportPortability PKT="7cec85d7bea7798e" enable="false"/>  
         <supportPortability PKT="31bf3856ad364e35" enable="false"/>  
      </assemblyBinding>  
   </runtime>  
</configuration>  
```  
  
## <a name="see-also"></a>另请参阅

- [-appconfig（C# 编译器选项）](../../../../csharp/language-reference/compiler-options/advanced.md#applicationconfiguration)
- [.NET Framework 程序集统一概述](/previous-versions/dotnet/netframework-4.0/db7849ey(v=vs.100))

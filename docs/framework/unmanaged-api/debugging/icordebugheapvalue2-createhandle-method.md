---
title: "ICorDebugHeapValue2::CreateHandle 方法"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-clr
ms.tgt_pltfrm: 
ms.topic: reference
api_name: ICorDebugHeapValue2.CreateHandle
api_location: mscordbi.dll
api_type: COM
f1_keywords: ICorDebugHeapValue2::CreateHandle
helpviewer_keywords:
- CreateHandle method [.NET Framework debugging]
- ICorDebugHeapValue2::CreateHandle method [.NET Framework debugging]
ms.assetid: fbc418e8-fa22-420d-84ec-e0e1800db041
topic_type: apiref
caps.latest.revision: "11"
author: rpetrusha
ms.author: ronpet
manager: wpickett
ms.openlocfilehash: 7feef9af174a63976729f91b5a0b4967fea55b23
ms.sourcegitcommit: bd1ef61f4bb794b25383d3d72e71041a5ced172e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="icordebugheapvalue2createhandle-method"></a>ICorDebugHeapValue2::CreateHandle 方法
创建此 ICorDebugHeapValue2 对象所表示的堆值的指定类型句柄。  
  
## <a name="syntax"></a>语法  
  
```  
HRESULT CreateHandle (  
    [in] CorDebugHandleType      type,   
    [out] ICorDebugHandleValue   **ppHandle  
);  
```  
  
#### <a name="parameters"></a>参数  
 `type`  
 [in]CorDebugHandleType 枚举，指定要创建的句柄的类型的值。  
  
 `ppHandle`  
 [out]指向一个 ICorDebugHandleValue 对象，表示此堆值的新句柄的地址的指针。  
  
## <a name="remarks"></a>备注  
 句柄将在堆值中，关联的应用程序域中创建，并将变为无效，如果应用程序域获取卸载。  
  
 对此函数对于相同的堆值的多个调用将创建多个句柄。 句柄会影响垃圾回收器的性能，因为调试器应本身将限制为相对较小的处于活动状态一次句柄 (大约 256) 数。  
  
## <a name="requirements"></a>要求  
 **平台：**请参阅[系统要求](../../../../docs/framework/get-started/system-requirements.md)。  
  
 **标头：** CorDebug.idl、 CorDebug.h  
  
 **库：** CorGuids.lib  
  
 **.NET framework 版本：**[!INCLUDE[net_current_v20plus](../../../../includes/net-current-v20plus-md.md)]

---
title: "ICorProfilerCallback::ManagedToUnmanagedTransition 方法"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-clr
ms.tgt_pltfrm: 
ms.topic: reference
api_name: ICorProfilerCallback.ManagedToUnmanagedTransition
api_location: mscorwks.dll
api_type: COM
f1_keywords: ICorProfilerCallback::ManagedToUnmanagedTransition
helpviewer_keywords:
- ManagedToUnmanagedTransition method [.NET Framework profiling]
- ICorProfilerCallback::ManagedToUnmanagedTransition method [.NET Framework profiling]
ms.assetid: ef3cd619-912d-40c5-a449-03ba02a39ee7
topic_type: apiref
caps.latest.revision: "14"
author: mairaw
ms.author: mairaw
manager: wpickett
ms.openlocfilehash: 9da8dd44d5b87cd1c65b8b8837c9dd378039d332
ms.sourcegitcommit: 4f3fef493080a43e70e951223894768d36ce430a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2017
---
# <a name="icorprofilercallbackmanagedtounmanagedtransition-method"></a>ICorProfilerCallback::ManagedToUnmanagedTransition 方法
通知探查器从托管代码转换到非托管代码已发生。  
  
## <a name="syntax"></a>语法  
  
```  
HRESULT ManagedToUnmanagedTransition(  
    [in] FunctionID functionId,  
    [in] COR_PRF_TRANSITION_REASON reason);  
```  
  
#### <a name="parameters"></a>参数  
 `functionId`  
 [in]正在调用的函数的 ID。  
  
 `reason`  
 [in]值为[COR_PRF_TRANSITION_REASON](../../../../docs/framework/unmanaged-api/profiling/cor-prf-transition-reason-enumeration.md)枚举，指示转换是否发生由于到非托管代码调用从托管代码中，或由于从托管函数由一个非托管调用返回。  
  
## <a name="remarks"></a>备注  
 如果值`reason`是 COR_PRF_TRANSITION_CALL 的函数 ID 的非托管函数中，将永远不会已编译的使用在实时编译器。 非托管的函数具有与它们，如名称和一些元数据关联的基本信息。 如果使用隐式平台调用非托管的函数调用 (PInvoke)，运行时调用的目标和的值不能确定`functionId`将为 null。 有关隐式 PInvoke 的详细信息，请参阅[使用 c + + 互操作 (隐式 PInvoke)](/cpp/dotnet/using-cpp-interop-implicit-pinvoke)。  
  
## <a name="requirements"></a>要求  
 **平台：**请参阅[系统要求](../../../../docs/framework/get-started/system-requirements.md)。  
  
 **头文件：** CorProf.idl、CorProf.h  
  
 **库：** CorGuids.lib  
  
 **.NET framework 版本：**[!INCLUDE[net_current_v20plus](../../../../includes/net-current-v20plus-md.md)]  
  
## <a name="see-also"></a>另请参阅  
 [ICorProfilerCallback 接口](../../../../docs/framework/unmanaged-api/profiling/icorprofilercallback-interface.md)  
 [UnmanagedToManagedTransition 方法](../../../../docs/framework/unmanaged-api/profiling/icorprofilercallback-unmanagedtomanagedtransition-method.md)  
 [在 C++ 中使用显式 PInvoke（DllImport 特性）](/cpp/dotnet/using-explicit-pinvoke-in-cpp-dllimport-attribute)

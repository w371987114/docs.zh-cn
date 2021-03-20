---
description: 了解详细信息： ICorProfilerCallback：： Initialize 方法
title: ICorProfilerCallback::Initialize 方法
ms.date: 03/30/2017
api_name:
- ICorProfilerCallback.Initialize
api_location:
- mscorwks.dll
api_type:
- COM
f1_keywords:
- ICorProfilerCallback::Initialize
helpviewer_keywords:
- Initialize method, ICorProfilerCallback interface [.NET Framework profiling]
- ICorProfilerCallback::Initialize method [.NET Framework profiling]
ms.assetid: dc5fab2a-4b45-4b12-8727-b89c9915f23e
topic_type:
- apiref
ms.openlocfilehash: c7138a1a39a1d32c751c205a86c00e6070a236b3
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104760413"
---
# <a name="icorprofilercallbackinitialize-method"></a>ICorProfilerCallback::Initialize 方法

调用以在新的公共语言运行时 (CLR) 应用程序启动时初始化代码探查器。  
  
## <a name="syntax"></a>语法  
  
```cpp  
HRESULT Initialize(  
    [in] IUnknown     *pICorProfilerInfoUnk);  
```  
  
## <a name="parameters"></a>parameters

`pICorProfilerInfoUnk` 中指向 [IUnknown](/cpp/atl/iunknown) 接口的指针，探查器必须查询该接口的 [ICorProfilerInfo](icorprofilerinfo-interface.md) 接口指针。  

## <a name="remarks"></a>备注  

 `Initialize`调用是启用 (或禁用不可变的) 回调的唯一机会。 一旦调用启用了回调 `Initialize` ，以后就不能再使用 [ICorProfilerInfo：： SetEventMask](icorprofilerinfo-seteventmask-method.md)来禁用它。 [COR_PRF_MONITOR](cor-prf-monitor-enumeration.md)枚举的 COR_PRF_MONITOR_IMMUTABLE 值指示哪些事件是不可变的。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **头文件：** CorProf.idl、CorProf.h  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v20plus](../../../../includes/net-current-v20plus-md.md)]  
  
## <a name="see-also"></a>另请参阅

- [ICorProfilerCallback 接口](icorprofilercallback-interface.md)
- [Shutdown 方法](icorprofilercallback-shutdown-method.md)

---
description: 了解详细信息： ICorProfilerCallback：： JITCachedFunctionSearchFinished 方法
title: ICorProfilerCallback::JITCachedFunctionSearchFinished 方法
ms.date: 03/30/2017
api_name:
- ICorProfilerCallback.JITCachedFunctionSearchFinished
api_location:
- mscorwks.dll
api_type:
- COM
f1_keywords:
- ICorProfilerCallback::JITCachedFunctionSearchFinished
helpviewer_keywords:
- JITCachedFunctionSearchFinished method [.NET Framework profiling]
- ICorProfilerCallback::JITCachedFunctionSearchFinished method [.NET Framework profiling]
ms.assetid: 3c325c82-cddd-4b00-b3da-e450c36abf62
topic_type:
- apiref
ms.openlocfilehash: 3dc655c2a049a2cac08f6e4856aab98ee690b9df
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759952"
---
# <a name="icorprofilercallbackjitcachedfunctionsearchfinished-method"></a>ICorProfilerCallback::JITCachedFunctionSearchFinished 方法

通知探查器搜索已完成先前使用本机映像生成器编译的函数)  (NGen.exe。  
  
## <a name="syntax"></a>语法  
  
```cpp  
HRESULT JITCachedFunctionSearchFinished(  
    [in] FunctionID        functionId,  
    [in] COR_PRF_JIT_CACHE result);  
```  
  
## <a name="parameters"></a>parameters

`functionId` 中执行搜索的函数的 ID。

`result` 中指示搜索结果的 [COR_PRF_JIT_CACHE](cor-prf-jit-cache-enumeration.md) 枚举的值。

## <a name="remarks"></a>备注  

 在 .NET Framework 版本2.0 中，将不会对常规 NGen 映像中的所有函数执行 [ICorProfilerCallback：： JITCachedFunctionSearchStarted](icorprofilercallback-jitcachedfunctionsearchstarted-method.md) 和 `JITCachedFunctionSearchFinished` 回调。 只有针对探查器优化的 NGen 映像将为映像中的所有函数生成回调。 但是，由于额外的开销，如果探查器打算使用这些回调来强制将函数实时编译 (JIT) ，则它应请求探查器优化的 NGen 映像。 否则，探查器应使用延迟策略来收集函数信息。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **头文件：** CorProf.idl、CorProf.h  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v20plus](../../../../includes/net-current-v20plus-md.md)]  
  
## <a name="see-also"></a>另请参阅

- [ICorProfilerCallback 接口](icorprofilercallback-interface.md)

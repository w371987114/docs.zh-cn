---
description: 了解详细信息： ICorProfilerCallback：： JITCachedFunctionSearchStarted 方法
title: ICorProfilerCallback::JITCachedFunctionSearchStarted 方法
ms.date: 03/30/2017
api_name:
- ICorProfilerCallback.JITCachedFunctionSearchStarted
api_location:
- mscorwks.dll
api_type:
- COM
f1_keywords:
- ICorProfilerCallback::JITCachedFunctionSearchStarted
helpviewer_keywords:
- JITCachedFunctionSearchStarted method [.NET Framework profiling]
- ICorProfilerCallback::JITCachedFunctionSearchStarted method [.NET Framework profiling]
ms.assetid: 5cba642c-0d80-48ee-889d-198c5044d821
topic_type:
- apiref
ms.openlocfilehash: 3f384c1a02da1747b28701d2eba994b56a85c18f
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759945"
---
# <a name="icorprofilercallbackjitcachedfunctionsearchstarted-method"></a>ICorProfilerCallback::JITCachedFunctionSearchStarted 方法

通知探查器搜索已开始使用本机映像生成器编译的函数)  (NGen.exe。  
  
## <a name="syntax"></a>语法  
  
```cpp  
HRESULT JITCachedFunctionSearchStarted(  
    [in]  FunctionID functionId,  
    [out] BOOL *pbUseCachedFunction);  
```  
  
## <a name="parameters"></a>parameters

`functionId` 中正在为其执行搜索的函数的 ID。

`pbUseCachedFunction` [out] `true` 如果执行引擎应使用函数的缓存版本 (如有) ，则为;否则为 `false` 。 如果值为 `false` ，则执行引擎将对函数进行 JIT 编译，而不是使用未进行 JIT 编译的版本。

## <a name="remarks"></a>备注  

 在 .NET Framework 版本2.0 中， `JITCachedFunctionSearchStarted` 对于常规 NGen 映像中的所有函数，不会进行和 [ICorProfilerCallback：： JITCachedFunctionSearchFinished 方法](icorprofilercallback-jitcachedfunctionsearchfinished-method.md) 回调。 只有针对配置文件优化的 NGen 映像将为映像中的所有函数生成回调。 但是，由于额外的开销，如果探查器打算使用这些回调来强制将函数实时编译 (JIT) ，则它应请求探查器优化的 NGen 映像。 否则，探查器应使用延迟策略来收集函数信息。  
  
 探查器必须支持分析的应用程序的多个线程同时调用同一方法的情况。 例如，线程 A 调用 `JITCachedFunctionSearchStarted` 和探查器通过将 *pbUseCachedFunction* 设置为 FALSE 来做出响应，以强制 JIT 编译。 然后，线程 A 调用 [ICorProfilerCallback：： JITCompilationStarted](icorprofilercallback-jitcompilationstarted-method.md) 和 [ICorProfilerCallback：： JITCompilationFinished](icorprofilercallback-jitcompilationfinished-method.md)。  
  
 现在，线程 B 调用 `JITCachedFunctionSearchStarted` 同一函数。 即使探查器已经规定了 JIT 编译函数的意图，探查器仍会收到第二次回调，因为线程 B 会在探查器响应线程 A 对的调用之前发送回调 `JITCachedFunctionSearchStarted` 。 线程发出调用的顺序取决于线程的计划方式。  
  
 当探查器收到重复的回调时，它必须将引用的值设置 `pbUseCachedFunction` 为所有重复回调的相同值。 也就是说，当 `JITCachedFunctionSearchStarted` 用相同的值调用多次时 `functionId` ，探查器每次都必须响应相同的值。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **头文件：** CorProf.idl、CorProf.h  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v20plus](../../../../includes/net-current-v20plus-md.md)]  
  
## <a name="see-also"></a>另请参阅

- [ICorProfilerCallback 接口](icorprofilercallback-interface.md)

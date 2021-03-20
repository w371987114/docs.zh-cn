---
description: 了解详细信息： FunctionIDMapper 函数
title: FunctionIDMapper 函数
ms.date: 03/30/2017
api_name:
- FunctionIDMapper
api_location:
- mscorwks.dll
api_type:
- COM
f1_keywords:
- FunctionIDMapper
helpviewer_keywords:
- FunctionIDMapper function [.NET Framework profiling]
ms.assetid: b8205b60-1893-4303-8cff-7ac5a00892aa
topic_type:
- apiref
ms.openlocfilehash: 2db616509bf5dcb5b8aee9cea76a9841369ec49d
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759231"
---
# <a name="functionidmapper-function"></a>FunctionIDMapper 函数

通知探查器可能会将函数的给定标识符重新映射到备用 ID，以在该函数的 [FunctionEnter2](functionenter2-function.md)、 [FunctionLeave2](functionleave2-function.md)和 [FunctionTailcall2](functiontailcall2-function.md) 回调中使用。 `FunctionIDMapper` 此外还要使探查器指示它是否想要接收该函数的回调。  
  
## <a name="syntax"></a>语法  
  
```cpp  
UINT_PTR __stdcall FunctionIDMapper (  
    [in]  FunctionID  funcId,
    [out] BOOL       *pbHookFunction  
);  
```  
  
## <a name="parameters"></a>parameters

`funcId` 中要重新映射的函数标识符。

`pbHookFunction` 弄一个指针，指向探查器设置为的值 `true` `FunctionEnter2` `FunctionLeave2` `FunctionTailcall2` ; 否则，它会将此值设置为 `false` 。

## <a name="return-value"></a>返回值  

 探查器返回一个执行引擎用作替代函数标识符的值。 返回值不能为 null，除非在 `pbHookFunction` 中返回 `false`。 否则，空返回值将产生不可预知的结果，包括可能停止进程。  
  
## <a name="remarks"></a>备注  

 `FunctionIDMapper`函数是回调。 它由探查器实现，用于将函数 ID 重新映射到其他更适用于探查器的标识符。 `FunctionIDMapper`返回要用于任何给定函数的备用 ID。 然后，执行引擎通过将此备用 ID （除了传统函数 ID）传递回探查器的参数中的探查器来实现探查器的请求，以 `clientData` `FunctionEnter2` `FunctionLeave2` `FunctionTailcall2` 标识调用挂钩的函数。  
  
 您可以使用 [ICorProfilerInfo：： SetFunctionIDMapper](icorprofilerinfo-setfunctionidmapper-method.md) 方法来指定函数的实现 `FunctionIDMapper` 。 只能调用 `ICorProfilerInfo::SetFunctionIDMapper` 一次该方法，我们建议在 [ICorProfilerCallback：： Initialize](icorprofilercallback-initialize-method.md) 回调中执行此操作。  
  
 默认情况下，假定探查器通过使用 [ICorProfilerInfo：： SetEventMask](icorprofilerinfo-seteventmask-method.md)设置 COR_PRF_MONITOR_ENTERLEAVE 标志，并通过 [ICorProfilerInfo：： SetEnterLeaveFunctionHooks](icorprofilerinfo-setenterleavefunctionhooks-method.md) 或 [ICorProfilerInfo2：： SetEnterLeaveFunctionHooks2](icorprofilerinfo2-setenterleavefunctionhooks2-method.md)设置挂钩，应接收 `FunctionEnter2` `FunctionLeave2` `FunctionTailcall2` 每个函数的、和回调。 但是，探查器可 `FunctionIDMapper` 通过将设置为来有选择地避免为某些函数接收这些回调 `pbHookFunction` `false` 。  
  
 在分析的应用程序的多个线程同时调用相同的方法/函数的情况下，探查器应该是一种容错方法。 在这种情况下，探查器可能会收到相同的多个 `FunctionIDMapper` 回调 `FunctionID` 。 如果多次调用相同的，则探查器应确保从此回调返回相同的值 `FunctionID` 。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **标头：** Corprof.idl .idl  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v10plus](../../../../includes/net-current-v10plus-md.md)]  
  
## <a name="see-also"></a>另请参阅

- [SetFunctionIDMapper 方法](icorprofilerinfo-setfunctionidmapper-method.md)
- [FunctionIDMapper2 函数](functionidmapper2-function.md)
- [FunctionEnter2 函数](functionenter2-function.md)
- [FunctionLeave2 函数](functionleave2-function.md)
- [FunctionTailcall2 函数](functiontailcall2-function.md)
- [分析全局静态函数](profiling-global-static-functions.md)

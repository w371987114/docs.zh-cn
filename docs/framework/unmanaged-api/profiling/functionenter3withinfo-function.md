---
description: 了解详细信息： FunctionEnter3WithInfo 函数
title: FunctionEnter3WithInfo 函数
ms.date: 03/30/2017
api_name:
- FunctionEnter3WithInfo
api_location:
- mscorwks.cll
api_type:
- COM
f1_keywords:
- FunctionEnter3WithInfo
helpviewer_keywords:
- FunctionEnter3WithInfo function [.NET Framework profiling]
ms.assetid: 277c3344-d0cb-431e-beae-eb1eeeba8eea
topic_type:
- apiref
ms.openlocfilehash: 9bcebba724f7ebb405bb3d404f028e3ebca3e0d7
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759307"
---
# <a name="functionenter3withinfo-function"></a>FunctionEnter3WithInfo 函数

通知探查器控制正在传递到函数，并提供一个句柄，该句柄可传递给 [ICorProfilerInfo3：： GetFunctionEnter3Info 方法](icorprofilerinfo3-getfunctionenter3info-method.md) 以检索堆栈帧和函数自变量。  
  
## <a name="syntax"></a>语法  
  
```cpp  
void __stdcall FunctionEnter3WithInfo(  
               [in] FunctionIDOrClientID functionIDOrClientID,  
               [in] COR_PRF_ELT_INFO eltInfo);  
```  
  
## <a name="parameters"></a>parameters

`functionIDOrClientID` 中要传递控制的函数的标识符。

`eltInfo` 中表示有关给定堆栈帧的信息的不透明的句柄。 此句柄仅在其传递到的回调期间有效。

## <a name="remarks"></a>备注  

 `FunctionEnter3WithInfo`回调方法会在调用函数时通知探查器，并使探查器可以使用[ICorProfilerInfo3：： GetFunctionEnter3Info 方法](icorprofilerinfo3-getfunctionenter3info-method.md)来检查参数值。 若要访问参数信息， `COR_PRF_ENABLE_FUNCTION_ARGS` 必须设置标志。 探查器可以使用 [ICorProfilerInfo：： SetEventMask 方法](icorprofilerinfo-seteventmask-method.md) 来设置事件标志，然后使用 [ICorProfilerInfo3：： SetEnterLeaveFunctionHooks3WithInfo 方法](icorprofilerinfo3-setenterleavefunctionhooks3withinfo-method.md) 来注册此函数的实现。  
  
 `FunctionEnter3WithInfo`函数是回调; 必须实现它。 实现必须使用 `__declspec(naked)` 存储类特性。  
  
 在调用此函数之前，执行引擎不会保存任何注册。  
  
- 进入时，必须保存使用的所有寄存器，包括 (FPU) 的浮点单元中的寄存器。  
  
- 退出时，必须通过弹出由其调用方推送的所有参数来还原堆栈。  
  
 的实现 `FunctionEnter3WithInfo` 不应阻塞，因为它将延迟垃圾回收。 实现不应尝试垃圾回收，因为堆栈可能不处于垃圾回收友好状态。 如果尝试垃圾回收，则运行时将被阻止，直到 `FunctionEnter3WithInfo` 返回。  
  
 `FunctionEnter3WithInfo`函数不得调入托管代码或以任何方式导致托管内存分配。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **标头：** Corprof.idl .idl  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v20plus](../../../../includes/net-current-v20plus-md.md)]  
  
## <a name="see-also"></a>另请参阅

- [GetFunctionEnter3Info](icorprofilerinfo3-getfunctionenter3info-method.md)
- [FunctionEnter3](functionenter3-function.md)
- [FunctionLeave3](functionleave3-function.md)
- [分析全局静态函数](profiling-global-static-functions.md)

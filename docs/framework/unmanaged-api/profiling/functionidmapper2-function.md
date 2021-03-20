---
description: 了解详细信息： FunctionIDMapper2 函数
title: FunctionIDMapper2 函数
ms.date: 03/30/2017
api_name:
- FunctionIDMapper2
api_location:
- mscorwks.dll
api_type:
- COM
f1_keywords:
- FunctionIDMapper2
helpviewer_keywords:
- FunctionIDMapper2 function [.NET Framework profiling]
ms.assetid: 466ad51b-8f0c-41d9-81f7-371aac3374cb
topic_type:
- apiref
ms.openlocfilehash: 8d1b2de62e75665de7116b28a4aa68246dda7bbd
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759529"
---
# <a name="functionidmapper2-function"></a>FunctionIDMapper2 函数

通知探查器可能会将函数的给定标识符重新映射到备用 ID，该 ID 将在 [FunctionEnter3](functionenter3-function.md)、 [FunctionLeave3](functionleave3-function.md)和 [FunctionTailcall3](functiontailcall3-function.md)中使用，或者为该函数的[FunctionEnter3WithInfo](functionenter3withinfo-function.md)、 [FunctionLeave3WithInfo](functionleave3withinfo-function.md)和 [FunctionTailcall3WithInfo](functiontailcall3withinfo-function.md) 回调。 `FunctionIDMapper2` 此外还要使探查器指示它是否想要接收该函数的回调。  
  
## <a name="syntax"></a>语法  
  
```cpp  
UINT_PTR __stdcall FunctionIDMapper2 (  
    [in]  FunctionID  funcId,  
    [in]  void * clientData,  
    [out] BOOL       *pbHookFunction  
);  
```  
  
## <a name="parameters"></a>parameters

`funcId` 中要重新映射的函数标识符。

`clientData` 中指向用于消除运行时之间的歧义的数据的指针。

`pbHookFunction` 弄一个指针，指向探查器设置为的值（ `true` 如果它要接收 `FunctionEnter3` 、 `FunctionLeave3` 、和 `FunctionTailcall3` ）、 `FunctionEnter3WithInfo` 、 `FunctionLeave3WithInfo` 和 `FunctionTailcall3WithInfo` 回调; 否则，它会将此值设置为 `false` 。

## <a name="return-value"></a>返回值  

 探查器返回一个执行引擎用作替代函数标识符的值。 返回值不能为 null，除非在 `pbHookFunction` 中返回 `false`。 否则为 null 的返回值将产生不可预知的结果，包括可能停止该过程。  
  
## <a name="remarks"></a>备注  

 此方法使用用于传递客户端数据的附加参数对 [FunctionIDMapper](functionidmapper-function.md) 函数进行扩展。 客户端数据用于消除运行时之间的歧义。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **标头：** Corprof.idl .idl  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v40plus](../../../../includes/net-current-v40plus-md.md)]  
  
## <a name="see-also"></a>另请参阅

- [ICorProfilerInfo::SetFunctionIDMapper](icorprofilerinfo-setfunctionidmapper-method.md)
- [ICorProfilerInfo3::SetFunctionIDMapper2](icorprofilerinfo3-setfunctionidmapper2-method.md)
- [FunctionEnter3](functionenter3-function.md)
- [FunctionLeave3](functionleave3-function.md)
- [FunctionTailcall3](functiontailcall3-function.md)
- [FunctionEnter3WithInfo](functionenter3withinfo-function.md)
- [FunctionLeave3WithInfo](functionleave3withinfo-function.md)
- [FunctionTailcall3WithInfo](functiontailcall3withinfo-function.md)
- [分析全局静态函数](profiling-global-static-functions.md)

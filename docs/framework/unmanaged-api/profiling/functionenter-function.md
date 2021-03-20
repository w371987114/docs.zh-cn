---
description: 了解详细信息： FunctionEnter 函数
title: FunctionEnter 函数
ms.date: 03/30/2017
api_name:
- FunctionEnter
api_location:
- mscorwks.dll
api_type:
- COM
f1_keywords:
- FunctionEnter
helpviewer_keywords:
- FunctionEnter function [.NET Framework profiling]
ms.assetid: bf4ffa50-4506-4dd4-aa13-a0457b47ca74
topic_type:
- apiref
ms.openlocfilehash: 0f5c9f0dde405aaadf50a7da476bbae664ef8ef7
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759373"
---
# <a name="functionenter-function"></a>FunctionEnter 函数

通知探查器控制正在传递到函数。  
  
> [!NOTE]
> 此 `FunctionEnter` 函数在 .NET Framework 版本2.0 中已弃用，其使用将导致性能下降。 改为使用 [FunctionEnter2](functionenter2-function.md) 函数。  
  
## <a name="syntax"></a>语法  
  
```cpp  
void __stdcall FunctionEnter (  
    [in]  FunctionID funcID  
);  
```  
  
## <a name="parameters"></a>parameters

`funcID` 中要传递控制的函数的标识符。

## <a name="remarks"></a>备注  

 `FunctionEnter`函数是回调; 必须实现它。 实现必须使用 `__declspec` `naked`) 存储类特性的 (。  
  
 在调用此函数之前，执行引擎不会保存任何注册。  
  
- 进入时，必须保存使用的所有寄存器，包括 (FPU) 的浮点单元中的寄存器。  
  
- 退出时，必须通过弹出由其调用方推送的所有参数来还原堆栈。  
  
 的实现 `FunctionEnter` 不应被阻止，因为它将延迟垃圾回收。 实现不应尝试垃圾回收，因为堆栈可能不处于垃圾回收友好状态。 如果尝试垃圾回收，则运行时将被阻止，直到 `FunctionEnter` 返回。  
  
 此外，该 `FunctionEnter` 函数不得调入托管代码或以任何方式导致托管的内存分配。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **标头：** Corprof.idl .idl  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：** 1.1、1。0  
  
## <a name="see-also"></a>另请参阅

- [FunctionEnter2 函数](functionenter2-function.md)
- [FunctionLeave2 函数](functionleave2-function.md)
- [FunctionTailcall2 函数](functiontailcall2-function.md)
- [SetEnterLeaveFunctionHooks2 方法](icorprofilerinfo2-setenterleavefunctionhooks2-method.md)
- [分析全局静态函数](profiling-global-static-functions.md)

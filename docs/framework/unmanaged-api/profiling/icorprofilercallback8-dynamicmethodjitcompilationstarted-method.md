---
description: 了解详细信息： ICorProfilerCallback8：:D ynamicMethodJITCompilationStarted 方法
title: ICorProfilerCallback8：:D ynamicMethodJITCompilationStarted 方法
ms.date: 04/10/2018
api_name:
- ICorProfilerCallback8.DynamicMethodJITCompilationStarted
api_location:
- mscorwks.dll
- corprof.idl
api_type:
- COM
ms.openlocfilehash: c03251ab3120fd93cbb8e6c2f1bb62a4527a92bb
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759919"
---
# <a name="icorprofilercallback8dynamicmethodjitcompilationstarted-method"></a>ICorProfilerCallback8：:D ynamicMethodJITCompilationStarted 方法

[.NET Framework 4.7 及更高版本中支持]  
  
当动态方法的 JIT 编译开始时，通知探查器。  
  
## <a name="syntax"></a>语法  
  
```cpp  
HRESULT DynamicMethodJITCompilationStarted(  
     [in]  FunctionID  functionId,
     [in]  BOOL        fIsSafeToBlock,
     [in]  LPCBYTE     pILHeader,
     [in]  LONG        cbILHeader
);  
```  
  
## <a name="parameters"></a>parameters  

`functionId`  
中开始 JIT 编译的内存中函数的标识符。

`fIsSafeToBlock` [in] `true` 指示阻止可能会导致运行时等待调用线程从该回调返回; `false` 指示阻止操作不会影响运行时的操作。  

`pILHeader` 中指向该方法的 IL 标头的第一个字节的指针。

`cbILHeader` 中IL 标头中的字节数。

## <a name="remarks"></a>备注  

只要 JIT 编译动态方法，就会触发此回调。 这包括各种 IL 存根和 LCG 方法。 其目标是为探查器编写者提供足够的信息，以便向用户标识编译的方法。

> [!NOTE]
> `functionId` 由于动态方法没有元数据，因此不能使用值来解析为其元数据标记。

`pILHeader`指针仅在回调期间有效。

## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **头文件：** CorProf.idl、CorProf.h  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v47plus](../../../../includes/net-current-v47plus.md)]  
  
## <a name="see-also"></a>另请参阅

- [DynamicMethodJITCompilationFinished 方法](icorprofilercallback8-dynamicmethodjitcompilationfinished-method.md)
- [ICorProfilerCallback8 接口](icorprofilercallback8-interface.md)

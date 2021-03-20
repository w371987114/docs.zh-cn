---
description: 了解详细信息： ICorProfilerAssemblyReferenceProvider：： AddAssemblyReference 方法
title: ICorProfilerAssemblyReferenceProvider::AddAssemblyReference 方法
ms.date: 03/30/2017
dev_langs:
- cpp
api_name:
- ICorProfilerAssemblyReferenceProvider.AddAssemblyReference
api_location:
- mscorwks.dll
api_type:
- COM
ms.assetid: 3d5af8e7-c337-48f4-9fa6-97c83878b9b1
topic_type:
- apiref
ms.openlocfilehash: e73a6d59b76744dcf9f4991be2589220669e154d
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104760739"
---
# <a name="icorprofilerassemblyreferenceprovideraddassemblyreference-method"></a>ICorProfilerAssemblyReferenceProvider::AddAssemblyReference 方法

[仅在 .NET Framework 4.5.2 及更高版本中受支持]  
  
 向公共语言运行 (时通知) 探查器计划在 [ICorProfilerCallback：： ModuleLoadFinished](icorprofilercallback-moduleloadfinished-method.md) 回调中添加的程序集引用的 CLR。  
  
## <a name="syntax"></a>语法  
  
```cpp
HRESULT AddAssemblyReference(  
        const COR_PRF_ASSEMBLY_REFERENCE_INFO* pAssemblyRefInfo  
);  
```  
  
## <a name="parameters"></a>parameters

`pAssemblyRefInfo` 指向 [COR_PRF_ASSEMBLY_REFERENCE_INFO](cor-prf-assembly-reference-info-structure.md) 结构的指针，该结构为 CLR 提供有关在执行程序集引用闭包审核时应考虑的程序集引用的信息。
  
## <a name="remarks"></a>备注  

 探查器为其计划从 `wszAssemblyPath` [ICorProfilerCallback6：： GetAssemblyReferences](icorprofilercallback6-getassemblyreferences-method.md) 回调的参数中指定的程序集引用的每个目标程序集调用此方法。 [ICorProfilerAssemblyReferenceProvider](icorprofilerassemblyreferenceprovider-interface.md)接口对象传递给探查器的[ICorProfilerCallback6：： GetAssemblyReferences](icorprofilercallback6-getassemblyreferences-method.md)回调，同时传递给参数中的程序集路径和名称 `wszAssemblyPath` 。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **头文件：** CorProf.idl、CorProf.h  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v452plus](../../../../includes/net-current-v452plus-md.md)]  
  
## <a name="see-also"></a>另请参阅

- [ICorProfilerAssemblyReferenceProvider 方法](icorprofilerassemblyreferenceprovider-interface.md)
- [GetAssemblyReferences 方法](icorprofilercallback6-getassemblyreferences-method.md)
- [ModuleLoadFinished 方法](icorprofilercallback-moduleloadfinished-method.md)

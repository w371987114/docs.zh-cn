---
description: 了解详细信息： ICorProfilerInfo10：： RequestReJITWithInliners 方法
title: ICorProfilerInfo10::RequestReJITWithInliners
ms.date: 08/06/2019
dev_langs:
- cpp
api_name:
- ICorProfilerInfo10.RequestReJITWithInliners
api_location:
- mscorwks.dll
api_type:
- COM
author: davmason
ms.author: davmason
ms.openlocfilehash: 3d85537030e042d53bb4ff859eb1d2c3a24a45a5
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104760777"
---
# <a name="icorprofilerinfo10requestrejitwithinliners-method"></a>ICorProfilerInfo10：： RequestReJITWithInliners 方法

ReJITs 请求的方法，以及所请求的方法的任何 inliners。

## <a name="syntax"></a>语法

```cpp
HRESULT RequestReJITWithInliners( [in]                       DWORD       dwRejitFlags,
                                  [in]                       ULONG       cFunctions,
                                  [in, size_is(cFunctions)]  ModuleID    moduleIds[],
                                  [in, size_is(cFunctions)]  mdMethodDef methodIds[]);
```

## <a name="parameters"></a>parameters

`dwRejitFlags` 中 [COR_PRF_REJIT_FLAGS](cor-prf-rejit-flags-enumeration.md)的位掩码。

`cFunctions` 中要重新编译的函数的数目。

`moduleIds` 中指定 `moduleId` (的部分 `module` ，) 对，用于 `methodDef` 标识要重新编译的函数。

`methodIds` 中指定 `methodId` (的部分 `module` ，) 对，用于 `methodDef` 标识要重新编译的函数。

## <a name="remarks"></a>备注

[RequestReJIT](icorprofilerinfo4-requestrejit-method.md) 不会对内联方法进行任何跟踪。 探查器应阻止内联或跟踪内联，并 `RequestReJIT` 为所有 inliners 调用以确保内联方法的每个实例都已 ReJITted。 这会在附加上出现 ReJIT 问题，因为探查器不会监视内联。 可以调用此方法来保证完整的 inliners 集也会 ReJITted。

## <a name="requirements"></a>要求

**平台：** 请参阅 [支持 .Net Core 的操作系统](../../../core/install/windows.md?pivots=os-windows)。

**头文件：** CorProf.idl、CorProf.h

**库：** CorGuids.lib

**.Net 版本：**[!INCLUDE[net_core_30](../../../../includes/net-core-30-md.md)]

## <a name="see-also"></a>另请参阅

- [ICorProfilerInfo10 接口](icorprofilerinfo10-interface.md)

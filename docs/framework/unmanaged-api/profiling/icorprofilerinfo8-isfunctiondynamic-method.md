---
description: 了解详细信息： ICorProfilerInfo8：： IsFunctionDynamic 方法
title: ICorProfilerInfo8::IsFunctionDynamic
ms.date: 08/06/2019
dev_langs:
- cpp
api_name:
- ICorProfilerInfo8.IsFunctionDynamic
api_location:
- mscorwks.dll
api_type:
- COM
author: davmason
ms.author: davmason
ms.openlocfilehash: 139edeed3e078668974382f1719c8e03f83e2a09
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759073"
---
# <a name="icorprofilerinfo8isfunctiondynamic-method"></a>ICorProfilerInfo8：： IsFunctionDynamic 方法

确定函数是否没有关联的元数据。

## <a name="syntax"></a>语法

```cpp
HRESULT IsFunctionDynamic( [in]  FunctionID  functionId,
                           [out] BOOL        *isDynamic);
```

## <a name="parameters"></a>parameters

`functionId` 中 `FunctionID` 标识相关函数的。

`isDynamic` 弄指向的指针 `BOOL` ，它将包含一个值，该值指示函数是否没有元数据。

## <a name="remarks"></a>备注

如果函数没有元数据，则将其视为动态函数。 某些方法（如 IL 存根或 LCG 方法）没有关联的元数据，可以使用 IMetaDataImport Api 来检索这些元数据。 探查器可以通过指令指针或通过侦听 ICorProfilerCallback 来完成这些方法 [：:D ynamicmethodjitcompilationstarted](icorprofilercallback8-dynamicmethodjitcompilationstarted-method.md)。

## <a name="requirements"></a>要求

**平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。

**头文件：** CorProf.idl、CorProf.h

**库：** CorGuids.lib

**.NET Framework 版本：**[!INCLUDE[net_current_v472plus](../../../../includes/net-current-v472plus.md)]

## <a name="see-also"></a>另请参阅

- [ICorProfilerInfo8 接口](icorprofilerinfo8-interface.md)

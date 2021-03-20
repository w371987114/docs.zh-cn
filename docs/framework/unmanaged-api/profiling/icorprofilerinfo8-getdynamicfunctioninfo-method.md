---
description: 了解详细信息： ICorProfilerInfo8：： GetDynamicFunctionInfo 方法
title: ICorProfilerInfo8::GetDynamicFunctionInfo
ms.date: 08/06/2019
dev_langs:
- cpp
api_name:
- ICorProfilerInfo8.GetDynamicFunctionInfo
api_location:
- mscorwks.dll
api_type:
- COM
author: davmason
ms.author: davmason
ms.openlocfilehash: b38bd7a4f440edba0a7156176f223ba38c9807cf
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759112"
---
# <a name="icorprofilerinfo8getdynamicfunctioninfo-method"></a>ICorProfilerInfo8：： GetDynamicFunctionInfo 方法

检索有关动态方法的信息。

## <a name="syntax"></a>语法

```cpp
HRESULT GetDynamicFunctionInfo( [in]  FunctionID              functionId,
                                [out] ModuleID                *moduleId,
                                [out] PCCOR_SIGNATURE         *ppvSig,
                                [out] ULONG                   *pbSig,
                                [in]  ULONG                   cchName,
                                [out] ULONG                   *pcchName,
                                [out] WCHAR                   wszName[]);
```

## <a name="parameters"></a>parameters

`functionId` 中要为其检索信息的函数的 ID。

`moduleId` 中指向定义函数父类的模块的指针。

`ppvSig` 弄指向函数的签名的指针。

`pbSig` 弄指向函数签名的字节计数的指针。

`cchName` 中数组的最大大小 `wszName` 。

`pcchName` 弄数组中的字符数 `wszName` 。

`wszName` 弄一个数组 `WCHAR` ，它是函数的名称（如果存在）。

## <a name="remarks"></a>备注

某些方法（如 IL 存根或 LCG）没有关联的元数据，可以使用 [IMetaDataImport](../metadata/imetadataimport-interface.md) 和 [IMetaDataImport2](../metadata/imetadataimport2-interface.md) api 来检索这些元数据。 探查器可以通过指令指针或通过侦听 [ICorProfilerCallback8：:D ynamicmethodjitcompilationstarted](icorprofilercallback8-dynamicmethodjitcompilationstarted-method.md)来遇到此类方法。

此 API 可用于检索有关动态方法的信息，包括友好名称（如果可用）。

## <a name="requirements"></a>要求

**平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。

**头文件：** CorProf.idl、CorProf.h

**库：** CorGuids.lib

**.NET Framework 版本：**[!INCLUDE[net_current_v472plus](../../../../includes/net-current-v472plus.md)]

## <a name="see-also"></a>另请参阅

- [ICorProfilerInfo8 接口](icorprofilerinfo8-interface.md)

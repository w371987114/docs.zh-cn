---
description: 了解详细信息： ICorProfilerInfo9：： GetCodeInfo4 方法
title: ICorProfilerInfo9::GetCodeInfo4
ms.date: 08/06/2019
dev_langs:
- cpp
api_name:
- ICorProfilerInfo9.GetCodeInfo4
api_location:
- mscorwks.dll
api_type:
- COM
author: davmason
ms.author: davmason
ms.openlocfilehash: c7897e266fbb84d44df719c127e24bd375b560bb
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759086"
---
# <a name="icorprofilerinfo9getcodeinfo4-method"></a>ICorProfilerInfo9：： GetCodeInfo4 方法

给定本机代码起始地址，返回存储此代码的虚拟内存块。

## <a name="syntax"></a>语法

```cpp
HRESULT GetCodeInfo4( [in]  UINT_PTR pNativeCodeStartAddress,
                      [in]  ULONG32 cCodeInfos,
                      [out] ULONG32* pcCodeInfos,
                      [out] COR_PRF_CODE_INFO codeInfos[]);
```

## <a name="parameters"></a>parameters

`pNativeCodeStartAddress` 中指向本机函数开头的指针。

`cCodeInfos` 中数组的大小 `codeInfos` 。

`pcCodeInfos` 弄指向可用 [COR_PRF_CODE_INFO](cor-prf-code-info-structure.md) 结构总数的指针。

`codeInfos` 弄调用方提供的缓冲区。 返回此方法后，它包含一个 `COR_PRF_CODE_INFO` 结构数组，每个结构描述一个本机代码块。

## <a name="remarks"></a>备注

`GetCodeInfo4`方法类似于[GetCodeInfo3](icorprofilerinfo4-getcodeinfo3-method.md)，只不过它可以查找方法的不同本机版本的代码信息。

> [!NOTE]
> `GetCodeInfo4` 可以触发垃圾回收。

范围按公共中间语言 (CIL) 偏移递增的顺序进行排序。

`GetCodeInfo4`返回后，必须验证 `codeInfos` 缓冲区大小是否足以包含所有[COR_PRF_CODE_INFO](cor-prf-code-info-structure.md)的结构。 为此，请将 `cCodeInfos` 的值和 `cchName` 参数的值进行比较。 如果 `cCodeInfos` 除以 [COR_PRF_CODE_INFO](cor-prf-code-info-structure.md) 结构的大小小于 `pcCodeInfos` ，则分配更大的 `codeInfos` 缓冲区， `cCodeInfos` 用新的、更大的大小更新，然后再次调用 `GetCodeInfo4` 。

或者，可以先用长度为零的 `codeInfos` 缓冲区调用 `GetCodeInfo4` 以获取正确的缓冲区大小。 然后，可以将 `codeInfos` 缓冲区大小设置为中返回的值 `pcCodeInfos` ，再乘以 [COR_PRF_CODE_INFO](cor-prf-code-info-structure.md) 结构的大小，然后 `GetCodeInfo4` 再次调用。

## <a name="requirements"></a>要求

**平台：** 请参阅 [支持 .Net Core 的操作系统](../../../core/install/windows.md?pivots=os-windows)。

**头文件：** CorProf.idl、CorProf.h

**库：** CorGuids.lib

**.Net 版本：**[!INCLUDE[net_core_21](../../../../includes/net-core-21-md.md)]

## <a name="see-also"></a>另请参阅

- [ICorProfilerInfo9 接口](ICorProfilerInfo9-interface.md)

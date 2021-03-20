---
description: 了解详细信息： ICorProfilerInfo6：： EnumNgenModuleMethodsInliningThisMethod 方法
title: ICorProfilerInfo6::EnumNgenModuleMethodsInliningThisMethod 方法
ms.date: 03/30/2017
ms.assetid: b933dfe6-7833-40cb-aad8-40842dc3034f
ms.openlocfilehash: 236aaa820162dcc1d5c6c8ade1e8da78f5f4acb0
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759125"
---
# <a name="icorprofilerinfo6enumngenmodulemethodsinliningthismethod-method"></a>ICorProfilerInfo6::EnumNgenModuleMethodsInliningThisMethod 方法

返回一个枚举器，该枚举器指向给定的 NGen 模块中定义的所有方法，并内联给定方法。

## <a name="syntax"></a>语法

```cpp
HRESULT EnumNgenModuleMethodsInliningThisMethod(
        [in] ModuleID inlinersModuleId,
        [in] ModuleID inlineeModuleId,
        [in] mdMethodDef inlineeMethodId,
        [out] BOOL *incompleteData,
        [out] ICorProfilerMethodEnum** ppEnum
);
```

## <a name="parameters"></a>parameters

`inlinersModuleId` 中NGen 模块的标识符。

`inlineeModuleId` 中定义的模块的标识符 `inlineeMethodId` 。 有关详细信息，请参阅备注部分。

`inlineeMethodId` 中内联方法的标识符。 有关详细信息，请参阅备注部分。

`incompleteData` 弄一个标志，该标志指示是否 `ppEnum` 将所有方法内联到给定方法。  有关详细信息，请参阅备注部分。

`ppEnum` 弄指向枚举器地址的指针

## <a name="remarks"></a>备注

`inlineeModuleId` 和 `inlineeMethodId` 共同构成了可能内联的方法的完整标识符。 例如，假设 module `A` 定义方法 `Simple.Add` ：

```csharp
Simple.Add(int a, int b)
{ return a + b; }
```

和模块 B 定义 `Fancy.AddTwice` ：

```csharp
Fancy.AddTwice(int a, int b)
{ return Simple.Add(a,b) + Simple.Add(a,b); }
```

还假定 `Fancy.AddTwice` inlines 调用 `SimpleAdd` 。 探查器可以使用此枚举器查找在模块 B 中定义的内联的所有方法 `Simple.Add` ，并将枚举结果 `AddTwice` 。  `inlineeModuleId` 是模块的标识符 `A` ，是的 `inlineeMethodId` 标识符 `Simple.Add(int a, int b)` 。

如果在 `incompleteData` 函数返回后为 true，则枚举器不包含内联给定方法的所有方法。 如果尚未加载 inliners 模块的一个或多个直接或间接依赖项，则可能会发生这种情况。 如果探查器需要准确的数据，则应在加载更多模块时重试，最好是在每个模块加载时重试。

`EnumNgenModuleMethodsInliningThisMethod`方法可用于解决 ReJIT 的内联限制。 ReJIT 使探查器可以更改方法的实现，然后动态为其创建新代码。 例如，我们可以 `Simple.Add` 按如下所示进行更改：

```csharp
Simple.Add(int a, int b)
{ return 42; }
```

但是 `Fancy.AddTwice` ，因为已内联 `Simple.Add` ，所以它的行为与以前相同。 若要解决此限制，调用方必须搜索所有在这些方法中内联和使用的模块中的所有方法 `Simple.Add` `ICorProfilerInfo5::RequestRejit` 。 重新编译这些方法时，它们将具有新的行为， `Simple.Add` 而不是旧的行为。

## <a name="requirements"></a>要求

**平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。

**头文件：** CorProf.idl、CorProf.h

**库：** CorGuids.lib

**.NET Framework 版本：**[!INCLUDE[net_current_v46plus](../../../../includes/net-current-v46plus-md.md)]

## <a name="see-also"></a>另请参阅

- [ICorProfilerInfo6 接口](icorprofilerinfo6-interface.md)

---
title: "ICLRDebugging::OpenVirtualProcess 方法"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-clr
ms.tgt_pltfrm: 
ms.topic: reference
api_name: ICLRDebugging.OpenVirtualProcess
api_location: mscordbi.dll
api_type: COM
f1_keywords: ICLRDebugging::OpenVirtualProcess
helpviewer_keywords:
- OpenVirtualProcess method [.NET Framework debugging]
- ICLRDebugging::OpenVirtualProcess method [.NET Framework debugging]
ms.assetid: e8ab7c41-d508-4ed9-8a31-ead072b5a314
topic_type: apiref
caps.latest.revision: "15"
author: rpetrusha
ms.author: ronpet
manager: wpickett
ms.openlocfilehash: 2a59d676e358b2e06ac04bdf586d8ad416445337
ms.sourcegitcommit: 4f3fef493080a43e70e951223894768d36ce430a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2017
---
# <a name="iclrdebuggingopenvirtualprocess-method"></a>ICLRDebugging::OpenVirtualProcess 方法
获取对应于进程中加载了公共语言运行时 (CLR) 模块 ICorDebugProcess 接口。  
  
## <a name="syntax"></a>语法  
  
```  
HRESULT OpenVirtualProcess(  
    [in] ULONG64 moduleBaseAddress,  
    [in] IUnknown * pDataTarget,  
    [in] ICLRDebuggingLibraryProvider * pLibraryProvider,  
    [in] CLR_DEBUGGING_VERSION * pMaxDebuggerSupportedVersion,  
    [in] REFIID riidProcess,  
    [out, iid_is(riidProcess)] IUnknown ** ppProcess,  
    [in, out] CLR_DEBUGGING_VERSION * pVersion,  
    [out] CLR_DEBUGGING_PROCESS_FLAGS * pdwFlags);  
```  
  
#### <a name="parameters"></a>参数  
 `moduleBaseAddress`  
 [in]目标进程中的模块基址。 如果指定的模块不是 CLR 模块，将返回 COR_E_NOT_CLR。  
  
 `pDataTarget`  
 [in]允许托管的调试器检查进程状态数据目标抽象。 调试器必须实现[ICorDebugDataTarget](../../../../docs/framework/unmanaged-api/debugging/icordebugdatatarget-interface.md)接口。 应实现[ICLRDebuggingLibraryProvider](../../../../docs/framework/unmanaged-api/debugging/iclrdebugginglibraryprovider-interface.md)接口以支持方案其中正在调试的 CLR 是本地计算机未安装。  
  
 `pLibraryProvider`  
 [in]允许特定于版本的调试库需要定位和加载上一个库提供者回调接口。 此参数是必需的仅当`ppProcess`或`pFlags`不`null`。  
  
 `pMaxDebuggerSupportedVersion`  
 [in]此调试器可以调试 CLR 最高版本。 你应指定的主要、 次版本号和生成版本从最新的 CLR 版本，此调试器支持，并设置为 65535 以容纳将来的就地 CLR 服务版本的修订号。  
  
 `riidProcess`  
 [in]要检索 ICorDebugProcess 接口的 ID。 目前，只接受的值是 IID_CORDEBUGPROCESS3、 IID_CORDEBUGPROCESS2 和 IID_CORDEBUGPROCESS。  
  
 `ppProcess`  
 [out]由标识的 COM 接口指针`riidProcess`。  
  
 `pVersion`  
 [在中，out]CLR 版本。 在输入时，此值可为`null`。 它还可以指向[CLR_DEBUGGING_VERSION](../../../../docs/framework/unmanaged-api/debugging/clr-debugging-version-structure.md)结构，在此情况下，该结构的`wStructVersion`字段必须初始化为 0 （零）。  
  
 在输出时，返回`CLR_DEBUGGING_VERSION`将使用 CLR 的版本信息填充结构。  
  
 `pdwFlags`  
 [out]有关指定的运行时的信息性标志。 请参阅[CLR_DEBUGGING_PROCESS_FLAGS](../../../../docs/framework/unmanaged-api/debugging/clr-debugging-process-flags-enumeration.md)有关标志的说明的主题。  
  
## <a name="return-value"></a>返回值  
 此方法返回以下特定 HRESULT 以及表示方法失败的 HRESULT 错误。  
  
|HRESULT|描述|  
|-------------|-----------------|  
|S_OK|该方法已成功完成。|  
|E_POINTER|`pDataTarget` 为 `null`。|  
|CORDBG_E_LIBRARY_PROVIDER_ERROR|[ICLRDebuggingLibraryProvider](../../../../docs/framework/unmanaged-api/debugging/iclrdebugginglibraryprovider-interface.md)回调返回错误或未提供有效的句柄。|  
|CORDBG_E_MISSING_DATA_TARGET_INTERFACE|`pDataTarget`未实现此版本的运行时所需的数据目标接口。|  
|CORDBG_E_NOT_CLR|所指示的模块不是 CLR 模块。 无法检测到 CLR 模块，因为内存损坏、 模块不可用，或的 CLR 版本不比填充码版本更高时，也会返回此 HRESULT。|  
|CORDBG_E_UNSUPPORTED_DEBUGGING_MODEL|此运行时版本不支持此调试的模型。 目前，通过在之前的 CLR 版本不支持的调试模型[!INCLUDE[net_v40_long](../../../../includes/net-v40-long-md.md)]。 `pwszVersion`输出参数仍将设置为正确的值后此错误。|  
|CORDBG_E_UNSUPPORTED_FORWARD_COMPAT|CLR 版本大于此调试器声明以支持的版本。 `pwszVersion`输出参数仍将设置为正确的值后此错误。|  
|E_NO_INTERFACE|`riidProcess`接口不可用。|  
|CORDBG_E_UNSUPPORTED_VERSION_STRUCT|`CLR_DEBUGGING_VERSION`结构，不能是识别的值为`wStructVersion`。 此时唯一接受的值为 0。|  
  
## <a name="exceptions"></a>异常  
  
## <a name="remarks"></a>备注  
  
## <a name="requirements"></a>要求  
 **平台：**请参阅[系统要求](../../../../docs/framework/get-started/system-requirements.md)。  
  
 **标头：** CorDebug.idl、 CorDebug.h  
  
 **库：** CorGuids.lib  
  
 **.NET framework 版本：**[!INCLUDE[net_current_v40plus](../../../../includes/net-current-v40plus-md.md)]  
  
## <a name="see-also"></a>另请参阅  
 [调试接口](../../../../docs/framework/unmanaged-api/debugging/debugging-interfaces.md)  
 [调试](../../../../docs/framework/unmanaged-api/debugging/index.md)

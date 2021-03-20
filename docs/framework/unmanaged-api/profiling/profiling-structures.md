---
description: 了解详细信息：分析结构
title: 分析结构
ms.date: 03/30/2017
helpviewer_keywords:
- profiling structures [.NET Framework]
- unmanaged structures [.NET Framework], profiling
- structures [.NET Framework profiling]
ms.assetid: 750385f2-f365-41b1-939f-ca2f2ff9b466
ms.openlocfilehash: 176830cac519f22864ba004b176cb575d80e50e2
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104760231"
---
# <a name="profiling-structures"></a>分析结构

本节描述分析 API 使用的非托管结构。  
  
## <a name="in-this-section"></a>本节内容  

 [COR_PRF_ASSEMBLY_REFERENCE_INFO 结构](cor-prf-assembly-reference-info-structure.md)  
 向公共语言运行时提供有关在执行程序集引用闭包审核时应考虑的引用程序集的信息。  
  
 [COR_PRF_CODE_INFO 结构](cor-prf-code-info-structure.md)  
 表示存储在内存中的一个本机代码连续块。  
  
 [COR_PRF_EX_CLAUSE_INFO 结构](cor-prf-ex-clause-info-structure.md)  
 存储有关特定的异常子句实例及其关联的帧的信息。  
  
 [COR_PRF_FUNCTION 结构](cor-prf-function-structure.md)  
 通过将函数的 ID 与其重新编译的 ID 版本组合起来，提供该函数的唯一表示形式。  
  
 [COR_PRF_FUNCTION_ARGUMENT_INFO 结构](cor-prf-function-argument-info-structure.md)  
 按从左向右的顺序表示函数的参数。  
  
 [COR_PRF_FUNCTION_ARGUMENT_RANGE 结构](cor-prf-function-argument-range-structure.md)  
 表示内存中按从左向右的顺序连续存储的函数自变量块。  
  
 [COR_PRF_GC_GENERATION_RANGE 结构](cor-prf-gc-generation-range-structure.md)  
 描述一个正进行垃圾回收的内存范围（即块）。  

 [COR_PRF_EVENTPIPE_PROVIDER_CONFIG 结构](cor-prf-eventpipe-provider-config-structure.md) 描述配置 EventPipe 提供程序所必需的字段。

 [COR_PRF_EVENTPIPE_PARAM_DESC 结构](cor-prf-eventpipe-param-desc-structure.md) 描述 EventPipe 事件的参数名称和类型。

 [COR_PRF_EVENT_DATA 结构](cor-prf-event-data-structure.md) 描述要写入的 EventPipe 事件的事件数据。
  
## <a name="related-sections"></a>相关章节  

 COR_DEBUG_IL_TO_NATIVE_MAP  
  
 COR_IL_MAP  
  
 [分析概述](profiling-overview.md)  
  
 [分析接口](profiling-interfaces.md)  
  
 [分析全局静态函数](profiling-global-static-functions.md)  
  
 [分析枚举](profiling-enumerations.md)

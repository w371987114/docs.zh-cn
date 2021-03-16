---
title: 如何：查看全局程序集缓存的内容
description: 了解如何使用全局程序集缓存 (GAC) 工具 (gacutil.exe) 查看 .NET 中的全局程序集缓存的内容。
ms.date: 03/30/2017
helpviewer_keywords:
- assemblies [.NET Framework], global assembly cache
- global assembly cache, viewing contents
- viewing assemblies in global assembly cache
- Gacutil.exe
- strong-named assemblies, global assembly cache
- GAC (global assembly cache), viewing contents
- list of assemblies in global assembly cache
- Global Assembly Cache tool
ms.assetid: c5f786a0-969b-4f14-9f02-e77c3384d9af
ms.openlocfilehash: a40c371e6f95f6c90ecbfbf28183226632a58e5b
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102258307"
---
# <a name="how-to-view-the-contents-of-the-global-assembly-cache"></a>如何：查看全局程序集缓存的内容

使用[全局程序集缓存工具 (Gacutil.exe)](../tools/gacutil-exe-gac-tool.md) 可查看全局程序集缓存 (GAC) 的内容。

## <a name="view-the-assemblies-in-the-gac"></a>查看 GAC 中的程序集

要查看全局程序集缓存中的程序集列表，请打开[开发人员命令行 shell](/visualstudio/ide/reference/command-prompt-powershell)，然后输入以下命令：

```shell
gacutil -l
```

- 或 -

```shell
gacutil /l
```

> [!NOTE]
> 在 .NET Framework 的早期版本中，可通过 [Shfusion.dll](/previous-versions/dotnet/netframework-4.0/34149zk3(v=vs.100)) Windows shell 扩展在文件资源管理器中查看全局程序集缓存。 从 .NET Framework 4 开始，Shfusion.dll 已过时。

## <a name="see-also"></a>请参阅

- [使用程序集和全局程序集缓存](working-with-assemblies-and-the-gac.md)
- [Gacutil.exe（全局程序集缓存工具）](../tools/gacutil-exe-gac-tool.md)

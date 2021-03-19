---
description: 了解详细信息： BC30145：无法发出程序集： <error message>
title: 无法发出程序集：<error message>
ms.date: 08/14/2018
f1_keywords:
- vbc30145
- bc30145
helpviewer_keywords:
- BC30145
ms.assetid: 2e7eb2b9-eda6-4bdb-95cc-72c7f0be7528
ms.openlocfilehash: 2ba476b39b6aa441d8778ee0618dcc3dcf3f7ac8
ms.sourcegitcommit: 1dbe25ff484a02025d5c34146e517c236f7161fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104653272"
---
# <a name="bc30145-unable-to-emit-assembly-error-message"></a>BC30145：无法发出程序集： \<error message>

Visual Basic 编译器调用程序集链接器 (*Al.exe*（也称为 Alink) ）以生成包含清单的程序集，并且链接器在创建该程序集的辐射阶段报告错误。

**错误 ID：** BC30145

## <a name="to-correct-this-error"></a>更正此错误

1. 检查引用的错误消息并参考主题 [Al.exe](../../../framework/tools/al-exe-assembly-linker.md) 以获取进一步的说明和建议。

2. 尝试使用 [Al.exe](../../../framework/tools/al-exe-assembly-linker.md) 或 [Sn.exe (强名称工具) ](../../../framework/tools/sn-exe-strong-name-tool.md)，手动对程序集进行签名。

3. 如果仍然出现错误，则收集有关该情况的信息并通知 Microsoft 产品支持服务。

### <a name="to-sign-the-assembly-manually"></a>手动对程序集进行签名

1. 使用 [Sn.exe (强名称工具) ](../../../framework/tools/sn-exe-strong-name-tool.md)) 来创建公钥/私钥对文件。

   此文件的扩展名为 *.snk* 。

2. 从项目中删除生成错误的 COM 引用。

3. 打开 [Visual studio 开发人员命令提示或 Visual Studio 开发人员 PowerShell](/visualstudio/ide/reference/command-prompt-powershell)。

4. 将目录更改为要在其中放置程序集包装的目录。

5. 输入以下命令：

    ```cmd
    tlbimp <path to COM reference file> /out:<output assembly name> /keyfile:<path to .snk file>
    ```

   你可能会输入的实际命令的示例如下：

    ```cmd
    tlbimp c:\windows\system32\msi.dll /out:Interop.WindowsInstaller.dll /keyfile:"c:\documents and settings\mykey.snk"
    ```

   > [!TIP]
   > 如果路径或文件包含空格，请使用双引号。

6. 在 Visual Studio 中，将 .NET 程序集引用添加到刚创建的文件。

## <a name="see-also"></a>请参阅

- [Al.exe](../../../framework/tools/al-exe-assembly-linker.md)
- [Sn.exe（强名称工具）](../../../framework/tools/sn-exe-strong-name-tool.md)
- [如何：创建公钥/私钥对](../../../standard/assembly/create-public-private-key-pair.md)
- [Visual Studio 反馈选项](/visualstudio/ide/feedback-options)

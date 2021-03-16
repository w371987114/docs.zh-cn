---
title: 如何：添加对类型库的引用
description: 了解如何在 Visual Studio 中或对命令行编译添加对类型库的引用。
ms.date: 03/30/2017
helpviewer_keywords:
- importing type library
- interop assemblies, generating
- type libraries
- COM interop, importing type library
ms.assetid: f5cfa6ba-cc25-4017-82cd-ba7391859113
ms.openlocfilehash: b5e58c5943eba8db7497b4db56bfbd99b17b1043
ms.sourcegitcommit: 0bb8074d524e0dcf165430b744bb143461f17026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2021
ms.locfileid: "103477623"
---
# <a name="how-to-add-references-to-type-libraries"></a>如何：添加对类型库的引用

当添加对类型库的引用时，Visual Studio 将生成包含元数据的互操作程序集。 如果主互操作程序集可用，则 Visual Studio 在生成新的互操作程序集之前将使用现有程序集。  
  
### <a name="to-add-a-reference-to-a-type-library-in-visual-studio"></a>在 Visual Studio 中添加对类型库的引用  
  
1. 在计算机上安装 COM DLL 或 EXE 文件，除非 Windows Setup.exe 文件会为你执行此安装。  
  
2. 选择“项目”、“添加引用” 。  
  
3. 在引用管理器中，选择“COM”。  
  
4. 从列表中选择类型库，或通过浏览选择 .tlb 文件。  
  
5. 选择 **“确定”** 。  
  
6. 在解决方案资源管理器中，打开刚刚添加的引用快捷菜单，然后选择“属性”。  
  
7. 在“属性”窗口中，确保将“嵌入互操作类型”属性设置为“True”  。 通过此设置，Visual Studio 将会在可执行文件中嵌入 COM 类型的类型信息，从而不必将主互操作程序集与你的应用一起部署。  
  
> [!NOTE]
> 根据你使用的 Visual Studio 版本，菜单和对话框选项可能会有所不同。  
  
### <a name="to-add-a-reference-to-a-type-library-for-command-line-compilation"></a>添加对类型库的引用以进行命令行编译  
  
1. 按后列文章中所述，生成一个互操作程序集：[如何：从类型库生成互操作程序集](how-to-generate-interop-assemblies-from-type-libraries.md)。  
  
2. 配合使用 [-link（C# 编译器选项）](../../csharp/language-reference/compiler-options/inputs.md#embedinteroptypes)或 [-link (Visual Basic)](../../visual-basic/reference/command-line-compiler/link.md) 编译器选项与互操作程序集名称，将 COM 类型的类型信息嵌入到可执行文件中。  
  
## <a name="see-also"></a>请参阅

- [将类型库作为程序集导入](importing-a-type-library-as-an-assembly.md)
- [向 .NET Framework 公开 COM 组件](exposing-com-components.md)
- [演练：在 Visual Studio 中嵌入托管程序集中的类型](../../standard/assembly/embed-types-visual-studio.md)
- [-link（C# 编译器选项）](../../csharp/language-reference/compiler-options/inputs.md#embedinteroptypes)
- [-link (Visual Basic)](../../visual-basic/reference/command-line-compiler/link.md)

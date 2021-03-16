---
title: 64 位应用程序
description: 获取有关在 64 位 Windows OS 上配置应用程序的信息，可以是本机 64 位应用程序，也可以是 WOW64 下（64 位 Windows 上的 32 位 Windows）的应用程序。
ms.date: 03/30/2017
helpviewer_keywords:
- applications [C++], 64-bit
- 64-bit applications [C++]
- 64-bit programming [C++]
ms.assetid: fd4026bc-2c3d-4b27-86dc-ec5e96018181
ms.openlocfilehash: 8274fe8f5d32b0f6258e450cc7d649fe3e184d78
ms.sourcegitcommit: 0bb8074d524e0dcf165430b744bb143461f17026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2021
ms.locfileid: "103477211"
---
# <a name="64-bit-applications"></a>64 位应用程序

编译应用程序时，您可以将其指定为在 Windows 64 位操作系统上作为本机应用程序或在 WOW64（Windows 64 位下的 Windows 32 位）下运行。 WOW64 是一种兼容性环境，它使 32 位应用能够在 64 位系统上运行。 WOW64 包括在所有 64 位版本的 Windows 操作系统中。  
  
## <a name="running-32-bit-vs-64-bit-applications-on-windows"></a>在 Windows 上运行 32 位与64 位应用程序  

 在 64 位操作系统上，所有基于 .NET Framework 1.0 或 1.1 生成的应用程序都会被视为 32 位应用程序，并始终在 WOW64 和 32 位公共语言运行时 (CLR) 下执行。 基于 .NET Framework 4 或更高版本生成的 32 位应用程序也可以在 64 位系统上的 WOW64 下运行。  
  
 在 x86 计算机上，Visual Studio 会安装 32 位版本的 CLR，而在 64 位 Windows 计算机上会同时安装 32 位版本和适当的 64 位版本的 CLR。 （因为 Visual Studio 是一个 32 位应用程序，所以当安装到 64 位系统上时，它会在 WOW64 下运行。）  
  
> [!NOTE]
> 由于 Itanium 处理器系列的 x86 仿真和 WOW64 子系统设计，仅限在一个处理器上执行应用程序。 这些因素会降低在基于 Itanium 的系统上运行的 32 位 .NET Framework 应用程序的性能和可伸缩性。 我们建议你使用 .NET Framework 4，它包括对基于 Itanium 的系统的本机 64 位支持，以提升性能和可伸缩性。  
  
 默认情况下，在 64 位 Windows 操作系统上运行 64 位托管应用程序时，您可以创建一个不超过 2 GB 的对象。 然而，在 .NET Framework 4.5 中，你可以增加该限制。  有关更多信息，请参见 [\<gcAllowVeryLargeObjects> 元素](./configure-apps/file-schema/runtime/gcallowverylargeobjects-element.md)。  
  
 很多程序集可在 32 位 CLR 和 64 位 CLR 上同样运行。 然而，因为包含下列一个或多个原因，对于不同的 CLR，有些程序可能会有不同表现：  
  
- 结构中包含大小随平台而改变的成员，例如任何指针类型。  
  
- 指针算术包含固定大小。  
  
- 平台调用错误，或使用句柄的 `Int32` 而非 `IntPtr` 的 COM 声明不正确。  
  
- 将 `IntPtr` 转换到 `Int32` 的代码。  
  
 有关如何移植 32 位应用程序以使其在 64 位 CLR 上运行的详细信息，请参阅[将 32 位托管代码迁移至 64 位](/previous-versions/dotnet/articles/ms973190(v=msdn.10))。  
  
## <a name="general-64-bit-programming-information"></a>常规 64 位编程信息  

 有关 64 位编程的常规信息，请参阅以下文档：  
  
- 在 Windows SDK 文档中，请参阅 [64 位 Windows 编程指南](/windows/win32/winprog64/programming-guide-for-64-bit-windows)。  
  
- 有关 Visual Studio 对创建 64 位应用程序提供的支持的信息，请参阅 [Visual Studio IDE 64 位支持](/visualstudio/ide/visual-studio-ide-64-bit-support)。  
  
## <a name="compiler-support-for-creating-64-bit-applications"></a>创建 64 位应用程序的编译器支持  

 默认情况下，如果您使用 .NET Framework 在 32 位或 64 位计算机中生成一个应用程序，该应用程序将会在 64 位计算机中作为本机应用程序运行（即不是在 WOW64 下运行）。 有关如何使用 Visual Studio 编译器创建 64 位应用程序，并且应用程序会作为本机应用程序运行和/或在 WOW64 下运行的信息，请参阅下表中的文档。  
  
|编译器|编译器选项|  
|--------------|---------------------|  
|Visual Basic|[-platform (Visual Basic)](../visual-basic/reference/command-line-compiler/platform.md)|  
|Visual C#|[-platform（C# 编译器选项）](../csharp/language-reference/compiler-options/output.md#platformtarget)|  
|Visual C++|可以通过使用 **/clr:safe** 创建与平台无关的 Microsoft 中间语言 (MSIL) 应用程序。 有关详细信息，请参阅 [-clr（公共语言运行时编译）](/cpp/build/reference/clr-common-language-runtime-compilation)。<br /><br /> Visual c++ 为每个 64 位操作系统均包括一个单独的编译器。 有关如何使用 Visual C++ 创建可在 64 位 Windows 操作系统上运行的本机应用程序的详细信息，请参阅 [64 位编程](/cpp/build/configuring-programs-for-64-bit-visual-cpp)。|  
  
## <a name="determining-the-status-of-an-exe-file-or-dll-file"></a>确定 .exe 文件或 .dll 文件的状态  

 若要确定 .exe 文件或 .dll 文件是只能在特定平台上运行还是可在 WOW64 下运行，请使用不带任何选项的 [CorFlags.exe（CorFlags 转换工具）](./tools/corflags-exe-corflags-conversion-tool.md)。 您也可以使用 CorFlags.exe 更改 .exe 文件或 .dll 文件的平台状态。 Visual Studio 程序集的 CLR 头的主运行时版本号设置为 2，次运行时版本号设置为 5。 将次运行时版本设置为 0 的应用程序会被视为旧版应用程序，且始终在 WOW64 下执行。  
  
 若要以编程方式查询 .exe 或 .dll，以查看其是只能在特定平台上运行还是在 WOW64 下运行，请使用 <xref:System.Reflection.Module.GetPEKind%2A?displayProperty=nameWithType> 方法。

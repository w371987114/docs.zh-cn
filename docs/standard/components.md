---
title: .NET 体系结构组件
description: 描述 .NET 体系结构组件，例如 .NET Standard、.NET 实现、.NET 运行时和工具。
author: cartermp
ms.date: 10/05/2020
ms.openlocfilehash: d1780d8f85159c12ef4e11b2ac53091b61629352
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605316"
---
# <a name="net-architectural-components"></a>.NET 体系结构组件

.NET 应用开发用于并运行于一个或多个 .NET 实现  。 .NET 实现包括 .NET Framework、.NET 5（和 .NET Core）以及 Mono。 对于多个 .NET 实现，有一个名为 .NET Standard 的通用 API 规范。 本文简要介绍了每个概念。

## <a name="net-standard"></a>.NET Standard

.NET Standard 是一组由 .NET 实现的基类库实现的 API。 更正式地说，它是构成协定统一集（这些协定是编写代码的依据）的特定 .NET API 组。 这些协定在多个 .NET 实现中实现。

.NET Standard 是一个[目标框架](glossary.md#target-framework)。 如果代码面向 .NET Standard 版本，则它可在支持该 .NET Standard 版本的任何 .NET 实现上运行。

创建 .NET Standard 是为了支持跨不同 .NET 实现的可移植性，但现在 .NET 5 提供了一种更好的方法来跨多个平台和工作负载共享代码。 有关详细信息，请参阅 [.NET 5 和 .NET Standard](net-standard.md#net-5-and-net-standard)。

## <a name="net-implementations"></a>.NET 实现

.NET 的每个实现都具有以下组件：

- 一个或多个运行时。 示例：.NET Framework CLR、.NET 5 CLR。
- 一个类库。 示例：.NET Framework 基类库、.NET 5 基类库。
- 可选择包含一个或多个应用程序框架。 示例：[ASP.NET](https://www.asp.net/)、[Windows 窗体](/dotnet/desktop/winforms/windows-forms-overview)和 [Windows Presentation Foundation (WPF)](/dotnet/desktop/wpf/) 包含在 .NET Framework 和 .NET 5 中。
- 可包含开发工具。 某些开发工具在多个实现之间共享。

Microsoft 支持以下四种 .NET 实现：

- .NET 5（和 .NET Core）及更高版本
- .NET Framework
- Mono
- UWP

.NET 5 现在是主要的实现，是正在进行的开发的重点。 .NET 5 是基于单个代码基底进行构建的，该代码基底支持多个平台和许多工作负载，例如 Windows 桌面应用和跨平台控制台应用、云服务和网站。

### <a name="net-5"></a>.NET 5

.NET 5 是 .NET 的跨平台实现，专门设计用于处理大规模的服务器和云工作负载。 它还支持其他工作负载，包括桌面应用。 可在 Windows、macOS 和 Linux 上运行。 它可实现 .NET Standard，因此面向 .NET Standard 的代码都可在 .NET 5 上运行。 [ASP.NET Core](https://dotnet.microsoft.com/learn/aspnet/what-is-aspnet-core)、[Windows 窗体](/dotnet/desktop/winforms/windows-forms-overview)和 [Windows Presentation Foundation (WPF)](/dotnet/desktop/wpf/) 都在 .NET 5 上运行。

有关更多信息，请参见以下资源：

- [.NET 简介](../core/introduction.md)
- [为服务器应用选择 .NET 5 或 .NET Framework](choosing-core-framework-server.md)
- [.NET 5 和 .NET Standard](net-standard.md#net-5-and-net-standard)

### <a name="net-framework"></a>.NET Framework

.Net Framework 是自 2002 年起就已存在的原始 .NET 实现。 4\.5 版以及更高版本实现 .NET Standard，因此面向 .NET Standard 的代码都可在这些版本的 .NET Framework 上运行。 它还包含一些特定于 Windows 的 API，如通过 Windows 窗体和 WPF 进行 Windows 桌面开发的 API。 .NET Framework 非常适合用于生成 Windows 桌面应用程序。

有关详细信息，请参阅 [.NET Framework 指南](../framework/index.yml)。

### <a name="mono"></a>Mono

Mono 是主要在需要小型运行时使用的 .NET 实现。 它是在 Android、macOS、iOS、tvOS 和 watchOS 上驱动 Xamarin 应用程序的运行时，且主要针对小内存占用。 Mono 还支持使用 Unity 引擎生成的游戏。

它支持所有当前已发布的 .NET Standard 版本。

以前，Mono 实现更大的 .NET Framework API 并模拟一些 Unix 上最常用的功能。 有时使用它运行依赖 Unix 上的这些功能的 .NET 应用程序。

Mono 通常与实时编译器一起使用，但它也提供在 iOS 之类的平台使用的完整静态编译器（预先编译）。

有关详细信息，请参阅 [Mono 文档](https://www.mono-project.com/docs/)。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

UWP 是用于为物联网 (IoT) 生成新式触控 Windows 应用程序和软件的 .NET 实现。 它旨在统一可能想要以其为目标的不同类型的设备，包括电脑、平板电脑、电话，甚至 Xbox。 UWP 提供许多服务，如集中式应用商店、执行环境 (AppContainer) 和一组 Windows API（用于代替 Win32 (WinRT)）。 应用可采用 C++、C#、Visual Basic 和 JavaScript 编写。

有关详细信息，请参阅[通用 Windows 平台简介](/windows/uwp/get-started/universal-application-platform-guide)。

## <a name="net-runtimes"></a>.NET 运行时

运行时是用于托管程序的执行环境。 操作系统属于运行时环境，但不属于 .NET 运行时。 下面是 .NET 运行时的一些示例：

- .NET Framework 公共语言运行时 (CLR)
- .NET 5 公共语言运行时 (CLR)
- 适用于通用 Windows 平台的 .NET Native
- 用于 Xamarin.iOS、Xamarin.Android、Xamarin.Mac 和 Mono 桌面框架的 Mono 运行时

## <a name="net-tooling-and-common-infrastructure"></a>.NET 工具和常见基础结构

可访问一整套适用于每种 .NET 实现的工具和基础结构组件。 这些工具和组件包括：

- .NET 语言及其编译器
- .NET 项目系统（基于 .csproj  .vbproj  和 .fsproj  文件）
- [MSBuild](/visualstudio/msbuild/msbuild)（用于生成项目的生成引擎）
- [NuGet](/nuget/)（适用于.NET 的 Microsoft 程序包管理器）
- 开放源生成业务流程工具，例如 [CAKE](https://cakebuild.net/) 和 [FAKE](https://fake.build/)

有关详细信息，请参阅[工具与工作效率](../core/introduction.md#tools-and-productivity)。

## <a name="applicable-standards"></a>适用标准

C# 语言和公共语言基础结构 (CLI) 规范通过 [Ecma International&reg;](https://www.ecma-international.org/) 进行标准化。 这些标准的第一版已于 2001 年 12 月由 Ecma 发布。

这些标准的后续版本由编程委员技术委员会 ([TC49](https://www.ecma-international.org/technical-committees/tc49/)) 的 TC49-TG2 (C#) 和 TC49-TG3 (CLI) 任务组编制，被 Ecma General Assembly 采纳，随后通过 ISO 快速跟踪流程被 ISO/IEC JTC 1 采纳。

### <a name="latest-standards"></a>最新标准

以下官方 Ecma 文档可用于 [C#](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/) 和 [CLI](https://www.ecma-international.org/publications-and-standards/standards/ecma-335/) ([TR-84](https://www.ecma-international.org/publications-and-standards/technical-reports/ecma-tr-84/))：

- **C# 语言标准（版本 5.0）** ：[ECMA-334.pdf](https://www.ecma-international.org/wp-content/uploads/ECMA-334_5th_edition_december_2017.pdf)
- **公共语言基础结构**：[ECMA-335.pdf](https://www.ecma-international.org/wp-content/uploads/ECMA-335_6th_edition_june_2012.pdf)。
- **派生自分区 IV XML 文件的信息**：[ECMA-084.pdf](https://www.ecma-international.org/publications/files/ECMA-TR/ECMA%20TR-084.pdf) 格式。

官方 ISO/IEC 文档可从 ISO/IEC [公开标准](https://standards.iso.org/ittf/PubliclyAvailableStandards/)页获取。 以下链接可从该页面直接获得：

- **信息技术 - 编程语言 - C#** ：[ISO/IEC 23270:2018](https://standards.iso.org/ittf/PubliclyAvailableStandards/c075178_ISO_IEC_23270_2018.zip)
- **信息技术 - 公共语言基础结构 (CLI) 分区 I 到 VI**：[ISO/IEC 23271:2012](https://standards.iso.org/ittf/PubliclyAvailableStandards/c058046_ISO_IEC_23271_2012(E).zip)
- **信息技术 - 公共语言基础结构 (CLI) - 有关派生自分区 IV XML 文件的信息的技术报告**：[ISO/IEC TR 23272:2011](https://www.ecma-international.org/wp-content/uploads/ECMA_TR-84_6th_edition_june_2012.pdf)

## <a name="see-also"></a>请参阅

- [.NET 简介](../core/introduction.md)
- [.NET Standard 简介](net-standard.md)
- [为服务器应用选择 .NET 5 或 .NET Framework](choosing-core-framework-server.md)
- [.NET Framework 指南](../framework/index.yml)
- [C# 指南](../csharp/index.yml)
- [F# 指南](../fsharp/index.yml)
- [Visual Basic 指南](../visual-basic/index.yml)

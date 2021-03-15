---
title: 从 .NET Framework 移植到 .NET 5
description: 了解移植过程，并发现可能会对将 .NET Framework 项目移植到 .NET 5（及 .NET Core 3.1）有用的工具。
author: adegeo
ms.date: 03/03/2020
no-loc:
- package.config
- PackageReference
ms.openlocfilehash: 8515689cf4a1be917f12bb8f3315cda89988d773
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605043"
---
# <a name="overview-of-porting-from-net-framework-to-net"></a>有关从 .NET Framework 移植到 .NET 的概述

本文概述了在将代码从 .NET Framework 移植到 .NET（旧称为 .NET Core）时应考虑的事项。 对于许多项目，从 .NET Framework 移植到 .NET 是相对简单的。 项目的复杂性决定了在项目文件的初始迁移之后要做多少工作。

应用模型在 .NET 中可用的项目（如库、控制台应用和桌面应用）通常不需要太大的更改。 需要使用新应用模型的项目（如从 ASP.NET 迁移到 ASP.NET Core）需要的工作要多一点。 旧应用模型中的很多模式都有可以在转换过程中使用的等效项。

## <a name="unavailable-technologies"></a>不可用的技术

.NET Framework 中有一些技术在 .NET 中是不存在：

- [应用程序域](net-framework-tech-unavailable.md#application-domains)

  不支持创建额外应用程序域。 对于代码隔离，将流程或容器用作备用。

- [远程处理](net-framework-tech-unavailable.md#remoting)

  远程处理用于跨不再受支持的应用程序域进行通信。 对于跨进程通信，可将进程间通信 (IPC) 机制视为远程处理的备用方案，如 <xref:System.IO.Pipes> 类或 <xref:System.IO.MemoryMappedFiles.MemoryMappedFile> 类。

- [代码访问安全性 (CAS)](net-framework-tech-unavailable.md#code-access-security-cas)

  CAS 是受 .NET Framework 支持、但在 .NET Framework 4.0 中已停用的沙盒技术。 它已被 Security Transparency 取代，并且在 .NET 中不受支持。 请改用操作系统提供的安全边界，如虚拟化、容器或用户帐户。

- [安全透明度](net-framework-tech-unavailable.md#security-transparency)

  与 CAS 类似，这种沙盒技术不再被推荐用于 .NET Framework 应用程序，而且在 .NET 中也不受支持。 请改用操作系统提供的安全边界，如虚拟化、容器或用户帐户。
  
- <xref:System.EnterpriseServices?displayProperty=fullName>

  .NET 不支持 <xref:System.EnterpriseServices?displayProperty=fullName> (COM+)。

- Windows Workflow Foundation (WF) 和 Windows Communication Foundation (WCF)

  .NET 5 及更高版本（包括 .NET Core）不支持 WF 和 WCF。 有关替代方法，请参阅 [CoreWF](https://github.com/UiPath/corewf) 和 [CoreWCF](https://github.com/CoreWCF/CoreWCF)。

若要详细了解这些不受支持的技术，请参阅 [.NET Framework 技术在 .NET Core 和 .NET 5 及更高版本上不可用](net-framework-tech-unavailable.md)。

## <a name="windows-desktop-technologies"></a>Windows 桌面技术

许多为 .NET Framework 创建的应用程序都使用桌面技术，如 Windows 窗体或 Windows Presentation Foundation (WPF)。 虽然 Windows 窗体和 WPF 均已移植到 .NET 中，但这些仍是仅适用于 Windows 的技术。

在迁移 Windows 窗体或 WPF 应用程序之前，请先考虑以下依赖项：

01. 适用于 .NET 的项目文件使用与 .NET Framework 不同的格式。
01. 你的项目可能会使用在 .NET 中不可用的 API。
01. 第三方控件和库可能还没有移植到 .NET 中，仍只对 .NET Framework 可用。
01. 你的项目使用在 .NET 中[不再可用的技术](net-framework-tech-unavailable.md)。

.NET 使用 Windows 窗体和 WPF 的开放源代码版本，并对 .NET Framework 进行了增强。

有关将桌面应用程序迁移到 .NET 5 的教程，请参阅以下文章之一：

- [将 .NET Framework WPF 应用迁移到 .NET](/dotnet/desktop/wpf/migration/convert-project-from-net-framework?view=netdesktop-5.0&preserve-view=true)
- [将 .NET Framework Windows 窗体应用迁移到 .NET](/dotnet/desktop/winforms/migration/?view=netdesktop-5.0&preserve-view=true)

## <a name="windows-specific-apis"></a>特定于 Windows 的 API

应用程序仍可以在 .NET 支持的平台上对本机库进行平台调用。 这项技术并不仅限于 Windows。 但是，如果你引用的库是特定于 Windows 的（如 user32.dll 或 kernal32.dll），那么代码只能在 Windows 上正常运行。 对于想要在其上运行应用的每个平台，你都必须查找特定于平台的版本，或者让你的代码足够通用以在所有平台上运行。

当将应用程序从 .NET Framework 移植到 .NET 时，应用程序可能使用了随 .NET Framework 一起分发的库。 许多在 .NET Framework 中可用的 API 都没有移植到 .NET 中，因为它们依赖特定于 Windows 的技术，如 Windows Registry 或 GDI+ 绘图模型。

Windows Compatibility Pack 为 .NET 提供了大部分的 .NET Framework API 面，并通过 [Microsoft.Windows.Compatibility NuGet 包](https://www.nuget.org/packages/Microsoft.Windows.Compatibility)提供。

有关详细信息，请参阅[使用 Windows Compatibility Pack 将代码移植到 .NET 中](windows-compat-pack.md)。

## <a name="net-framework-compatibility-mode"></a>.NET Framework 兼容性模式

.NET Framework 兼容性模式是在 .NET Standard 2.0 中引入的。 使用此兼容性模式，.NET Standard 和 .NET 5 及更高版本（以及 .NET Core 3.1）项目可以在仅适用于 Windows 的情况下引用 .NET Framework 库。 引用 .NET Framework 库不适用于所有项目（如库使用 Windows Presentation Foundation (WPF) API 时），但它的开启了很多移植方案。 有关详细信息，请参阅[分析依赖项以将代码从 .NET Framework 移植到 .NET 中](third-party-deps.md#net-framework-compatibility-mode)。

## <a name="cross-platform"></a>跨平台

.NET（旧称为 .NET Core）是为跨平台而设计的。 如果代码不依赖特定于 Windows 的技术，那么它可以在 macOS、Linux 和 Android 等其他平台上运行。 这包括如下项目类型：

- 库
- 基于控制台的工具
- 自动化
- ASP.NET 站点

.NET Framework 是仅适用于 Windows 的组件。 当代码使用特定于 Windows 的技术或 API（如 Windows 窗体和 Windows Presentation Foundation (WPF)）时，代码仍可以在 .NET 上运行，但不能在其他操作系统上运行。

库或基于控制台的应用程序不需要太多更改就可以跨平台使用。 当移植到 .NET 时，可能需要考虑这一点，并在其他平台上测试应用程序。

## <a name="the-future-of-net-standard"></a>.NET Standard 的未来

[.NET Standard](https://github.com/dotnet/standard) 是针对多个 .NET 实现推出的一套正式的 .NET API 规范。 推出 .NET Standard 的背后动机是要提高 .NET 生态系统中的一致性。 自 .NET 5 起，采用了一种不同的方法来建立一致性；使用这种新方法，在很多情况下，都不需要使用 .NET Standard。 有关详细信息，请参阅 [.NET 5 和 .NET Standard](../../standard/net-standard.md#net-5-and-net-standard)。

.NET Standard 2.0 是支持 .NET Framework 的最后一个版本。

## <a name="tools-to-assist-porting"></a>移植辅助工具

可以使用不同的工具来帮助自动执行迁移的某些方面，而不是将应用程序从 .NET Framework 手动移植到 .NET 中。 移植复杂的项目本身就是一个复杂的过程。 这些工具可能在此过程中有所帮助。

即使你使用工具来帮助移植应用程序，也应查阅本文中的[“移植时的注意事项”部分](#considerations-when-porting)。

### <a name="net-upgrade-assistant"></a>.NET 升级助手

[.NET 升级助手](upgrade-assistant-overview.md)是一款可以在不同类型的 .NET Framework 应用上运行的命令行工具。 它旨在帮助将 .NET Framework 应用升级到 .NET 5。 在运行此工具后，大多数情况下，应用将需要更多操作才能完成迁移。 此工具会安装可以帮助完成迁移的分析器。 此工具适用于以下类型的 .NET Framework 应用程序：

- Windows 窗体
- WPF
- ASP.NET MVC
- 控制台
- 类库

此工具使用本文中列出的其他工具，并指导迁移过程。 若要详细了解此工具，请参阅 [.NET 升级助手概述](upgrade-assistant-overview.md)。

### <a name="try-convert"></a>try-convert

try-convert 工具是一款 .NET 全局工具，可用于将项目或整个解决方案转换为 .NET SDK，包括将桌面应用迁移到 .NET 5。 但是，如果你的项目有复杂的生成进程（如自定义任务、目标或导入），则不建议使用此工具。

有关详细信息，请参阅 [try-convert GitHub 存储库](https://github.com/dotnet/try-convert)。

### <a name="net-portability-analyzer"></a>.NET 可移植性分析器

.NET 可移植性分析器是一种工具，可分析程序集并为应用程序或库提供有关缺失的 .NET API 的详细报告，以便在指定的目标 .NET 平台上实现可移植性。

若要使用 Visual Studio 中的 .NET 可移植性分析器，请[从市场中安装此扩展](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)。

有关详细信息，请参阅 [.NET 可移植性分析器](../../standard/analyzers/portability-analyzer.md)。

### <a name="net-api-analyzer"></a>.NET API 分析器

[.NET API 分析器](../../standard/analyzers/api-analyzer.md)分析你是否在使用将会在运行时抛出 <xref:System.PlatformNotSupportedException> 的 API。 尽管这并不常见，但如果从 .NET Framework 4.7.2 或更高版本进行移动，最好进行检查。 若要详细了解会在 .NET 上抛出异常的 API，请参阅[始终在 .NET Core 上抛出异常的 API](../compatibility/unsupported-apis.md)。

API 分析器作为 NuGet 包 [Microsoft.DotNet.Analyzers.Compatibility](https://www.nuget.org/packages/Microsoft.DotNet.Analyzers.Compatibility/) 添加到项目中。

有关详细信息，请参阅 [.NET API 分析器](../../standard/analyzers/api-analyzer.md)。

## <a name="considerations-when-porting"></a>移植时的注意事项

将应用程序移植到 .NET 时，请按顺序考虑以下建议。

✔️ 考虑使用 [.NET 升级助手](upgrade-assistant-overview.md)来迁移项目。 尽管此工具处于预览阶段，但它自动执行本文中详细介绍的大部分手动步骤，并为你继续迁移路径提供了一个很好的起点。

✔️ 考虑先检查依赖项。 依赖项必须定目标到 .NET 5、.NET Standard 或 .NET Core。

✔️ 务必从 NuGet packages.config 文件迁移到项目文件中的 [PackageReference](/nuget/consume-packages/package-references-in-project-files) 设置。 使用 Visual Studio [转换 package.config 文件](/nuget/consume-packages/migrate-packages-config-to-package-reference#migration-steps)。

✔️ 考虑升级到最新的项目文件格式，即使你还不能移植应用，也不例外。 .NET Framework 项目使用过时的项目格式。 尽管最新的项目格式（称为“SDK 样式项目”）是为 .NET Core 及更高版本创建的，它们也适用于 .NET Framework。 拥有最新格式的项目文件可以为将来移植应用打下良好的基础。

✔️ 务必将 .NET Framework 项目重新定目标到 .NET Framework 4.7.2 及更高版本。 在 .NET Standard 不支持现有 API 情况下，这可确保最新备用 API 的可用性。

✔️ 考虑定目标到 .NET 5（而不是 .NET Core 3.1）。 虽然 .NET Core 3.1 是长期支持 (LTS) 版本，但 .NET 5 是最新的，并且 .NET 6 也将在发布后成为 LTS。

✔️ 务必为 Windows 窗体和 WPF 项目定目标到 .NET 5。 .NET 5 包含许多对桌面应用的改进。

✔️ 若要迁移也可以用于 .NET Framework 项目的库，请考虑定目标到 .NET Standard 2.0。 也可以为库设定多个目标，同时定目标到 .NET Framework 和 .NET Standard。

✔️ 如果迁移之后出现缺少 API 的错误，请务必添加对 [Microsoft.Windows.Compatibility NuGet 包](https://www.nuget.org/packages/Microsoft.Windows.Compatibility)的引用。 大部分 .NET Framework API 面是通过 NuGet 包提供给 .NET 的。

## <a name="see-also"></a>另请参阅

- [.NET 升级助手概述](upgrade-assistant-overview.md)
- [ASP.NET 到 ASP.NET Core 迁移](/aspnet/core/migration/proper-to-2x)
- [将 .NET Framework WPF 应用迁移到 .NET](/dotnet/desktop/wpf/migration/convert-project-from-net-framework?view=netdesktop-5.0&preserve-view=true)
- [将 .NET Framework Windows 窗体应用迁移到 .NET](/dotnet/desktop/winforms/migration/?view=netdesktop-5.0&preserve-view=true)
- [将 .NET Framework 库移植到 .NET 中](libraries.md)
- [适用于服务器应用的 .NET 5 与 .NET Framework](../../standard/choosing-core-framework-server.md)

---
title: 迁移到 .NET 5 的示例
description: 演示如何将面向 .NET Framework 的示例应用程序迁移到 .NET 5。
ms.date: 01/19/2021
ms.openlocfilehash: 02a45859dfca891598e235e3de1ed968aefb5bf4
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605160"
---
# <a name="example-of-migrating-to-net"></a>迁移到 .NET 的示例

本章节提供了实用的指导原则，可帮助你将现有应用程序从 .NET Framework 迁移到 .NET。

章节中提供了一个结构良好的过程（你可以遵循此过程），以及每一步需要考虑的最重要事项。

接着，章节中记录了示例桌面应用程序从 WinForms 和 WPF 版本的分步迁移过程。

## <a name="migration-process-overview"></a>迁移过程概述

迁移过程包括四个连续步骤：

1. 准备工作：了解项目所具有的依赖项，对之后的操作有一个概念。 在此步骤中，将当前项目带入一种简化迁移启动点的状态。

2. 迁移项目文件：.NET 项目使用新的 SDK 样式项目格式。 创建一个采用此格式的新项目文件，或更新必须使用 SDK 样式的项目文件。

3. 修复代码并生成：在 .NET 中生成代码，解决 .NET Framework 和 .NET 之间的 API 级差异。 如需要，将第三方包更新为支持 .NET 的包。

4. 运行并测试：可能存在直到运行时才会显示的差异。 因此，请勿忘记运行应用程序并测试所有操作是否按预期方式工作。

### <a name="preparation"></a>准备工作

#### <a name="migrate-packagesconfig-file"></a>迁移 packages.config 文件

在 .NET Framework 应用程序中，所有对外部包的引用都在 packages.config 文件中声明。 在 .NET 中，不再需要使用 packages.config 文件。 而请使用项目文件中的 [PackageReference](../../core/project-sdk/msbuild-props.md#packagereference) 属性来指定应用的 NuGet 包。

因此，你需要从一种格式转换为另一种格式。 可手动进行更新，从而获取 packages.config 文件中包含的依赖项并将其迁移到采用 `PackageReference` 格式的项目文件中。 或者，可让 Visual Studio 为你完成该工作：右键单击 packages.config 文件，然后选择“将 packages.config 迁移到 PackageReference”选项。

#### <a name="verify-every-dependency-compatibility-in-net"></a>验证 .NET 中每个依赖项的兼容性

迁移包引用后，必须检查每个引用的兼容性。 可在 [nuget.org](https://www.nuget.org/) 上浏览应用程序使用的每个 NuGet 包的依赖项。如果包具有 .NET Standard 依赖项，则它将在 .NET 5.0 上运行，因为 .NET [支持](../../standard/net-standard.md#net-implementation-support)所有版本的 .NET Standard。 下图显示了 `Castle.Windsor` 包的依赖项：

![Castle.Windsor 包的 NuGet 依赖项的屏幕截图](./media/example-migration-core/nuget-dependencies.png)

若要检查包的兼容性，可使用 <https://fuget.org> ，该工具提供了有关版本和依赖项的更详细信息。

该项目可能引用了不支持 .NET 的旧版包，但你可能会找到支持它的较新版本。 因此，将包更新到较新版本通常是一个不错的建议。 但是，你应该考虑到，更新包版本可能会引入一些中断性变更，这些变更将强制你更新代码。

如果找不到兼容的版本，会发生什么情况？ 如果你只是因为这些中断性变更而不想更新包的版本，该怎么办？ 请不要担心，因为可以依赖于 .NET 应用程序中的 .NET Framework 程序包。 请勿忘记对其进行大量测试，因为如果外部包调用的 API 在 .NET 中不可用，则可能会导致运行时错误。 在你使用不会更新的旧包时，这非常有用，你只需重新设定目标即可在 .NET 上工作。

#### <a name="check-for-api-compatibility"></a>检查 API 兼容性

由于 .NET Framework 和 .NET 中的 API 图面类似但并不完全相同，因此必须检查 .NET 上可用的 API 和不可用的 API。 可使用 .NET 可移植性分析器工具来显示 .NET 上不存在的已使用 API。 它会查看应用的二进制级别，提取调用的所有 API，然后列出目标框架（本例中，目标框架是 .NET 5.0）上不可用的 API。

有关此工具的详细信息，请参阅：

<https://docs.microsoft.com/dotnet/standard/analyzers/portability-analyzer>

对于此工具来说，有趣的是，它只显示与你自己的代码的差异，而不显示与你不能更改的外部包中代码的差异。 请记住，你应该已经更新了这些包中的大部分包，使它们能够与 .NET 一起工作。

### <a name="migrate-with-try-convert-tool"></a>通过 Try Convert 工具进行迁移

[Try Convert](https://github.com/dotnet/try-convert/releases) 工具非常适合用于迁移项目。 它是一个全局工具，它尝试将项目文件从旧样式升级到新的 SDK 样式，并将适用项目的目标重定为 .NET 5。 安装后，你可运行以下命令：

```dotnetcli
try-convert -p "<path to your project file>"
```

或：

```dotnetcli
try-convert -w "<path to your solution>"
```

> [!NOTE]
> try-convert 工具作为 [.NET 升级助手工具](https://aka.ms/dotnet-upgrade-assistant)的一部分自动运行。 请考虑运行完整的升级助手，而不只是 Try Convert。

在该工具尝试转换后，在 Visual Studio 中重载文件以运行和测试。 由于项目的具体情况，Try Convert 可能无法执行转换。 在这种情况下，可参考以下步骤。

#### <a name="migrate-manually"></a>手动迁移

1. 创建新的 .NET 项目

在大多数情况下，你会希望将现有项目更新为新的 .NET 格式。 不过，你也可以创建一个新项目，同时保留旧项目。 更新旧项目的主要缺点是你会丢失设计器支持，而这对你和你的开发团队来说可能很重要。 如果要继续使用设计器，则必须与旧 .NET 项目并行创建一个新 .NET 项目，并共享资产。 如果需要修改设计器中的 UI 元素，可切换到旧项目来执行该操作。 由于资源已链接，因此这些更改也会在 .NET 项目中更新。

适用于 .NET 的 [SDK 样式项目](../../core/project-sdk/msbuild-props.md)比 .NET Framework 的项目格式简单得多。 除了前面提到的 `PackageReference` 条目外，无需执行更多操作。 新的项目格式[包括具有特定扩展名的文件](../../core/project-sdk/overview.md#default-includes-and-excludes)（如 `.cs` 和 `.xaml` 文件），而无需在项目文件中显式包含这些文件。

#### <a name="assemblyinfo-considerations"></a>AssemblyInfo 注意事项

特性针对 .NET 项目自动生成。 如果项目包含 AssemblyInfo.cs 文件，则定义将重复，这会导致编译冲突。 可删除旧版 AssemblyInfo.cs 文件，或通过将以下条目添加到 .NET 项目文件来禁用自动生成：

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
  </PropertyGroup>
</Project>
```

#### <a name="resources"></a>资源

嵌入的资源会自动包含在内，但资源不会，因此需要将资源迁移到新的项目文件。

#### <a name="package-references"></a>包引用

通过“将 packages.config 迁移到 PackageReference”选项，可轻松地将外部包引用移至前面提到的新格式。

#### <a name="update-package-references"></a>更新包引用

如前一部分中所示，更新发现的兼容包版本。

### <a name="fix-the-code-and-build"></a>修复代码和生成

#### <a name="microsoftwindowscompatibility"></a>Microsoft.Windows.Compatibility

如果应用程序依赖于 .NET 上不可用的 API（如注册表、ACL 或 WCF），则必须包含对 `Microsoft.Windows.Compatibility` 包的引用以添加这些特定于 Windows 的 API。 它们可在 .NET 上工作，但由于它们无法跨平台，因此未包含在内。

有一个名为 API Analyzer (<https://docs.microsoft.com/dotnet/standard/analyzers/api-analyzer>) 的工具，可帮助你识别与你的代码不兼容的 API。

#### <a name="use-if-directives"></a>使用 \#if 指令

如果在以 .NET Framework 和 .NET 为目标时需要不同的执行路径，则应使用编译常量。 向代码中添加一些 \#if 指令，为这两个目标保留相同的代码库。

#### <a name="technologies-not-available-on-net"></a>.NET 上不可用的技术

某些技术在 .NET 上不可用，例如：

* AppDomain
* 远程处理
* 代码访问安全性
* WCF 服务器
* Windows 工作流

因此，如果在应用程序中使用这些技术，则需要寻找这些技术的替代技术。 有关详细信息，请参阅文章[在 .NET Core 和 .NET 5+ 上不可用的 .NET Framework 技术](../../core/porting/net-framework-tech-unavailable.md)。

#### <a name="regenerate-autogenerated-clients"></a>重新生成自动生成的客户端

如果应用程序使用自动生成的代码（例如 WCF 客户端），则可能需要重新生成此代码，从而以 .NET 为目标。 有时，你可以找到某些缺少的引用，因为它们可能不包含在一组默认的 .NET 程序集中。 使用 <https://apisof.net/> 等工具，可轻松地找到缺少的引用所在的程序集，并从 NuGet 添加该引用。

#### <a name="rolling-back-package-versions"></a>回滚包版本

一般情况下，我们之前已提到过，你最好更新每个单独的包版本，使其与 .NET 兼容。 然而，你会发现以一个程序集的更新版本和兼容版本为目标是不值得的。 如果更改的成本不可接受，可考虑回滚包版本，保留你在 .NET Framework 上使用的版本。 尽管它们可能不以 .NET 为目标，但它们应该能够正常工作，除非它们调用了某些不受支持的 API。

### <a name="run-and-test"></a>运行和测试

若应用程序生成无错误，便可通过测试每个功能开始迁移的最后一步。

在最后一步中，你会发现一些不同的问题，具体取决于应用程序的复杂性以及使用的依赖项和 API。

例如，如果你使用配置文件 (app.config)，则可能会在运行时发现一些错误，如“配置节”不存在。 使用 `Microsoft.Extensions.Configuration` NuGet 包应可修复该错误。

出现错误的另一个原因是使用了 `BeginInvoke` 和 `EndInvoke` 方法，因为它们在 .NET 上不受支持。 .NET 不支持它们，因为它们依赖于远程处理，而远程处理 .NET 上并不存在。 若要解决此问题，请尝试使用 `await` 关键字（如可用）或 <xref:System.Threading.Tasks.Task.Run%2A?displayProperty=nameWithType> 方法。

可使用兼容性分析器，识别代码中可能会在 .NET 中运行时引发问题的 API 和代码模式。 转到 <https://github.com/dotnet/platform-compat>，对你的项目使用 .NET API 分析器。

## <a name="migrating-a-windows-forms-application"></a>迁移 Windows 窗体应用程序

为了展示 Windows 窗体应用程序的完整迁移过程，我们选择迁移 <https://github.com/dotnet-architecture/eShopModernizing/tree/master/eShopLegacyNTier/src/eShopWinForms> 处提供的 eShop 示例应用程序。 可在 <https://github.com/dotnet-architecture/eShopModernizing/tree/master/eShopModernizedNTier/src/eShopWinForms> 处找到迁移的完整结果。

此应用程序显示一个产品目录，并允许用户导航、筛选和搜索产品。 从体系结构的角度来看，该应用依赖于充当后端数据库外观的外部 WCF 服务。

下图中显示了主应用程序窗口：

![主应用程序窗口](./media/example-migration-core/main-application-window.png)

如果你打开 .csproj 项目文件，则可看到如下所示的内容：

![csproj 文件内容的屏幕截图](./media/example-migration-core/csproj-file.png)

如前所述，.NET 项目的样式更精简，你需要将项目结构迁移到新的 .NET SDK 样式。

在解决方案资源管理器中，右键单击该 Windows 窗体项目，然后选择“卸载项目” > “编辑” 。

现在，可更新该 .csproj 文件。 你将删除整个内容，并将其替换为以下代码：

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net5.0-windows</TargetFramework>
    <UseWindowsForms>true</UseWindowsForms>
    <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
  </PropertyGroup>
</Project>
```

保存并重载该项目。 你现在已经完成了项目文件的更新，并且该项目面向 .NET。

如果此时编译项目，你会发现一些与 WCF 客户端引用相关的错误。 由于此代码是自动生成的，因此必须重新生成它，使其以 .NET 为目标。

![Visual Studio 中显示错误的错误列表](./media/example-migration-core/winforms-compilation-errors.png)

删除 Reference.cs 文件并生成新的服务客户端。

右键单击“连接的服务”，然后选择“添加连接的服务”选项 。

![已选中“添加连接的服务”选项的“连接的服务”菜单屏幕截图](./media/example-migration-core/add-connected-service.png)

“连接的服务”窗口随即打开。 选择“Microsoft WCF Web 服务”选项。

![“连接的服务”窗口的屏幕截图](./media/example-migration-core/connected-services-window.png)

如果你在与本示例相同的解决方案中有 WCF 服务，则可选择“发现”选项，而不是指定服务 URL。

![“配置 WCF Web Service Reference”窗口的屏幕截图](./media/example-migration-core/configure-wcf-reference.png)

找到该服务后，该工具将反映该服务实现的 API 协定。 将命名空间的名称更改为 `eShopServiceReference`，如下图所示：

![API 协定和更改后的命名空间的屏幕截图](./media/example-migration-core/api-contract-namespace.png)

选择“完成”按钮。 一段时间后，你将看到生成的代码。

应会显示三个自动生成的文件：

1. Getting Started：一个指向 GitHub 的链接，提供某些有关 WCF 的信息。
2. ConnectedService.json：用于连接到服务的配置参数。
3. Reference.cs：实际 WCF 客户端代码。

![包含三个自动生成的文件的“解决方案资源管理器”窗口的屏幕截图](./media/example-migration-core/autogenerated-files.png)

如果再次编译，你会看到许多来自 Helper 文件夹内的 .cs 文件的错误 。 此文件夹存在于 .NET Framework 版本中，但不包含在旧 .csproj 中。 但对于新的 SDK 样式项目，默认包括项目文件位置下存在的每个代码文件。 也就是说，新的 .NET Core 项目会尝试编译 Helper 文件夹内的文件。 由于不需要该文件夹，因此你可安全地将其删除。

如果再次编译该项目并执行它，你将看不到产品图像。 问题在于，文件的路径现在略有变化。 若要解决此问题，需要在路径中添加添加另一个深度级别，更新文件 `CatalogView.cs` 中的以下行：

```csharp
string image_name = Environment.CurrentDirectory + "\\..\\..\\Assets\\Images\\Catalog\\" + catalogItems.Picturefilename;
```

to

```csharp
string image_name = Environment.CurrentDirectory + "\\..\\..\\..\\Assets\\Images\\Catalog\\" + catalogItems.Picturefilename;
```

在此更改后，可检查该应用程序是否在 .NET Core 上按预期启动并运行。

## <a name="migrating-a-wpf-application"></a>迁移 WPF 应用程序

使用 `Shop.ClassicWPF` 示例应用程序来执行迁移。 下图显示了迁移之前的应用的屏幕截图：

![迁移之前的示例应用](./media/example-migration-core/app-before-migration.png)

此应用程序使用本地 SQL Server Express 数据库来保存产品目录信息。 可直接从 WPF 应用程序访问此数据库。

首先，必须将 .csproj 文件更新为 .NET Core 应用程序使用的新 SDK 样式。 按照 Windows 窗体迁移中所述的相同步骤进行操作：卸载项目、打开 .csproj 文件、更新其内容并重载该项目。

在本例中，请删除 .csproj 文件的所有内容，并将其替换为以下代码：

```xml
 <Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net5.0-windows</TargetFramework>
    <UseWpf>true</UseWpf>
    <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
  </PropertyGroup>
</Project>
```

如果重载并编译该项目，你会收到以下错误：

![Visual Studio 中显示单个错误 CS0234 的错误列表](./media/example-migration-core/wpf-compilation-error.png)

由于已删除所有 .csproj 内容，因此你丢失了旧项目中的项目引用规范。 只需将此行添加到 .csproj 文件，即可再次包含该项目引用：

```xml
<ItemGroup>
    <ProjectReference Include="..\\eShop.SqlProvider\\eShop.SqlProvider.csproj" />
<ItemGroup>
```

你也可通过右键单击“依赖项”节点并选择“添加项目引用”，让 Visual Studio 为你提供帮助 。 从解决方案中选择该项目，然后单击“确定”：

![已选中 eShop.SqlProvider 项目的“引用管理器”对话框的屏幕截图](./media/example-migration-core/reference-manager.png)

添加缺少的项目引用后，应用程序将在 .NET 上按预期方式编译和运行。

>[!div class="step-by-step"]
>[上一页](windows-migration.md)
>[下一页](deploy-modern-applications.md)

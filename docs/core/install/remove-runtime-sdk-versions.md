---
title: 删除 .NET 运行时和 SDK
description: 本文介绍如何确定当前所安装 .NET 运行时和 SDK 的版本，以及如何在 Windows、Mac 和 Linux 上删除它们。
author: adegeo
ms.author: adegeo
ms.date: 03/02/2021
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 8ef6ab531d6c3eada5226b1682f19bfe5537bfe4
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102255628"
---
# <a name="how-to-remove-the-net-runtime-and-sdk"></a>如何删除 .NET 运行时和 SDK

经过一段时间后，在安装 .NET 运行时和 SDK 的更新版本时，你可能需要从计算机中删除过时的 .NET 版本。 如 [.NET 版本选择](../versions/selection.md)一文中详述，删除旧版运行时可能会更改为运行共享框架应用程序所选择的运行时。

## <a name="should-i-remove-a-version"></a>是否应删除某个版本？

借助 [.NET 版本选择](../versions/selection.md)行为和 .NET 各个更新之间的运行时兼容性，可安全地删除以前的版本。 .NET 运行时更新在主版本“区段”（如 1.x 和 2.x）中兼容。 此外，较新版本的 .NET SDK 通常能够兼容地生成面向运行时早期版本的应用程序。

通常，只需要应用程序所需的最新 SDK 和运行时的最新补丁版本。 需要保留旧版 SDK 或运行时版本的实例包括维护基于 project.json 的应用程序  。 除非应用程序有需保留早期 SDK 或运行时的特定原因，否则可以安全地删除旧版本。

## <a name="determine-what-is-installed"></a>确定安装内容

.NET CLI 提供了相关的选项，你可以使用它们来列出计算机上安装的 SDK 和运行时。  使用 [`dotnet --list-sdks`](../tools/dotnet.md#options) 查看计算机上安装的 SDK 列表。 使用 [`dotnet --list-runtimes`](../tools/dotnet.md#options) 查看计算机上安装的运行时列表。 有关详细信息，请参阅[如何检查是否已安装 .NET](how-to-detect-installed-versions.md)。

## <a name="uninstall-net"></a>卸载 .NET

::: zone pivot="os-windows"

.NET 使用 Windows“应用和功能”对话框来删除各版 .NET 运行时和 SDK。 下图显示了“应用和功能”对话框  。 你可以搜索 core sdk 或 .net sdk 来筛选和显示安装的 .NET 版本 。

![使用“添加/删除程序”来删除 .NET](./media/remove-runtime-sdk-versions/programs-and-features.png)

选择要从计算机中删除的任何版本，然后单击“卸载”  。

::: zone-end

::: zone pivot="os-linux"

Linux 还提供其他选项来卸载 .NET（SDK 或运行时）。 卸载 .NET 的最佳方法是镜像用来安装 .NET 的操作。 具体取决于所选择的分发和安装方法。

> [!IMPORTANT]
> 有关 Red Hat 安装，请参阅 [Red Hat Product Documentation for .NET](https://access.redhat.com/documentation/en-us/net/5.0/)（Red Hat .NET 产品文档）。

除非从预览版本进行升级，否则使用包管理器升级时无需卸载 .NET SDK。 包管理器 `update` 或 `refresh` 命令将在成功安装较新版本后自动删除旧版本。 如果已安装预览版本，请卸载该版本。

如果使用包管理器安装 .NET，则使用同一包管理器来卸载 .NET SDK 或运行时。 .NET 安装支持常用的包管理器。 有关环境中的精确语法，请查阅分发的包管理器文档：

- [apt-get(8)](https://linux.die.net/man/8/apt-get) 由基于 Debian 的系统（包括 Ubuntu）使用。
- [yum(8)](https://linux.die.net/man/8/yum) 用于 Fedora、CentOS 和 Oracle Linux。
- [zypper(8)](https://en.opensuse.org/SDB:Zypper_manual_(plain)) 用于 openSUSE 和 SUSE Linux Enterprise System (SLES)。
- [dnf(8)](https://dnf.readthedocs.io/en/latest/command_ref.html) 用于 Fedora。

几乎在所有情况下，删除包的命令都是 `remove`。

大多数包管理器的 .NET SDK 安装包名称为 `dotnet-sdk`，后跟版本号。 从 2.1.300 版 .NET SDK 和 `2.1` 版运行时开始，只需要主版本号和次版本号：例如，可将 .NET SDK 2.1.300 版引用为包 `dotnet-sdk-2.1`。 以前的版本则需要整个版本字符串：例如，2.1.200 版 .NET SDK 需要 `dotnet-sdk-2.1.200`。

对于仅安装了运行时而未安装 SDK 的计算机，.NET 运行时的包名称为 `dotnet-runtime-<version>`，整个运行时堆栈的包名称为 `aspnetcore-runtime-<version>`。

> [!TIP]
> 使用包管理器卸载 SDK 时，2.0 之前的 .NET Core 安装不会卸载主机应用程序。 使用 `apt-get`，该命令为：
>
> ```bash
> apt-get remove dotnet-host
> ```
>
> 没有版本附加到 `dotnet-host`。

如果使用 tarball 安装，则必须手动删除 .NET。

在 Linux 上，必须通过删除进行版本控制的目录，分别删除 SDK 和运行时。 这些目录可能因你的 Linux 分发版而异。 删除它们会从磁盘中删除 SDK 和运行时。 例如，要删除 1.0.1 SDK 和运行时，可使用以下 bash 命令：

```bash
version="1.0.1"
sudo rm -rf /usr/share/dotnet/sdk/$version
sudo rm -rf /usr/share/dotnet/shared/Microsoft.NETCore.App/$version
sudo rm -rf /usr/share/dotnet/shared/Microsoft.AspNetCore.All/$version
sudo rm -rf /usr/share/dotnet/shared/Microsoft.AspNetCore.App/$version
sudo rm -rf /usr/share/dotnet/host/fxr/$version
```

SDK 和运行时的父目录列在 `dotnet --list-sdks` 和 `dotnet --list-runtimes` 命令的输出中，如上表所示。

::: zone-end

::: zone pivot="os-macos"

在 Mac 上，必须通过删除进行版本控制的目录，分别删除 SDK 和运行时。 删除它们会从磁盘中删除 SDK 和运行时。 例如，要删除 1.0.1 SDK 和运行时，可使用以下 bash 命令：

```bash
version="1.0.1"
sudo rm -rf /usr/local/share/dotnet/sdk/$version
sudo rm -rf /usr/local/share/dotnet/shared/Microsoft.NETCore.App/$version
sudo rm -rf /usr/local/share/dotnet/shared/Microsoft.AspNetCore.All/$version
sudo rm -rf /usr/local/share/dotnet/shared/Microsoft.AspNetCore.App/$version
sudo rm -rf /usr/local/share/dotnet/host/fxr/$version
```

SDK 和运行时的父目录列在 `dotnet --list-sdks` 和 `dotnet --list-runtimes` 命令的输出中，如上表所示。

::: zone-end

## <a name="net-uninstall-tool"></a>.NET 卸载工具

你可以使用 [.NET 卸载工具](../additional-tools/uninstall-tool.md) (`dotnet-core-uninstall`) 从系统中删除 .NET SDK 和运行时。 可使用选项集合来指定应卸载的版本。

::: zone pivot="os-windows"

## <a name="visual-studio-dependency-on-net-core-sdk-versions"></a>.NET Core SDK 版本的 Visual Studio 依赖项

在 Visual Studio 2019 版本 16.3 之前，Visual Studio 安装程序称为独立的 .NET Core SDK 安装程序。 因此，SDK 版本显示在 Windows“应用和功能”对话框中  。 使用独立安装程序删除 Visual Studio 安装的 .NET Core SDK 可能会破坏 Visual Studio。 如果 Visual Studio 在卸载 SDK 之后出现问题，请在该特定版本的 Visual Studio 上运行修复。 下表显示了 .NET Core SDK 版本的一些 Visual Studio 依赖项：

| Visual Studio 版本           | .NET Core SDK 版本          |
|---------------------------------|--------------------------------|
| Visual Studio 2019 版本 16.2 | .NET Core SDK 2.2.4xx、2.1.8xx |
| Visual Studio 2019 版本 16.1 | .NET Core SDK 2.2.3xx、2.1.7xx |
| Visual Studio 2019 版本 16.0 | .NET Core SDK 2.2.2xx、2.1.6xx |
| Visual Studio 2017 版本 15.9 | .NET Core SDK 2.2.1xx、2.1.5xx |
| Visual Studio 2017 版本 15.8 | .NET Core SDK 2.1.4xx          |

从 Visual Studio 2019 16.3 版开始，Visual Studio 负责其自己的 .NET SDK 副本。 因此，“应用和功能”对话框中将不再显示这些 SDK 版本  。

::: zone-end

## <a name="remove-the-nuget-fallback-folder"></a>删除 NuGet 回退文件夹

在 .NET Core 3.0 SDK 之前，.NET Core SDK 安装程序使用名为 NuGetFallbackFolder 的文件夹存储 NuGet 包的缓存  。 此缓存在操作期间（如 `dotnet restore` 或 `dotnet build /t:Restore`）使用。 NuGetFallbackFolder 在 Windows 上位于 C:\Program Files\dotnet\sdk，在 macOS 上位于 /usr/local/share/dotnet/sdk    。

如果是以下情况，则可能需要删除此文件夹：

- 仅使用 .NET Core 3.0 SDK 或 .NET 5.0（或更高版本）进行开发。
- 你使用早于 3.0 的 .NET Core SDK 版本进行开发，但可以联机工作。

如果要删除 NuGet 回退文件夹，可以将其删除，但需要管理员权限才能执行此操作。

建议不要删除 dotnet  文件夹。 这样做会删除以前安装的所有全局工具。 此外，在 Windows 上：

- 你将中断 Visual Studio 2019 版本 16.3 及更高版本。 可以运行“修复”  来恢复。
- 如果“应用和功能”对话框中存在 .NET Core SDK 条目，它们将是孤立的  。

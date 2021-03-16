---
title: NETSDK1045：当前的 .NET SDK 不支持将“更新的版本”作为目标。
description: 了解 .NET SDK 错误 NETSDK1045，这是在生成工具找不到请求的 .NET SDK 版本时发生的错误。
ms.topic: error-reference
ms.date: 02/12/2021
f1_keywords:
- NETSDK1045
ms.openlocfilehash: 7f21270fdc7c2db862a49302a302bf8121fc86a5
ms.sourcegitcommit: 42d436ebc2a7ee02fc1848c7742bc7d80e13fc2f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102104097"
---
# <a name="netsdk1045-the-current-net-sdk-does-not-support-newer-version-as-a-target"></a>NETSDK1045：当前的 .NET SDK 不支持将“更新的版本”作为目标。

本文适用于：✔️ .NET Core 2.1.100 SDK 及更高版本

当生成工具找不到生成项目所需的 .NET SDK 版本时，会发生此错误。 这通常是由于 .NET Core SDK 安装或配置问题导致的。 完整的错误消息类似于以下示例：

> NETSDK1045：当前的 .NET SDK 不支持将“更新的版本”作为目标。 将“更旧的版本”或更低版本作为目标，或者使用支持“更新的版本”的 .NET SDK 版本。

以下部分介绍此错误的一些可能原因。 查看每个原因并查看哪一项适用于你。 请记住，在对环境或配置文件进行更改时，可能需要重新启动命令窗口、重新启动 Visual Studio 或重新启动计算机，才能使所做的更改生效。

## <a name="net-sdk-version"></a>.NET SDK 版本

打开项目文件（.csproj、.vbproj 或 .fsproj），并检查目标框架。 这是应用尝试使用的框架版本。

```xml
<TargetFramework>netcoreapp3.0</TargetFramework>
```

确保计算机上已安装列出的 .NET 版本。 可以使用以下命令（打开开发人员命令提示并运行此命令）来列出已安装的版本：

```dotnetcli
dotnet --list-sdks
```

### <a name="x86-or-x64-architecture"></a>x86 或 x64 体系结构

.NET SDK 的每个版本均可用于 x86 和 x64 体系结构。 项目可能会尝试查找适用于错误体系结构的 .NET SDK，或者适用于项目所需体系结构的 .NET SDK 可能未安装。 检查所需体系结构的安装文件夹。 例如，在 Windows 上，x86 版本的 .NET SDK 安装在 C:\Program Files (x86)\dotnet 中，而 x64 版本安装在 C:\Program Files\dotnet 中。 请参阅[如何检查是否已安装 .NET](../../install/how-to-detect-installed-versions.md) 并选择操作系统，查明如何检测计算机上安装的内容。

如果未安装所需的版本，请在 [.NET 下载](https://dotnet.microsoft.com/download/dotnet)页面找到你需要的版本。

## <a name="preview-not-enabled"></a>未启用预览版

如果已安装所请求的 .NET SDK 版本的预览版，还需要设置用于在 Visual Studio 中启用预览版的选项。 请转到“工具” > “选项” > “环境” > “预览功能”并确保选中了“使用 .NET Core SDK 的预览版”。

## <a name="visual-studio-version"></a>Visual Studio 版本

例如，.NET Core 3.0 和更高版本需要 Visual Studio 2019。 升级到 [Visual Studio 2019 版本 16.3](https://visualstudio.microsoft.com/downloads) 或更高版本以生成项目。

## <a name="path-environment-variable"></a>PATH 环境变量

生成工具使用 PATH 环境变量查找 .NET 生成工具的正确版本。 如果 PATH 环境变量包含指向较旧生成工具的直接路径，则可能出现此错误消息。 请确保 PATH 环境变量中 .NET 工具的唯一路径为指向顶级 dotnet 文件夹的路径，例如 C:\Program Files\dotnet。 错误 PATH 的示例如下所示：C:\Program Files\dotnet\2.1.0\sdks。

## <a name="msbuildsdkpath-environment-variable"></a>MSBuildSDKPath 环境变量

检查 MSBuildSDKPath 环境变量。 这一可选的环境变量由 MSBuild 识别，如果已设置，则会覆盖默认值。 它可能会设置为 .NET SDK 的特定版本。 如果已设置它，请尝试删除它并重新生成项目。

## <a name="globaljson-file"></a>global.json 文件

在项目的根文件夹中查找并沿着目录链向上一直到该卷的根目录进行查找，看看是否有 global.json 文件，因为它可存在于此文件夹结构中的任何位置。 如果它包含 SDK 版本，请删除 `sdk` 节点及其所有子节点，或将其更新为所需的较新 .NET Core 版本。

```json
{
  "sdk": {
    "version": "2.1.0"
  }
}
```

global.json 文件不是必需的，因此，如果除 `sdk` 节点以外，该文件未包含任何内容，则可以删除整个文件。

## <a name="directorybuildprops-file"></a>Directory.build.props 文件

Directory.build.props 文件是可选的 MSBuild 文件，它可以设置全局属性。 在项目的根文件夹中查找并沿着目录链向上一直到该卷的根目录进行查找，看看是否有这些文件，因为它们可存在于此文件夹结构中的任何位置。 查找 `TargetFramework` 元素或 `MSBuildSDKPath` 的设置，这些可能会覆盖所需的设置。

## <a name="see-also"></a>请参阅

- [The Current .NET SDK does not support targeting .NET Core 3.0 – Fix](https://www.ryadel.com/current-net-sdk-not-support-net-core-3-0-fix/)（当前的 .NET SDK 不支持将 .NET Core 3.0 作为目标 – 解决）

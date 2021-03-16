---
title: 适用于 VS Code 扩展创建者的 .NET 安装工具
description: 对适用于扩展创建者的 .NET 安装工具的概述，该工具是用于安装 .NET 运行时的 Visual Studio Code 扩展。
author: sfoslund
ms.date: 11/18/2020
ms.openlocfilehash: 4be931a254e4ce969f9eaf2efde7939cb54e1d5f
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605147"
---
# <a name="net-install-tool-for-extension-authors"></a>适用于扩展创建者的 .NET 安装工具

[适用于扩展创建者的 .NET 安装工具](https://github.com/dotnet/vscode-dotnet-runtime)是一种专门供 VS Code 扩展创建者获取 .NET 运行时的 Visual Studio Code 扩展。 此工具专门供采用 .NET 编写并且需要 .NET 启动其各个部分的扩展（例如语言服务器）使用。 此扩展并非直接供用户用来安装用于开发的 .NET。

## <a name="getting-started-extension-authors"></a>入门指南：扩展创建者

为确保适用于扩展创建者的 .NET 安装工具适合你的方案，请先从 GitHub 页查看[此扩展的目标](https://github.com/dotnet/vscode-dotnet-runtime#goals-acquiring-net-core-for-extensions)。

> [!NOTE]
> 此工具只可用于安装 .NET 运行时，当前无法安装 .NET SDK。

验证适用于扩展创建者的 .NET 安装工具符合你的需求后，即可在[扩展清单](https://code.visualstudio.com/api/references/extension-manifest)中利用对它的依赖关系，并开始通过 [VS Code API](https://code.visualstudio.com/api/extension-guides/command#programmatically-executing-a-command) 使用我们所公开的命令。 你可找到此扩展在 [GitHub](https://github.com/dotnet/vscode-dotnet-runtime/blob/master/Documentation/commands.md) 上公开的命令列表。

请查看此[扩展示例](https://github.com/dotnet/vscode-dotnet-runtime/tree/master/sample)，了解相关操作步骤。

若要获取更多示例，请查看当前利用了此工具的以下开源扩展：

- [适用于 Visual Studio Code 的 Azure 资源管理器 (ARM) 工具](https://github.com/microsoft/vscode-azurearmtools)

- [.NET 交互式笔记本](https://github.com/dotnet/interactive/tree/main/src/dotnet-interactive-vscode)

## <a name="getting-started-end-users"></a>入门指南：最终用户

最终用户通常完全不需要与适用于扩展创建者的 .NET 安装工具交互。 如果你在使用此扩展时遇到问题，请查看[“故障排除”页](https://github.com/dotnet/vscode-dotnet-runtime/blob/master/Documentation/troubleshooting-runtime.md)或通过 [GitHub](https://github.com/dotnet/vscode-dotnet-runtime/issues) 提交问题。

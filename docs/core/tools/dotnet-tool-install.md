---
title: dotnet tool install 命令
description: Dotnet tool install 命令在计算机上安装指定的 .NET 工具。
ms.date: 02/14/2020
ms.openlocfilehash: b04e7fd24edce5d5da67cdd83fbea797118689b4
ms.sourcegitcommit: bdbf6472de867a0a11aaa5b9384a2506c24f27d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102206476"
---
# <a name="dotnet-tool-install"></a>dotnet tool install

本文适用于： ✔️ .NET Core 2.1 SDK 及更高版本

## <a name="name"></a>“属性”

`dotnet tool install` - 在计算机上安装指定的 [.NET 工具](global-tools.md)。

## <a name="synopsis"></a>摘要

```dotnetcli
dotnet tool install <PACKAGE_NAME> -g|--global
    [--add-source <SOURCE>] [--configfile <FILE>]
    [--framework <FRAMEWORK>] [-v|--verbosity <LEVEL>]
    [--version <VERSION_NUMBER>]

dotnet tool install <PACKAGE_NAME> --tool-path <PATH>
    [--add-source <SOURCE>] [--configfile <FILE>]
    [--framework <FRAMEWORK>] [-v|--verbosity <LEVEL>]
    [--version <VERSION_NUMBER>]

dotnet tool install <PACKAGE_NAME>
    [--add-source <SOURCE>] [--configfile <FILE>]
    [--framework <FRAMEWORK>] [-v|--verbosity <LEVEL>]
    [--version <VERSION_NUMBER>]

dotnet tool install -h|--help
```

## <a name="description"></a>描述

`dotnet tool install` 命令提供一种在计算机上安装 .NET 工具的方法。 若要使用命令，请指定以下安装选项之一：

* 若要在默认位置中安装全局工具，请使用 `--global` 选项。
* 若要在自定义位置中安装全局工具，请使用 `--tool-path` 选项。
* 若要安装本地工具，请省略 `--global` 和 `--tool-path` 选项。

**本地工具从 .NET Core SDK 3.0 开始可用。**

指定 `-g` 或 `--global` 选项时，全局工具默认安装在以下目录中：

| (OS)          | 路径                          |
|-------------|-------------------------------|
| Linux/macOS | `$HOME/.dotnet/tools`         |
| Windows     | `%USERPROFILE%\.dotnet\tools` |

本地工具将添加到当前目录下 .config 目录中的 dotnet-tools.json 文件中。 如果清单文件尚不存在，请通过运行以下命令来创建它：

```dotnetcli
dotnet new tool-manifest
```

有关详细信息，请参阅[安装本地工具](global-tools.md#install-a-local-tool)。

## <a name="arguments"></a>自变量

- **`PACKAGE_NAME`**

  包含要安装的 .NET 工具的 NuGet 包的名称/ID。

## <a name="options"></a>选项

- **`--add-source <SOURCE>`**

  添加安装过程中要使用的其他 NuGet 包源。 系统会并行访问这些源，而不是按某种优先级顺序依次访问。 如果同一个包和版本在多个源中，则选取速度最快的源。 有关详细信息，请查看[安装 NuGet 包时会发生什么情况？](/nuget/concepts/package-installation-process)。

- **`--configfile <FILE>`**

  要使用的 NuGet 配置 (nuget.config) 文件。

- **`--framework <FRAMEWORK>`**

  指定要安装工具的[目标框架](../../standard/frameworks.md)。 默认情况下，.NET SDK 尝试选择最合适的目标框架。

- **`-g|--global`**

  指定安装是用户范围的。 不能与 `--tool-path` 选项一起使用。 省略 `--global` 和 `--tool-path` 指定本地工具安装。

- **`-h|--help`**

  打印出有关命令的简短帮助。

- **`--tool-path <PATH>`**

  指定全局工具的安装位置。 路径可以是绝对的，也可以是相对的。 如果路径不存在，命令会尝试创建它。 省略 `--global` 和 `--tool-path` 指定本地工具安装。

- **`-v|--verbosity <LEVEL>`**

  设置命令的详细级别。 允许使用的值为 `q[uiet]`、`m[inimal]`、`n[ormal]`、`d[etailed]` 和 `diag[nostic]`。

- **`--version <VERSION_NUMBER>`**

  要安装的工具版本。 默认情况下，安装最新的稳定包版本。 使用此选项安装工具的预览版或较旧版本。

## <a name="examples"></a>示例

- **`dotnet tool install -g dotnetsay`**

  在默认位置中安装 [dotnetsay](https://www.nuget.org/packages/dotnetsay/) 全局工具。

- **`dotnet tool install dotnetsay --tool-path c:\global-tools`**

  在特定 Windows 目录中安装 [dotnetsay](https://www.nuget.org/packages/dotnetsay/) 全局工具。

- **`dotnet tool install dotnetsay --tool-path ~/bin`**

  在特定 Linux/macOS 目录中安装 [dotnetsay](https://www.nuget.org/packages/dotnetsay/) 全局工具。

- **`dotnet tool install -g dotnetsay --version 2.0.0`**

  安装 2.0.0 版的 [dotnetsay](https://www.nuget.org/packages/dotnetsay/) 全局工具。

- **`dotnet tool install dotnetsay`**

  在当前目录中安装 [dotnetsay](https://www.nuget.org/packages/dotnetsay/) 本地工具。

## <a name="see-also"></a>请参阅

- [.NET 工具](global-tools.md)
- [教程：使用 .NET CLI 安装和使用 .NET 全局工具](global-tools-how-to-use.md)
- [教程：使用 .NET CLI 安装和使用 .NET 本地工具](local-tools-how-to-use.md)

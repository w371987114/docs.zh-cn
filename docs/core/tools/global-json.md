---
title: global.json 概述
description: 了解如何在运行 .NET CLI 命令时使用 global.json 文件设置 .NET SDK 版本。
ms.topic: how-to
ms.date: 05/01/2020
ms.custom: updateeachrelease
ms.openlocfilehash: cc471cf5b50cf91c38b46607ccf38bd4d087aa6a
ms.sourcegitcommit: 42d436ebc2a7ee02fc1848c7742bc7d80e13fc2f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102104136"
---
# <a name="globaljson-overview"></a>global.json 概述

本文适用于： ✔️ .NET Core 2.0 SDK 及更高版本

通过 global.json 文件，可定义在运行 .NET CLI 命令时使用的 .NET SDK 版本。 选择 .NET SDK 与指定项目所面向的运行时无关。 .NET SDK 版本指示使用哪个版本的 .NET CLI。

通常会使用最新版 SDK 工具，因此不需要 global.json 文件。 在某些高级方案中，你可能需要控制 SDK 工具的版本，本文对如何完成此操作进行了介绍。

有关指定运行时的详细信息，请参阅[目标框架](../../standard/frameworks.md)。

.NET SDK 在当前工作目录（不必与项目目录相同）或其某个父目录中查找 global.json 文件。

## <a name="globaljson-schema"></a>global.json 架构

### <a name="sdk"></a>SDK

类型：`object`

指定要选择的 .NET SDK 的相关信息。

#### <a name="version"></a>version

- 类型：`string`

- 自 .NET Core 1.0 SDK 起可用。

要使用的 .NET SDK 版本。

此字段：

- 不支持通配符，也就是说，必须指定完整版本号。
- 不支持版本范围。

#### <a name="allowprerelease"></a>allowPrerelease

- 类型：`boolean`

- 自 .NET Core 3.0 SDK 起可用。

指示在选择要使用的 SDK 版本时，SDK 解析程序是否应考虑预发布版本。

如果未显式设置此值，则默认值将取决于是否从 Visual Studio 运行：

- 如果未使用 Visual Studio，则默认值为 `true`。
- 如果使用 Visual Studio，它将使用请求的预发布状态。 也就是说，如果使用 Visual Studio 的预览版本，或者设置了“使用 .NET Core SDK 的预览版”选项（在“工具” > “选项” > “环境” > “预览功能”下方），则默认值为 `true`，否则为 `false`    。

#### <a name="rollforward"></a>rollForward

- 类型：`string`

- 自 .NET Core 3.0 SDK 起可用。

选择 SDK 版本时要使用的前滚策略，可作为特定 SDK 版本缺失时的回退，或者作为使用更高版本的指令。 必须使用 `rollForward` 值指定[版本](#version)，除非将其设置为 `latestMajor`。
默认的前滚行为由[匹配规则](#matching-rules)确定。

要了解可用策略及其行为，请考虑以下格式为 `x.y.znn` 的 SDK 版本定义：

- `x` 是主版本。
- `y` 是次版本。
- `z` 是功能区段。
- `nn` 是修补程序版本。

下表列出了 `rollForward` 键的可能值：

| “值”         | 行为 |
| ------------- | ---------- |
| `patch`       | 使用指定的版本。 <br> 如果找不到，则前滚到最新的修补程序级别。 <br> 如果找不到，则失败。 <br><br> 此值是早期 SDK 版本中的旧行为。 |
| `feature`     | 对指定的主版本、次版本和功能区段使用最新的修补程序级别。 <br> 如果找不到，则前滚到同一主/次版本中的下一个较高功能区段，并使用该功能区段的最新修补程序级别。 <br> 如果找不到，则失败。 |
| `minor`       | 对指定的主版本、次版本和功能区段使用最新的修补程序级别。 <br> 如果找不到，则前滚到同一主/次版本中的下一个较高功能区段，并使用该功能区段的最新修补程序级别。 <br> 如果找不到，则前滚到同一主版本中的下一个较高次版本和功能区段，并使用该功能区段的最新修补程序级别。 <br> 如果找不到，则失败。 |
| `major`       | 对指定的主版本、次版本和功能区段使用最新的修补程序级别。 <br> 如果找不到，则前滚到同一主/次版本中的下一个较高功能区段，并使用该功能区段的最新修补程序级别。 <br> 如果找不到，则前滚到同一主版本中的下一个较高次版本和功能区段，并使用该功能区段的最新修补程序级别。 <br> 如果找不到，则前滚到下一个较高主版本、次版本和功能区段，并使用该功能区段的最新修补程序级别。 <br> 如果找不到，则失败。 |
| `latestPatch` | 使用安装的最新修补程序级别，以与请求的主版本、次版本和功能区段匹配，并且该修补程序级别大于或等于指定的值。 <br> 如果找不到，则失败。 |
| `latestFeature` | 使用安装的最高功能区段和修补程序级别，以与请求的主版本和次版本匹配，并且该功能区段和修补级别大于或等于指定的值。 <br> 如果找不到，则失败。 |
| `latestMinor` | 使用安装的最高次版本、功能区段和修补程序级别，以与请求的主版本匹配，并且该次版本、功能区段和修补级别大于或等于指定的值。 <br> 如果找不到，则失败。 |
| `latestMajor` | 使用安装的最高版本的 .NET SDK，并且该版本大于或等于指定的值。 <br> 如果找不到，则失败。 |
| `disable`     | 不前滚。 需要完全匹配。 |

### <a name="msbuild-sdks"></a>msbuild-sdks

类型：`object`

可便于在一个位置（而不是在各个项目中）控制项目 SDK 版本。 有关详细信息，请参阅[如何解析项目 SDK](/visualstudio/msbuild/how-to-use-project-sdk#how-project-sdks-are-resolved)。

## <a name="examples"></a>示例

下面的示例演示如何不使用预发布版本：

```json
{
  "sdk": {
    "allowPrerelease": false
  }
}
```

下面的示例展示了如何使用安装的最高版本，此版本大于或等于指定的版本。 所示的 JSON 不允许任何低于 2.2.200 的 SDK 版本，而允许 2.2.200 或任何更高版本（包括 3.0.xxx 和 3.1.xxx）。

```json
{
  "sdk": {
    "version": "2.2.200",
    "rollForward": "latestMajor"
  }
}
```

下面的示例演示如何使用完全指定的版本：

```json
{
  "sdk": {
    "version": "3.1.100",
    "rollForward": "disable"
  }
}
```

下面的示例展示了如何使用特定主要版本和次要版本的已安装最新功能区段和修补程序版本。 所示的 JSON 不允许任何低于 3.1.102 的 SDK 版本，而允许 3.1.102 或任何更高的 3.1.xxx 版本（如 3.1.103 或 3.1.200）。

```json
{
  "sdk": {
    "version": "3.1.102",
    "rollForward": "latestFeature"
  }
}
```

下面的示例展示了如何使用特定版本的已安装最高修补程序版本。 所示的 JSON 不允许任何低于 3.1.102 的 SDK 版本，而允许 3.1.102 或任何更高的 3.1.1xx 版本（如 3.1.103 或 3.1.199）。

```json
{
  "sdk": {
    "version": "3.1.102",
    "rollForward": "latestPatch"
  }
}
```

## <a name="globaljson-and-the-net-cli"></a>global.json 和 .NET CLI

最好能知道计算机上安装了哪些 SDK 版本，以便在 global.json 文件中设置相应版本。 有关如何执行此操作的详细信息，请参阅[如何检查是否已安装 .NET](../install/how-to-detect-installed-versions.md#check-sdk-versions)。

若要在计算机上安装其他 .NET SDK 版本，请访问[下载 .NET](https://dotnet.microsoft.com/download/dotnet) 页面。

可执行 [dotnet new](dotnet-new.md) 命令在当前目录中创建一个新的 global.json 文件，类似于以下示例：

```dotnetcli
dotnet new globaljson --sdk-version 3.0.100
```

## <a name="matching-rules"></a>匹配规则

> [!NOTE]
> 匹配规则由 `dotnet.exe` 入口点控制，该入口点在所有已安装的 .NET 运行时中很常见。 如果并行安装了多个运行时，或者正在使用 global.json 文件，则使用已安装的最新版 .NET 运行时的匹配规则。

## <a name="net-core-3x"></a>[.NET Core 3.x](#tab/netcore3x)

从 .NET Core 3.0 开始，在确定要使用的 SDK 版本时，适用以下规则：

- 如果未找到 global.json 文件，或者 global.json 未指定 SDK 版本和 `allowPrerelease` 值，则使用安装的最高 SDK 版本（相当于将 `rollForward` 设置为 `latestMajor`） 。 是否考虑 SDK 预发布版本取决于 `dotnet` 的调用方式。
  - 如果未使用 Visual Studio，则考虑预发布版本。
  - 如果使用 Visual Studio，它将使用请求的预发布状态。 也就是说，如果使用 Visual Studio 的预览版本，或者设置了“使用 .NET Core SDK 的预览版”选项（在“工具” > “选项” > “环境” > “预览功能”下方），则考虑预发布版本；否则仅考虑发布版本    。
- 如果找到了未指定 SDK 版本但指定了 `allowPrerelease` 值的 global.json 文件，则使用安装的最高 SDK 版本（相当于将 `rollForward` 设置为 `latestMajor`）。 最新 SDK 版本是发布版本还是预发布版本取决于 `allowPrerelease` 的值。 `true` 指示考虑预发布版本；`false` 指示仅考虑发布版本。
- 如果找到 global.json 文件，并且该文件指定了 SDK 版本：

  - 如果未设置 `rollForward` 值，它将使用 `latestPatch` 作为默认 `rollForward` 策略。 否则，请在 [rollForward](#rollforward) 部分中检查每个值及其行为。
  - 有关是否考虑预发布版本以及未设置 `allowPrerelease` 时的默认行为的信息，请参阅 [allowPrerelease](#allowprerelease) 部分。

## <a name="net-core-2x"></a>[.NET Core 2.x](#tab/netcore2x)

在 .NET Core 2.x SDK 中，在确定要使用的 SDK 版本时，适用以下规则：

- 如果未找到 global.json 文件或 global.json 未指定 SDK 版本，则使用最新安装的 SDK 版本 。 最新 SDK 版本可能是发布版本或预发布版本 - 以版本号更高的为准。
- 如果 global.json 指定了 SDK 版本：
  - 如果计算机上安装了该指定的 SDK 版本，则使用该版本。
  - 如果计算机上未安装指定的 SDK 版本，则使用最新安装的该版本的 SDK 修补程序版本。 安装的最新 SDK 修补程序版本可能是发布版本或预发布版本 - 以版本号更高的为准。 在 .NET Core 2.1 及更高版本中，在选择 SDK 版本时将忽略低于指定修补程序版本的修补程序版本 。
  - 如果找不到指定的 SDK 版本和相应的 SDK 修补程序版本，会引发错误。

SDK 版本由以下部分组成：

`[.NET Core major version].[.NET Core minor version].[xyz][-optional preview name]`

.NET Core SDK 的功能版本由 SDK 版本 2.1.100 及更高版本所采用的版本号的最后一部分 (`xyz`) 的第一个数字 (`x`) 表示。 通常，.NET Core SDK 的发布周期比 .NET Core 快。

修补程序版本由 SDK 版本 2.1.100 及更高版本所采用的版本号的最后一部分 (`xyz`) 的后两位数字 (`yz`) 表示。 例如，如果将 `2.1.300` 指定为 SDK 版本，则可选的 SDK 版本号最多到 `2.1.399`，但 `2.1.400` 不视为 `2.1.300` 的一个修补程序版本。

版本号架构过渡期间发布了 .NET Core SDK 版本 `2.1.100` 到 `2.1.201`，但未正确使用 `xyz` 表示法。 强烈建议如果在 global.json 文件中指定了这些版本，请确保目标计算机上安装了指定版本。

---

## <a name="troubleshoot-build-warnings"></a>针对生成警告的疑难解答

* 以下警告指示你的项目使用 .NET Core SDK 的预发布版本进行编译：

  > 使用的是 .NET Core SDK 的预览版本， 可通过当前项目中的 global.json 文件定义 SDK 版本。 有关详细信息，请访问 <https://go.microsoft.com/fwlink/?linkid=869452>。

  .NET Core SDK 的各种版本均品质优良稳定，在业内口碑良好。 但是，如果不希望使用预发布版本，请在 [allowPrerelease](#allowprerelease) 部分中查看可用于 .NET Core 3.0 SDK 或更高版本的各种策略。 对于从未安装 .NET Core 3.0 或更高版本运行时或 SDK 的计算机，需要创建一个 global.json 文件，并指定要使用的确切版本。

* 以下警告指示项目面向 EF Core 1.0 或 1.1，后者与 .NET Core 2.1 SDK 及更高版本不兼容：

  > 启动项目 '{startupProject}' 面向框架 '.NETCoreApp' 的版本 '{targetFrameworkVersion}'。 此版本的 Entity Framework Core .NET 命令行工具仅支持 2.0 或更高版本。 有关使用旧版工具的信息，请参阅 <https://go.microsoft.com/fwlink/?linkid=871254>。

  从 .NET Core 2.1 SDK（版本 2.1.300）开始，SDK 中包含 `dotnet ef` 命令。 若要编译项目，请在计算机上安装 .NET Core 2.0 SDK（版本 2.1.201）或更早版本，并使用 global.json 文件定义所需 SDK 版本。 有关 `dotnet ef` 命令的详细信息，请参阅 [EF Core .NET 命令行工具](/ef/core/miscellaneous/cli/dotnet)。

## <a name="see-also"></a>请参阅

- [如何解析 SDK 项目](/visualstudio/msbuild/how-to-use-project-sdk#how-project-sdks-are-resolved)

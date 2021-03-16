---
title: dotnet nuget add source 命令
description: Dotnet nuget add source 命令将新的包源添加到 NuGet 配置文件中。
ms.date: 03/20/2020
ms.openlocfilehash: df31a2eaba997d0e9fe4f4c2666052fd7c7c2f03
ms.sourcegitcommit: 42d436ebc2a7ee02fc1848c7742bc7d80e13fc2f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102105046"
---
# <a name="dotnet-nuget-add-source"></a>dotnet nuget add source

**本文适用于：** ✔️ .NET Core 3.1.200 SDK 及更高版本

## <a name="name"></a>“属性”

`dotnet nuget add source` - 添加 NuGet 源。

## <a name="synopsis"></a>摘要

```dotnetcli
dotnet nuget add source <PACKAGE_SOURCE_PATH> [--name <SOURCE_NAME>] [--username <USER>]
    [--password <PASSWORD>] [--store-password-in-clear-text]
    [--valid-authentication-types <TYPES>] [--configfile <FILE>]

dotnet nuget add source -h|--help
```

## <a name="description"></a>描述

`dotnet nuget add source` 命令将新的包源添加到 NuGet 配置文件中。

> [!WARNING]
> 添加多个包源时，请注意不要引入[依赖关系混乱漏洞](https://aka.ms/pkg-sec-wp)。

## <a name="arguments"></a>自变量

- **`PACKAGE_SOURCE_PATH`**

  包源的路径。

## <a name="options"></a>选项

- **`--configfile <FILE>`**

  NuGet 配置文件。 如果指定，则只使用此文件中的设置。 如果不指定，将使用当前目录中的配置文件的层次结构。 有关详细信息，请参阅[常见的 NuGet 配置](/nuget/consume-packages/configuring-nuget-behavior)。

- **`-n|--name <SOURCE_NAME>`**

  源的名称。

- **`-p|--password <PASSWORD>`**

  连接到已验证源时要使用的密码。

- **`--store-password-in-clear-text`**

  通过禁用密码加密允许存储可移植包源凭据。

- **`-u|--username <USER>`**

  连接到已经过身份验证的源时要使用的用户名。

- **`--valid-authentication-types <TYPES>`**

  此源的有效身份验证类型的逗号分隔列表。 如果服务器公布 NTLM 或协商，并且你必须使用基本机制发送凭据（例如，在本地 Azure DevOps Server 中使用 PAT 时），则将此项设置为 `basic`。 其他有效值包括 `negotiate`、`kerberos`、`ntlm` 和 `digest`，但这些值不太可能有用。

## <a name="examples"></a>示例

- 将 `nuget.org` 添加为源：

  ```dotnetcli
  dotnet nuget add source https://api.nuget.org/v3/index.json -n nuget.org
  ```

- 将 `c:\packages` 添加为本地源：

  ```dotnetcli
  dotnet nuget add source c:\packages
  ```

- 添加需要身份验证的源：

  ```dotnetcli
  dotnet nuget add source https://someServer/myTeam -n myTeam -u myUsername -p myPassword --store-password-in-clear-text
  ```

- 添加需要身份验证的源（然后继续安装凭据提供程序）：

  ```dotnetcli
  dotnet nuget add source https://azureartifacts.microsoft.com/myTeam -n myTeam
  ```

## <a name="see-also"></a>请参阅

- [NuGet.config 文件中的包源部分](/nuget/reference/nuget-config-file#package-source-sections)

- [sources 命令 (nuget.exe)](/nuget/reference/cli-reference/cli-ref-sources)

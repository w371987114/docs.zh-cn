---
ms.openlocfilehash: 370c6eb23a50ed388f0b10a5c5f4779ba2a1b144
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623588"
---
### <a name="project-tools-now-included-in-sdk"></a>SDK 现已包含项目工具

.NET Core 2.1 SDK 现包括常见 CLI 工具，你无需再从项目中引用这些工具。

#### <a name="change-description"></a>更改描述

在 .NET Core 2.0 中，项目通过 `<DotNetCliToolReference>` 项目设置引用外部 .NET 工具。 在 .NET Core 2.1 中，其中的某些工具包含在 .NET Core SDK 中，不再需要设置。 如果在项目中包含对这些工具的引用，则会收到如下所示的错误：“Microsoft.EntityFrameworkCore.Tools.DotNet”工具现包含在 .NET Core SDK 中。

.NET Core 2.1 SDK 中现在包含的工具：

| \<DotNetCliToolReference> 值                   | 工具                                                                                                            |
|------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| `Microsoft.DotNet.Watcher.Tools`               | `dotnet-watch`               |
| `Microsoft.Extensions.SecretManager.Tools`     | [dotnet-user-secrets](https://github.com/dotnet/aspnetcore/blob/master/src/Tools/dotnet-user-secrets/README.md) |
| `Microsoft.Extensions.Caching.SqlConfig.Tools` | [dotnet-sql-cache](https://github.com/dotnet/aspnetcore/blob/master/src/Tools/dotnet-sql-cache/README.md)       |
| `Microsoft.EntityFrameworkCore.Tools.DotNet`   | [dotnet-ef](/ef/core/miscellaneous/cli/dotnet)                                                                  |

#### <a name="version-introduced"></a>引入的版本

.NET Core SDK 2.1.300

#### <a name="recommended-action"></a>建议的操作

从项目中删除 `<DotNetCliToolReference>` 设置。

#### <a name="category"></a>类别

MSBuild

#### <a name="affected-apis"></a>受影响的 API

不可用

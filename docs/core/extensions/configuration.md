---
title: .NET 中的配置
description: 了解如何使用配置 API 配置 .NET 应用程序。
author: IEvangelist
ms.author: dapine
ms.date: 09/16/2020
ms.topic: overview
ms.openlocfilehash: 5955e46c2f5acb6776ada4e3fd6a65507d3faa1f
ms.sourcegitcommit: ecd9e9bb2225eb76f819722ea8b24988fe46f34c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2020
ms.locfileid: "102401950"
---
# <a name="configuration-in-net"></a>.NET 中的配置

.NET 中的配置是使用一个或多个[配置提供程序](#configuration-providers)执行的。 配置提供程序使用各种配置源从键值对读取配置数据：

- 设置文件，例如 appsettings.json
- 环境变量
- [Azure Key Vault](/azure/key-vault/general/overview)
- [Azure 应用配置](/azure/azure-app-configuration/overview)
- 命令行参数
- 已安装或已创建的自定义提供程序
- 目录文件
- 内存中的 .NET 对象

## <a name="configure-console-apps"></a>配置控制台应用

在默认情况下，使用 [dotnet new](../tools/dotnet-new.md) 或 Visual Studio 新建的 .NET 控制台应用程序不会公开配置功能。 若要在新的 .NET 控制台应用程序中添加配置，请[添加](../tools/dotnet-add-package.md)对 `Microsoft.Extensions.Hosting` 的包引用。 修改 Program.cs 文件，使其与以下代码相匹配：

:::code language="csharp" source="snippets/configuration/console/Program.cs" highlight="18":::

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder(System.String[])?displayProperty=nameWithType> 方法按照以下顺序为应用提供默认配置：

1. [ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource)：添加现有的 `IConfiguration` 作为源。
1. 使用 [JSON 配置提供程序](configuration-providers.md#file-configuration-provider)通过 *appsettings.json* 提供。
1. 使用 [JSON 配置提供程序](configuration-providers.md#file-configuration-provider)通过 appsettings.`Environment`.json 提供 。 例如，appsettings.Production.json 和 appsettings.Development.json 。
1. 应用在 `Development` 环境中运行时的应用机密。
1. 使用[环境变量配置提供程序](configuration-providers.md#environment-variable-configuration-provider)通过环境变量提供。
1. 使用[命令行配置提供程序](configuration-providers.md#command-line-configuration-provider)通过命令行参数提供。

后来添加的配置提供程序会替代之前的密钥设置。 例如，如果在 appsettings.json 和环境中设置了 `SomeKey`，则会使用环境值。 通过默认配置提供程序，[命令行配置提供程序](configuration-providers.md#command-line-configuration-provider)将替代其他所有提供程序。

### <a name="binding"></a>绑定

.NET 中的配置的其中一个关键优点是，可将配置值绑定到 .NET 对象的实例。 例如，JSON 配置提供程序可用于将 appsettings.json 文件映射到 .NET 对象中并与依赖注入一起使用。 此可实现选项模式，后者使用类来提供对相关设置组的强类型访问。

## <a name="configuration-providers"></a>配置提供程序

下表显示了 .NET Core 应用可用的配置提供程序。

| 提供程序                                                                                                               | 通过以下对象提供配置        |
|------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| [Azure 应用配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)                          | Azure 应用程序配置            |
| [Azure Key Vault 配置提供程序](/azure/key-vault/general/tutorial-net-virtual-machine)                        | Azure Key Vault                    |
| [命令行配置提供程序](configuration-providers.md#command-line-configuration-provider)                  | 命令行参数            |
| [自定义配置提供程序](custom-configuration-provider.md)                                                      | 自定义源                      |
| [环境变量配置提供程序](configuration-providers.md#environment-variable-configuration-provider) | 环境变量              |
| [文件配置提供程序](configuration-providers.md#file-configuration-provider)                                  | JSON、XML 和 INI 文件           |
| [Key-per-file 配置提供程序](configuration-providers.md#key-per-file-configuration-provider)                  | 目录文件                    |
| [内存配置提供程序](configuration-providers.md#memory-configuration-provider)                              | 内存中集合              |
| [应用机密（机密管理器）](/aspnet/core/security/app-secrets)                                                      | 用户配置文件目录中的文件 |

若要详细了解各种配置提供程序，请查看 [.NET 中的配置提供程序](configuration-providers.md)。

## <a name="see-also"></a>另请参阅

- [.NET 中的配置提供程序](configuration-providers.md)
- [实现自定义配置提供程序](custom-configuration-provider.md)
- 配置 bug 应在 [github.com/dotnet/extensions](https://github.com/dotnet/extensions/issues) 存储库中创建

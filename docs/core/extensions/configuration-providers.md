---
title: .NET 中的配置提供程序
description: 了解如何使用配置提供程序 API 来配置 .NET 应用程序。
author: IEvangelist
ms.author: dapine
ms.date: 03/08/2021
ms.openlocfilehash: 5f248c93de1773a8bbe8209f002806fb196bb260
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605004"
---
# <a name="configuration-providers-in-net"></a>.NET 中的配置提供程序

可通过配置提供程序进行 .NET 配置。 存在几种类型的提供程序，它们依赖于不同的配置源。 本文详细介绍了所有不同的配置提供程序及其相应的源。

- [文件配置提供程序](#file-configuration-provider)
- [环境变量配置提供程序](#environment-variable-configuration-provider)
- [命令行配置提供程序](#command-line-configuration-provider)
- [Key-per-file 配置提供程序](#key-per-file-configuration-provider)
- [内存配置提供程序](#memory-configuration-provider)

## <a name="file-configuration-provider"></a>文件配置提供程序

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。 以下配置提供程序派生自 `FileConfigurationProvider`：

- [JSON 配置提供程序](#json-configuration-provider)
- [XML 配置提供程序](#xml-configuration-provider)
- [INI 配置提供程序](#ini-configuration-provider)

### <a name="json-configuration-provider"></a>JSON 配置提供程序

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 类从 JSON 文件加载配置。 安装 NuGet 包 [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json)。

重载可以指定：

- 文件是否可选
- 如果文件更改，是否重载配置

考虑下列代码：

:::code language="csharp" source="snippets/configuration/console-json/Program.cs" range="1-39,43-44" highlight="23-29":::

前面的代码：

- 清除 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder(System.String[])> 方法中默认添加的所有现有的配置提供程序。
- 将 JSON 配置提供程序配置为使用以下选项加载 appsettings.json 和 appsettings.`Environment`.json 文件  ：
  - `optional: true`：文件是可选的。
  - `reloadOnChange: true`：保存更改后会重载文件。

JSON 设置会被[环境变量配置提供程序](#environment-variable-configuration-provider)和[命令行配置提供程序](#command-line-configuration-provider)中的设置替代。

下面是一个具有各种配置设置的示例 appsettings.json 文件：

:::code language="json" source="snippets/configuration/console-json/appsettings.json":::

从 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 实例中，在添加配置提供程序后，可调用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Build?displayProperty=nameWithType> 来获取 <xref:Microsoft.Extensions.Configuration.IConfigurationRoot> 对象。 配置根表示配置层次结构的根。 可将配置中的节绑定到 .NET 对象的实例，稍后再通过依赖注入将其作为 <xref:Microsoft.Extensions.Options.IOptions%601> 提供。

请考虑如下定义的 `TransientFaultHandlingOptions` 类：

:::code language="csharp" source="snippets/configuration/console-json/TransientFaultHandlingOptions.cs":::

下面的代码会生成配置根，将一个节绑定到类 `TransientFaultHandlingOptions` 类型，并将绑定值输出到控制台窗口：

:::code language="csharp" source="snippets/configuration/console-json/Program.cs" range="31-38":::

应用程序会编写以下示例输出：

:::code language="csharp" source="snippets/configuration/console-json/Program.cs" range="40-42":::

### <a name="xml-configuration-provider"></a>XML 配置提供程序

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 类在运行时从 XML 文件加载配置。 安装 NuGet 包 [`Microsoft.Extensions.Configuration.Xml`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Xml)。

下面的代码演示如何使用 XML 配置提供程序配置 XML 文件。

:::code language="csharp" source="snippets/configuration/console-xml/Program.cs" range="1-34,52,58-59" highlight="23-34":::

上述代码：

- 清除 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder(System.String[])> 方法中默认添加的所有现有的配置提供程序。
- 将 XML 配置提供程序配置为使用以下选项加载 appsettings.xml 和 repeating-example.xml 文件 ：
  - `optional: true`：文件是可选的。
  - `reloadOnChange: true`：保存更改后会重载文件。
- 配置环境变量配置提供程序。
- 如果给定的 `args` 包含自变量，则配置命令行配置提供程序。

XML 设置会被[环境变量配置提供程序](#environment-variable-configuration-provider)和[命令行配置提供程序](#command-line-configuration-provider)中的设置替代。

下面是一个具有各种配置设置的示例 appsettings.xml 文件：

:::code language="xml" source="snippets/configuration/console-xml/appsettings.xml":::

如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：

:::code language="xml" source="snippets/configuration/console-xml/repeating-example.xml":::

以下代码会读取前面的配置文件并显示键和值：

:::code language="csharp" source="snippets/configuration/console-xml/Program.cs" range="36-51":::

该应用程序会编写以下示例输出：

:::code language="csharp" source="snippets/configuration/console-xml/Program.cs" range="53-57":::

属性可用于提供值：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

以前的配置文件使用 `value` 加载以下键：

- `key:attribute`
- `section:key:attribute`

### <a name="ini-configuration-provider"></a>INI 配置提供程序

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 类在运行时从 INI 文件加载配置。 安装 NuGet 包 [`Microsoft.Extensions.Configuration.Ini`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Ini)。

以下代码将清除所有配置提供程序，并添加具有两个 INI 文件的 `IniConfigurationProvider` 作为源：

:::code language="csharp" source="snippets/configuration/console-ini/Program.cs" range="1-37,44-45" highlight="24-30":::

下面是一个具有各种配置设置的示例 appsettings.ini 文件：

:::code language="ini" source="snippets/configuration/console-ini/appsettings.ini":::

以下代码通过将上述配置设置写入控制台窗口来显示这些设置：

:::code language="csharp" source="snippets/configuration/console-ini/Program.cs" range="32-36":::

该应用程序会编写以下示例输出：

:::code language="csharp" source="snippets/configuration/console-ini/Program.cs" range="38-43":::

## <a name="environment-variable-configuration-provider"></a>环境变量配置提供程序

通过默认配置，<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 会在读取 appsettings.json、appsettings.`Environment`.json 和机密管理器后，从环境变量键值对加载配置  。 因此，从环境中读取的键值会替代从 appsettings.json、appsettings.`Environment`.json 和机密管理器中读取的值  。

所有平台上的环境变量分层键都不支持 `:` 分隔符。 双下划线 (`__`) 会自动替换为 `:` 且受各大平台支持。 例如，[Bash](https://linuxhint.com/bash-environment-variables) 不支持 `:` 分隔符，但支持 `__`。

以下 `set` 命令：

- 在 Windows 上设置上述示例的环境键和值。
- 通过将设置更改为非默认值来对其进行测试。 `dotnet run` 命令必须在项目目录中运行。

```dotnetcli
set SecretKey="Secret key from environment"
set TransientFaultHandlingOptions__Enabled="true"
set TransientFaultHandlingOptions__AutoRetryDelay="00:00:13"

dotnet run
```

前面的环境设置：

- 仅在进程中设置，这些进程是从设置进程的命令窗口启动的。
- 不会由通过 Visual Studio 启动的 Web 应用读取。

以下 [setx](/windows-server/administration/windows-commands/setx) 命令可用于在 Windows 上设置环境键和值。 与 `set` 不同，`setx` 设置是持久的。 `/M` 在系统环境中设置变量。 如果未使用 `/M` 开关，则会设置用户环境变量。

```dotnetcli
setx SecretKey "Secret key from setx environment" /M
setx TransientFaultHandlingOptions__Enabled "true" /M
setx TransientFaultHandlingOptions__AutoRetryDelay "00:00:05" /M

dotnet run
```

测试前面的命令是否会替代 appsettings.json 和 appsettings.`Environment`.json：

- 使用 Visual Studio：退出并重启 Visual Studio。
- 使用 CLI：启动新的命令窗口并输入 `dotnet run`。

使用字符串调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> 以指定环境变量的前缀：

:::code language="csharp" source="snippets/configuration/console-env/Program.cs" highlight="21-22":::

在上述代码中：

- `config.AddEnvironmentVariables(prefix: "CustomPrefix_")` 被添加到默认配置提供程序的后面。 有关对配置提供程序进行排序的示例，请查看 [XML 配置提供程序](#xml-configuration-provider)。
- 设有 `CustomPrefix_` 前缀的环境变量会替代默认配置提供程序。 这包括没有前缀的环境变量。

前缀会在读取配置键值对时被去除。

以下命令对自定义前缀进行测试：

```dotnetcli
set CustomPrefix__SecretKey="Secret key with CustomPrefix_ environment"
set CustomPrefix__TransientFaultHandlingOptions__Enabled=true
set CustomPrefix__TransientFaultHandlingOptions__AutoRetryDelay=00:00:21

dotnet run
```

默认配置会加载前缀为 `DOTNET_` 的环境变量和命令行自变量。 `DOTNET_` 前缀由 .NET 用于[主机](generic-host.md#host-configuration)和[应用配置](generic-host.md#app-configuration)，但不用于用户配置。

有关主机和应用配置的详细信息，请参阅 [.NET 通用主机](generic-host.md)。

在 [Azure 应用服务](https://azure.microsoft.com/services/app-service)上，选择“设置”>“配置”页面上的“新应用程序设置” 。 Azure 应用服务应用程序设置：

- 已静态加密且通过加密的通道进行传输。
- 已作为环境变量公开。

### <a name="connection-string-prefixes"></a>连接字符串前缀

对于四个连接字符串环境变量，配置 API 具有特殊的处理规则。 这些连接字符串涉及了为应用环境配置 Azure 连接字符串。 使用默认配置或没有向 `AddEnvironmentVariables` 应用前缀时，具有表中所示前缀的环境变量将加载到应用中。

| 连接字符串前缀 | 提供程序                                                                |
|--------------------------|-------------------------------------------------------------------------|
| `CUSTOMCONNSTR_`         | 自定义提供程序                                                         |
| `MYSQLCONNSTR_`          | [MySQL](https://www.mysql.com)                                          |
| `SQLAZURECONNSTR_`       | [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database) |
| `SQLCONNSTR_`            | [SQL Server](https://www.microsoft.com/sql-server)                      |

当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：

- 通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。
- 创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。

| 环境变量键 | 转换的配置键 | 提供程序配置条目                                                    |
|--------------------------|-----------------------------|---------------------------------------------------------------------------------|
| `CUSTOMCONNSTR_{KEY}`    | `ConnectionStrings:{KEY}`   | 配置条目未创建。                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | 键：`ConnectionStrings:{KEY}_ProviderName`：<br>值：`MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | 键：`ConnectionStrings:{KEY}_ProviderName`：<br>值：`System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | 键：`ConnectionStrings:{KEY}_ProviderName`：<br>值：`System.Data.SqlClient`  |

### <a name="environment-variables-set-in-launchsettingsjson"></a>在 launchSettings.json 中设置的环境变量

在 launchSettings.json 中设置的环境变量将替代在系统环境中设置的变量。

## <a name="command-line-configuration-provider"></a>命令行配置提供程序

使用默认配置，<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 会从以下配置源后的命令行参数键值对中加载配置：

- appsettings.json 和 appsettings.`Environment`.json 文件  。
- `Development` 环境中的应用机密（机密管理器）。
- 环境变量。

默认情况下，在命令行上设置的配置值会替代通过所有其他配置提供程序设置的配置值。

### <a name="command-line-arguments"></a>命令行参数

以下命令使用 `=` 设置键和值：

```dotnetcli
dotnet run SecretKey="Secret key from command line"
```

以下命令使用 `/` 设置键和值：

```dotnetcli
dotnet run /SecretKey "Secret key set from forward slash"
```

以下命令使用 `--` 设置键和值：

```dotnetcli
dotnet run --SecretKey "Secret key set from double hyphen"
```

键值：

- 必须后跟 `=`，或者当值后跟一个空格时，键必须具有一个 `--` 或 `/` 的前缀。
- 如果使用 `=`，则不是必需的。 例如 `SomeKey=`。

在同一命令中，请勿将使用 `=` 的命令行参数键值对与使用空格的键值对混合使用。

## <a name="key-per-file-configuration-provider"></a>Key-per-file 配置提供程序

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。 该键是文件名。 该值是文件的内容。 Key-per-file 配置提供程序用于 Docker 托管方案。

若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile%2A> 扩展方法。 文件的 `directoryPath` 必须是绝对路径。

重载允许指定：

- 配置源的 `Action<KeyPerFileConfigurationSource>` 委托。
- 目录是否可选以及目录的路径。

双下划线字符 (`__`) 用作文件名中的配置键分隔符。 例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。

构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：

```csharp
.ConfigureAppConfiguration((_, configuration) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");

    configuration.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>内存配置提供程序

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。

以下代码将内存集合添加到配置系统中：

:::code language="csharp" source="snippets/configuration/console-memory/Program.cs" highlight="22-29":::

在上述代码中，<xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection(Microsoft.Extensions.Configuration.IConfigurationBuilder,System.Collections.Generic.IEnumerable{System.Collections.Generic.KeyValuePair{System.String,System.String}})?displayProperty=nameWithType> 会在默认配置提供程序之后添加内存提供程序。 有关对配置提供程序进行排序的示例，请查看 [XML 配置提供程序](#xml-configuration-provider)。

## <a name="see-also"></a>另请参阅

- [.NET 中的配置](configuration.md)
- [.NET 通用主机](generic-host.md)
- [实现自定义配置提供程序](custom-configuration-provider.md)

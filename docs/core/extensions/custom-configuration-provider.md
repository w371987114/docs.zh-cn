---
title: 在 .NET 中实现自定义配置提供程序
description: 了解如何在 .NET 应用程序中实现自定义配置提供程序。
author: IEvangelist
ms.author: dapine
ms.date: 12/04/2020
ms.topic: how-to
ms.openlocfilehash: 22e46b7df8b02421633d6be251d990879baa8b2b
ms.sourcegitcommit: ecd9e9bb2225eb76f819722ea8b24988fe46f34c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2020
ms.locfileid: "102401949"
---
# <a name="implement-a-custom-configuration-provider-in-net"></a>在 .NET 中实现自定义配置提供程序

有许多[配置提供程序](configuration-providers.md)可用于常见的配置源，如 JSON、XML 和 INI 文件。 如果某个可用的提供程序不满足你的应用程序需要，说明你可能需要实现自定义配置提供程序。 在本文中，你将了解如何实现一个依赖数据库作为配置源的自定义配置提供程序。

## <a name="custom-configuration-provider"></a>自定义配置提供程序

此示例应用演示了如何使用[实体框架 (EF) Core](/ef/core) 创建从数据库读取配置键值对的基本配置提供程序。

提供程序具有以下特征：

- EF 内存中数据库用于演示目的。 若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。
- 提供程序在启动时将数据库表读入配置。 提供程序不会基于每个键查询数据库。
- 未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。

定义一个 `Settings` 记录类型实体，用于在数据库中存储配置值。 例如，可以将 Settings.cs 文件添加到“Models”文件夹中：

:::code language="csharp" source="snippets/configuration/custom-provider/Models/Settings.cs":::

有关记录类型的信息，请参阅 [C# 9 中的记录类型](../../csharp/whats-new/csharp-9.md#record-types)。

添加 `EntityConfigurationContext` 以存储和访问配置的值。

Providers/EntityConfigurationContext.cs：

:::code language="csharp" source="snippets/configuration/custom-provider/Providers/EntityConfigurationContext.cs":::

创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。

Providers/EntityConfigurationSource.cs：

:::code language="csharp" source="snippets/configuration/custom-provider/Providers/EntityConfigurationSource.cs":::

通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。 当数据库为空时，配置提供程序将对其进行初始化。 由于配置密钥不区分大小写，因此用来初始化数据库的字典是用不区分大小写的比较程序 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) 创建的。

Providers/EntityConfigurationProvider.cs：

:::code language="csharp" source="snippets/configuration/custom-provider/Providers/EntityConfigurationProvider.cs":::

可以使用 `AddEntityConfiguration` 扩展方法将配置源添加到 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 实例中。

Extensions/ConfigurationBuilderExtensions.cs：

:::code language="csharp" source="snippets/configuration/custom-provider/Extensions/ConfigurationBuilderExtensions.cs":::

下面的代码演示如何在 Program.cs 中使用自定义的 `EntityConfigurationProvider`：

:::code language="csharp" source="snippets/configuration/custom-provider/Program.cs" highlight="27-28":::

## <a name="see-also"></a>另请参阅

- [.NET 中的配置](configuration.md)
- [.NET 中的配置提供程序](configuration-providers.md)

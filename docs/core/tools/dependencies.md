---
title: 管理 .NET 中的依赖项
description: 介绍如何管理 .NET 应用程序的项目依赖项。
no-loc:
- dotnet add package
- dotnet remove package
- dotnet list package
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 03311902e99962553b5b740a285021f27d94bfbc
ms.sourcegitcommit: 20b4565974d185c7716656a6c63e3cfdbdf4bf41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104759698"
---
# <a name="manage-dependencies-in-net-applications"></a>管理 .NET 应用程序中的依赖项

本文介绍如何通过编辑项目文件或使用 CLI 来添加和删除依赖项。

## <a name="the-packagereference-element"></a>\<PackageReference> 元素

`<PackageReference>` 项目文件元素具有以下结构：

```xml
<PackageReference Include="PACKAGE_ID" Version="PACKAGE_VERSION" />
```

`Include` 特性指定要添加到项目的包的 ID。 `Version` 特性指定要获取的版本。 版本根据 [NuGet 版本规则](/nuget/create-packages/dependency-versions#version-ranges)进行指定。

> [!NOTE]
> 如果不熟悉“项目-文件”语法，可参阅 [MSBuild 项目参考](/visualstudio/msbuild/msbuild-project-file-schema-reference)文档了解详细信息。

使用条件来添加仅在特定目标中可用的依赖项，如以下示例所示：

```xml
<PackageReference Include="PACKAGE_ID" Version="PACKAGE_VERSION" Condition="'$(TargetFramework)' == 'netcoreapp2.1'" />
```

上述示例中的依赖项只有在对给定目标生成时才有效。 条件中的 `$(TargetFramework)` 是将在项目中设置的 MSBuild 属性。 对于大多数常见的 .NET 应用程序，无需这样做。

## <a name="add-a-dependency-by-editing-the-project-file"></a>通过编辑项目文件添加依赖项

若要添加依赖项，请在 `<ItemGroup>` 元素内添加 `<PackageReference>` 元素。 可以添加到现有 `<ItemGroup>`，也可以新建一个元素。 下面的示例使用 `dotnet new console` 创建的默认控制台应用程序项目：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="3.1.2" />
  </ItemGroup>

</Project>
```

## <a name="add-a-dependency-by-using-the-cli"></a>使用 CLI 添加依赖项

若要添加依赖项，请运行 [dotnet add package](dotnet-add-package.md) 命令，如以下示例中所示：

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore
```

## <a name="remove-a-dependency-by-editing-the-project-file"></a>通过编辑项目文件删除依赖项

若要删除依赖项，请从项目文件中删除其 `<PackageReference>` 元素。

## <a name="remove-a-dependency-by-using-the-cli"></a>使用 CLI 删除依赖项

若要删除依赖项，请运行 [dotnet remove package](dotnet-remove-package.md) 命令，如以下示例中所示：

```dotnetcli
dotnet remove package Microsoft.EntityFrameworkCore
```

## <a name="see-also"></a>请参阅

* [项目文件中的包引用](../project-sdk/msbuild-props.md#reference-properties)
* [dotnet list package 命令](dotnet-list-package.md)

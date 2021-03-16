---
title: 使用可移植类库的跨平台开发
description: 在 .NET 中使用可移植类库项目快速轻松地为 Microsoft 平台构建跨平台应用和库。
ms.date: 09/17/2018
helpviewer_keywords:
- Portable Class Library [.NET Framework]
- targeting multiple platforms
- multiple platforms, targeting
ms.assetid: c31e1663-c164-4e65-b66d-d3aa8750a154
ms.openlocfilehash: ced595f62249609f4524d0b4b7bf734929619bfd
ms.sourcegitcommit: 279fb6e8d515df51676528a7424a1df2f0917116
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "102402222"
---
# <a name="cross-platform-development-with-the-portable-class-library"></a>使用可移植类库的跨平台开发

Visual Studio 中的可移植类库项目类型可帮助你快速轻松地为 Microsoft 平台构建跨平台应用和库。

[!INCLUDE[standard](../../../includes/pcl-to-standard.md)]

可移植类库可以帮助你减少开发和测试代码的时间和成本。 使用此项目类型编写和构建可移植 .NET Framework 程序集，然后从面向多个平台（例如 .NET Framework、iOS 或 Mac）的应用引用这些程序集。

即使是在 Visual Studio 中创建可移植类库项目并开始开发它之后，你仍然可以更改目标平台。 Visual Studio 会使用新的程序集编译你的库，这可帮助识别你需要在代码中进行的更改。

## <a name="create-a-portable-class-library-project"></a>创建可移植类库项目

若要创建可移植类库，请使用 Visual Studio 中提供的模板。 创建新项目（“文件” > “新建项目”），然后在“新建项目”中选择你的编程语言（Visual C# 或 Visual Basic）  。 接着，选择“类库（旧版可移植）”模板。 输入项目的名称，再选择“确定”。

此时会出现"添加可移植类库"对话框。 选择两个或多个目标，然后选择 “确定”。

![在 Visual Studio 中添加可移植类库目标](media/add-portable-class-library.png)

## <a name="change-targets"></a>更改目标

可在创建可移植类库项目时或在开始开发后更改该项目的目标平台。 如果想要在创建项目后更改这些目标，请在“解决方案资源管理器”，打开可移植类库项目（不是解决方案）的快捷菜单，然后选择“属性” 。 在项目属性页上，“库”选项卡显示了项目当前针对的平台。

![Visual Studio 中可移植类库的项目属性](media/pcl-project-properties.png)

若要添加或删除目标，请选择“更改”按钮，然后选择和清除相应的复选框。

在你更改目标时，供你在开发项目时使用的 API 也将发生更改以匹配你的选择。 Visual Studio 报告因目标更改而可能导致的错误和警告。

如果要在 Visual Studio 中进行更改之前评估程序集的可移植性，可使用 [.NET 可移植性分析器](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)。

## <a name="supported-types-and-members"></a>支持的类型和成员

可移植类库项目中可用的类型和成员受若干兼容性因素的约束：

- 它们必须在所选各目标之间共享。

- 它们必须在这些目标上具有类似的行为方式。

- 它们不能是要弃用的候选项。

- 它们必须在可移植环境中有意义，特别是在支持成员不可移植时。

如果一个成员在可移植类库和所选的目标中受支持，它将会你的 IntelliSense 项目中出现。 但是，请记住某一 API 可能在可移植类库中受支持，但你是否可以使用该 API ​​取决于你选择的目标。

## <a name="api-differences-in-the-portable-class-library"></a>可移植类库中的 API 差异

为了使可移植类库程序集在所有支持的平台中都兼容，可移植类库中对部分成员进行了轻微更改。

## <a name="use-the-portable-class-library"></a>使用可移植类库

构建可移植类库项目后，只需从其他项目中引用它即可。 可以引用该项目或包含你要访问的类的特定程序集。

若要运行引用可移植类库程序集的应用，必须在计算机上安装所需版本（或更高版本）的目标平台。 Visual Studio 包含所有必需的框架，因此你无需进一步修改即可在用来开发应用的计算机上运行该应用。

### <a name="deploy-a-universal-windows-app"></a>部署通用 Windows 应用

创建引用可移植类库程序集的通用 Windows 应用时，部署该应用所需的一切都包含在应用包中，无需执行更多步骤。

### <a name="deploy-a-net-framework-app"></a>部署 .NET Framework 应用

部署引用可移植类库程序集的 .NET Framework 应用时，你必须指定一个对 .NET Framework 正确版本的依赖项。 通过指定此依赖项，可确保与你的应用程序一起安装所需的版本。

- 若要创建 ClickOnce 部署的依赖项，请在“解决方案资源管理器”中，选择要发布的项目对应的项目节点。 （这是引用了可移植类库项目的项目。）在菜单栏上，选择“项目” > “属性”，然后选择“发布”选项卡  。在“发布”页面上，选择“系统必备” 。 选择所需 .NET Framework 版本作为系统必备组件。

- 若要创建安装项目的依赖项，请在“解决方案资源管理器”中，选择安装项目。 在菜单栏上，选择“项目” > “属性” > “系统必备”  。 选择所需 .NET Framework 版本作为系统必备组件。

若要详细了解如何部署 .NET Framework 应用，请查看[开发人员部署指南](../../framework/deployment/deployment-guide-for-developers.md)。

## <a name="see-also"></a>另请参阅

- [将可移植类库与 MVVM 配合使用](using-portable-class-library-with-model-view-view-model.md)
- [面向多个平台的库的应用资源](app-resources-for-libraries-that-target-multiple-platforms.md)
- [.NET 可移植性分析器](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
- [.NET Framework 对 Windows 应用商店应用和 Windows 运行时的支持情况](support-for-windows-store-apps-and-windows-runtime.md)
- [部署](../../framework/deployment/net-framework-applications.md)

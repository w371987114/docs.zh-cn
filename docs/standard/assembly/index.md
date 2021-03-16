---
title: .NET 中的程序集
description: 程序集是基于 .NET 的应用程序的部署、版本控制、重用、激活范围和安全权限的基本单元。
ms.date: 08/15/2019
ms.assetid: 149f5ca5-5b34-4746-9542-1ae43b2d0256
helpviewer_keywords:
- dynamic assemblies
- security [.NET Framework], boundaries
- boundaries of assemblies
- assemblies [.NET Framework], about
- assemblies [.NET Framework], boundaries
- reference scope boundaries
- assemblies [.NET Framework]
- version boundaries
- type boundaries
ms.openlocfilehash: f5703377782977bb69815255d04cf1ee5a274c18
ms.sourcegitcommit: 0bb8074d524e0dcf165430b744bb143461f17026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2021
ms.locfileid: "103477219"
---
# <a name="assemblies-in-net"></a>.NET 中的程序集

程序集构成了 .NET 应用程序的部署、版本控制、重用、激活范围和安全权限的基本单元。 程序集是为协同工作而生成的类型和资源的集合，这些类型和资源构成了一个逻辑功能单元。 程序集采用可执行文件 (.exe) 或动态链接库文件 (.dll) 的形式，是 .NET 应用程序的构建基块 。 它们向公共语言运行时提供了注意类型实现代码所需的信息。

在 .NET Core 和 .NET Framework 中，可以从一个或多个源代码文件生成程序集。 在 .NET Framework 中，程序集可以包含一个或多个模块。 因此，大型项目可以采用以下规划：由多个开发者单独开发各源代码文件或模块，最后整合所有这些内容以创建一个程序集。 若要详细了解模块，请参阅[操作说明：生成多文件程序集](../../framework/app-domains/build-multifile-assembly.md)。

程序集具有以下属性：

- 程序集以 .exe 或 .dll 文件的形式实现 。

- 对于面向 .NET Framework 的库，可以通过将程序集放入[全局程序集缓存 (GAC)](../../framework/app-domains/gac.md)，在应用程序之间共享程序集。 必须先对程序集进行强命名，然后才能将它们包含到 GAC 中。 有关详细信息，请参阅[具有强名称的程序集](strong-named.md)。

- 只有在需要使用时才会将程序集加载到内存中。 如果未使用程序集，则不加载。 也就是说，使用程序集，可以在大型项目中高效管理资源。

- 可以使用反射，以编程方式获取程序集的相关信息。 有关详细信息，请参阅[反射 (C#)](../../csharp/programming-guide/concepts/reflection.md) 或[反射 (Visual Basic)](../../visual-basic/programming-guide/concepts/reflection.md)。

- 你可以加载一个程序集，以使用 .NET Core 中的 <xref:System.Reflection.MetadataLoadContext> 类以及 .NET Core 和 .NET Framework 中的 <xref:System.Reflection.Assembly.ReflectionOnlyLoad%2A?displayProperty=nameWithType> 或 <xref:System.Reflection.Assembly.ReflectionOnlyLoadFrom%2A?displayProperty=nameWithType> 方法来检查该程序集。

## <a name="assemblies-in-the-common-language-runtime"></a>公共语言运行时中的程序集

程序集向公共语言运行时提供了注意类型实现代码所需的信息。 对于运行时，类型不存在于程序集上下文之外。

程序集定义以下信息：

- 公共语言运行时执行的代码。 请注意，每个程序集只能有一个入口点：`DllMain`、`WinMain` 或 `Main`。

- 安全边界。 程序集就是在其中请求和授予权限的单元。 有关程序集中安全边界的详细信息，请参阅[程序集安全注意事项](security-considerations.md)。

- 类型边界。 每一类型的标识均包括该类型所驻留的程序集的名称。 在一个程序集的范围中加载的称为 `MyType` 的类型不同于在另一个程序集范围中加载的称为 `MyType` 的类型。

- 引用范围边界。 [程序集清单](#assembly-manifest)包含用于解析类型和满足资源请求的元数据。 该清单指定要在程序集外公开的类型和资源，并枚举它所依赖的其他程序集。 除非可迁移可执行 (PE) 文件中的 Microsoft 中间语言 (MSIL) 代码具有相关的[程序集清单](#assembly-manifest)，否则不执行此代码。

- 版本边界。 程序集是公共语言运行时中无版本冲突的最小单元。 同一程序集中的所有类型和资源均会被版本化为一个单元。 [程序集清单](#assembly-manifest)描述你为任何依赖项程序集所指定的版本依赖性。 有关版本控制的详细信息，请参阅[程序集版本控制](versioning.md)。

- 部署单元。 当一个应用程序启动时，只有该应用程序最初调用的程序集必须存在。 其他程序集（例如，包含本地化资源或实用工具类的程序集）可以按需检索。 这样，应用在第一次下载时就会比较精简。 有关部署程序集的详细信息，请参阅[部署应用程序](../../framework/deployment/index.md)。

- 并行执行单元。 有关运行多个版本的程序集的详细信息，请参阅[程序集和并行执行](side-by-side-execution.md)。

## <a name="create-an-assembly"></a>创建程序集

程序集可以为静态或动态。 静态程序集存储在磁盘上的可迁移可执行 (PE) 文件中。 静态程序集可以包括接口、类和资源（如位图、JPEG 文件和其他资源文件）。 你还可以创建动态程序集，动态程序集直接从内存运行并且在执行前不保存到磁盘上。 你可以在执行动态程序集后将它们保存在磁盘上。

有几种创建程序集的方法。 你可以使用可创建 .dll 或 .exe 文件的开发工具，例如 Visual Studio 。 可以使用 Windows SDK 中的工具创建具有从其他开发环境中创建的模块的程序集。 还可以使用公共语言运行时 API（例如 <xref:System.Reflection.Emit?displayProperty=nameWithType>）来创建动态程序集。

可以采用以下方法编译程序集：在 Visual Studio 中生成程序集、使用 .NET Core 命令行接口工具生成程序集，或使用命令行编译器生成 .NET Framework 程序集。 要详细了解如何使用 .NET Core CLI 生成程序集，请参阅 [.NET Core CLI 概述](../../core/tools/index.md)。

> [!NOTE]
> 若要在 Visual Studio 中生成程序集，请在“生成”菜单上选择“生成” 。

## <a name="assembly-manifest"></a>程序集清单

每个程序集都有一个程序集清单文件。 与目录类似，程序集清单包含以下内容：

- 程序集的标识（名称和版本）。

- 文件表，描述构成程序集的其他所有文件（例如，.exe 或 .dll 文件所依赖的你创建的其他程序集、位图文件或自述文件） 。

- 程序集引用列表，即所有外部依赖项的列表，如 .dll 或其他文件 。 程序集既可以引用全局对象，也可以引用私有对象。 全局对象可用于所有其他应用程序。 在 .NET Core 中，全局对象与特定的 .NET Core 运行时结合使用。 在 .NET Framework 中，全局对象位于全局程序集缓存 (GAC) 中。 System.IO.dll 是 GAC 中程序集的一个示例。 私有对象必须位于级别不高于应用安装目录的目录中。

由于程序集包含内容、版本控制和依赖项的相关信息，因此使用它们的应用程序不依赖 Windows 系统上的注册表等外部源也能正常运行。 程序集减少了 .dll 冲突，让应用程序变得更可靠、更易于部署。 在许多情况下，只需将 .NET 应用程序的文件复制到目标计算机，即可进行安装。 有关详细信息，请参阅[程序集清单](manifest.md)。

## <a name="add-a-reference-to-an-assembly"></a>添加对程序集的引用

必须添加对应用程序中的程序集的引用，才能使用该程序集。 引用程序集后，应用程序可以使用其名称空间的所有可访问类型、属性、方法和其他成员，就好像它们的代码是源文件的一部分一样。

> [!NOTE]
> .NET 类库中的大多数程序集都是自动引用的。 如果系统程序集不是自动引用的，则对于 .NET Core，可以添加对包含该程序集的 NuGet 包的引用。 请使用 Visual Studio 中的 NuGet 包管理器，或者将程序集的 [\<PackageReference>](../../core/tools/dependencies.md#the-packagereference-element) 元素添加到 .csproj 或 .vbproj 项目 。 在 .NET Framework 中，可以通过在 Visual Studio 中使用“添加引用”对话框，或者通过使用 [C#](../../csharp/language-reference/compiler-options/inputs.md#references) 或 [Visual Basic](../../visual-basic/reference/command-line-compiler/reference.md) 编译器的 `-reference` 命令行选项，添加对该程序集的引用。

在 C# 中，可以在单个应用程序中使用同一程序集的两个版本。 有关详细信息，请参阅[外部别名](../../csharp/language-reference/keywords/extern-alias.md)。

## <a name="related-content"></a>相关的内容

|Title|描述|
|-----------|-----------------|
|[程序集内容](contents.md)|组成程序集的元素。|
|[程序集清单](manifest.md)|程序集清单中的数据，以及它如何存储在程序集中。|
|[全局程序集缓存](../../framework/app-domains/gac.md)|GAC 如何存储和使用程序集。|
|[具有强名称的程序集](strong-named.md)|具有强名称的程序集的特征。|
|[程序集安全注意事项](security-considerations.md)|安全性如何作用于程序集。|
|[程序集版本控制](versioning.md)|.NET Framework 版本控制策略的概述。|
|[程序集位置](../../framework/app-domains/assembly-placement.md)|在何处可以找到程序集。|
|[程序集和并行执行](side-by-side-execution.md)|同时使用多个版本的运行时或程序集。|
|[发出动态方法和程序集](../../framework/reflection-and-codedom/emitting-dynamic-methods-and-assemblies.md)|如何创建动态程序集。|
|[运行时如何定位程序集](../../framework/deployment/how-the-runtime-locates-assemblies.md)|.NET Framework 如何在运行时解析程序集引用。|

## <a name="reference"></a>参考

<xref:System.Reflection.Assembly?displayProperty=nameWithType>

## <a name="see-also"></a>请参阅

- [.NET 程序集文件格式](file-format.md)
- [友元程序集](friend.md)
- [引用程序集](reference-assemblies.md)
- [如何：加载和卸载程序集](load-unload.md)
- [如何：在 .NET Core 中使用和调试程序集可卸载性](unloadability.md)
- [如何：确定文件是否为程序集](identify.md)
- [如何：使用 MetadataLoadContext 检查程序集内容](inspect-contents-using-metadataloadcontext.md)

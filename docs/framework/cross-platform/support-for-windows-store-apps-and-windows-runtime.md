---
description: 详细了解：.NET Framework 对 Microsoft Store 应用和 Windows 运行时的支持
title: .NET Framework 对 Microsoft Store 应用和 Windows 运行时的支持
ms.date: 03/30/2017
helpviewer_keywords:
- Windows Store apps, .NET Framework support for
- Windows Runtime, .NET Framework support for
- .NET for Windows Store apps
- .NET Framework, and Windows Store apps
- .NET Framework, and Windows Runtime
ms.assetid: 6fa7d044-ae12-4c54-b8ee-50915607a565
ms.openlocfilehash: 851deb30420eb19c4fe2037bebe2cf8f84743c49
ms.sourcegitcommit: ddf7edb67715a5b9a45e3dd44536dabc153c1de0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "102402226"
---
# <a name="net-framework-support-for-microsoft-store-apps-and-windows-runtime"></a>.NET Framework 对 Microsoft Store 应用和 Windows 运行时的支持

.NET Framework 4.5 支持基于 Windows 运行时的许多软件开发方案。 这些方案分为三类：

- 使用 XAML 控件开发 Windows 8.x 应用商店应用，如[使用 C# 或 Visual Basic 的 Windows Store 应用的路线图](/previous-versions/windows/apps/br229583(v=win.10))、[操作说明 (XAML)](/previous-versions/windows/apps/br229566(v=win.10)) 和[适用于 Windows Store 应用的 .NET 概述](/previous-versions/windows/apps/br230302(v=vs.140))中所述。

- 开发类库以用于通过 .NET Framework 开发的 Windows 8.x 应用商店应用。

- 开发以 .WinMD 文件打包的 Windows 运行时组件，这类文件可由任何支持 Windows 运行时的编程语言使用。 例如，请参阅[用 C# 和 Visual Basic 创建 Windows 运行时组件](/windows/uwp/winrt-components/creating-windows-runtime-components-in-csharp-and-visual-basic)。

本文概述 .NET Framework 为所有这三个类别提供的支持，并介绍用于 Windows 运行时组件的方案。 第一部分包括有关 .NET Framework 和 Windows 运行时之间的关系的基本信息，并阐释了在帮助系统和 IDE 中可能遇到的一些问题。 [第二部分](#WindowsRuntimeComponents)讨论 Windows 运行时组件的开发方案。

## <a name="the-basics"></a>基础知识

.NET Framework 提供适用于 Windows 8.x 应用商店应用的 .NET，并支持 Windows 运行时本身，因此支持前面列出的三个开发方案。

- [.NET Framework 和 Windows 运行时命名空间](/previous-versions/windows/apps/br230302(v=vs.140)#net-framework-and-windows-runtime-namespaces)提供了 .NET Framework 类库的简化视图，并只包含可用于创建 Windows 8.x 应用商店应用和 Windows 运行时组件的类型和成员。

  - 使用 Visual Studio（Visual Studio 2012 或更高版本）开发 Windows 8.x 应用商店应用或 Windows 运行时组件时，一组引用程序集可确保只显示相关类型和成员。

  - 通过移除 .NET Framework 中的重复功能或移除重复的 Windows 运行时功能，这个简化的 API 集得到进一步简化。 例如，它仅包含集合类型的泛型版本，并且 XML 文档对象模型被消除以支持 Windows 运行时 XML API 集。

  - 只包装操作系统 API 的功能也被移除，因为 Windows 运行时易于从托管代码调用。

  若要详细了解适用于 Windows 8.x 应用商店应用的 .NET，请参阅[适用于 Windows Store 应用的 .NET 概述](/previous-versions/windows/apps/br230302(v=vs.140))。 若要了解 API 选择过程，请参阅 .NET 博客中的博文[适用于 Metro 风格应用的 .NET](https://devblogs.microsoft.com/dotnet/net-for-metro-style-apps/)。

- [Windows 运行时](/uwp/api/)提供用于生成 Windows 8.x 应用商店应用的用户界面元素，并提供对操作系统功能的访问。 与 .NET Framework 相似，Windows 运行时有一些可以让 C# 和 Visual Basic 编译器按照它们使用 .NET Framework 类库的方式来使用 Windows 运行时的元数据。 .NET Framework 将某些差异隐藏起来，令使用 Windows 运行时变得更简单：

  - 隐藏 .NET Framework 和 Windows 运行时之间在编程模式方面的一些差异，如添加和移除事件处理程序的模式。 只需使用 .NET Framework 模式即可。

  - 隐藏在常用类型上存在一些差异（例如，基元类型和集合）。 只需使用 .NET Framework 类型即可，如本文后面的[在 IDE 中可见的差异](#DifferencesVisibleInIDE)所述。

大多数情况下，.NET Framework 对 Windows 运行时的支持是透明的。 下一节介绍托管代码和 Windows 运行时之间的一些明显差异。

<a name="AboutReferenceDocumentation"></a>

### <a name="the-net-framework-and-the-windows-runtime-reference-documentation"></a>.NET Framework 和 Windows 运行时参考文档

Windows 运行时和 .NET Framework 文档集相互独立。 如果按 F1 显示关于类型或成员的帮助，将显示相应集合中的参考文档。 但是，如果浏览 [Windows 运行时参考](/uwp/api/)，可能会遇到一些令人困惑的情况，示例如下：

- <xref:Windows.Foundation.Collections.IIterable%601> 接口等主题没有 Visual Basic 或 C# 的声明语法。 相反，注释显示在语法部分上方（在这种情况下，即“.NET：此接口显示为 System.Collections.Generic.IEnumerable\<T>”）。 这是因为，.NET Framework 和 Windows 运行时以不同的接口提供类似的功能。 此外，它们还有下列行为差异：`IIterable` 有一个 `First` 方法，而没有返回枚举器的 <xref:System.Collections.Generic.IEnumerable%601.GetEnumerator%2A> 方法。 .NET Framework 并不强制你了解执行常规任务的另一种方法，而是让托管代码看上去是在使用你熟悉的类型，通过这种方式来支持 Windows 运行时。 在 IDE 中，你将看不到 `IIterable` 接口，因此你在 Windows 运行时参考文档中遇到它的唯一方式是直接浏览该文档。

- <xref:Windows.Web.Syndication.SyndicationFeed.%23ctor(System.String,System.String,Windows.Foundation.Uri)> 文档阐释了一个紧密相关的问题：对不同语言显示不同的参数类型。 对于 C# 和 Visual Basic，参数类型为 <xref:System.String?displayProperty=nameWithType> 和 <xref:System.Uri?displayProperty=nameWithType>。 同样，这是因为 .NET Framework 具有自己的 `String` 和 `Uri` 类型，并且，对于这种常用的类型，不宜强制 .NET Framework 用户了解执行操作的不同方式。 在 IDE 中，.NET Framework 隐藏相应的 Windows 运行时类型。

- 在少数情况下，例如 <xref:Windows.UI.Xaml.GridLength> 结构，.NET Framework 提供名称相同但功能更多的类型。 例如，有些构造函数和属性主题与 `GridLength` 相关，但是其语法部分仅适用于 Visual Basic 和 C#，因为成员仅在托管代码时是可用的。 在 Windows 运行时中，结构只有字段。 Windows 运行时结构需要帮助程序类 <xref:Windows.UI.Xaml.GridLengthHelper> 来提供等效功能。 当你在编写托管代码时，不会在 IDE 中看到该帮助器类。

- 在 IDE 中，Windows 运行时类型看上去是从 <xref:System.Object?displayProperty=nameWithType> 派生的。 它们看上去拥有从 <xref:System.Object> 继承的成员，例如 <xref:System.Object.ToString%2A?displayProperty=nameWithType>。 如果这些类型的确继承自 <xref:System.Object> 并且 Windows 运行时类型可强制转换为 <xref:System.Object>，则这些成员可正常工作。 此功能是 .NET Framework 为 Windows 运行时提供的支持中的一部分。 但是，如果你在 Windows 运行时参考文档中查看该类型，则不会出现此类成员。 <xref:System.Object?displayProperty=nameWithType> 参考文档为这些明显继承的成员提供了文档。

<a name="DifferencesVisibleInIDE"></a>

### <a name="differences-that-are-visible-in-the-ide"></a>可在 IDE 中看到的差异

在更高级的编程方案中，如使用由 C# 编写的 Windows 运行时组件为使用 JavaScript 面向 Windows 生成的 Windows 8.x 应用商店应用提供应用程序逻辑，这种差异在 IDE 中以及文档中都是一目了然的。 当组件返回 `IDictionary<int, string>` 至 JavaScript 时，如果你在 JavaScript 调试器中查看它，将会看到 `IMap<int, string>` 方法，因为 JavaScript 使用 Windows 运行时类型。 下表显示了这两种语言中以不同方式显示的一些常用的集合类型：

|Windows 运行时类型|对应的 .NET Framework 类型|
|--------------------------------------------------------------|---------------------------------------|
|`IIterable<T>`|`IEnumerable<T>`|
|`IIterator<T>`|`IEnumerator<T>`|
|`IVector<T>`|`IList<T>`|
|`IVectorView<T>`|`IReadOnlyList<T>`|
|`IMap<K, V>`|`IDictionary<TKey, TValue>`|
|`IMapView<K, V>`|`IReadOnlyDictionary<TKey, TValue>`|
|`IBindableIterable`|`IEnumerable`|
|`IBindableVector`|`IList`|
|`Windows.UI.Xaml.Data.INotifyPropertyChanged`|`System.ComponentModel.INotifyPropertyChanged`|
|`Windows.UI.Xaml.Data.PropertyChangedEventHandler`|`System.ComponentModel.PropertyChangedEventHandler`|
|`Windows.UI.Xaml.Data.PropertyChangedEventArgs`|`System.ComponentModel.PropertyChangedEventArgs`|

在 Windows 运行时中，使用 `IKeyValuePair` 来循环访问 `IMap<K, V>` 和 `IMapView<K, V>`。 在将它们传递给托管代码时，它们显示为 `IDictionary<TKey, TValue>` 和 `IReadOnlyDictionary<TKey, TValue>`，因此自然要使用 `System.Collections.Generic.KeyValuePair<TKey, TValue>` 来枚举它们。

接口在托管代码中的显示方式将影响实现这些接口的类型的显示方式。 例如， `PropertySet` 类实现 `IMap<K, V>`，后者在托管代码中显示为 `IDictionary<TKey, TValue>`。 `PropertySet` 显示为已实现 `IDictionary<TKey, TValue>` 而不是 `IMap<K, V>`，因此在托管代码中，它显示为具有 `Add` 方法，其行为类似于 .NET Framework 字典中的 `Add` 方法。 它不会显示为具有 `Insert` 方法。

若要深入了解如何使用 .NET Framework 创建 Windows 运行时组件，并了解演示如何将这种组件与 JavaScript 一起使用的演练，请参阅[用 C# 和 Visual Basic 创建 Windows 运行时组件](/windows/uwp/winrt-components/creating-windows-runtime-components-in-csharp-and-visual-basic)。

### <a name="primitive-types"></a>基元类型

为了能够在托管代码中自然使用 Windows 运行时，代码中显示 .NET Framework 基元类型而非 Windows 运行时基元类型。 在 .NET Framework 中，基元类型（如 `Int32` 结构）具有许多有用的属性和方法，如 `Int32.TryParse` 方法。 相反，Windows 运行时中的基元类型和结构只有字段。 在托管代码中使用基元时，它们将显示为 .NET Framework 类型，您可以如往常一样使用 .NET Framework 类型的属性和方法。 以下列表提供了一个摘要：

- 对于 Windows 运行时基元 `Int32`、`Int64`、`Single`、`Double`、`Boolean`、`String`（Unicode 字符的不可变集合）、`Enum`、`UInt32`、`UInt64` 和 `Guid`，请使用 `System` 命名空间中的同名类型。

- 对于 `UInt8`，请使用 `System.Byte`。

- 对于 `Char16`，请使用 `System.Char`。

- 对于 `IInspectable` 接口，使用 `System.Object`。

- 对于 `HRESULT`，使用包含一个 `System.Int32` 成员的结构。

与接口类型一样，若要看到此表示形式的证据，唯一的情形可能是，.NET Framework 项目是一个 Windows 运行时组件，该组件由使用 JavaScript 生成的 Windows 8.x 应用商店应用使用。

在托管代码和 .NET Framework 中显示相同的其他基本常用 Windows 运行时类型包括 `Windows.Foundation.DateTime` 结构（在托管代码显示为 <xref:System.DateTimeOffset?displayProperty=nameWithType> 结构）和 `Windows.Foundation.TimeSpan` 结构（在托管代码显示为 <xref:System.TimeSpan?displayProperty=nameWithType> 结构）。

### <a name="other-differences"></a>其他差异

在少数情况下，由于代码中显示 .NET Framework 类型而非 Windows 运行时类型，某些操作需要由你来执行。 例如，<xref:Windows.Foundation.Uri?displayProperty=nameWithType> 类在 .NET Framework 代码中显示为 <xref:System.Uri?displayProperty=nameWithType>。 <xref:System.Uri?displayProperty=nameWithType> 允许使用相对 URI，但 <xref:Windows.Foundation.Uri?displayProperty=nameWithType> 需要绝对 URI。 因此，在将 URI 传递给 Windows 运行时方法时，必须确保它是绝对 URI。 请参阅 [将 URI 传递给 Windows 运行时](passing-a-uri-to-the-windows-runtime.md)。

<a name="WindowsRuntimeComponents"></a>

## <a name="scenarios-for-developing-windows-runtime-components"></a>开发的 Windows 运行时组件的方案

托管 Windows 运行时组件支持的方案取决于下列一般原则：

- 使用 .NET Framework 生成的 Windows 运行时组件与其他 Windows 运行时库没有明显的差异。 例如，如果使用托管代码重新实现一个本机 Windows 运行时组件，这两个组件表面上是无法区分的。 在托管代码中编写的组件对使用它的代码来说是不可见的，即使该代码本身是托管代码。 但是，在内部，组件是真正的托管代码并运行在公共语言运行时 (CLR) 上。

- 组件可以包含实现应用程序逻辑和/或 Windows 8.x 应用商店 UI 控件的类型。

  > [!NOTE]
  > 最好将 UI 元素与应用程序逻辑分离开来。 此外，在使用 JavaScript 和 HTML 为 Windows 生成的 Windows 8.x 应用商店应用中，不能使用 Windows 8.x 应用商店 UI 控件。

- 组件可以是用于 Windows 8.x 应用商店应用的 Visual Studio 解决方案中的一个项目，也可以是一个可添加到多个解决方案中的可重用组件。

  > [!NOTE]
  > 如果你的组件将仅用于 C# 或 Visual Basic，那么没有必要使其成为 Windows 运行时组件。 如果要使其成为一个普通的 .NET Framework 类库，不必将其公共 API 图面限制为 Windows 运行时类型。

- 通过使用 Windows 运行时 <xref:Windows.Foundation.Metadata.VersionAttribute> 属性标识不同版本中添加的类型（以及类型中的成员），可发布可重用组件的版本。

- 组件中的类型可以从 Windows 运行时类型派生。 控件可以派生自 <xref:Windows.UI.Xaml.Controls.Primitives> 命名空间中的基元控件类型，也可以派生自更为成熟的控件（如 <xref:Windows.UI.Xaml.Controls.Button>）。

  > [!IMPORTANT]
  > 从 Windows 8 和 .NET Framework 4.5 开始，托管 Windows 运行时组件中的所有公共类型都必须是密封的。 另一个 Windows 运行时组件中的类型无法从它们派生。 如果要在组件中提供多态行为，可以创建一个接口并在多态类型中实现它。

- 组件中公共类型的所有参数和返回类型都必须是 Windows 运行时类型（包括组件定义的 Windows 运行时类型）。

以下部分提供常见方案的示例。

### <a name="application-logic-for-a-windows-8x-store-app-with-javascript"></a>使用 JavaScript 生成的 Windows 8.x 应用商店应用的应用程序逻辑

在使用 JavaScript 开发面向 Windows 的 Windows 8.x 应用商店应用时，你可能会发现，应用程序逻辑的某些部分在托管代码中性能更佳或更易于开发。 JavaScript 不能直接使用 .NET Framework 类库，但是，您可以使将类库包装为 .WinMD 文件。 在此方案中，Windows 运行时组件是应用中不可分割的组成部分，因此，不宜提供版本特性。

### <a name="reusable-windows-8x-store-ui-controls"></a>可重用的 Windows 8.x 应用商店 UI 控件

对于一组相关的 UI 控件，可打包为可重用的 Windows 运行时组件。 该组件可以独立销售，或作为您创建的应用元素使用。 在此方案中，有必要使用 Windows 运行时 <xref:Windows.Foundation.Metadata.VersionAttribute> 属性增强兼容性。

### <a name="reusable-application-logic-from-existing-net-framework-apps"></a>来自现有 .NET Framework 应用的可重用的应用程序逻辑

对于现有桌面应用中的托管代码，可打包为一个独立的 Windows 运行时组件。 这样即可将该组件用于使用 C++ 或 JavaScript 生成的 Windows 8.x 应用商店应用，以及使用 C# 或 Visual Basic 生成的 Windows 8.x 应用商店应用。 如果代码有多个可重用的方案，可选择进行版本控制。

## <a name="related-topics"></a>相关主题

|Title|说明|
|-----------|-----------------|
|[适用于 Microsoft Store 应用的 .NET 概述](/previous-versions/windows/apps/br230302(v=vs.140))|描述可用于创建 Windows 8.x 应用商店应用和 Windows 运行时组件的 .NET Framework 类型和成员。 （位于 Windows 开发中心。）|
|[使用 C# 或 Visual Basic 的 Windows 运行时应用的路线图](/previous-versions/windows/apps/br229583(v=win.10))|提供关键资源来帮助你开始使用 C# 或 Visual Basic 开发 Windows 8.x 应用商店应用，这些资源包括许多快速入门主题、指南和最佳做法。 （位于 Windows 开发中心。）|
|[操作说明 (XAML)](/previous-versions/windows/apps/br229566(v=win.10))|提供关键资源来帮助你开始使用 C# 或 Visual Basic 开发 Windows 8.x 应用商店应用，这些资源包括许多快速入门主题、指南和最佳做法。 （位于 Windows 开发中心。）|
|[用 C# 和 Visual Basic 创建 Windows 运行时组件](/windows/uwp/winrt-components/creating-windows-runtime-components-in-csharp-and-visual-basic)|描述如何使用 .NET Framework 创建 Windows 运行时组件，说明如何在使用 JavaScript 面向 Windows 生成的 Windows 8.x 应用商店应用中使用该组件，并描述如何使用 Visual Studio 调试这一组合。 （位于 Windows 开发中心。）|
|[Windows 运行时参考](/uwp/api/)|Windows 运行时的参考文档。 （位于 Windows 开发中心。）|
|[向 Windows 运行时传递 URI](passing-a-uri-to-the-windows-runtime.md)|描述将托管代码中的 URI 传递给 Windows 运行时时可能出现的问题，以及如何避免这一问题。|

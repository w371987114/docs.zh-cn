---
description: 详细了解：如何将可移植类库与模型-视图-视图模型配合使用
title: 将可移植类库与模型-视图-视图模型配合使用
ms.date: 07/18/2018
dev_langs:
- csharp
- vb
helpviewer_keywords:
- Portable Class Library [.NET Framework], and MVVM
- MVVM, and Portable Class Library
ms.assetid: 41a0b9f8-15a2-431a-bc35-e310b2953b03
ms.openlocfilehash: 4eea099aaa515c2b7d9874fd6c6d5c4132c5e7a2
ms.sourcegitcommit: ddf7edb67715a5b9a45e3dd44536dabc153c1de0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "102402224"
---
# <a name="using-portable-class-library-with-model-view-view-model"></a>将可移植类库与模型-视图-视图模型配合使用

你可以使用 .NET Framework [可移植类库](portable-class-library.md)来实现模型-视图-视图模型 (MVVM) 模式，并在多个平台之间共享程序集。

[!INCLUDE[standard](../../../includes/pcl-to-standard.md)]

 MVVM 是将用户界面与基础业务逻辑相隔离的应用程序模式。 你可以在 Visual Studio 2012 中的可移植类库项目内实现模型和视图模型类，然后创建针对不同平台自定义的视图。 通过此方法，只需编写数据模型和业务逻辑一次，即可将该代码用于 .NET Framework、Silverlight、Windows Phone 和 Windows 8.x 应用商店应用，如下图所示。

 ![展示将可移植类库与 MVVM 配合使用，并在多个平台之间共享程序集。](./media/using-portable-class-library-with-model-view-view-model/mvvm-share-assemblies-across-platforms.png)

 本主题不提供有关 MVVM 模式的一般信息。 只介绍如何使用可移植类库来实现 MVVM。 有关 MVVM 的详细信息，请参阅[使用适用于 WPF 的 Prism 库 5.0 的 MVVM 快速入门](/previous-versions/msp-n-p/gg430857(v=pandp.40))。

## <a name="classes-that-support-mvvm"></a>支持 MVVM 的类

 当你将 .NET Framework 4.5、适用于 Windows 8.x 应用商店应用的 .NET、Silverlight 或 Windows Phone 7.5 作为可移植类库项目的目标时，可使用以下类实现 MVVM 模式：

- <xref:System.Collections.ObjectModel.ObservableCollection%601?displayProperty=nameWithType> 类

- <xref:System.Collections.ObjectModel.ReadOnlyObservableCollection%601?displayProperty=nameWithType> 类

- <xref:System.Collections.Specialized.INotifyCollectionChanged?displayProperty=nameWithType> 类

- <xref:System.Collections.Specialized.NotifyCollectionChangedAction?displayProperty=nameWithType> 类

- <xref:System.Collections.Specialized.NotifyCollectionChangedEventArgs?displayProperty=nameWithType> 类

- <xref:System.Collections.Specialized.NotifyCollectionChangedEventHandler?displayProperty=nameWithType> 类

- <xref:System.ComponentModel.DataErrorsChangedEventArgs?displayProperty=nameWithType> 类

- <xref:System.ComponentModel.INotifyDataErrorInfo?displayProperty=nameWithType> 类

- <xref:System.ComponentModel.INotifyPropertyChanged?displayProperty=nameWithType> 类

- <xref:System.Windows.Input.ICommand?displayProperty=nameWithType> 类

- <xref:System.ComponentModel.DataAnnotations?displayProperty=nameWithType> 命名空间中的所有类

## <a name="implementing-mvvm"></a>实现 MVVM

 要实现 MVVM，通常需要在可移植类库项目中同时创建模型和视图模型，因为可移植类库项目不能引用不可移植的项目。 模型和视图模型可以在同一个项目中，也可以在不同的项目中。 如果使用不同的项目，请将视图模型项目中的引用添加到模型项目中。

 编译模型和视图模型项目后，在包含视图的应用中引用这些程序集。 如果视图仅与视图模型交互，你就只需要引用包含视图模型的程序集。

### <a name="model"></a>建模

 下面的示例展示了可在可移植类库项目中驻留的一个简化的模型类。

 [!code-csharp[PortableClassLibraryMVVM#1](../../../samples/snippets/csharp/VS_Snippets_CLR/portableclasslibrarymvvm/cs/customer.cs#1)]
 [!code-vb[PortableClassLibraryMVVM#1](../../../samples/snippets/visualbasic/VS_Snippets_CLR/portableclasslibrarymvvm/vb/customer.vb#1)]

 下面的示例展示了在可移植类库项目中填充、检索和更新数据的一种简单的方法。 在实际应用中，你将从 Windows Communication Foundation (WCF) 服务等源中检索数据。

 [!code-csharp[PortableClassLibraryMVVM#2](../../../samples/snippets/csharp/VS_Snippets_CLR/portableclasslibrarymvvm/cs/customerrepository.cs#2)]
 [!code-vb[PortableClassLibraryMVVM#2](../../../samples/snippets/visualbasic/VS_Snippets_CLR/portableclasslibrarymvvm/vb/customerrepository.vb#2)]

### <a name="view-model"></a>视图模型

 在实现 MVVM 模式时，经常会添加一个视图模型的基类。 下面的示例展示了一个基类。

 [!code-csharp[PortableClassLibraryMVVM#3](../../../samples/snippets/csharp/VS_Snippets_CLR/portableclasslibrarymvvm/cs/viewmodelbase.cs#3)]
 [!code-vb[PortableClassLibraryMVVM#3](../../../samples/snippets/visualbasic/VS_Snippets_CLR/portableclasslibrarymvvm/vb/viewmodelbase.vb#3)]

 <xref:System.Windows.Input.ICommand> 接口的实现经常与 MVVM 模式配合使用。 下面的示例演示 <xref:System.Windows.Input.ICommand> 接口的实现。

 [!code-csharp[PortableClassLibraryMVVM#4](../../../samples/snippets/csharp/VS_Snippets_CLR/portableclasslibrarymvvm/cs/relaycommand.cs#4)]
 [!code-vb[PortableClassLibraryMVVM#4](../../../samples/snippets/visualbasic/VS_Snippets_CLR/portableclasslibrarymvvm/vb/relaycommand.vb#4)]

 下面的示例展示了一个简化的视图模型。

 [!code-csharp[PortableClassLibraryMVVM#5](../../../samples/snippets/csharp/VS_Snippets_CLR/portableclasslibrarymvvm/cs/mainpageviewmodel.cs#5)]
 [!code-vb[PortableClassLibraryMVVM#5](../../../samples/snippets/visualbasic/VS_Snippets_CLR/portableclasslibrarymvvm/vb/customerviewmodel.vb#5)]  
  
### <a name="view"></a>查看  

 从 .NET Framework 4.5 应用、Windows 8.x 应用商店应用、基于 Silverlight 的应用或 Windows Phone 7.5 应用中，可以引用包含模型和视图模型项目的程序集。  然后创建一个与视图模型交互的视图。 下面的示例展示了一个简化的 Windows Presentation Foundation (WPF) 应用，此应用从视图模型检索和更新数据。 你可以在 Silverlight、Windows Phone 或 Windows 8.x 应用商店应用中创建类似的视图。  
  
 [!code-xaml[PortableClassLibraryMVVM#6](../../../samples/snippets/csharp/VS_Snippets_CLR/portableclasslibrarymvvm/cs/mainwindow.xaml#6)]  
  
## <a name="see-also"></a>另请参阅

- [可移植类库](portable-class-library.md)

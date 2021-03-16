---
description: 详细了解：向 Windows 运行时传递 URI
title: 向 Windows 运行时传递 URI
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- Windows Runtime, .NET Framework support for
- Windows Runtime, passing a URI to
ms.assetid: 3eb5ce6f-f304-4f87-8e81-0f25092f5ad4
ms.openlocfilehash: 918f8ea71f4b23aeaf2a1295f2edd043a73a95ed
ms.sourcegitcommit: ddf7edb67715a5b9a45e3dd44536dabc153c1de0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "102402229"
---
# <a name="passing-a-uri-to-the-windows-runtime"></a>向 Windows 运行时传递 URI

Windows 运行时方法只接受绝对 URI。 如果向 Windows 运行时方法传递相对的 URI，则会引发 <xref:System.ArgumentException> 异常。 原因是当你在 .NET Framework 代码中使用 Windows 运行时，<xref:Windows.Foundation.Uri?displayProperty=nameWithType> 类会显示为 Intellisense 中的 <xref:System.Uri?displayProperty=nameWithType>。 <xref:System.Uri?displayProperty=nameWithType> 类允许相对 URI，但 <xref:Windows.Foundation.Uri?displayProperty=nameWithType> 类不允许。 这也适用于 Windows 运行时组件中公开的方法。 如果组件公开接收 URI 的方法，则代码中的签名包含 <xref:System.Uri?displayProperty=nameWithType>。 但对于组件的用户来说，签名包含 <xref:Windows.Foundation.Uri?displayProperty=nameWithType>。 传递给组件的 URI 必须是绝对 URI。  
  
本主题演示了如何检测绝对 URI 以及如何在引用应用包中的资源时创建一个。  
  
## <a name="detecting-and-using-an-absolute-uri"></a>检测和使用绝对 URI  

使用 <xref:System.Uri.IsAbsoluteUri%2A?displayProperty=nameWithType> 属性确保在将 URI 传递给 Windows 运行时之前，该 URI 是绝对的。 使用此属性比捕获和处理 <xref:System.ArgumentException> 异常更为有效。  
  
## <a name="using-an-absolute-uri-for-a-resource-in-the-app-package"></a>将绝对 URI 用于应用包中的资源  

如果想要为应用包包含的资源指定 URI，可以使用 `ms-appx` 或 `ms-appx-web` 方案来创建绝对 URI。  
  
下面的示例演示如何使用 XAML 和代码将 <xref:Windows.UI.Xaml.Controls.WebView> 控件的 <xref:Windows.UI.Xaml.Controls.WebView.Source%2A> 属性和 <xref:Windows.UI.Xaml.Controls.Image> 控件的 <xref:Windows.UI.Xaml.Controls.Image.Source%2A> 属性设置为包含在“Pages”文件夹中的资源。  
  
[!code-xaml[System.URIToWindowsURI#1](../../../samples/snippets/csharp/VS_Snippets_CLR_System/system.uritowindowsuri/cs/mainpage.xaml#1)]  
[!code-csharp[System.URIToWindowsURI#2](../../../samples/snippets/csharp/VS_Snippets_CLR_System/system.uritowindowsuri/cs/mainpage.xaml.cs#2)]
[!code-vb[System.URIToWindowsURI#2](../../../samples/snippets/visualbasic/VS_Snippets_CLR_System/system.uritowindowsuri/vb/mainpage.xaml.vb#2)]  
  
有关这些方案的详细信息，请参阅 Windows 开发人员中心中的 [URI 方案](/windows/uwp/app-resources/uri-schemes)。  
  
## <a name="see-also"></a>请参阅

- [.NET Framework 对 Windows 应用商店应用和 Windows 运行时的支持情况](support-for-windows-store-apps-and-windows-runtime.md)

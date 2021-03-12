---
title: 如何：通过 MMC 管理单元查看证书
description: 安全 WCF 客户端或服务可以使用证书作为凭据。 了解可以使用 MMC 插件检查的证书存储类型。
ms.date: 02/25/2019
helpviewer_keywords:
- certificates [WCF], viewing with the MMC snap-in
ms.assetid: 2b8782aa-ebb4-4ee7-974b-90299e356dc5
ms.openlocfilehash: 788161db2e38dc942b0c638128f8599b0436c8fd
ms.sourcegitcommit: 46cfed35d79d70e08c313b9c664c7e76babab39e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102604575"
---
# <a name="how-to-view-certificates-with-the-mmc-snap-in"></a>如何：通过 MMC 管理单元查看证书

创建安全客户端或服务时，可以使用 [证书](working-with-certificates.md) 作为凭据。 例如，通用类型的凭据是使用方法创建的 x.509 证书 <xref:System.ServiceModel.Security.X509CertificateInitiatorClientCredential.SetCertificate%2A?displayProperty=nameWithType> 。

有三种不同类型的证书存储，你可以通过 Microsoft 管理控制台 (MMC) 在 Windows 系统上进行检查：

- 本地计算机：存储区是设备的本地存储区，并且是设备上所有用户的全局存储区。

- 当前用户：存储区是设备上当前用户帐户的本地存储。

- 服务帐户：存储是设备上特定服务的本地存储。

## <a name="view-certificates-in-the-mmc-snap-in"></a>在 MMC 管理单元中查看证书

下面的过程演示如何检查本地设备上的存储以查找适当的证书：
  
1. 从 "**开始**" 菜单中选择 "**运行**"，然后输入 *mmc*。

    随即出现 MMC。
  
2. 从 " **文件** " 菜单中，选择 " **添加/删除管理单元**"。

    此时将显示 " **添加或删除管理单元** " 窗口。
  
3. 从 " **可用的管理单元** " 列表中，选择 " **证书**"，然后选择 " **添加**"。  

    ![添加证书管理单元](./media/mmc-add-certificate-snap-in.png)
  
4. 在 " **证书管理单元** " 窗口中，选择 " **计算机帐户**"，然后选择 " **下一步**"。
  
    还可以选择 " **我的用户帐户** " 作为特定服务的当前用户或 **服务帐户** 。

    > [!NOTE]
    > 如果你不是设备的管理员，则只能为你的用户帐户管理证书。
  
5. 在 " **选择计算机** " 窗口中，选择 " **本地计算机** "，然后选择 " **完成**"。  
  
6. 在 " **添加或删除管理单元** " 窗口中，选择 **"确定"**。  
  
    ![突出显示 "确定" 按钮的 "添加或删除管理单元" 窗口](./media/mmc-certificate-snap-in-selected.png)

7. 可选：在 " **文件** " 菜单中，选择 " **保存** " 或 " **另存为** " 以保存 MMC 控制台文件以供以后使用。  

8. 若要在 MMC 管理单元中查看证书，请在左窗格中选择 " **控制台根** "，然后展开 " **证书 (本地计算机")**。

    将显示每种类型的证书的目录列表。 可以从每个证书目录查看、导出、导入和删除其证书。

## <a name="view-certificates-with-the-certificate-manager-tool"></a>用证书管理器工具查看证书

你还可以使用证书管理器工具查看、导出、导入和删除证书。

### <a name="to-view-certificates-for-the-local-device"></a>查看本地设备的证书

1. 从 "**开始**" 菜单中选择 "**运行**"，然后输入 *certlm.msc*。

    此时将显示本地设备的“证书管理器”工具。
  
2. 若要查看证书，请在左窗格中的 " **证书-本地计算机** " 下，展开要查看的证书类型的目录。

### <a name="to-view-certificates-for-the-current-user"></a>查看当前用户的证书

1. 从“开始”菜单中选择“运行”，然后输入“certmgr.msc”  。

    此时会显示当前用户的证书管理器工具。
  
2. 若要查看证书，请在左窗格中的 " **证书-当前用户** " 下，展开要查看的证书类型的目录。

## <a name="see-also"></a>另请参阅

- [使用证书](working-with-certificates.md)
- [如何：创建在开发期间使用的临时证书](how-to-create-temporary-certificates-for-use-during-development.md)
- [如何：检索证书的指纹](how-to-retrieve-the-thumbprint-of-a-certificate.md)

---
title: 在 Fedora 上安装 .NET - .NET
description: 演示在 Fedora 上安装 .NET SDK 和 .NET 运行时的各种方式。
author: adegeo
ms.author: adegeo
ms.date: 02/17/2021
ms.openlocfilehash: efaad4788db2200b1a47f9b4ae2414730588c6ae
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102255747"
---
# <a name="install-the-net-sdk-or-the-net-runtime-on-fedora"></a>在 Fedora 上安装 .NET SDK 或 .NET 运行时

.NET 在 Fedora 上受支持，本文就将介绍如何在 Fedora 上安装 .NET。 如果 Fedora 版本不受支持，则该版本不再支持 .NET。

[!INCLUDE [linux-intro-sdk-vs-runtime](includes/linux-intro-sdk-vs-runtime.md)]

有关在不使用包管理器的情况下安装 .NET 的详细信息，请参阅以下文章之一：

- [通过 Snap 安装 .NET SDK 或 .NET Runtime。](linux-snap.md)
- [使用脚本安装 .NET SDK 或 .NET Runtime。](linux-scripted-manual.md#scripted-install)
- [手动安装 .NET SDK 或 .NET Runtime。](linux-scripted-manual.md#manual-install)

## <a name="install-net-50"></a>安装 .NET 5.0

Fedora 的默认包存储库中提供的 .NET 最新版是 .NET 5.0。

[!INCLUDE [linux-dnf-install-50](includes/linux-install-50-dnf.md)]

## <a name="install-net-core-31"></a>安装 .NET Core 3.1

[!INCLUDE [linux-dnf-install-31](includes/linux-install-31-dnf.md)]

## <a name="supported-distributions"></a>支持的发行版

下表列出了当前支持的 .NET 版本以及支持它们的 Fedora 版本。 这些版本在 [.NET 版本达到支持终止日期](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)或 [Fedora 版本达到生命周期](https://fedoraproject.org/wiki/End_of_life)之前仍受支持。

- ✔️ 指示 Fedora 或 .NET 版本仍受支持。
- ❌ 指示 Fedora 或 .NET 版本在该 Fedora 版本上不受支持。
- 当 Fedora 版本和 .NET 版本都有 ✔️ 时，将支持该 OS 和 .NET 组合。

| .NET 版本  | Fedora 33 ✔️ | 32 ✔️ | 31 ❌ | 30 ❌ | 29 ❌ | 28 ❌ | 27 ❌ |
| ------------  | ---------: | --: | --: | --: | --: | --: | --: |
| .NET 5.0      | ✔️        | ✔️ | ❌|❌ |❌ |❌  |❌ |
| .NET Core 3.1 | ✔️        | ✔️ | ✔️|✔️ |✔️ |❌  |❌ |
| .NET Core 2.1 | ✔️        | ✔️ | ✔️|✔️ |✔️ |✔️  |✔️ |

以下 .NET 版本不再受到支持。 这些版本的下载仍保持发布状态：

- 3.0
- 2.2
- 2.0

## <a name="remove-preview-versions"></a>删除预览版本

[!INCLUDE [package-manager uninstall notice](./includes/linux-uninstall-preview-info.md)]

## <a name="dependencies"></a>依赖项

[!INCLUDE [linux-rpm-install-dependencies](includes/linux-rpm-install-dependencies.md)]

## <a name="install-on-older-distributions"></a>在早期的发行版上进行安装

Fedora 的早期版本的默认包存储库中并不包含 .NET Core。 可使用 [snap](linux-snap.md) 通过 [dotnet-install.sh 脚本](linux-scripted-manual.md#scripted-install)安装 .NET，或使用 Microsoft 的存储库安装 .NET：

01. 首先，将 Microsoft 签名密钥添加到受信任的密钥列表。

    ```bash
    sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
    ```

02. 接下来，添加 Microsoft 包存储库。 存储库的源基于你的 Fedora 版本。

    | Fedora 版本 | 包存储库 |
    | -------------- | ------- |
    | 31             | `https://packages.microsoft.com/config/fedora/31/prod.repo` |
    | 30             | `https://packages.microsoft.com/config/fedora/30/prod.repo` |
    | 29             | `https://packages.microsoft.com/config/fedora/29/prod.repo` |
    | 28             | `https://packages.microsoft.com/config/fedora/28/prod.repo` |
    | 27             | `https://packages.microsoft.com/config/fedora/27/prod.repo` |

    ```bash
    sudo wget -O /etc/yum.repos.d/microsoft-prod.repo https://packages.microsoft.com/config/fedora/31/prod.repo
    ```

[!INCLUDE [linux-dnf-install-31](./includes/linux-install-31-dnf.md)]

## <a name="how-to-install-other-versions"></a>如何安装其他版本

[!INCLUDE [package-manager-switcher](./includes/package-manager-heading-hack-pkgname.md)]

## <a name="troubleshoot-the-package-manager"></a>包管理器疑难解答

本部分提供有关使用包管理器安装 .NET 或 .NET Core 时可能会遇到的常见错误的信息。

### <a name="unable-to-find-package"></a>找不到包

有关在不使用包管理器的情况下安装 .NET 的详细信息，请参阅以下文章之一：

- [通过 Snap 安装 .NET SDK 或 .NET Runtime。](linux-snap.md)
- [使用脚本安装 .NET SDK 或 .NET Runtime。](linux-scripted-manual.md#scripted-install)
- [手动安装 .NET SDK 或 .NET Runtime。](linux-scripted-manual.md#manual-install)

### <a name="failed-to-fetch"></a>未能提取

[!INCLUDE [package-manager-failed-to-fetch-rpm](includes/package-manager-failed-to-fetch-rpm.md)]

## <a name="next-steps"></a>后续步骤

- [如何为 .NET CLI 启用 Tab 自动补全](../tools/enable-tab-autocomplete.md)
- [教程：使用 Visual Studio Code 通过 .NET SDK 创建控制台应用程序](../tutorials/with-visual-studio-code.md)

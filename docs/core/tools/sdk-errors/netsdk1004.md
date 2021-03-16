---
title: NETSDK1004：找不到资产文件
description: 了解在找不到 project.assets.json 文件时出现的 .NET SDK 错误 NETSDK1004。
ms.topic: error-reference
ms.date: 01/29/2021
f1_keywords:
- NETSDK1004
ms.openlocfilehash: aa01ff657143faac96baa5ae1133493516edfb1c
ms.sourcegitcommit: bdbf6472de867a0a11aaa5b9384a2506c24f27d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102206708"
---
# <a name="netsdk1004-assets-file-not-found"></a>NETSDK1004：找不到资产文件

本文适用于：✔️ .NET Core 2.1.100 SDK 及更高版本

NuGet 在“obj”文件夹中写入名为 project.assets.json 的文件，.NET SDK 使用该文件来获取有关要传递到编译器的包的信息 。 如果在生成过程中找不到资产文件 project.assets.json，则会发生此错误。 完整的错误消息类似于以下示例：

**NETSDK1004：找不到资产文件“C:\path\to\project.assets.json”。运行 NuGet 包还原以生成此文件。**

下面是一些可能导致此错误的原因：

* 你正在从包含 `%` 字符的目录路径运行 `dotnet build` 命令。 若要解决此错误，请从文件夹名称中删除 `%`，然后重新运行 `dotnet build`。
* 项目系统不会自动检测和还原对项目文件的更改。 若要解决此错误，请打开命令提示符并对项目运行 `dotnet restore`。
* 一个项目由更早版本的 Nuget.exe 单独还原。 若要解决此错误，请打开命令提示符并对项目运行 `dotnet restore`。
* 之前的错误，如 NETSDK1045（正在使用的 SDK 版本不支持项目的目标框架），会阻止 NuGet 创建项目资产文件。 若要解决 NETSDK1004 错误，请解决以前的错误，然后对该项目运行 `dotnet restore`。
* App Center CI 正在生成一个项目，该项目具有不在 NuGet 中的外部程序集。 若要解决此错误，请对程序集使用 NuGet 包。
* 你在 Visual Studio 中添加了一个名称开头是句点的解决方案文件夹。 若要解决此错误，请删除文件夹名称中的前导句点。

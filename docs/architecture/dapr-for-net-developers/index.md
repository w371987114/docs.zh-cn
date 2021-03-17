---
title: 面向 .NET 开发人员的 Dapr
description: 可帮助 .NET 开发人员了解并充分利用 Microsoft 开源分散式应用程序运行时的全部功能的指南。
author: robvet
ms.date: 02/17/2021
ms.openlocfilehash: 2ab66257cca6f99074e2aa45a24869012b4976fe
ms.sourcegitcommit: d623f686701b94bef905ec5e93d8b55d031c5d6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2021
ms.locfileid: "103623715"
---
# <a name="dapr-for-net-developers"></a>面向 .NET 开发人员的 Dapr

![封面图像](./media/cover.png)

**预览版**

发布者

Microsoft 开发人员部门、.NET 和 Azure Incubations 团队

Microsoft Corporation 的一个部门

One Microsoft Way

Redmond, Washington 98052-6399

版权所有 &copy; 2021 Microsoft Corporation

保留所有权利。 未经发布者书面许可，不得以任何形式或任何方式复制或传播本书中的任何内容。

本书“按原样”提供，表达作者的观点和看法。 本书中表达的观点、看法和信息（包括 URL 和其他 Internet 网站引用）如有更改，恕不另行通知。

本书中提及的一些示例仅用于说明，纯属虚构。 不存在任何实际关联或联系，请勿妄加推断。

Microsoft 和 <https://www.microsoft.com> 上“商标”网页列出的商标是 Microsoft 集团公司的商标。

Mac 和 macOS 是 Apple Inc. 的商标

Docker 的鲸鱼徽标是 Docker Inc. 的注册商标经许可方可使用。

所有其他标记和徽标均为其各自所有者的财产。

作者：

> Rob Vettor，Microsoft 首席云解决方案架构师 - [thinkingincloudnative.com](https://thinkingincloudnative.com/about/)
>
> Sander Molenkamp，[Info Support](https://www.infosupport.com/en/) 首席云架构师/Microsoft MVP - [sandermolenkamp.com](https://www.sandermolenkamp.com)
>
> Edwin van Wijk，[Info Support](https://www.infosupport.com/en/) 首席解决方案架构师/Microsoft MVP - [defaultconstructor.com](https://defaultconstructor.com)

参与者和审阅者：

> Mark Russinovich，Microsoft Azure Office of CTO 的 Azure CTO 兼技术研究员
>
> Nish Anil，Microsoft .NET 团队高级项目经理 
>
> Mark Fussell，Microsoft Azure Incubations 首席项目经理
>
> Yaron Schneider，Microsoft Azure Incubations 首席软件工程师
>
> Ori Zohar，Microsoft Azure Incubations 高级项目经理

编辑：

> David Pine，Microsoft .NET 团队资深内容开发人员
>
> Microsoft .NET 团队高级项目经理 Maira Wenzel 

## <a name="version"></a>Version

本指南已编写为涵盖 Dapr 1.0 版本。 .NET Core 示例基于 .NET Core 3.1。

## <a name="who-should-use-this-guide"></a>本指南的目标读者

本指南的受众主要是对学习如何构建为云设计的应用程序感兴趣的开发人员、开发主管和架构师。

次级受众是计划选择是否使用云本机方法构建其应用程序的技术决策者。

## <a name="how-you-can-use-this-guide"></a>如何使用本指南

本指南有 [PDF](https://aka.ms/dapr-ebook) 格式和在线版本。 欢迎随时将此文档或其在线版本的链接转发给你的团队，以确保对这些主题有共同的理解。 这些主题中的大多数都得益于对基本原则和模式的一致理解，以及与这些主题相关的决策所涉及的权衡。 本文档的目标是为团队及其领导提供所需的信息，使其能够为应用程序的体系结构、开发和托管作出明智的决策。

## <a name="send-your-feedback"></a>提供你的反馈

我们正在不断完善本书和相关示例，欢迎你提供反馈！ 如果对如何改进本书有任何建议，请使用 [GitHub 问题](https://github.com/dotnet/docs/issues)上任何页面底部的反馈部分进行反馈。

>[!div class="step-by-step"]
>[下一页](foreword.md)

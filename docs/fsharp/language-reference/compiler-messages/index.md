---
title: 编译器错误和警告
description: F# 编译器将发出的错误和警告的说明和解决方案
ms.date: 12/21/2019
ms.openlocfilehash: 9769ddee989f0774cfae8842e60dbd3fd2065f9c
ms.sourcegitcommit: e3cf8227573e13b8e1f4e3dc007404881cdafe47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103190172"
---
# <a name="f-compiler-messages"></a>F# 编译器消息

此部分详细介绍 F# 编译器将针对某些构造发出的编译器错误和警告。 可以通过以下方式更改默认错误集：

- 使用 `-warnaserror+` 编译器选项，将特定警告视为错误，

- 使用 `-nowarn` 编译器选项，忽略特定警告

如果此部分中尚未记录特定警告或错误：

- 请转到此页末尾，然后发送包含错误号或文本的反馈，或者
- 按照 [create-new-fsharp-compiler-message.fsx](https://github.com/dotnet/docs/blob/main/docs/fsharp/language-reference/compiler-messages/util/create-new-fsharp-compiler-message.fsx) 中的说明进行操作，并打开此存储库的拉取请求，自行添加。

## <a name="see-also"></a>另请参阅

- [F# 编译器选项](../compiler-options.md)

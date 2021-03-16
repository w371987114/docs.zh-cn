---
title: 中断性变更：Linux 上的 .NET 的默认 TLS 密码套件
description: 了解 .NET 5 中的中断性变更：在 Linux 上，.NET 现在执行 TLS/SSL 时，将遵循默认密码套件的 OpenSSL 配置。
ms.date: 10/16/2020
ms.openlocfilehash: c28207aa5bf4324a7921deb006c57fbd5e1d6f5f
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256811"
---
# <a name="default-tls-cipher-suites-for-net-on-linux"></a>Linux 上的 .NET 的默认 TLS 密码套件

现在，在 Linux 上，.NET 通过 <xref:System.Net.Security.SslStream> 类或更高级别的操作（如通过 <xref:System.Net.Http.HttpClient> 类的 HTTPS）执行 TLS/SSL 时，将遵循默认密码套件的 OpenSSL 配置。 如果未显式配置默认密码套件，则 Linux 上的 .NET 将使用严格限制的允许密码套件列表。

## <a name="change-description"></a>更改描述

在以前的 .NET 版本中，.NET 不遵循默认密码套件的系统配置。 Linux 上的 .NET 的默认密码套件列表非常宽松。

从 .NET 5 开始，若在 openssl.cnf 中指定了默认密码套件的 OpenSSL 配置，Linux 上的 .NET 会遵循该配置。 如果未显式配置密码套件，则唯一允许的密码套件如下：

- TLS 1.3 密码套件
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

由于此回退默认值不包括与 TLS 1.0 或 TLS 1.1 兼容的任何密码套件，因此默认情况下将有效禁用这些较旧的协议版本。

为特定会话的 SslStream 提供一个 CipherSuitePolicy 值仍将替换配置文件内容和/或 .NET 回退默认值。

## <a name="reason-for-change"></a>更改原因

在 Linux 上运行 .NET 的用户要求将 <xref:System.Net.Security.SslStream> 的默认配置更改为由第三方评估工具提供高安全等级的配置。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

与新式客户端或服务器通信时，新的默认值可能会生效。 如果需要扩展默认密码套件列表以接受旧客户端（或联系旧服务器），请指定 `CipherSuitePolicy` 值或更改 OpenSSL 配置文件。 在许多 Linux 发行版中，OpenSSL 配置文件位于 /etc/ssl/openssl.cnf。

此示例 openssl.cnf 文件是与 Linux 上的 .NET 5 及更高版本的默认密码套件策略等效的最小文件。 不要替换系统文件，而是将这些概念与系统中的文件合并。

```ini
openssl_conf = default_conf

[default_conf]
ssl_conf = ssl_sect

[ssl_sect]
system_default = system_default_sect

[system_default_sect]
CipherString = ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256
```

在 Red Hat Enterprise Linux、CentOS 和 Fedora 发行版上，.NET 应用程序默认使用系统范围的加密策略允许的密码套件。 在这些发行版上，使用加密策略配置，而不是 openssl.cnf。

## <a name="affected-apis"></a>受影响的 API

无法通过 API 分析检测到。

<!--

### Affected APIs

- Not detectible via API analysis.

### Category

- Cryptography
- Security

-->

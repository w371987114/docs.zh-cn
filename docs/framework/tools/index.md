---
title: .NET Framework 工具
description: 查看 .NET 工具的列表，你可使用这些工具更轻松地创建、部署和管理面向 .NET 的应用程序和组件。
ms.date: 03/30/2017
helpviewer_keywords:
- command line, .NET Framework tools
- .NET Framework, tools
- tools [.NET Framework]
- running .NET Framework tools
ms.assetid: a2ca532d-91f7-426a-9303-417c2ee1247c
ms.openlocfilehash: 606ead6010ddfd223e4ed38e0271642d36079bcc
ms.sourcegitcommit: 1dbe25ff484a02025d5c34146e517c236f7161fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654085"
---
# <a name="net-framework-tools"></a>.NET Framework 工具

您可以使用 .NET Framework 工具轻松创建、部署和管理面向 .NET Framework 的应用程序和组件。

此节中介绍的大部分 .NET Framework 工具将自动随 Visual Studio 一起安装。 Visual Studio 可从 [Visual Studio 下载](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)页面下载。

你可以从命令行运行除程序集缓存查看器 (Shfusion.dll) 之外的所有工具。 必须从文件资源管理器访问 Shfusion.dll  。
  
运行命令行工具的最佳方法是使用 Visual Studio 安装的其中一个开发人员 shell。 使用这些实用程序可以轻松运行工具，而不需要导航到安装文件夹。 有关详细信息，请参阅[开发人员命令提示和开发人员 PowerShell](/visualstudio/ide/reference/command-prompt-powershell)。

> [!NOTE]
> 某些工具特定于 32 位或 64 位计算机。 确保为你的计算机运行适当的工具版本。

## <a name="in-this-section"></a>本节内容

- [Al.exe（程序集链接器）](al-exe-assembly-linker.md)  
从模块或资源文件中生成一个具有程序集清单的文件。

- [Aximp.exe（Windows 窗体 ActiveX 控件导入程序）](aximp-exe-windows-forms-activex-control-importer.md)  
将 ActiveX 控件的 COM 类型库中的类型定义转换成 Windows 窗体控件。

- [Caspol.exe（代码访问安全策略工具）](caspol-exe-code-access-security-policy-tool.md)  
用于查看和配置计算机策略级别、用户策略级别和企业策略级别的安全策略。 在 .NET Framework 4 及更高版本中，此工具不会影响代码访问安全性 (CAS) 策略，除非 [\<legacyCasPolicy> 元素](../configure-apps/file-schema/runtime/netfx40-legacysecuritypolicy-element.md)设置为 `true`。 有关详细信息，请参阅[安全更改](/previous-versions/dotnet/framework/security/security-changes)。

- [Cert2spc.exe（软件发行者证书测试工具）](cert2spc-exe-software-publisher-certificate-test-tool.md)  
通过一个或多个 X.509 证书创建发行者证书 (SPC)。 此工具仅用于测试目的。

- [Certmgr.exe（证书管理器工具）](certmgr-exe-certificate-manager-tool.md)  
管理证书、证书信任列表 (CTL) 和证书吊销列表 (CRL)。

- [Clrver.exe（CLR 版本工具）](clrver-exe-clr-version-tool.md)  
报告计算机上公共语言运行时 (CLR) 的所有已安装版本。

- [CorFlags.exe（CorFlags 转换工具）](corflags-exe-corflags-conversion-tool.md)  
可用于配置可移植可执行 (PE) 映像的标头的 CorFlags 部分。

- [Fuslogvw.exe（程序集绑定日志查看器）](fuslogvw-exe-assembly-binding-log-viewer.md)  
显示有关程序集绑定的信息，以帮助您诊断 .NET Framework 无法在运行时定位某个程序集的原因。

- [Gacutil.exe（全局程序集缓存工具）](gacutil-exe-gac-tool.md)  
可用于查看和操作全局程序集缓存和下载缓存的内容。

- [Ilasm.exe（IL 汇编程序）](ilasm-exe-il-assembler.md)  
从中间语言 (IL) 生成可移植可执行 (PE) 文件。 可以运行生成的可执行文件以确定 IL 是否按预期执行。

- [Ildasm.exe（IL 反汇编程序）](ildasm-exe-il-disassembler.md)  
采用包含中间语言 (IL) 代码的可移植可执行 (PE) 文件，并创建可作为 IL 汇编程序 (Ilasm.exe) 的输入的文本文件。

- [Installutil.exe（安装程序工具）](installutil-exe-installer-tool.md)  
可用于通过执行指定程序集中的安装程序组件，安装和卸载服务器资源。 （与 <xref:System.Configuration.Install> 命名空间中的类一起工作。）

- [Lc.exe（许可证编译器）](lc-exe-license-compiler.md)  
读取包含许可信息的文本文件，并生成一个可作为资源嵌入到公共语言运行时可执行文件中的  .licenses 文件。

- [Mage.exe（清单生成和编辑工具）](mage-exe-manifest-generation-and-editing-tool.md)  
可用来创建、编辑应用程序和部署清单并为其签名。 可以从批脚本和其他基于 Windows 的应用程序（包括 ASP.NET 应用程序）运行命令行工具 Mage.exe  。

- [MageUI.exe（图形化客户端中的清单生成和编辑工具）](mageui-exe-manifest-generation-and-editing-tool-graphical-client.md)  
支持命令行工具 Mage.exe 提供的相同功能，只不过使用了基于 Windows 的用户界面 (UI)。 支持命令行工具 Mage.exe 提供的相同功能，只不过使用基于 Windows 的用户界面 (UI)  。

- [MDbg.exe（.NET Framework 命令行调试程序）](mdbg-exe.md)  
帮助工具供应商和应用程序开发人员可查找和修复面向 .NET Framework 公共语言运行时的程序中的 bug。 此工具使用运行时调试 API 提供调试服务。

- [Mgmtclassgen.exe（管理强类型类生成器）](mgmtclassgen-exe.md)  
可用于为指定的 Windows Management Instrumentation (WMI) 类生成早期绑定的托管类。

- [Mpgo.exe（按托管配置文件优化工具）](mpgo-exe-managed-profile-guided-optimization-tool.md)  
使用公共最终用户方案，可以调整本机映像程序集。 利用 Mpgo.exe，可以通过使用应用程序开发人员选择的培训方案来为本机映像应用程序（而非 .NET Framework 程序集）生成和使用配置文件数据。

- [Ngen.exe（本机映像生成器）](ngen-exe-native-image-generator.md)  
通过使用本机映像（包含已编译的处理器专用机器代码的文件）来提高托管应用程序的性能。 运行时可从缓存中使用本机映像，而不必使用实时 (JIT) 编译器编译原始程序集。

- [Peverify.exe（PEVerify 工具）](peverify-exe-peverify-tool.md)  
帮助验证 Microsoft 中间语言 (MSIL) 代码和关联的元数据是否满足类型安全要求。

- [Regasm.exe（程序集注册工具）](regasm-exe-assembly-registration-tool.md)  
读取程序集中的元数据，并在注册表中添加必要的项。 这使 COM 客户端可以显示为 .NET Framework 类。

- [Regsvcs.exe（.NET 服务安装工具）](regsvcs-exe-net-services-installation-tool.md)  
加载和注册程序集，生成类型库并将其安装到指定的 COM+ 1.0 版应用程序中，以及配置已通过编程方式添加到某个类的服务。

- [Resgen.exe（资源文件生成器）](resgen-exe-resource-file-generator.md)  
将文本（.txt 或 .restext）文件和基于 XML 的资源格式 (.resx) 文件转换为公共语言运行时二进制 (.resources) 文件，这些 .resources 文件可嵌入到运行时二进制可执行文件中或编译到附属程序集中     。

- [SecAnnotate.exe（.NET 安全批注器工具）](secannotate-exe-net-security-annotator-tool.md)  
标识程序集的 `SecurityCritical` 和 `SecuritySafeCritical` 部分。

- [SignTool.exe（签名工具）](signtool-exe.md)  
对文件进行数字签名，验证文件中的签名并设置文件的时间戳。

- [Sn.exe（强名称工具）](sn-exe-strong-name-tool.md)  
帮助创建带强名称的程序集。 此工具提供有关密钥管理、签名生成和签名验证的选项。

- [SOS.dll（SOS 调试扩展）](sos-dll-sos-debugging-extension.md)  
通过提供有关内部公共语言运行时环境的信息，帮助您在 WinDbg.exe 调试器和 Visual Studio 中调试托管程序。

- [SqlMetal.exe（代码生成工具）](sqlmetal-exe-code-generation-tool.md)  
为 .NET Framework 的 LINQ to SQL 组件生成代码和映射。

- [Storeadm.exe（独立存储工具）](storeadm-exe-isolated-storage-tool.md)  
管理独立存储；提供用于列出和删除用户的存储区的选项。

- [Tlbexp.exe（类型库导出程序）](tlbexp-exe-type-library-exporter.md)  
生成一个类型库，其中描述在一个公共语言运行时程序集中定义的类型。

- [Tlbimp.exe（类型库导入程序）](tlbimp-exe-type-library-importer.md)  
将在 COM 类型库中找到的类型定义转换为公共语言运行时程序集中的等效定义。

- [Winmdexp.exe（Windows 运行时元数据导出工具）](winmdexp-exe-windows-runtime-metadata-export-tool.md)  
将作为 .winmdobj 文件编译的 .NET Framework 程序集导出到 Windows 运行时组件，此组件打包为同时包含 Windows 运行时元数据和实现信息的 .winmd 文件   。

- [Winres.exe（Windows 窗体资源编辑器）](winres-exe-windows-forms-resource-editor.md)  
帮助你对 Windows 窗体使用的用户界面 (UI) 资源（.resx 或 .resources 文件）进行本地化   。 您可以翻译字符串，然后对控件进行大小调整、移动和隐藏操作，以使控件可以容纳本地化字符串。

## <a name="related-sections"></a>相关章节

- [WPF 工具](/previous-versions/ms742404(v=vs.110))  
包括 isXPS 合规性工具 (isXPS.exe) 和性能分析工具等工具。

- [Windows Communication Foundation 工具](../wcf/tools.md)  
包括多种工具，可使创建、部署和管理 Windows Communication Foundation (WCF) 应用程序更加容易。

---
title: 中断性变更：UTF-7 代码路径已过时
description: 了解核心 .NET 库中的 .NET 5 中断性变更：UTF7 和 UTF7Encoding 构造函数已过时。
ms.date: 11/01/2020
ms.openlocfilehash: 7a0ba771e0fac2908ca37f16afb10118e1537161
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256980"
---
# <a name="utf-7-code-paths-are-obsolete"></a>UTF-7 代码路径已过时

UTF-7 编码在应用程序中不再广泛使用，并且许多规范现在在交换中[禁止其使用](https://security.stackexchange.com/a/68609/3573)。 它偶尔还会在不期望遇到 UTF-7 编码数据的应用程序中[用作攻击途径](https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=utf-7)。 Microsoft 警告不要使用 <xref:System.Text.UTF7Encoding?displayProperty=nameWithType>，因为它不提供错误检测。

因此，<xref:System.Text.Encoding.UTF7?displayProperty=nameWithType> 属性和 <xref:System.Text.UTF7Encoding.%23ctor%2A> 构造函数现已过时。 此外，<xref:System.Text.Encoding.GetEncoding%2A?displayProperty=nameWithType> 和 <xref:System.Text.Encoding.GetEncodings%2A?displayProperty=nameWithType> 不再允许你指定 `UTF-7`。

## <a name="change-description"></a>更改描述

以前，你可以使用 <xref:System.Text.Encoding.GetEncoding%2A?displayProperty=nameWithType> API 创建 UTF-7 编码的实例。 例如：

```csharp
Encoding enc1 = Encoding.GetEncoding("utf-7"); // By name.
Encoding enc2 = Encoding.GetEncoding(65000); // By code page.
```

此外，表示 UTF-7 编码的实例是由 <xref:System.Text.Encoding.GetEncodings?displayProperty=nameWithType> 方法枚举的，该方法枚举在系统上注册的所有 <xref:System.Text.Encoding> 实例。

从 .NET 5 开始，<xref:System.Text.Encoding.UTF7?displayProperty=nameWithType> 属性和 <xref:System.Text.UTF7Encoding.%23ctor%2A> 构造函数已过时并生成警告 `SYSLIB0001`（若在预览版中，则生成 `MSLIB0001`）。 但是，若要减少在使用 <xref:System.Text.UTF7Encoding> 类时调用方收到的警告数，则 <xref:System.Text.UTF7Encoding> 类型本身不会标记为已过时。

```csharp
// The next line generates warning SYSLIB0001.
UTF7Encoding enc = new UTF7Encoding();
// The next line does not generate a warning.
byte[] bytes = enc.GetBytes("Hello world!");
```

此外，<xref:System.Text.Encoding.GetEncoding%2A?displayProperty=nameWithType> 方法将编码名称 `utf-7` 和代码页 `65000` 视为 `unknown`。 将编码视为 `unknown` 会使方法引发 <xref:System.ArgumentException>。

```csharp
// Throws ArgumentException, same as calling Encoding.GetEncoding("unknown").
Encoding enc = Encoding.GetEncoding("utf-7");
```

最后，<xref:System.Text.Encoding.GetEncodings?displayProperty=nameWithType> 方法不会将 UTF-7 编码包括在它返回的 <xref:System.Text.EncodingInfo> 数组中。 已排除编码，因为无法对其进行实例化。

```csharp
foreach (EncodingInfo encInfo in Encoding.GetEncodings())
{
    // The next line would throw if GetEncodings included UTF-7.
    Encoding enc = Encoding.GetEncoding(encInfo.Name);
}
```

## <a name="reason-for-change"></a>更改原因

许多应用程序使用由不受信任的源提供的编码名称值调用 `Encoding.GetEncoding("encoding-name")`。 例如，Web 客户端或服务器可能采用 `Content-Type` 标头的 `charset` 部分，并且不进行验证将值直接传递给 `Encoding.GetEncoding`。 这可能会使恶意终结点指定 `Content-Type: ...; charset=utf-7`，而这可能会导致接收应用程序出现异常行为。

此外，禁用 UTF-7 代码路径可以优化编译器（如 Blazor 使用的编译器），以便从生成的应用程序中完全删除这些代码路径。 因此，已编译的应用程序会更有效地运行，且占用更少的磁盘空间。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name="recommended-action"></a>建议操作

在大多数情况下，你不必执行任何操作。 但是，对于以前激活了与 UTF-7 相关的代码路径的应用，请考虑下面的指南。

- 如果你的应用使用不受信任的源提供的未知编码名称调用 <xref:System.Text.Encoding.GetEncoding%2A?displayProperty=nameWithType>：

  请改为将编码名称与可配置的允许列表进行比较。 可配置的允许列表至少应包含行业标准的“utf-8”。 根据你的客户端和法规要求，你可能还需要允许特定于区域的编码，如“GB18030”。

  如果未执行允许列表，<xref:System.Text.Encoding.GetEncoding%2A?displayProperty=nameWithType> 将返回内置于系统中或通过自定义 <xref:System.Text.EncodingProvider> 注册的任何 <xref:System.Text.Encoding>。 审核你服务的要求以验证这是否是所需的行为。 除非你的应用程序重新启用本文后面所述的兼容性开关，否则默认情况下将继续禁用 UTF-7。

- 如果在你自己的协议或文件格式中使用的是 <xref:System.Text.Encoding.UTF7?displayProperty=nameWithType> 或 <xref:System.Text.UTF7Encoding>：

  切换到使用 <xref:System.Text.Encoding.UTF8?displayProperty=nameWithType> 或 <xref:System.Text.UTF8Encoding>。 UTF-8 是一种行业标准，并且受到语言、操作系统和运行时的广泛支持。 使用 UTF-8 简化了代码的将来维护，并使其与生态系统的其余部分更具互操作性。

- 如果要将 <xref:System.Text.Encoding> 实例与 <xref:System.Text.Encoding.UTF7?displayProperty=nameWithType> 进行比较：

  可转为考虑针对众所周知的 UTF-7 代码页（即 `65000`）执行检查。 通过与代码页进行比较，可以避免出现警告，还可以处理一些边缘情况，例如，如果有人调用 `new UTF7Encoding()` 或子类化类型。

  ```csharp
  void DoSomething(Encoding enc)
  {
      // Don't perform the check this way.
      // It produces a warning and misses some edge cases.
      if (enc == Encoding.UTF7)
      {
          // Encoding is UTF-7.
      }

      // Instead, perform the check this way.
      if (enc != null && enc.CodePage == 65000)
      {
          // Encoding is UTF-7.
      }
  }
  ```

- 如果必须使用 <xref:System.Text.Encoding.UTF7?displayProperty=nameWithType> 或 <xref:System.Text.UTF7Encoding>：

  可以在代码中或在项目的 *.csproj* 文件中禁止显示 `SYSLIB0001` 警告。

  ```csharp
  #pragma warning disable SYSLIB0001 // Disable the warning.
  Encoding enc = Encoding.UTF7;
  #pragma warning restore SYSLIB0001 // Re-enable the warning.
  ```

  ```xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
     <TargetFramework>net5.0</TargetFramework>
     <!-- NoWarn below suppresses SYSLIB0001 project-wide -->
     <NoWarn>$(NoWarn);SYSLIB0001</NoWarn>
    </PropertyGroup>
  </Project>
  ```

  > [!NOTE]
  > 禁止显示 `SYSLIB0001` 只会禁用 <xref:System.Text.Encoding.UTF7?displayProperty=nameWithType> 和 <xref:System.Text.UTF7Encoding> 过时警告。 不会禁用任何其他警告，也不会更改 API 的行为，如 <xref:System.Text.Encoding.GetEncoding%2A?displayProperty=nameWithType>。

- 如果必须支持 `Encoding.GetEncoding("utf-7", ...)`：

  可以通过兼容性开关重新启用对此项的支持。 可以在应用程序的 *.csproj* 文件或 [运行时配置文件](../../../run-time-config/index.md)中指定此兼容性开关，如以下示例中所示。

  在应用程序的 *.csproj* 文件中：

  ```xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
     <TargetFramework>net5.0</TargetFramework>
     <!-- Re-enable support for UTF-7 -->
     <EnableUnsafeUTF7Encoding>true</EnableUnsafeUTF7Encoding>
    </PropertyGroup>
  </Project>
  ```

  在应用程序的 *runtimeconfig.template.json* 文件中：

  ```json
  {
    "configProperties": {
      "System.Text.Encoding.EnableUnsafeUTF7Encoding": true
    }
  }
  ```

  > [!TIP]
  > 如果重新启用对 UTF-7 的支持，则应对调用 <xref:System.Text.Encoding.GetEncoding%2A?displayProperty=nameWithType> 的代码执行安全检查。

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Text.Encoding.UTF7?displayProperty=fullName>
- <xref:System.Text.UTF7Encoding.%23ctor>
- <xref:System.Text.UTF7Encoding.%23ctor(System.Boolean)>
- <xref:System.Text.Encoding.GetEncoding(System.Int32)?displayProperty=fullName>
- <xref:System.Text.Encoding.GetEncoding(System.String)?displayProperty=fullName>
- <xref:System.Text.Encoding.GetEncoding(System.Int32,System.Text.EncoderFallback,System.Text.DecoderFallback)?displayProperty=fullName>
- <xref:System.Text.Encoding.GetEncoding(System.String,System.Text.EncoderFallback,System.Text.DecoderFallback)?displayProperty=fullName>
- <xref:System.Text.Encoding.GetEncodings?displayProperty=fullName>

<!--

#### Category

- Core .NET libraries
- Security

### Affected APIs

- `System.Text.Encoding.UTF7`
- `System.Text.UTF7Encoding.#ctor`
- `System.Text.UTF7Encoding.#ctor(System.Boolean)`
- `System.Text.Encoding.GetEncoding(System.Int32)`
- `System.Text.Encoding.GetEncoding(System.String)`
- `System.Text.Encoding.GetEncoding(System.Int32,System.Text.EncoderFallback,System.Text.DecoderFallback)`
- `System.Text.Encoding.GetEncoding(System.String,System.Text.EncoderFallback,System.Text.DecoderFallback)`
- `System.Text.Encoding.GetEncodings`

-->

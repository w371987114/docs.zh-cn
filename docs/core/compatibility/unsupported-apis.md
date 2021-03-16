---
title: .NET Core 和 .NET 5+ 上不支持的 API
titleSuffix: ''
description: 了解哪些 .NET API 始终在 .NET Core 和 .NET 5 及更高版本上引发异常。
ms.date: 10/13/2020
ms.openlocfilehash: b0dc425bf5f7d8f2a44f51ffe75ffcb0c8a5a7ae
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256252"
---
# <a name="apis-that-always-throw-exceptions-on-net-core-and-net-5"></a>始终在 .NET Core 和 .NET 5+ 上引发异常的 API

以下 API 将始终在所有或部分平台上的 .NET 5 及更高版本（包括所有 .NET Core 版本）中引发 <xref:System.PlatformNotSupportedException>。

本文按命名空间组织受影响的 API。

> [!NOTE]
>
> - 本文是当前正在进行的工作。 它不是在 .NET 5+ 上引发异常的 API 的完整列表。
> - 本文不包括在 .NET 5+ 上引发的二进制序列化的显式接口实现。 有关详细信息，请参阅 [.NET Core 中的二进制序列化](../../standard/serialization/binary-serialization.md#net-core)。

## <a name="system"></a>系统

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.AppDomain.CreateDomain%2A?displayProperty=nameWithType> | All |
| <xref:System.AppDomain.ExecuteAssembly(System.String,System.String[],System.Byte[],System.Configuration.Assemblies.AssemblyHashAlgorithm)?displayProperty=nameWithType> | All |
| <xref:System.Console.CapsLock?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Console.NumberLock?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Delegate.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | All |
| <xref:System.Exception.SerializeObjectState?displayProperty=nameWithType> | 全部 |
| <xref:System.MarshalByRefObject.GetLifetimeService?displayProperty=nameWithType> | 全部 |
| <xref:System.MarshalByRefObject.InitializeLifetimeService?displayProperty=nameWithType> | 全部 |
| <xref:System.OperatingSystem.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | 全部 |
| <xref:System.Type.ReflectionOnlyGetType(System.String,System.Boolean,System.Boolean)?displayProperty=nameWithType> | All |

## <a name="systemcodedomcompiler"></a>System.CodeDom.Compiler

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.CodeDom.Compiler.CodeDomProvider.CompileAssemblyFromDom%2A?displayProperty=nameWithType> | All |
| <xref:System.CodeDom.Compiler.CodeDomProvider.CompileAssemblyFromFile%2A?displayProperty=nameWithType> | 全部 |
| <xref:System.CodeDom.Compiler.CodeDomProvider.CompileAssemblyFromSource%2A?displayProperty=nameWithType> | All |

## <a name="systemcollectionsspecialized"></a>System.Collections.Specialized

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Collections.Specialized.NameObjectCollectionBase.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | All |
| <xref:System.Collections.Specialized.NameObjectCollectionBase.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | 全部 |
| <xref:System.Collections.Specialized.NameObjectCollectionBase.OnDeserialization(System.Object)?displayProperty=nameWithType> | All |

## <a name="systemconfiguration"></a>System.Configuration

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Configuration.RsaProtectedConfigurationProvider?displayProperty=nameWithType>（所有成员） | All |

## <a name="systemconsole"></a>System.Console

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Console.Beep?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Console.BufferHeight?displayProperty=nameWithType>（仅设置） | Linux 和 macOS |
| <xref:System.Console.BufferWidth?displayProperty=nameWithType>（仅设置） | Linux 和 macOS |
| <xref:System.Console.CursorSize?displayProperty=nameWithType>（仅设置） | Linux 和 macOS |
| <xref:System.Console.CursorVisible?displayProperty=nameWithType>（仅获取） | Linux 和 macOS |
| <xref:System.Console.MoveBufferArea%2A?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Console.SetWindowPosition%2A?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Console.SetWindowSize%2A?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Console.Title?displayProperty=nameWithType>（仅获取） | Linux 和 macOS |
| <xref:System.Console.WindowHeight?displayProperty=nameWithType>（仅设置） | Linux 和 macOS |
| <xref:System.Console.WindowLeft?displayProperty=nameWithType>（仅设置） | Linux 和 macOS |
| <xref:System.Console.WindowTop?displayProperty=nameWithType>（仅设置） | Linux 和 macOS |
| <xref:System.Console.WindowWidth?displayProperty=nameWithType>（仅设置） | Linux 和 macOS |

## <a name="systemdatacommon"></a>System.Data.Common

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Data.Common.DbDataReader.GetSchemaTable%2A?displayProperty=nameWithType>（引发 <xref:System.NotSupportedException>） | All |

## <a name="systemdiagnosticsprocess"></a>System.Diagnostics.Process

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Diagnostics.Process.MaxWorkingSet?displayProperty=nameWithType>（仅设置） | Linux |
| <xref:System.Diagnostics.Process.MinWorkingSet?displayProperty=nameWithType>（仅设置） | Linux |
| <xref:System.Diagnostics.Process.ProcessorAffinity?displayProperty=nameWithType> | macOS |
| <xref:System.Diagnostics.Process.MainWindowHandle?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Diagnostics.Process.Start(System.String,System.String,System.String,System.Security.SecureString,System.String)?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Diagnostics.Process.Start(System.String,System.String,System.Security.SecureString,System.String)?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Diagnostics.ProcessStartInfo.UserName?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Diagnostics.ProcessStartInfo.PasswordInClearText?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Diagnostics.ProcessStartInfo.Domain?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Diagnostics.ProcessStartInfo.LoadUserProfile?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Diagnostics.ProcessThread.BasePriority?displayProperty=nameWithType>（仅设置） | Linux 和 macOS |
| <xref:System.Diagnostics.ProcessThread.BasePriority?displayProperty=nameWithType>（仅获取） | macOS |
| <xref:System.Diagnostics.ProcessThread.ProcessorAffinity?displayProperty=nameWithType>（仅设置） | Linux 和 macOS |

## <a name="systemio"></a>System.IO

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.IO.FileSystemInfo.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | All |
| <xref:System.IO.FileSystemInfo.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | All |

## <a name="systemiopipes"></a>System.IO.Pipes

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.IO.Pipes.NamedPipeClientStream.NumberOfServerInstances?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.IO.Pipes.NamedPipeServerStream.GetImpersonationUserName?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.IO.Pipes.PipeStream.InBufferSize?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.IO.Pipes.PipeStream.OutBufferSize?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.IO.Pipes.PipeStream.ReadMode?displayProperty=nameWithType>（仅设置） | Linux 和 macOS |
| <xref:System.IO.Pipes.PipeStream.WaitForPipeDrain?displayProperty=nameWithType> | Linux 和 macOS |

## <a name="systemmedia"></a>System.Media

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Media.SoundPlayer.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | All |

## <a name="systemnet"></a>System.Net

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Net.AuthenticationManager.Authenticate(System.String,System.Net.WebRequest,System.Net.ICredentials)?displayProperty=nameWithType> | All |
| <xref:System.Net.AuthenticationManager.PreAuthenticate(System.Net.WebRequest,System.Net.ICredentials)?displayProperty=nameWithType> | 全部 |
| <xref:System.Net.FileWebRequest.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | 全部 |
| <xref:System.Net.FileWebRequest.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | 全部 |
| <xref:System.Net.FileWebResponse.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | 全部 |
| <xref:System.Net.FileWebResponse.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | 全部 |
| <xref:System.Net.HttpWebRequest.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | 全部 |
| <xref:System.Net.HttpWebRequest.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | 全部 |
| <xref:System.Net.HttpWebResponse.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | 全部 |
| <xref:System.Net.HttpWebResponse.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | 全部 |
| <xref:System.Net.WebProxy.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | 全部 |
| <xref:System.Net.WebProxy.GetDefaultProxy?displayProperty=nameWithType> | 全部 |
| <xref:System.Net.WebProxy.GetObjectData%2A?displayProperty=nameWithType> | 全部 |
| <xref:System.Net.WebRequest.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | 全部 |
| <xref:System.Net.WebRequest.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | 全部 |
| <xref:System.Net.WebResponse.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | 全部 |
| <xref:System.Net.WebResponse.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | All |

## <a name="systemnetnetworkinformation"></a>System.Net.NetworkInformation

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Net.NetworkInformation.Ping.Send%2A?displayProperty=nameWithType> | Windows (UWP) |

## <a name="systemnetsockets"></a>System.Net.Sockets

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Net.Sockets.Socket.%23ctor(System.Net.Sockets.SocketInformation)> | All |
| <xref:System.Net.Sockets.Socket.DuplicateAndClose(System.Int32)?displayProperty=nameWithType> | All |

## <a name="systemnetwebsockets"></a>System.Net.WebSockets

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Net.WebSockets.WebSocket.RegisterPrefixes?displayProperty=nameWithType> | All |

## <a name="systemreflection"></a>System.Reflection

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Reflection.Assembly.ReflectionOnlyLoad%2A?displayProperty=nameWithType> | All |
| <xref:System.Reflection.Assembly.ReflectionOnlyLoadFrom(System.String)?displayProperty=nameWithType> | 全部 |
| <xref:System.Reflection.AssemblyName.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | 全部 |
| <xref:System.Reflection.AssemblyName.OnDeserialization(System.Object)?displayProperty=nameWithType> | 全部 |
| <xref:System.Reflection.StrongNameKeyPair.%23ctor(System.String)> | 全部 |
| <xref:System.Reflection.StrongNameKeyPair.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | 全部 |
| <xref:System.Reflection.StrongNameKeyPair.PublicKey?displayProperty=nameWithType> | All |

## <a name="systemruntimecompilerservices"></a>System.Runtime.CompilerServices

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Runtime.CompilerServices.DebugInfoGenerator.CreatePdbGenerator?displayProperty=nameWithType> | All |

## <a name="systemruntimeinteropservices"></a>System.Runtime.InteropServices

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Runtime.InteropServices.Marshal.GetIDispatchForObject(System.Object)?displayProperty=nameWithType> | All |
| <xref:System.Runtime.InteropServices.RuntimeEnvironment.SystemConfigurationFile?displayProperty=nameWithType> | 全部 |
| <xref:System.Runtime.InteropServices.RuntimeEnvironment.GetRuntimeInterfaceAsIntPtr(System.Guid,System.Guid)?displayProperty=nameWithType> | 全部 |
| <xref:System.Runtime.InteropServices.RuntimeEnvironment.GetRuntimeInterfaceAsObject(System.Guid,System.Guid)?displayProperty=nameWithType> | All |
| <xref:System.Runtime.InteropServices.WindowsRuntime.WindowsRuntimeMarshal.StringToHString(System.String)?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Runtime.InteropServices.WindowsRuntime.WindowsRuntimeMarshal.PtrToStringHString(System.IntPtr)?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Runtime.InteropServices.WindowsRuntime.WindowsRuntimeMarshal.FreeHString(System.IntPtr)?displayProperty=nameWithType> | Linux 和 macOS |

## <a name="systemruntimeserialization"></a>System.Runtime.Serialization

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Runtime.Serialization.XsdDataContractExporter.Schemas?displayProperty=nameWithType> | All |

## <a name="systemsecurity"></a>System.Security

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Security.CodeAccessPermission.Deny?displayProperty=nameWithType> | All |
| <xref:System.Security.CodeAccessPermission.PermitOnly?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.PermissionSet.ConvertPermissionSet(System.String,System.Byte[],System.String)?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.PermissionSet.Deny?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.PermissionSet.PermitOnly?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.SecurityContext.Capture?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.SecurityContext.CreateCopy?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.SecurityContext.Dispose?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.SecurityContext.IsFlowSuppressed?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.SecurityContext.IsWindowsIdentityFlowSuppressed?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.SecurityContext.RestoreFlow?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.SecurityContext.Run(System.Security.SecurityContext,System.Threading.ContextCallback,System.Object)?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.SecurityContext.SuppressFlow?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.SecurityContext.SuppressFlowWindowsIdentity?displayProperty=nameWithType> | All |

## <a name="systemsecurityclaims"></a>System.Security.Claims

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Security.Claims.ClaimsPrincipal.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | All |
| <xref:System.Security.Claims.ClaimsPrincipal.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.Claims.ClaimsIdentity.%23ctor(System.Runtime.Serialization.SerializationInfo)> | 全部 |
| <xref:System.Security.Claims.ClaimsIdentity.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | 全部 |
| <xref:System.Security.Claims.ClaimsIdentity.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | All |

## <a name="systemsecuritycryptography"></a>System.Security.Cryptography

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Security.Cryptography.AsymmetricAlgorithm.Create(System.String)?displayProperty=nameWithType> | All |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.%23ctor%2A> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.Accessible?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.Exportable?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.HardwareDevice?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.KeyContainerName?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.KeyNumber?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.MachineKeyStore?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.Protected?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.ProviderName?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.ProviderType?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.RandomlyGenerated?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.Removable?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.CspKeyContainerInfo.UniqueKeyContainerName?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.HashAlgorithm.Create?displayProperty=nameWithType> | All |
| <xref:System.Security.Cryptography.HMAC.Create?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.Cryptography.HMAC.Create(System.String)?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.Cryptography.HMAC.HashCore%2A?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.Cryptography.HMAC.HashFinal%2A?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.Cryptography.HMAC.Initialize%2A?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.Cryptography.KeyedHashAlgorithm.Create?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.Cryptography.KeyedHashAlgorithm.Create(System.String)?displayProperty=nameWithType> | All |
| <xref:System.Security.Cryptography.ProtectedData.Protect%2A?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.ProtectedData.Unprotect%2A?displayProperty=nameWithType> | Linux 和 macOS |
| <xref:System.Security.Cryptography.RSA.FromXmlString%2A?displayProperty=nameWithType> | All |
| <xref:System.Security.Cryptography.RSA.ToXmlString%2A?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.Cryptography.SymmetricAlgorithm.Create?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.Cryptography.SymmetricAlgorithm.Create(System.String)?displayProperty=nameWithType> | All |

## <a name="systemsecuritycryptographypkcs"></a>System.Security.Cryptography.Pkcs

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Security.Cryptography.Pkcs.CmsSigner.%23ctor(System.Security.Cryptography.CspParameters)> | All |
| <xref:System.Security.Cryptography.Pkcs.SignerInfo.ComputeCounterSignature?displayProperty=nameWithType> | All |

## <a name="systemsecuritycryptographyx509certificates"></a>System.Security.Cryptography.X509Certificates

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Security.Cryptography.X509Certificates.X509Certificate.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | All |
| <xref:System.Security.Cryptography.X509Certificates.X509Certificate.Import%2A?displayProperty=nameWithType> | 全部 |
| <xref:System.Security.Cryptography.X509Certificates.X509Certificate2.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | All |
| <xref:System.Security.Cryptography.X509Certificates.X509Certificate2.PrivateKey?displayProperty=nameWithType>（仅设置） | All |

## <a name="systemsecurityauthenticationextendedprotection"></a>System.Security.Authentication.ExtendedProtection

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Security.Authentication.ExtendedProtection.ExtendedProtectionPolicy.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | All |

## <a name="systemsecuritypolicy"></a>System.Security.Policy

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Security.Policy.Hash.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | All |

## <a name="systemserviceprocessservicecontroller"></a>System.ServiceProcess.ServiceController

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.ServiceProcess.TimeoutException.%23ctor(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)> | All |

## <a name="systemtextregularexpressions"></a>System.Text.RegularExpressions

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Text.RegularExpressions.Regex.CompileToAssembly%2A?displayProperty=nameWithType> | All |

## <a name="systemthreading"></a>System.Threading

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Threading.CompressedStack.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | All |
| <xref:System.Threading.ExecutionContext.GetObjectData(System.Runtime.Serialization.SerializationInfo,System.Runtime.Serialization.StreamingContext)?displayProperty=nameWithType> | 全部 |
| <xref:System.Threading.Thread.Abort%2A?displayProperty=nameWithType> | 全部 |
| <xref:System.Threading.Thread.ResetAbort?displayProperty=nameWithType> | 全部 |
| <xref:System.Threading.Thread.Resume?displayProperty=nameWithType> | 全部 |
| <xref:System.Threading.Thread.Suspend?displayProperty=nameWithType> | All |

## <a name="systemxml"></a>System.Xml

| 成员 | 引发异常的平台 |
| - | - |
| <xref:System.Xml.XmlDictionaryReader.CreateMtomReader(System.Byte[],System.Int32,System.Int32,System.Text.Encoding[],System.String,System.Xml.XmlDictionaryReaderQuotas,System.Int32,System.Xml.OnXmlDictionaryReaderClose)?displayProperty=nameWithType> | All |
| <xref:System.Xml.XmlDictionaryReader.CreateMtomReader(System.IO.Stream,System.Text.Encoding[],System.String,System.Xml.XmlDictionaryReaderQuotas,System.Int32,System.Xml.OnXmlDictionaryReaderClose)?displayProperty=nameWithType> | 全部 |
| <xref:System.Xml.XmlDictionaryWriter.CreateMtomWriter(System.IO.Stream,System.Text.Encoding,System.Int32,System.String,System.String,System.String,System.Boolean,System.Boolean)?displayProperty=nameWithType> | All |

## <a name="see-also"></a>另请参阅

- [从 .NET Framework 迁移到 .NET Core 的中断性变更](fx-core.md)
- [.NET Core 中的二进制序列化](../../standard/serialization/binary-serialization.md#net-core)
- [.NET 可移植性分析器](../../standard/analyzers/portability-analyzer.md)

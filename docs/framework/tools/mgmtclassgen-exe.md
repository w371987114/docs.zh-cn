---
title: Mgmtclassgen.exe（管理强类型类生成器）
description: 了解 Mgmtclassgen.exe（管理强类型类生成器）。 使用此工具可以为 WMI 类快速生成早期绑定的托管类。
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- CIM types
- Management Strongly Typed Class Generator
- WMI class
- Mgmtclassgen.exe
- early-bound managed classes
ms.assetid: 02ce6699-49b5-4a0b-b0d5-1003c491232e
ms.openlocfilehash: 8008644a4c443a2fcd4430c6710f4845f1e4bb30
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102259364"
---
# <a name="mgmtclassgenexe-management-strongly-typed-class-generator"></a>Mgmtclassgen.exe（管理强类型类生成器）

利用管理强类型类生成器工具，你可以为指定的 Windows Management Instrumentation (WMI) 类快速生成早期绑定的托管类。 生成的类简化了访问 WMI 类的实例所必须编写的代码。  
  
## <a name="syntax"></a>语法  
  
```console  
mgmtclassgen
WMIClass [options]
```  
  
|参数|说明|  
|--------------|-----------------|  
|WMIClass|为其生成早期绑定的托管类的 Windows Management Instrumentation 类。|  
  
|选项|描述|  
|------------|-----------------|  
|/l  language|指定用于生成早期绑定的托管类的语言。 可以指定 CS（C#；默认）、VB (Visual Basic)、MC (C++) 或 JS (JScript) 作为语言参数。|  
|/m  machine|指定要连接到的计算机，WMI 类驻留在该计算机中。 默认为本地计算机。|  
|/n  path|指定包含 WMI 类的 WMI 命名空间的路径。 如果没有指定该选项，则该工具为默认 Root\cimv2 命名空间中的 WMIClass 生成代码。|  
|/o  classnamespace|指定在其中生成托管代码类的 .NET 命名空间。 如果没有指定该选项，则该工具使用 WMI 命名空间和架构前缀生成命名空间。 架构前缀是下划线字符前面的类名的一部分。 例如，对于 Root\cimv2 命名空间中的 Win32_OperatingSystem 类，该工具会在 ROOT.CIMV2.Win32 中生成类。|  
|/p  filepath|指定用于保存已生成代码的文件的路径。 如果没有指定该选项，则该工具将在当前目录中创建文件。 它使用 WMIClass 参数为类和在其中生成类的文件命名。 类名和文件名与 WMIClass 的名称相同。 如果 WMIClass 包含下划线字符，则该工具使用下划线字符后面的类名那部分。 例如，如果 WMIClass 名称采用 Win32_LogicalDisk 格式，则生成的类和文件名为“logicaldisk”。 如果文件已存在，则该工具会覆盖现有文件。|  
|/pw  password|指定登录到由 /m 选项指定的计算机时使用的密码。|  
|/u  user name|指定登录到由 /m 选项指定的计算机时使用的用户名。|  
|**/?**|显示该工具的命令语法和选项。|  
  
## <a name="remarks"></a>备注  

 Mgmtclassgen.exe 使用 <xref:System.Management.ManagementClass.GetStronglyTypedClassCode%2A?displayProperty=nameWithType> 方法。 因此，你可使用任何自定义代码提供程序用 C#、Visual Basic 和 JScript 以外的托管语言生成代码。  
  
 请注意，生成的类会绑定到为其生成类的架构。 在基础架构发生更改的情况下，如果你希望类反映架构的更改，则必须重新生成类。  
  
 下表显示 WMI 通用信息模型 (CIM) 类型映射到生成的类中的数据类型的方式：  
  
|CIM 类型|生成的类中的数据类型|  
|--------------|--------------------------------------|  
|CIM_SINT8|**SByte**|  
|CIM_UINT8|**Byte**|  
|CIM_SINT16|**Int16**|  
|CIM_UINT16|**UInt16**|  
|CIM_SINT32|**Int32**|  
|SIM_UINT32|**UInt32**|  
|CIM_SINT64|**Int64**|  
|CIM_UINT64|**UInt64**|  
|CIM_REAL32|**单精度**|  
|CIM_REAL64|**双精度**|  
|CIM_BOOLEAN|**布尔值**|  
|CIM_String|**字符串**|  
|CIM_DATETIME|DateTime 或 TimeSpan|  
|CIM_REFERENCE|ManagementPath|  
|CIM_CHAR16|**Char**|  
|CIM_OBJECT|ManagementBaseObject|  
|CIM_IUNKNOWN|**对象**|  
|CIM_ARRAY|上述对象的数组|  
  
 记下生成 WMI 类时的以下行为：  
  
- 标准公共属性或方法可能与现有属性或方法具有相同名称。 如果出现这种情况，该工具会更改生成的类中的属性或方法的名称，以避免命名冲突。  
  
- 生成的类中的属性或方法的名称可能是目标编程语言中的关键字。 如果出现这种情况，该工具会更改生成的类中的属性或方法的名称，以避免命名冲突。  
  
- 在 WMI 中，限定符是包含用于描述类、实例、属性或方法的信息的修饰符。 WMI 使用标准限定符（如 Read、Write 和 Key）描述生成的类中的属性。 例如，生成的类中用 Read 限定符修饰的属性只能使用属性 get 访问器定义。 因为使用 Read 限定符标记的属性将是只读的，所以未定义 set 访问器。  
  
- 可使用 Values 和 ValueMaps 限定符来修饰数值型属性，以指示仅可将该属性设置为指定的允许值。 使用这些 Values 和 ValueMaps 生成枚举，并且属性将映射到该枚举。  
  
- WMI 使用术语单一实例 (singleton) 来描述只能有一个实例的类。 因此，单一实例类无参数构造函数会将该类初始化为该类的唯一实例。  
  
- WMI 类可以有对象类型的属性。 为此类型的 WMI 类生成强类型类时，应考虑为嵌入式对象属性的类型生成强类型类。 这使你能以强类型方式访问嵌入对象。 请注意，生成的代码可能无法检测嵌入对象的类型。 在这种情况下，生成的代码中将创建一条注释，以告知你存在此问题。 然后，你可修改生成的代码以将该属性的类型转换为其他生成类的类型。  
  
- 在 WMI 中，CIM_DATETIME 数据类型的数据值可表示某一特定的日期和时间或时间间隔。 如果数据值表示日期和时间，则生成的类中的数据类型为 DateTime。 如果数据值表示时间间隔，则生成的类中的数据类型为 TimeSpan。  
  
 你可以选择使用 Visual Studio .NET 中的服务器资源管理器管理扩展来生成强类型的类。  
  
 有关 WMI 的详细信息，请参阅平台 SDK 文档中的 Windows Management Instrumentation 主题。  
  
## <a name="examples"></a>示例  

 以下命令在 C# 代码中为 Root\cimv2 命名空间中的 Win32_LogicalDisk WMI 类生成托管类。 该工具将 ROOT.CIMV2.Win32 命名空间中的托管类写入位于 c:\disk.cs 的源文件中。  
  
```console  
mgmtclassgen Win32_LogicalDisk /n root\cimv2 /l CS /p c:\disk.cs  
```  
  
 以下代码示例说明如何以编程方式使用生成的类。 首先，枚举类的一个实例并打印路径。 接下来，使用 WMI 的实例创建要初始化的生成类的实例。 `Process` 是为 Win32_Process 生成的类，`LogicalDisk` 是为 Root\cimv2 命名空间中的 Win32_LogicalDisk 生成的类。  
  
```vb  
Imports System  
Imports System.Management  
Imports ROOT.CIMV2.Win32  
  
Public Class App
   Public Shared Sub Main()
      ' Enumerate instances of the Win32_process.  
      ' Print the Name property of the instance.  
      Dim ps As Process
      For Each ps In  Process.GetInstances()  
         Console.WriteLine(ps.Name)  
      Next ps  
  
      ' Initialize the instance of LogicalDisk with  
      ' the WMI instance pointing to logical drive d:.  
      Dim dskD As New LogicalDisk(New _  
         ManagementPath("win32_LogicalDisk.DeviceId=""d:"""))  
      Console.WriteLine(dskD.Caption)  
   End Sub  
End Class  
```  
  
```csharp  
using System;  
using System.Management;  
using ROOT.CIMV2.Win32;  
  
public class App  
{  
   public static void Main()  
   {  
      // Enumerate instances of the Win32_process.  
      // Print the Name property of the instance.  
      foreach(Process ps in Process.GetInstances())  
      {  
         Console.WriteLine(ps.Name);  
      }  
  
      // Initialize the instance of LogicalDisk with  
      // the WMI instance pointing to logical drive d:.  
      LogicalDisk dskD = new LogicalDisk(new ManagementPath(  
        "win32_LogicalDisk.DeviceId=\"d:\""));  
      Console.WriteLine(dskD.Caption);  
   }  
}  
```  
  
## <a name="see-also"></a>请参阅

- <xref:System.Management>
- <xref:System.Management.ManagementClass.GetStronglyTypedClassCode%2A?displayProperty=nameWithType>
- <xref:System.CodeDom.Compiler.CodeDomProvider?displayProperty=nameWithType>
- [工具](index.md)
- [开发人员命令行 shell](/visualstudio/ide/reference/command-prompt-powershell)

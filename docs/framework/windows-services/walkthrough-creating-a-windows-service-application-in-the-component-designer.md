---
title: 教程：创建 Windows 服务应用
description: 本教程在 Visual Studio 中创建可向事件日志中写入消息的 Windows 服务应用程序。 添加功能，设置状态，添加安装程序等。
ms.date: 03/27/2019
dev_langs:
- csharp
- vb
helpviewer_keywords:
- Windows service applications, walkthroughs
- Windows service applications, creating
ms.assetid: e24d8a3d-edc6-485c-b6e0-5672d91fb607
ms.openlocfilehash: b6e4937b71c50f887a7eb784bc9106360a05fdc2
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102259793"
---
# <a name="tutorial-create-a-windows-service-app"></a>教程：创建 Windows 服务应用

本文演示了如何在 Visual Studio 中创建可向事件日志中写入消息的 Windows 服务应用程序。

## <a name="create-a-service"></a>创建服务

首先，创建项目并设置服务正常运行所必需的值。

1. 从 Visual Studio“文件”菜单中，选择“新建” > “项目”（或按 Ctrl+Shift+N），打开“新建项目”窗口        。

2. 导航到并选择“Windows 服务 (.NET Framework)”项目模板  。 若要找到它，请展开“已安装”和 **“Visual C#”** 或 **“Visual Basic”** ，然后选择“Windows 桌面”   。 或者，在右上方的搜索框中输入 Windows 服务，然后按 Enter   。

   ![Visual Studio“新建项目”对话框中的“Windows 服务”模板](./media/new-project-dialog.png)

   > [!NOTE]
   > 如果未看到“Windows 服务”模板，建议安装“.NET 桌面开发”工作负载   ：
   >
   > 在“新建项目”对话框中，选择左下方的“打开 Visual Studio 安装程序”   。 选择“.NET 桌面开发”工作负载，然后选择“修改”   。

3. 对于“名称”，请输入 MyNewService，然后选择“确定”    。

   将出现“设计”选项卡（“Service1.cs [Design]”或“Service1.vb [Design]”）    。

   项目模板包括从 <xref:System.ServiceProcess.ServiceBase?displayProperty=nameWithType> 继承的名为 `Service1` 的组件类。 它包括许多基本服务代码，例如用于启动服务的代码。

## <a name="rename-the-service"></a>对服务进行重命名

将服务从 **Service1** 重命名为 **MyNewService**。

1. 在“解决方案资源管理器”中，选择“Service1.cs”或“Service1.vb”，然后从快捷菜单中选择“重命名”     。 将文件重命名为“MyNewService.cs”或“MyNewService.vb”，然后按 Enter

    随即将出现一个弹出窗口，询问是否要重命名对代码元素 Service1 的所有引用  。

2. 在弹出窗口中，选择“是”  。

    ![重命名提示](./media/windows-service-rename.png "Windows 服务重命名提示")

3. 在“设计”选项卡中，从快捷菜单中选择“属性”   。 在“属性”窗口中，将“ServiceName”值更改为 MyNewService    。

    ![服务属性](./media/windows-service-properties.png "Windows 服务属性")

4. 从“文件”菜单中选择“全部保存” 。

## <a name="add-features-to-the-service"></a>向服务添加功能

在本节中，你会添加自定义事件日志到 Windows 服务。 <xref:System.Diagnostics.EventLog> 组件是可以添加到 Windows 服务的组件类型的示例。

### <a name="add-custom-event-log-functionality"></a>添加自定义事件日志功能

1. 在“解决方案资源管理器”中，从“MyNewService.cs”或“MyNewService.vb”的快捷菜单中，选择“查看设计器”     。

2. 在“工具箱”中，展开“组件”，然后将“EventLog”组件拖到“Service1.cs [Design]”或“Service1.vb [Design]”标签      。

3. 在“解决方案资源管理器”中，从“MyNewService.cs”或“MyNewService.vb”的快捷菜单中，选择“查看代码”     。

4. 定义自定义事件日志。 对于 C#，编辑现有的 `MyNewService()` 构造函数；对于 Visual Basic，添加 `New()` 构造函数：

   [!code-csharp[VbRadconService#2](../../../samples/snippets/csharp/VS_Snippets_VBCSharp/VbRadconService/CS/MyNewService.cs#2)]
   [!code-vb[VbRadconService#2](../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbRadconService/VB/MyNewService.vb#2)]

5. 将 `using` 语句添加到“MyNewService.cs”（如果它尚不存在）或者，对于 <xref:System.Diagnostics?displayProperty=nameWithType> 命名空间，将 `Imports` 语句添加到“MyNewService.vb”   ：

    ```csharp
    using System.Diagnostics;
    ```

    ```vb
    Imports System.Diagnostics
    ```

6. 从“文件”菜单中选择“全部保存”   。

### <a name="define-what-occurs-when-the-service-starts"></a>定义服务启动时发生的情况

在“MyNewService.cs”或“MyNewService.vb”的代码编辑器中，找到 <xref:System.ServiceProcess.ServiceBase.OnStart%2A> 方法；创建项目时，Visual Studio 会自动创建一个空方法定义   。 添加代码，以在服务启动时向事件日志写入一个条目：

[!code-csharp[VbRadconService#3](../../../samples/snippets/csharp/VS_Snippets_VBCSharp/VbRadconService/CS/MyNewService.cs#3)]
[!code-vb[VbRadconService#3](../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbRadconService/VB/MyNewService.vb#3)]

#### <a name="polling"></a>轮询

由于服务应用程序设计为长时间运行，因此它通常会轮询或监视你在 <xref:System.ServiceProcess.ServiceBase.OnStart%2A> 方法中设置的系统。 在服务操作开始后，`OnStart` 方法必须返回操作系统，以便不阻止系统。

若要设置简单的轮询机制，请使用 <xref:System.Timers.Timer?displayProperty=nameWithType> 组件。 计时器定期引发 <xref:System.Timers.Timer.Elapsed> 事件，此时你的服务可以进行监视。 可按如下方式使用 <xref:System.Timers.Timer> 组件：

- 在 `MyNewService.OnStart` 方法中设置 <xref:System.Timers.Timer> 组件的属性。
- 通过调用 <xref:System.Timers.Timer.Start%2A> 方法启动计时器。

##### <a name="set-up-the-polling-mechanism"></a>设置轮询机制。

1. 在 `MyNewService.OnStart` 事件中添加以下代码以设置轮询机制：

   ```csharp
   // Set up a timer that triggers every minute.
   Timer timer = new Timer();
   timer.Interval = 60000; // 60 seconds
   timer.Elapsed += new ElapsedEventHandler(this.OnTimer);
   timer.Start();
   ```

   ```vb
   ' Set up a timer that triggers every minute.
   Dim timer As Timer = New Timer()
   timer.Interval = 60000 ' 60 seconds
   AddHandler timer.Elapsed, AddressOf Me.OnTimer
   timer.Start()
   ```

2. 将 `using` 语句添加到“MyNewService.cs”，或者，对于 <xref:System.Timers?displayProperty=nameWithType> 命名空间，将 `Imports` 语句添加到“MyNewService.vb”   ：

   ```csharp
   using System.Timers;
   ```

   ```vb
   Imports System.Timers
   ```

3. 在 `MyNewService` 类中，添加 `OnTimer` 方法来处理 <xref:System.Timers.Timer.Elapsed?displayProperty=nameWithType> 事件：

   ```csharp
   public void OnTimer(object sender, ElapsedEventArgs args)
   {
       // TODO: Insert monitoring activities here.
       eventLog1.WriteEntry("Monitoring the System", EventLogEntryType.Information, eventId++);
   }
   ```

   ```vb
   Private Sub OnTimer(sender As Object, e As Timers.ElapsedEventArgs)
      ' TODO: Insert monitoring activities here.
      eventLog1.WriteEntry("Monitoring the System", EventLogEntryType.Information, eventId)
      eventId = eventId + 1
   End Sub
   ```

4. 在 `MyNewService` 类中，添加成员变量。 它包含下一个要写入事件日志的事件的标识符：

   ```csharp
   private int eventId = 1;
   ```

   ```vb
   Private eventId As Integer = 1
   ```

可以使用后台工作线程来运行任务，而不是在主线程上运行所有工作。 有关详细信息，请参阅 <xref:System.ComponentModel.BackgroundWorker?displayProperty=fullName>。

### <a name="define-what-occurs-when-the-service-is-stopped"></a>定义服务停止时发生的情况

向 <xref:System.ServiceProcess.ServiceBase.OnStop%2A> 方法插入代码行，以在服务停止时向事件日志添加一个条目：

[!code-csharp[VbRadconService#2](../../../samples/snippets/csharp/VS_Snippets_VBCSharp/VbRadconService/CS/MyNewService.cs#4)]
[!code-vb[VbRadconService#4](../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbRadconService/VB/MyNewService.vb#4)]

### <a name="define-other-actions-for-the-service"></a>定义服务的其他操作

可以重写 <xref:System.ServiceProcess.ServiceBase.OnPause%2A>、<xref:System.ServiceProcess.ServiceBase.OnContinue%2A> 和 <xref:System.ServiceProcess.ServiceBase.OnShutdown%2A> 方法来定义对组件的其他处理。

以下代码显示了如何替代 `MyNewService` 类中的 <xref:System.ServiceProcess.ServiceBase.OnContinue%2A> 方法：

[!code-csharp[VbRadconService#5](../../../samples/snippets/csharp/VS_Snippets_VBCSharp/VbRadconService/CS/MyNewService.cs#5)]
[!code-vb[VbRadconService#5](../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbRadconService/VB/MyNewService.vb#5)]

## <a name="set-service-status"></a>设置服务状态

服务向[服务控制管理器](/windows/desktop/Services/service-control-manager)报告其状态，以便用户可以判断服务是否运行正常。 默认情况下，从 <xref:System.ServiceProcess.ServiceBase> 继承的服务会报告一组有限的状态设置，包括 SERVICE_STOPPED、SERVICE_PAUSED 和 SERVICE_RUNNING。 如果服务需要一段时间才能启动，则报告 SERVICE_START_PENDING 状态非常有用。

可以通过添加调用 Windows [ SetServiceStatus](/windows/desktop/api/winsvc/nf-winsvc-setservicestatus) 函数的代码来实现 SERVICE_START_PENDING 和 SERVICE_STOP_PENDING 状态设置。

### <a name="implement-service-pending-status"></a>实现服务挂起状态

1. 将 `using` 语句添加到“MyNewService.cs”，或者，对于 <xref:System.Runtime.InteropServices?displayProperty=nameWithType> 命名空间，将 `Imports` 语句添加到“MyNewService.vb”   ：

    ```csharp
    using System.Runtime.InteropServices;
    ```

    ```vb
    Imports System.Runtime.InteropServices
    ```

2. 将以下代码添加到 MyNewService.cs 或 MyNewService.vb，声明 `ServiceState` 值和添加你将在平台调用中使用的状态结构   ：

    ```csharp
    public enum ServiceState
    {
        SERVICE_STOPPED = 0x00000001,
        SERVICE_START_PENDING = 0x00000002,
        SERVICE_STOP_PENDING = 0x00000003,
        SERVICE_RUNNING = 0x00000004,
        SERVICE_CONTINUE_PENDING = 0x00000005,
        SERVICE_PAUSE_PENDING = 0x00000006,
        SERVICE_PAUSED = 0x00000007,
    }

    [StructLayout(LayoutKind.Sequential)]
    public struct ServiceStatus
    {
        public int dwServiceType;
        public ServiceState dwCurrentState;
        public int dwControlsAccepted;
        public int dwWin32ExitCode;
        public int dwServiceSpecificExitCode;
        public int dwCheckPoint;
        public int dwWaitHint;
    };
    ```

    ```vb
    Public Enum ServiceState
        SERVICE_STOPPED = 1
        SERVICE_START_PENDING = 2
        SERVICE_STOP_PENDING = 3
        SERVICE_RUNNING = 4
        SERVICE_CONTINUE_PENDING = 5
        SERVICE_PAUSE_PENDING = 6
        SERVICE_PAUSED = 7
    End Enum

    <StructLayout(LayoutKind.Sequential)>
    Public Structure ServiceStatus
        Public dwServiceType As Long
        Public dwCurrentState As ServiceState
        Public dwControlsAccepted As Long
        Public dwWin32ExitCode As Long
        Public dwServiceSpecificExitCode As Long
        Public dwCheckPoint As Long
        Public dwWaitHint As Long
    End Structure
    ```

    > [!NOTE]
    > 服务控制管理器使用 [SERVICE_STATUS 结构](/windows/win32/api/winsvc/ns-winsvc-service_status)的 `dwWaitHint` 和 `dwCheckpoint` 成员来确定等待 Windows 服务启动或关闭所需的时间。 如果 `OnStart` 和 `OnStop` 方法运行时间较长，服务可以使用递增的 `dwCheckPoint` 值再次调用 `SetServiceStatus` 来请求更多时间。

3. 在 `MyNewService` 类中，使用[平台调用](../interop/consuming-unmanaged-dll-functions.md)声明 [SetServiceStatus 函数](/windows/desktop/api/winsvc/nf-winsvc-setservicestatus)：

    ```csharp
    [DllImport("advapi32.dll", SetLastError = true)]
    private static extern bool SetServiceStatus(System.IntPtr handle, ref ServiceStatus serviceStatus);
    ```

    ```vb
    Declare Auto Function SetServiceStatus Lib "advapi32.dll" (ByVal handle As IntPtr, ByRef serviceStatus As ServiceStatus) As Boolean
    ```

4. 若要实现“SERVICE_START_PENDING”状态，请将以下代码添加到 <xref:System.ServiceProcess.ServiceBase.OnStart%2A> 方法的开头：

    ```csharp
    // Update the service state to Start Pending.
    ServiceStatus serviceStatus = new ServiceStatus();
    serviceStatus.dwCurrentState = ServiceState.SERVICE_START_PENDING;
    serviceStatus.dwWaitHint = 100000;
    SetServiceStatus(this.ServiceHandle, ref serviceStatus);
    ```

    ```vb
    ' Update the service state to Start Pending.
    Dim serviceStatus As ServiceStatus = New ServiceStatus()
    serviceStatus.dwCurrentState = ServiceState.SERVICE_START_PENDING
    serviceStatus.dwWaitHint = 100000
    SetServiceStatus(Me.ServiceHandle, serviceStatus)
    ```

5. 将代码添加到 `OnStart` 方法的末尾，可将状态设置为 SERVICE_RUNNING：

    ```csharp
    // Update the service state to Running.
    serviceStatus.dwCurrentState = ServiceState.SERVICE_RUNNING;
    SetServiceStatus(this.ServiceHandle, ref serviceStatus);
    ```

    ```vb
    ' Update the service state to Running.
    serviceStatus.dwCurrentState = ServiceState.SERVICE_RUNNING
    SetServiceStatus(Me.ServiceHandle, serviceStatus)
    ```

6. （可选）如果 <xref:System.ServiceProcess.ServiceBase.OnStop%2A> 是长时间运行的方法，请在 `OnStop` 方法中重复此过程。 实现 SERVICE_STOP_PENDING 状态并在 `OnStop` 方法退出之前返回 SERVICE_STOPPED 状态。

   例如：

    ```csharp
    // Update the service state to Stop Pending.
    ServiceStatus serviceStatus = new ServiceStatus();
    serviceStatus.dwCurrentState = ServiceState.SERVICE_STOP_PENDING;
    serviceStatus.dwWaitHint = 100000;
    SetServiceStatus(this.ServiceHandle, ref serviceStatus);

    // Update the service state to Stopped.
    serviceStatus.dwCurrentState = ServiceState.SERVICE_STOPPED;
    SetServiceStatus(this.ServiceHandle, ref serviceStatus);
    ```

    ```vb
    ' Update the service state to Stop Pending.
    Dim serviceStatus As ServiceStatus = New ServiceStatus()
    serviceStatus.dwCurrentState = ServiceState.SERVICE_STOP_PENDING
    serviceStatus.dwWaitHint = 100000
    SetServiceStatus(Me.ServiceHandle, serviceStatus)

    ' Update the service state to Stopped.
    serviceStatus.dwCurrentState = ServiceState.SERVICE_STOPPED
    SetServiceStatus(Me.ServiceHandle, serviceStatus)
    ```

## <a name="add-installers-to-the-service"></a>向服务添加安装程序

需要先安装 Windows 服务然后才能运行，这会将其注册到服务控制管理器。 将安装程序添加到项目以处理注册详细信息。

1. 在“解决方案资源管理器”中，从“MyNewService.cs”或“MyNewService.vb”的快捷菜单中，选择“查看设计器”     。

2. 在“设计”视图中，选择背景区域，然后从快捷菜单中选择“添加安装程序”   。

     默认情况下，Visual Studio 会向项目中添加一个名为 `ProjectInstaller` 的组件类，其中包含两个安装程序。 这些安装程序是为你的服务以及服务的相关过程准备的。

3. 在“ProjectInstaller”的“设计”视图中，为 Visual C# 项目选择“serviceInstaller1”，或为 Visual Basic 项目选择“ServiceInstaller1”，然后选择快捷菜单中的“属性”      。

4. 在“属性”窗口中，验证 <xref:System.ServiceProcess.ServiceInstaller.ServiceName%2A> 属性是否已设置为“MyNewService”   。

5. 将文本添加到 <xref:System.ServiceProcess.ServiceInstaller.Description%2A> 属性，例如“一个示例服务”  。

     此文本显示在“服务”窗口的“说明”列中，并向用户说明了该服务   。

    ![“服务”窗口中的服务说明。](./media/windows-service-description.png "服务说明")

6. 将文本添加到 <xref:System.ServiceProcess.ServiceInstaller.DisplayName%2A> 属性。 例如，“MyNewService 显示名称”  。

     此文本显示在“服务”窗口的“显示名称”列中   。 此名称可以不同于 <xref:System.ServiceProcess.ServiceInstaller.ServiceName%2A> 属性，它是系统使用的名称（例如，用于启动服务的 `net start` 命令的名称）。

7. 从下拉列表中将 <xref:System.ServiceProcess.ServiceInstaller.StartType%2A> 属性设置为 <xref:System.ServiceProcess.ServiceStartMode.Automatic>。

8. 完成后，“属性”窗口应如下图所示  ：

     ![Windows 服务的安装程序属性](./media/windows-service-installer-properties.png "Windows 服务安装程序属性")

9. 在“ProjectInstaller”的“设计”视图中，为 Visual C# 项目选择“serviceProcessInstaller1”，或为 Visual Basic 项目选择“serviceProcessInstaller1”，然后选择快捷菜单中的“属性”      。 从下拉列表中将 <xref:System.ServiceProcess.ServiceProcessInstaller.Account%2A> 属性设置为 <xref:System.ServiceProcess.ServiceAccount.LocalSystem>。

     此设置将安装服务并使用本地系统帐户运行它。

    > [!IMPORTANT]
    > <xref:System.ServiceProcess.ServiceAccount.LocalSystem> 帐户具有广泛的权限，包括能够写入事件日志。 使用此帐户时要特别小心，因为它会增加你受到恶意软件攻击的风险。 对于其他任务，请考虑使用 <xref:System.ServiceProcess.ServiceAccount.LocalService> 帐户，该帐户用作本地计算机上的非特权用户，并向任意远程服务器提供匿名凭据。 如果你尝试使用 <xref:System.ServiceProcess.ServiceAccount.LocalService> 账户，此例子将失败，因为它需要写入事件日志的权限。

有关安装程序的详细信息，请参阅[如何：将安装程序添加到服务应用程序](how-to-add-installers-to-your-service-application.md)。

## <a name="optional-set-startup-parameters"></a>（可选）设置启动参数

> [!NOTE]
> 在决定添加启动参数前，请考虑这是否是向你的服务传递信息的最好办法。 虽然它们易于使用和分析，而且用户可以轻松地替代它们，但是如果没有相关的文档说明，用户可能较难发现和使用它们。 通常情况下，如果服务需要不止几个启动参数，则应改用注册表或配置文件。

Windows 服务可以接受命令行参数或启动参数。 将代码添加到进程启动参数后，用户可以在服务属性窗口中使用自己的自定义启动参数启动服务。 但是，这些启动参数不会保留到下一次服务启动。 若要永久设置启动参数，请在注册表中设置它们。

每个 Windows 服务在“HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services”子项下都有一个注册表项  。 在每个服务的子项下，使用“参数”子项存储服务可以访问的信息  。 你可以使用 Windows 服务的应用程序配置文件，与使用其他类型的程序一样。 有关示例代码，请参阅 <xref:System.Configuration.ConfigurationManager.AppSettings?displayProperty=nameWithType>。

### <a name="to-add-startup-parameters"></a>添加启动参数

1. 选择“Program.cs”或“MyNewService.Designer.vb”，然后从快捷菜单中选择“查看代码”   。 在 `Main` 方法中，更改代码以添加输入参数并将其传递给服务构造函数：

   [!code-csharp[VbRadconService](../../../samples/snippets/csharp/VS_Snippets_VBCSharp/VbRadconService/CS/Program-add-parameter.cs?highlight=1,6)]
   [!code-vb[VbRadconService](../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbRadconService/VB/MyNewService.Designer-add-parameter.vb?highlight=1-2)]

2. 在“MyNewService.cs”或“MyNewService.vb”中，更改 `MyNewService` 构造函数以处理输入参数，如下所示   ：

   ```csharp
   using System.Diagnostics;

   public MyNewService(string[] args)
   {
       InitializeComponent();

       string eventSourceName = "MySource";
       string logName = "MyNewLog";

       if (args.Length > 0)
       {
          eventSourceName = args[0];
       }

       if (args.Length > 1)
       {
           logName = args[1];
       }

       eventLog1 = new EventLog();

       if (!EventLog.SourceExists(eventSourceName))
       {
           EventLog.CreateEventSource(eventSourceName, logName);
       }

       eventLog1.Source = eventSourceName;
       eventLog1.Log = logName;
   }
   ```

   ```vb
   Imports System.Diagnostics

   Public Sub New(ByVal cmdArgs() As String)
       InitializeComponent()
       Dim eventSourceName As String = "MySource"
       Dim logName As String = "MyNewLog"
       If (cmdArgs.Count() > 0) Then
           eventSourceName = cmdArgs(0)
       End If
       If (cmdArgs.Count() > 1) Then
           logName = cmdArgs(1)
       End If
       eventLog1 = New EventLog()
       If (Not EventLog.SourceExists(eventSourceName)) Then
           EventLog.CreateEventSource(eventSourceName, logName)
       End If
       eventLog1.Source = eventSourceName
       eventLog1.Log = logName
   End Sub
   ```

   此代码根据用户提供的启动参数设置事件源和日志名称。 如果未提供参数，则使用默认值。

3. 若要指定命令行参数，请将以下代码添加到“ProjectInstaller.cs”或“ProjectInstaller.vb”中的 `ProjectInstaller` 类   ：

   ```csharp
   protected override void OnBeforeInstall(IDictionary savedState)
   {
       string parameter = "MySource1\" \"MyLogFile1";
       Context.Parameters["assemblypath"] = "\"" + Context.Parameters["assemblypath"] + "\" \"" + parameter + "\"";
       base.OnBeforeInstall(savedState);
   }
   ```

   ```vb
   Protected Overrides Sub OnBeforeInstall(ByVal savedState As IDictionary)
       Dim parameter As String = "MySource1"" ""MyLogFile1"
       Context.Parameters("assemblypath") = """" + Context.Parameters("assemblypath") + """ """ + parameter + """"
       MyBase.OnBeforeInstall(savedState)
   End Sub
   ```

   通常，此值包含 Windows 服务的可执行文件的完整路径。 要使服务正确启动，用户必须为路径和每个参数提供引号。 用户可以更改“ImagePath”注册表项中的参数，以更改 Windows 服务的启动参数  。 但是，更好的方法是以编程方式更改值，并以用户友好的方式公开功能，例如使用管理或配置实用程序。

## <a name="build-the-service"></a>生成服务

1. 在“解决方案资源管理器”中，从“MyNewService”项目的快捷菜单中选择“属性”    。

   项目的属性页面会显示出来。

2. 在“应用程序”选项卡上，在“启动对象”列表中，为 Visual Basic 项目选择 **“MyNewService.Program”** 或 **“Sub Main”**   。

3. 要在“解决方案资源管理器”中生成项目，请从项目的快捷菜单中选择“生成”（或按 Ctrl+Shift+B）      。

## <a name="install-the-service"></a>安装服务

由于已经生成了 Windows 服务，你现在可以安装它。 要安装 Windows 服务，必须在安装它的计算机上拥有管理员凭据。

1. 使用管理凭据打开[“Visual Studio 开发人员命令提示”](/visualstudio/ide/reference/command-prompt-powershell)。

2. 在“Visual Studio 的开发人员命令提示”中，导航到包含项目输出的文件夹（默认情况下，它是项目的 \bin\Debug 子目录）。

3. 输入以下命令：

    ```shell
    installutil MyNewService.exe
    ```

    如果服务成功安装，该命令将报告成功。

    如果系统找不到 installutil.exe，请确保你的计算机上存在该安装程序  。 此工具随 .NET Framework 安装在 %windir%\Microsoft.NET\Framework[64]\\&lt;framework version&gt; 文件夹下  。 例如，64 位版本的默认路径是 %windir%\Microsoft.NET\Framework64\v4.0.30319\InstallUtil.exe  。

    如果“installutil.exe”进程失败，请查看安装日志以找出原因  。 默认情况下，该日志与服务可执行文件位于同一文件夹中。 在以下情况下安装可能会失败：
    - `ProjectInstaller` 类中不存在 <xref:System.ComponentModel.RunInstallerAttribute> 类。
    - 该属性未设置为 `true`。
    - `ProjectInstaller` 类未定义为 `public`。

有关详细信息，请参阅[如何：安装和卸载服务](how-to-install-and-uninstall-services.md)。

## <a name="start-and-run-the-service"></a>启动并运行服务

1. 在 Windows 中，打开  “服务”桌面应用程序。 按“Windows 徽标键+R”以打开“运行”框，然后输入 services.msc 并按 Enter 或单击“确定”       。

     你会看到  “服务”中列出的服务按其设置的显示名称的字母顺序显示。

     ![服务窗口中的 MyNewService](./media/windowsservices-serviceswindow.PNG)

2. 若要启动该服务，请从服务的快捷菜单中选择“启动”  。

3. 若要停止该服务，请从服务的快捷菜单中选择“停止”  。

4. （可选）可在命令行使用“net start&lt;服务名称&gt;”和“net stop&lt;服务名称&gt;”来启动和停止服务   。

### <a name="verify-the-event-log-output-of-your-service"></a>验证服务的事件日志输出

1. 在 Windows 中，打开“事件查看器”桌面应用  。 在 Windows 搜索栏中输入事件查看器，然后从搜索结果中选择“事件查看器”   。

   > [!TIP]
   > 在 Visual Studio 中，可通过从“查看”菜单（或按 Ctrl+Alt+S）打开“服务器资源管理器”并展开本地计算机的“事件日志”节点来访问事件日志       。

2. 在  “事件查看器”中，展开“应用程序和服务日志”  。

3. 找到“MyNewLog”（如果按照过程来添加命令行参数，则找到“MyLogFile1”）列表并将其展开   。 你会看到你的服务所执行的两个操作（启动和停止）的条目。

     ![使用事件查看器查看事件日志条目](./media/windows-service-event-viewer.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要 Windows 服务应用，则可以将其删除。

1. 使用管理凭据打开 **“Visual Studio 开发人员命令提示”** 。

2. 在“Visual Studio 的开发人员命令提示”窗口中，导航到包含项目输出的文件夹  。

3. 输入以下命令：

    ```shell
    installutil.exe /u MyNewService.exe
    ```

   如果服务成功卸载，该命令将报告已成功删除服务。 有关详细信息，请参阅[如何：安装和卸载服务](how-to-install-and-uninstall-services.md)。

## <a name="next-steps"></a>后续步骤

创建服务后，现在可以：

- 为其他人创建一个独立的安装程序，用于安装 Windows 服务。 使用 [WiX 工具集](https://wixtoolset.org/)为 Windows 服务创建安装程序。 有关其他提示，请参阅[创建安装程序包](/visualstudio/deployment/deploying-applications-services-and-components#create-an-installer-package-windows-desktop)。

- 探索 <xref:System.ServiceProcess.ServiceController> 组件，以便将命令发送到已安装的服务。

- 不是在应用程序运行时创建事件日志，而是在安装应用程序时使用安装程序创建事件日志。 卸载应用程序时，安装程序将删除事件日志。 有关详细信息，请参阅 <xref:System.Diagnostics.EventLogInstaller>。

## <a name="see-also"></a>请参阅

- [Windows 服务应用程序](index.md)
- [Windows 服务应用程序简介](introduction-to-windows-service-applications.md)
- [如何：调试 Windows 服务应用程序](how-to-debug-windows-service-applications.md)
- [服务 (Windows)](/windows/desktop/Services/services)

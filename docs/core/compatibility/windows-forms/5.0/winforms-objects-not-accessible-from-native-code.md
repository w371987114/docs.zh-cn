---
title: 中断性变更：WinForms 对象不可通过本机代码访问
description: 了解 .NET 5 中的中断性变更：不可再通过本机代码访问 Windows 窗体对象。
ms.date: 01/29/2021
ms.openlocfilehash: 823d37cb8115b8669b254878325a350809393e79
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102256084"
---
# <a name="native-code-cant-access-windows-forms-objects"></a>本机代码无法访问 Windows 窗体对象

从 .NET 5 开发，你无法再通过本机代码访问 Windows 窗体对象了。

## <a name="change-description"></a>更改描述

在旧版 .NET 中，某些 Windows 窗体类型被修饰为对 COM 互操作可见，因此可通过本机代码访问。 从 .NET 5 开始，Windows 窗体 API 对 COM 互操作不可见或不可通过本机代码访问。 .NET 运行时不再直接支持创建自定义类型库。 此外，.NET 运行时也无法依赖于 .NET Framework 的类型库（这就要求保持类在 .NET Framework 中的形状）。

## <a name="reason-for-change"></a>更改原因

- 从用于生成和查找类型库（TLB 文件）的枚举中删除 `ComVisible(true)`：由于 .NET Core 没有提供 WinForms TLB，因此保留这个特性没有价值。
- 从 `AccessibleObject` 类中删除 `ComVisible(true)`：这些类是不可 CoCreateable 的（它们没有无参数构造函数），并且向 COM 公开已存在的实例不需要此特性。
- 从 `Control` 和 `Component` 类中删除 `ComVisible(true)`：这是用来允许通过 OLE/ActiveX 托管 WinForms 控件的（例如，在 VB6 或 MFC 中）。 但是，这需要用于 WinForms 的 TLB （不再提供），以及基于注册表的激活（这也不能开箱即用）。 一般来说，没有维护基于 COM 的 WinForms 控件托管，因此支持被删除，而不是让它处于不受支持的状态。
- 从控件中删除 `ClassInterface` 特性：如果不支持通过 OLE/ActiveX 进行托管，则不再需要这些特性。 它们被保存在对象仍向 COM 公开且特性可能相关的其他位置。
- 从 `EventArgs` 中删除 `ComVisible(true)`：它们很可能与不再受支持的 OLE/ActiveX 托管一起使用。 它们也是不可 CoCreateable 的，所以此特性没有任何用途。 此外，公开现有实例而不提供 TLB 也没有意义。
- 从委托中删除 `ComVisible(true)`：目的未知，但由于不再支持通过 ActiveX 托管 WinForms 控件，因此它不太可能有任何用处。
- 从某些非公共代码中删除 `ComVisible(true)`：唯一的潜在使用者是新的 Visual Studio 设计器，但如果没有指定 GUID，就不太可能仍然需要它。
- 从某些任意公共设计器类中删除 `ComVisible(true)`：旧版 Visual Studio 设计器可能已使用 COM 互操作来与这些类通信。 然而，旧版设计器不支持 .NET Core，所以很少有人会需要将这些设置为 `ComVisible`。
- `IWin32Window` 定义了与 .NET Framework 中定义的 GUID 相同的 GUID，这会带来危险的后果。 如果你需要与 .NET Framework 互操作，请使用 `ComImport`。
- WinForms 管理的 `IDataObject` 被设置为 `ComVisible`。 这不是必需的，对于 `IDataObject` COM 互操作，有一个单独的 `ComImport` 接口声明。 将管理的 `IDataObject` 设置为 `ComVisible` 会适得其反，因为没有提供 TLB，封送处理总是会失败。 另外，GUID 没有被指定，并且与 .NET Framework 不同，因此删除未记录的 IID 不太可能对客户产生负面影响。
- 删除 `ComVisible(false)`：当默认不向 COM 互操作公开类时，它们被放置在看似任意的位置上，并且是多余的。

## <a name="version-introduced"></a>引入的版本

.NET 5.0

## <a name="recommended-action"></a>建议的操作

下面的示例适用于 .NET Framework 和 .NET Core 3.1。 此示例依赖于 .NET Framework 类型库，这个类型库允许 JavaScript 通过反射回叫窗体子类。

```cs
[PermissionSet(SecurityAction.Demand, Name="FullTrust")]
[System.Runtime.InteropServices.ComVisibleAttribute(true)]
public class Form1 : Form
{
    private WebBrowser webBrowser1 = new WebBrowser();

    protected override void OnLoad(EventArgs e)
    {
        webBrowser1.AllowWebBrowserDrop = false;
        webBrowser1.IsWebBrowserContextMenuEnabled = false;
        webBrowser1.WebBrowserShortcutsEnabled = false;
        webBrowser1.ObjectForScripting = this;

        webBrowser1.DocumentText =
            "<html><body><button " +
            "onclick=\"window.external.Test('called from script code')\">" +
            "call client code from script code</button>" +
            "</body></html>";
    }

    public void Test(String message)
    {
        MessageBox.Show(message, "client code");
    }
}
```

可采用两种方法使此示例在 .NET 5 及更高版本上运行：

- 引入支持 `IDispatch` 的用户声明的 `ObjectForScripting` 对象（默认情况下应用，除非在项目级别显式更改）。

  ```cs
  public class MyScriptObject
  {
      private Form1 _form;

      public MyScriptObject(Form1 form)
      {
          _form = form;
      }

      public void Test(string message)
      {
          MessageBox.Show(message, "client code");
      }
  }

  public partial class Form1 : Form
  {
      protected override void OnLoad(EventArgs e)
      {
          ...

          // Works correctly.
          webBrowser1.ObjectForScripting = new MyScriptObject(this);

          ...
      }
  }
  ```

- 使用要公开的方法来声明接口。

  ```cs
  public interface IForm1
  {
      void Test(string message);
  }

  [ComDefaultInterface(typeof(IForm1))]
  public partial class Form1 : Form, IForm1
  {
      protected override void OnLoad(EventArgs e)
      {
          ...

          // Works correctly.
          webBrowser1.ObjectForScripting = this;

          ...
      }
  }
  ```

## <a name="affected-apis"></a>受影响的 API

所有 Windows 窗体 API。

<!--

### Category

- Windows Forms

-->

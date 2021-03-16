---
title: 中断性变更：PrincipalPermissionAttribute 已过时，报告为错误
description: 了解核心 .NET 库中的 .NET 5 中断性变更：PrincipalPermissionAttribute 构造函数已过时，会生成编译时错误。
ms.date: 11/01/2020
ms.openlocfilehash: 7568883935633e98b884b553efccf50504448b77
ms.sourcegitcommit: 9c589b25b005b9a7f87327646020eb85c3b6306f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102257227"
---
# <a name="principalpermissionattribute-is-obsolete-as-error"></a>PrincipalPermissionAttribute 已过时，报告为错误

<xref:System.Security.Permissions.PrincipalPermissionAttribute> 构造函数已过时，会产生编译时错误。 不能实例化此属性或将其应用于方法。

## <a name="change-description"></a>更改描述

在 .NET Framework 和 .NET Core 上，可以用 <xref:System.Security.Permissions.PrincipalPermissionAttribute> 属性批注方法。 例如：

```csharp
[PrincipalPermission(SecurityAction.Demand, Role = "Administrators")]
public void MyMethod()
{
    // Code that should only run when the current user is an administrator.
}
```

从 .NET 5 开始，不能将 <xref:System.Security.Permissions.PrincipalPermissionAttribute> 属性应用于方法。 该属性的构造函数已过时，会产生编译时错误。 与其他过时警告不同，无法禁止显示此错误。

## <a name="reason-for-change"></a>更改原因

<xref:System.Security.Permissions.PrincipalPermissionAttribute> 类型与子类 <xref:System.Security.Permissions.SecurityAttribute> 等其他类型一样，是 .NET 的代码访问安全性 (CAS) 基础结构的一部分。 在 .NET Framework 2.x - 4.x 中，即使应用程序在完全信任的情况下运行，运行时也会在方法条目上强制使用 <xref:System.Security.Permissions.PrincipalPermissionAttribute> 批注。 .NET Core 和 .NET 5 及更高版本不支持 CAS 属性，而且运行时将忽略这些属性。

从 .NET Framework 到 .NET Core 和 .NET 5 的行为差异可能导致“无法打开”的情况，在这种情况下，本应阻止但却允许了访问。 为防止出现“无法打开”的情况，你不能再在面向 .NET 5 或更高版本的代码中应用该属性。

## <a name="version-introduced"></a>引入的版本

5.0

## <a name=""></a><a id="permission-action">建议的操作</a>

如果遇到过时错误，则必须采取措施。

- **如果要将属性应用于 ASP.NET MVC 操作方法，请执行以下操作：**

  考虑使用 ASP.NET 的内置授权基础结构。 以下代码演示如何使用 <xref:System.Web.Mvc.AuthorizeAttribute> 属性来为控制器添加批注。 ASP.NET 运行时将在执行操作之前向用户授权。

  ```csharp
  using Microsoft.AspNetCore.Authorization;

  namespace MySampleApp
  {
      [Authorize(Roles = "Administrator")]
      public class AdministrationController : Controller
      {
          public ActionResult MyAction()
          {
              // This code won't run unless the current user
              // is in the 'Administrator' role.
          }
      }
  }
  ```

  有关详细信息，请参阅 [ASP.NET Core 中基于角色的授权](/aspnet/core/security/authorization/roles)和 [ASP.NET Core 中的授权简介](/aspnet/core/security/authorization/introduction)。

- **如果要将属性应用到 Web 应用上下文之外的库代码，请执行以下操作：**

  在方法开始时手动执行检查。 这可以通过使用 <xref:System.Security.Principal.IPrincipal.IsInRole(System.String)?displayProperty=nameWithType> 方法来完成。

  ```csharp
  using System.Threading;

  void DoSomething()
  {
      if (Thread.CurrentPrincipal == null
          || !Thread.CurrentPrincipal.IsInRole("Administrators"))
      {
          throw new Exception("User is anonymous or isn't an admin.");
      }

      // Code that should run only when user is an administrator.
  }
  ```

## <a name="affected-apis"></a>受影响的 API

- <xref:System.Security.Permissions.PrincipalPermissionAttribute?displayProperty=fullName>

<!--

#### Category

- Core .NET libraries
- Security

### Affected APIs

- `T:System.Security.Permissions.PrincipalPermissionAttribute`

-->

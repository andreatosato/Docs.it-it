---
title: Autorizzazione semplice
author: rick-anderson
description: Questo documento viene illustrato come utilizzare l'attributo Authorize per limitare l'accesso alle azioni e i controller di ASP.NET Core.
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/simple
ms.openlocfilehash: f1d5671785da815f2f4fcf5bef1352f4c9e62877
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="simple-authorization"></a><span data-ttu-id="8109f-103">Autorizzazione semplice</span><span class="sxs-lookup"><span data-stu-id="8109f-103">Simple Authorization</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="8109f-104">L'autorizzazione in MVC è controllata tramite il `AuthorizeAttribute` attributo e i relativi parametri diversi.</span><span class="sxs-lookup"><span data-stu-id="8109f-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="8109f-105">Nella forma più semplice, l'applicazione di `AuthorizeAttribute` dell'attributo a un controller o un'azione verrà limitato l'accesso al controller o un'azione da qualsiasi utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="8109f-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="8109f-106">Ad esempio, il codice seguente limita l'accesso per il `AccountController` da qualsiasi utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="8109f-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="8109f-107">Se si desidera applicare l'autorizzazione a un'azione anziché il controller, applicare il `AuthorizeAttribute` attributo per l'azione di se stesso:</span><span class="sxs-lookup"><span data-stu-id="8109f-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="8109f-108">Ora possono accedere solo gli utenti autenticati di `Logout` (funzione).</span><span class="sxs-lookup"><span data-stu-id="8109f-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="8109f-109">È inoltre possibile utilizzare il `AllowAnonymousAttribute` attributo per consentire l'accesso agli utenti non autenticati di singole azioni.</span><span class="sxs-lookup"><span data-stu-id="8109f-109">You can also use the `AllowAnonymousAttribute` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="8109f-110">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8109f-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="8109f-111">In questo modo solo gli utenti autenticati per il `AccountController`, fatta eccezione per il `Login` azione, che è accessibile da tutti gli utenti, indipendentemente dal loro stato autenticato o anonimo / non autenticato.</span><span class="sxs-lookup"><span data-stu-id="8109f-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

>[!WARNING]
> <span data-ttu-id="8109f-112">`[AllowAnonymous]`Ignora tutte le istruzioni di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="8109f-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="8109f-113">Se si applica a combinare `[AllowAnonymous]` e qualsiasi `[Authorize]` attributo quindi gli attributi di autorizzazione verranno sempre ignorati.</span><span class="sxs-lookup"><span data-stu-id="8109f-113">If you apply combine `[AllowAnonymous]` and any `[Authorize]` attribute then the Authorize attributes will always be ignored.</span></span> <span data-ttu-id="8109f-114">Ad esempio, se si applica `[AllowAnonymous]` il controller a livello qualsiasi `[Authorize]` gli attributi allo stesso controller o in qualsiasi azione all'interno di esso verranno ignorati.</span><span class="sxs-lookup"><span data-stu-id="8109f-114">For example if you apply `[AllowAnonymous]` at the controller level any `[Authorize]` attributes on the same controller, or on any action within it will be ignored.</span></span>
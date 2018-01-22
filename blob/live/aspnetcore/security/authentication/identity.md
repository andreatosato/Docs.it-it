---
title: "Introduzione all'identità su ASP.NET Core"
author: rick-anderson
description: "Usa l'identità con un'applicazione ASP.NET di base"
ms.author: riande
manager: wpickett
ms.date: 01/02/2018
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity
ms.openlocfilehash: 436a5ecfd126c9660591cd55efc1cc52b9493136
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="bbe4a-103">Introduzione all'identità su ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbe4a-103">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="bbe4a-104">Da [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), Jon Galloway, [Erik Reitan](https://github.com/Erikre), e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bbe4a-104">By [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), Jon Galloway, [Erik Reitan](https://github.com/Erikre), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bbe4a-105">Identità di ASP.NET Core è un sistema di appartenenze che consente di aggiungere funzionalità di accesso all'applicazione.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-105">ASP.NET Core Identity is a membership system which allows you to add login functionality to your application.</span></span> <span data-ttu-id="bbe4a-106">Gli utenti possono creare un account e l'account di accesso con un nome utente e password o che è possibile utilizzare un provider di accesso esterno, ad esempio Facebook, Google, Account Microsoft, Twitter o di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-106">Users can create an account and login with a user name and password or they can use an external login provider such as Facebook, Google, Microsoft Account, Twitter or others.</span></span>

<span data-ttu-id="bbe4a-107">È possibile configurare ASP.NET Identity Core per l'utilizzo di un database di SQL Server per archiviare i nomi utente, password e i dati di profilo.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-107">You can configure ASP.NET Core Identity to use a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="bbe4a-108">In alternativa, è possibile utilizzare un archivio permanente, ad esempio, un archivio tabelle di Azure.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-108">Alternatively, you can use your own persistent store, for example, an Azure Table Storage.</span></span> <span data-ttu-id="bbe4a-109">Questo documento contiene istruzioni per Visual Studio e per usando l'interfaccia CLI.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-109">This document contains instructions for Visual Studio and for using the CLI.</span></span>

[<span data-ttu-id="bbe4a-110">Consente di visualizzare o scaricare il codice di esempio.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-110">View or download the sample code.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) [<span data-ttu-id="bbe4a-111">(Come scaricare)</span><span class="sxs-lookup"><span data-stu-id="bbe4a-111">(How to download)</span></span>](https://docs.microsoft.com/en-us/aspnet/core/tutorials/index#how-to-download-a-sample)

## <a name="overview-of-identity"></a><span data-ttu-id="bbe4a-112">Panoramica dell'identità</span><span class="sxs-lookup"><span data-stu-id="bbe4a-112">Overview of Identity</span></span>

<span data-ttu-id="bbe4a-113">In questo argomento, verranno imparare a usare ASP.NET Identity Core per aggiungere funzionalità a registrare, accedere e disconnettersi da un utente.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-113">In this topic, you'll learn how to use ASP.NET Core Identity to add functionality to register, log in, and log out a user.</span></span> <span data-ttu-id="bbe4a-114">Per istruzioni più dettagliate sulla creazione di App scritte in ASP.NET Identity Core, vedere la sezione passaggi successivi alla fine di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-114">For more detailed instructions about creating apps using ASP.NET Core Identity, see the Next Steps section at the end of this article.</span></span>

1.  <span data-ttu-id="bbe4a-115">Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-115">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

    # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bbe4a-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbe4a-116">Visual Studio</span></span>](#tab/visual-studio)

    <span data-ttu-id="bbe4a-117">In Visual Studio, selezionare **File** > **New** > **progetto**.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-117">In Visual Studio, select **File** > **New** > **Project**.</span></span> <span data-ttu-id="bbe4a-118">Selezionare **applicazione Web di ASP.NET Core** e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-118">Select **ASP.NET Core Web Application** and click **OK**.</span></span>

    ![Finestra di dialogo Nuovo progetto](identity/_static/01-new-project.png)

    <span data-ttu-id="bbe4a-120">Selezionare un ASP.NET Core **applicazione Web (Model-View-Controller)** per ASP.NET Core 2. x, quindi selezionare **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-120">Select an ASP.NET Core **Web Application (Model-View-Controller)** for ASP.NET Core 2.x, then select **Change Authentication**.</span></span>

    ![Finestra di dialogo Nuovo progetto](identity/_static/02-new-project.png)

    <span data-ttu-id="bbe4a-122">Offerta una finestra di dialogo Opzioni di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-122">A dialog appears offering authentication choices.</span></span> <span data-ttu-id="bbe4a-123">Selezionare **singoli account utente di** e fare clic su **OK** per tornare alla finestra di dialogo precedente.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-123">Select **Individual User Accounts** and click **OK** to return to the previous dialog.</span></span>

    ![Finestra di dialogo Nuovo progetto](identity/_static/03-new-project-auth.png)

    <span data-ttu-id="bbe4a-125">Selezione **singoli account utente di** indica a Visual Studio per creare modelli, ViewModel, visualizzazioni, controller e altre risorse necessarie per l'autenticazione come parte del modello di progetto.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-125">Selecting **Individual User Accounts** directs Visual Studio to create Models, ViewModels, Views, Controllers, and other assets required for authentication as part of the project template.</span></span>

    # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bbe4a-126">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="bbe4a-126">.NET Core CLI</span></span>](#tab/netcore-cli)

    <span data-ttu-id="bbe4a-127">Se si utilizza l'interfaccia CLI Core .NET, creare il nuovo progetto utilizzando ``dotnet new mvc --auth Individual``.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-127">If using the .NET Core CLI, create the new project using ``dotnet new mvc --auth Individual``.</span></span> <span data-ttu-id="bbe4a-128">Questo comando crea un nuovo progetto con lo stesso codice di modello di identità che Visual Studio crea.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-128">This command creates a new project with the same Identity template code Visual Studio creates.</span></span>

    <span data-ttu-id="bbe4a-129">Il progetto creato contiene il `Microsoft.AspNetCore.Identity.EntityFrameworkCore` pacchetto, che mantiene i dati di identità e schema a SQL Server utilizzando [Entity Framework Core](https://docs.microsoft.com/ef/).</span><span class="sxs-lookup"><span data-stu-id="bbe4a-129">The created project contains the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` package, which persists the Identity data and schema to SQL Server using [Entity Framework Core](https://docs.microsoft.com/ef/).</span></span>

    ---

2.  <span data-ttu-id="bbe4a-130">Configurare i servizi di identità e aggiungere middleware `Startup`.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-130">Configure Identity services and add middleware in `Startup`.</span></span>

    <span data-ttu-id="bbe4a-131">I servizi di identità vengono aggiunti all'applicazione nel `ConfigureServices` metodo la `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="bbe4a-131">The Identity services are added to the application in the `ConfigureServices` method in the `Startup` class:</span></span>

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bbe4a-132">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bbe4a-132">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-39)]
    
    <span data-ttu-id="bbe4a-133">Questi servizi vengono resi disponibili per l'applicazione tramite [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bbe4a-133">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
    
    <span data-ttu-id="bbe4a-134">Identità è abilitata per l'applicazione chiamando `UseAuthentication` nel `Configure` metodo.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-134">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="bbe4a-135">`UseAuthentication`Aggiunge l'autenticazione [middleware](xref:fundamentals/middleware) alla pipeline delle richieste.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-135">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware) to the request pipeline.</span></span>
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bbe4a-136">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bbe4a-136">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-34)]
    
    <span data-ttu-id="bbe4a-137">Questi servizi vengono resi disponibili per l'applicazione tramite [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bbe4a-137">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
    
    <span data-ttu-id="bbe4a-138">Identità è abilitata per l'applicazione chiamando `UseIdentity` nel `Configure` metodo.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-138">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="bbe4a-139">`UseIdentity`Aggiunge l'autenticazione basata su cookie [middleware](xref:fundamentals/middleware) alla pipeline delle richieste.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-139">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware) to the request pipeline.</span></span>
        
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]
    
    ---
     
    <span data-ttu-id="bbe4a-140">Per ulteriori informazioni sull'avvio dell'applicazione, vedere [avvio dell'applicazione](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="bbe4a-140">For more information about the application start up process, see [Application Startup](xref:fundamentals/startup).</span></span>

3.  <span data-ttu-id="bbe4a-141">Creare un utente.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-141">Create a user.</span></span>

    <span data-ttu-id="bbe4a-142">Avviare l'applicazione e quindi fare clic su di **registrare** collegamento.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-142">Launch the application and then click on the **Register** link.</span></span>

    <span data-ttu-id="bbe4a-143">Se questa è la prima volta che si sta eseguendo questa azione, potrebbe essere necessario eseguire le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-143">If this is the first time you're performing this action, you may be required to run migrations.</span></span> <span data-ttu-id="bbe4a-144">L'applicazione viene richiesto di **applicare le migrazioni**.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-144">The application prompts you to **Apply Migrations**.</span></span> <span data-ttu-id="bbe4a-145">Se necessario, aggiornare la pagina.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-145">Refresh the page if needed.</span></span>
    
    ![Applicare le migrazioni pagina](identity/_static/apply-migrations.png)
    
    <span data-ttu-id="bbe4a-147">In alternativa, è possibile verificare l'utilizzo di ASP.NET Identity Core con l'app senza un database persistente utilizzando un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-147">Alternately, you can test using ASP.NET Core Identity with your app without a persistent database by using an in-memory database.</span></span> <span data-ttu-id="bbe4a-148">Per utilizzare un database in memoria, aggiungere il ``Microsoft.EntityFrameworkCore.InMemory`` pacchetto all'App e modificare la chiamata dell'applicazione a ``AddDbContext`` in ``ConfigureServices`` come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="bbe4a-148">To use an in-memory database, add the ``Microsoft.EntityFrameworkCore.InMemory`` package to your app and modify your app's call to ``AddDbContext`` in ``ConfigureServices`` as follows:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseInMemoryDatabase(Guid.NewGuid().ToString()));
    ```
    
    <span data-ttu-id="bbe4a-149">Quando l'utente sceglie il **registrare** collegamento, il ``Register`` azione viene richiamata sul ``AccountController``.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-149">When the user clicks the **Register** link, the ``Register`` action is invoked on ``AccountController``.</span></span> <span data-ttu-id="bbe4a-150">Il ``Register`` azione consente all'utente chiamando `CreateAsync` sul `_userManager` oggetto (fornito a ``AccountController`` dall'inserimento di dipendenze):</span><span class="sxs-lookup"><span data-stu-id="bbe4a-150">The ``Register`` action creates the user by calling `CreateAsync` on the `_userManager` object (provided to ``AccountController`` by dependency injection):</span></span>
 
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

    <span data-ttu-id="bbe4a-151">Se l'utente è stato creato correttamente, l'utente è connesso tramite la chiamata di ``_signInManager.SignInAsync``.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-151">If the user was created successfully, the user is logged in by the call to ``_signInManager.SignInAsync``.</span></span>

    <span data-ttu-id="bbe4a-152">**Nota:** vedere [account conferma](xref:security/authentication/accconfirm#prevent-login-at-registration) per la procedura impedire l'accesso immediato al momento della registrazione.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-152">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>
 
4.  <span data-ttu-id="bbe4a-153">Accedi.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-153">Log in.</span></span>
 
    <span data-ttu-id="bbe4a-154">Gli utenti possono accedere facendo clic di **Accedi** collegamento nella parte superiore del sito, o può essere reindirizzati alla pagina di accesso se si tenta di accedere a una parte del sito che richiede l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-154">Users can sign in by clicking the **Log in** link at the top of the site, or they may be navigated to the Login page if they attempt to access a part of the site that requires authorization.</span></span> <span data-ttu-id="bbe4a-155">Quando l'utente invia il form nella pagina di accesso, il ``AccountController`` ``Login`` azione viene chiamata.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-155">When the user submits the form on the Login page, the ``AccountController`` ``Login`` action is called.</span></span>

    <span data-ttu-id="bbe4a-156">Il ``Login`` azione chiama ``PasswordSignInAsync`` sul ``_signInManager`` oggetto (fornito a ``AccountController`` dall'inserimento di dipendenze).</span><span class="sxs-lookup"><span data-stu-id="bbe4a-156">The ``Login`` action calls ``PasswordSignInAsync`` on the ``_signInManager`` object (provided to ``AccountController`` by dependency injection).</span></span>

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]
 
    <span data-ttu-id="bbe4a-157">La base ``Controller`` classe espone un ``User`` proprietà che è possibile accedere dai metodi di controller.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-157">The base ``Controller`` class exposes a ``User`` property that you can access from controller methods.</span></span> <span data-ttu-id="bbe4a-158">Ad esempio, è possibile enumerare `User.Claims` e prendere decisioni di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-158">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="bbe4a-159">Per ulteriori informazioni, vedere [autorizzazione](xref:security/authorization/index).</span><span class="sxs-lookup"><span data-stu-id="bbe4a-159">For more information, see [Authorization](xref:security/authorization/index).</span></span>
 
5.  <span data-ttu-id="bbe4a-160">Effettuare la disconnessione.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-160">Log out.</span></span>
 
    <span data-ttu-id="bbe4a-161">Fare clic su di **disconnettersi** collegamento chiamate il `LogOut` azione.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-161">Clicking the **Log out** link calls the `LogOut` action.</span></span>
 
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]
 
    <span data-ttu-id="bbe4a-162">Il codice precedente le chiamate di sopra di `_signInManager.SignOutAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-162">The preceding code above calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="bbe4a-163">Il `SignOutAsync` metodo cancella attestazioni dell'utente archiviate in un cookie.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-163">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>
 
6.  <span data-ttu-id="bbe4a-164">Configurazione.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-164">Configuration.</span></span>

    <span data-ttu-id="bbe4a-165">Identità dispone di alcuni comportamenti predefiniti che è possibile eseguire l'override in una classe di avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-165">Identity has some default behaviors that you can override in your application's startup class.</span></span> <span data-ttu-id="bbe4a-166">Non è necessario configurare ``IdentityOptions`` se si usano i comportamenti predefiniti.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-166">You do not need to configure ``IdentityOptions`` if you are using the default behaviors.</span></span>

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bbe4a-167">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bbe4a-167">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-39)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bbe4a-168">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bbe4a-168">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=13-34)]

    ---
    
    <span data-ttu-id="bbe4a-169">Per ulteriori informazioni su come configurare l'identità, vedere [configurare identità](xref:security/authentication/identity-configuration).</span><span class="sxs-lookup"><span data-stu-id="bbe4a-169">For more information about how to configure Identity, see [Configure Identity](xref:security/authentication/identity-configuration).</span></span>
    
    <span data-ttu-id="bbe4a-170">È anche possibile configurare il tipo di dati della chiave primaria, vedere [tipo di dati di identità di configurare le chiavi primarie](xref:security/authentication/identity-primary-key-configuration).</span><span class="sxs-lookup"><span data-stu-id="bbe4a-170">You also can configure the data type of the primary key, see [Configure Identity primary keys data type](xref:security/authentication/identity-primary-key-configuration).</span></span>
 
7.  <span data-ttu-id="bbe4a-171">Visualizzare il database.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-171">View the database.</span></span>

    <span data-ttu-id="bbe4a-172">Se l'app Usa un database di SQL Server (impostazione predefinita in Windows e per gli utenti di Visual Studio), è possibile visualizzare il database dell'applicazione creata.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-172">If your app is using a SQL Server database (the default on Windows and for Visual Studio users), you can view the database the app created.</span></span> <span data-ttu-id="bbe4a-173">È possibile utilizzare **SQL Server Management Studio**.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-173">You can use **SQL Server Management Studio**.</span></span> <span data-ttu-id="bbe4a-174">In alternativa, da Visual Studio, selezionare **vista** > **Esplora oggetti di SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-174">Alternatively, from Visual Studio, select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="bbe4a-175">Connettersi a **(localdb) \MSSQLLocalDB**.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-175">Connect to **(localdb)\MSSQLLocalDB**.</span></span> <span data-ttu-id="bbe4a-176">Il database con un nome corrispondente **aspnet - <*nome del progetto*>-<*stringa data* >**  viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-176">The database with a name matching **aspnet-<*name of your project*>-<*date string*>** is displayed.</span></span>

    ![Menu di scelta rapida nella tabella di database AspNetUsers](identity/_static/04-db.png)
    
    <span data-ttu-id="bbe4a-178">Espandere il database e il relativo **tabelle**, quindi fare doppio clic su di **dbo. AspNetUsers** tabella e selezionare **Visualizza dati**.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-178">Expand the database and its **Tables**, then right-click the **dbo.AspNetUsers** table and select **View Data**.</span></span>

8. <span data-ttu-id="bbe4a-179">Verificare il funzionamento dell'identità</span><span class="sxs-lookup"><span data-stu-id="bbe4a-179">Verify Identity works</span></span>

    <span data-ttu-id="bbe4a-180">Il valore predefinito *applicazione Web di ASP.NET Core* modello di progetto consente agli utenti di accedere a qualsiasi azione nell'applicazione senza che sia all'account di accesso.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-180">The default *ASP.NET Core Web Application* project template allows users to access any action in the application without having to login.</span></span> <span data-ttu-id="bbe4a-181">Per verificare il funzionamento di ASP.NET Identity, aggiungere un`[Authorize]` attributo il `About` azione il `Home` Controller.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-181">To verify that ASP.NET Identity works, add an`[Authorize]` attribute to the `About` action of the `Home` Controller.</span></span>
 
    ```cs
    [Authorize]
    public IActionResult About()
    {
        ViewData["Message"] = "Your application description page.";
        return View();
    }
    ```
    
    # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bbe4a-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbe4a-182">Visual Studio</span></span>](#tab/visual-studio)

    <span data-ttu-id="bbe4a-183">Eseguire il progetto utilizzando **Ctrl** + **F5** e passare il **su** pagina.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-183">Run the project using **Ctrl** + **F5** and navigate to the **About** page.</span></span> <span data-ttu-id="bbe4a-184">Possono accedere solo gli utenti autenticati di **su** pagina ora, in modo ASP.NET si viene reindirizzati alla pagina di accesso per accedere o registrare.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-184">Only authenticated users may access the **About** page now, so ASP.NET redirects you to the login page to login or register.</span></span>

    # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bbe4a-185">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="bbe4a-185">.NET Core CLI</span></span>](#tab/netcore-cli)

    <span data-ttu-id="bbe4a-186">Aprire una finestra di comando e passare alla radice del progetto directory contenente il `.csproj` file.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-186">Open a command window and navigate to the project's root directory containing the `.csproj` file.</span></span> <span data-ttu-id="bbe4a-187">Eseguire il `dotnet run` comando per eseguire l'app:</span><span class="sxs-lookup"><span data-stu-id="bbe4a-187">Run the `dotnet run` command to run the app:</span></span>

    ```cs
    dotnet run 
    ```

    <span data-ttu-id="bbe4a-188">Individuare l'URL specificato nell'output del `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-188">Browse the URL specified in the output from the `dotnet run` command.</span></span> <span data-ttu-id="bbe4a-189">L'URL deve puntare a `localhost` con un numero di porta generato.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-189">The URL should point to `localhost` with a generated port number.</span></span> <span data-ttu-id="bbe4a-190">Passare il **su** pagina.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-190">Navigate to the **About** page.</span></span> <span data-ttu-id="bbe4a-191">Possono accedere solo gli utenti autenticati di **su** pagina ora, in modo ASP.NET si viene reindirizzati alla pagina di accesso per accedere o registrare.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-191">Only authenticated users may access the **About** page now, so ASP.NET redirects you to the login page to login or register.</span></span>

    ---

## <a name="identity-components"></a><span data-ttu-id="bbe4a-192">Componenti di identità</span><span class="sxs-lookup"><span data-stu-id="bbe4a-192">Identity Components</span></span>

<span data-ttu-id="bbe4a-193">L'assembly di riferimento principale per il sistema di identità è `Microsoft.AspNetCore.Identity`.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-193">The primary reference assembly for the Identity system is `Microsoft.AspNetCore.Identity`.</span></span> <span data-ttu-id="bbe4a-194">Questo pacchetto contiene il set di base di interfacce per ASP.NET Identity Core e viene incluso per `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-194">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

<span data-ttu-id="bbe4a-195">Queste dipendenze sono necessarie per utilizzare il sistema di identità nelle applicazioni ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bbe4a-195">These dependencies are needed to use the Identity system in ASP.NET Core applications:</span></span>

* <span data-ttu-id="bbe4a-196">`Microsoft.AspNetCore.Identity.EntityFrameworkCore`-Contiene i tipi necessari per l'uso di identità con Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-196">`Microsoft.AspNetCore.Identity.EntityFrameworkCore` - Contains the required types to use Identity with Entity Framework Core.</span></span>

* <span data-ttu-id="bbe4a-197">`Microsoft.EntityFrameworkCore.SqlServer`-Entity Framework Core è una tecnologia di accesso ai dati consigliati di Microsoft per i database relazionali, ad esempio SQL Server.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-197">`Microsoft.EntityFrameworkCore.SqlServer` - Entity Framework Core is Microsoft's recommended data access technology for relational databases like SQL Server.</span></span> <span data-ttu-id="bbe4a-198">Per i test, è possibile utilizzare `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-198">For testing, you can use `Microsoft.EntityFrameworkCore.InMemory`.</span></span>

* <span data-ttu-id="bbe4a-199">`Microsoft.AspNetCore.Authentication.Cookies`-Middleware che consente a un'applicazione utilizzare l'autenticazione basata su cookie.</span><span class="sxs-lookup"><span data-stu-id="bbe4a-199">`Microsoft.AspNetCore.Authentication.Cookies` - Middleware that enables an app to use cookie-based authentication.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="bbe4a-200">La migrazione a identità ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbe4a-200">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="bbe4a-201">Per ulteriori informazioni e istruzioni sulla migrazione dell'identità esistenti dell'archivio vedere [la migrazione di autenticazione e identità](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="bbe4a-201">For additional information and guidance on migrating your existing Identity store see [Migrating Authentication and Identity](xref:migration/identity).</span></span>

## <a name="next-steps"></a><span data-ttu-id="bbe4a-202">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="bbe4a-202">Next Steps</span></span>

* [<span data-ttu-id="bbe4a-203">Autenticazione della migrazione e identità</span><span class="sxs-lookup"><span data-stu-id="bbe4a-203">Migrating Authentication and Identity</span></span>](xref:migration/identity)
* [<span data-ttu-id="bbe4a-204">Conferma account e recupero password</span><span class="sxs-lookup"><span data-stu-id="bbe4a-204">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="bbe4a-205">Autenticazione a due fattori con SMS</span><span class="sxs-lookup"><span data-stu-id="bbe4a-205">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="bbe4a-206">Abilitazione dell'autenticazione con Facebook, Google e altri provider esterni</span><span class="sxs-lookup"><span data-stu-id="bbe4a-206">Enabling authentication using Facebook, Google and other external providers</span></span>](xref:security/authentication/social/index)
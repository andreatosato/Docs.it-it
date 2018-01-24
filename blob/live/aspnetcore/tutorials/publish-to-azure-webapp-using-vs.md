---
title: Pubblicare un'app ASP.NET Core in Azure con Visual Studio
author: rick-anderson
description: Informazioni su come pubblicare un'app ASP.NET Core in Servizio app di Azure con Visual Studio.
ms.author: riande
manager: wpickett
ms.date: 12/16/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: dd31e3a9583a0c152e97ae7cf6b215389298a20c
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
<span data-ttu-id="05b61-103">/it-it</span><span class="sxs-lookup"><span data-stu-id="05b61-103">/en-us</span></span>

# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a><span data-ttu-id="05b61-104">Pubblicare un'app Web ASP.NET Core in Servizio app di Azure con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b61-104">Publish an ASP.NET Core web app to Azure App Service using Visual Studio</span></span>

<span data-ttu-id="05b61-105">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs) e [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="05b61-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs), and [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="05b61-106">Vedere [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) (Pubblicare in Azure da Visual Studio per Mac) se si usa un Mac.</span><span class="sxs-lookup"><span data-stu-id="05b61-106">See [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) if you are working on a Mac.</span></span>

## <a name="set-up"></a><span data-ttu-id="05b61-107">Impostare</span><span class="sxs-lookup"><span data-stu-id="05b61-107">Set up</span></span>

* <span data-ttu-id="05b61-108">Aprire un [account Azure gratuito](https://aka.ms/K5y5yh) se non è già disponibile un account.</span><span class="sxs-lookup"><span data-stu-id="05b61-108">Open a [free Azure account](https://aka.ms/K5y5yh) if you do not have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="05b61-109">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="05b61-109">Create a web app</span></span>

<span data-ttu-id="05b61-110">Nella Pagina iniziale di Visual Studio selezionare **File > Nuovo > Progetto**</span><span class="sxs-lookup"><span data-stu-id="05b61-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![File (menu)](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="05b61-112">Completare la finestra di dialogo **Nuovo progetto**:</span><span class="sxs-lookup"><span data-stu-id="05b61-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="05b61-113">Nel riquadro a sinistra selezionare **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="05b61-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="05b61-114">Nel riquadro al centro selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="05b61-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="05b61-115">Scegliere **OK**.</span><span class="sxs-lookup"><span data-stu-id="05b61-115">Select **OK**.</span></span>

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="05b61-117">Nella finestra di dialogo **Nuova applicazione Web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="05b61-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="05b61-118">Selezionare **Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="05b61-118">Select **Web Application**.</span></span>
* <span data-ttu-id="05b61-119">Selezionare **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="05b61-119">Select **Change Authentication**.</span></span>

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="05b61-121">Viene visualizzata la finestra di dialogo **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="05b61-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="05b61-122">Selezionare **Account utente individuali**.</span><span class="sxs-lookup"><span data-stu-id="05b61-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="05b61-123">Selezionare **OK** per tornare a **Nuova applicazione Web ASP.NET Core** e selezionare nuovamente **OK**.</span><span class="sxs-lookup"><span data-stu-id="05b61-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Finestra per autenticazione Nuova applicazione Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="05b61-125">Visual Studio crea la soluzione.</span><span class="sxs-lookup"><span data-stu-id="05b61-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="05b61-126">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="05b61-126">Run the app</span></span>

* <span data-ttu-id="05b61-127">Premere CTRL+F5 per eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="05b61-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="05b61-128">Eseguire il test dei collegamenti **About** (Informazioni su) e **Contact** (Contatto).</span><span class="sxs-lookup"><span data-stu-id="05b61-128">Test the **About** and **Contact** links.</span></span>

![Applicazione Web aperta in Microsoft Edge su localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="05b61-130">Registrare un utente</span><span class="sxs-lookup"><span data-stu-id="05b61-130">Register a user</span></span>

* <span data-ttu-id="05b61-131">Selezionare **Registra** e registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="05b61-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="05b61-132">È possibile usare un indirizzo di posta elettronica fittizio.</span><span class="sxs-lookup"><span data-stu-id="05b61-132">You can use a fictitious email address.</span></span> <span data-ttu-id="05b61-133">Quando si esegue l'invio, nella pagina viene visualizzato l'errore seguente:</span><span class="sxs-lookup"><span data-stu-id="05b61-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="05b61-134">*"Internal Server Error: A database operation failed while processing the request. Per un'eccezione SQL, non è possibile aprire il database. Applying existing migrations for Application DB context may resolve this issue."* (Errore interno del server: Operazione sul database non riuscita durante l'elaborazione della richiesta. Eccezione SQL: Impossibile aprire il file di database. Per risolvere il problema, applicare le migrazioni esistenti per il contesto di database dell'applicazione).</span><span class="sxs-lookup"><span data-stu-id="05b61-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="05b61-135">Selezionare **Apply Migrations** (Applica migrazioni) e, quando la pagina è stata caricata, eseguire l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="05b61-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Un errore interno del server indicante che un'operazione di database non è riuscita durante l'elaborazione della richiesta.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="05b61-139">L'app visualizza l'indirizzo di posta elettronica usato per registrare il nuovo utente e un collegamento **Disconnessione**.</span><span class="sxs-lookup"><span data-stu-id="05b61-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Applicazione Web aperta in Microsoft Edge](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="05b61-142">Distribuire l'app in Azure</span><span class="sxs-lookup"><span data-stu-id="05b61-142">Deploy the app to Azure</span></span>

<span data-ttu-id="05b61-143">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica...**.</span><span class="sxs-lookup"><span data-stu-id="05b61-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="05b61-145">Nella finestra di dialogo **Pubblica**:</span><span class="sxs-lookup"><span data-stu-id="05b61-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="05b61-146">Selezionare **Servizio app di Microsoft Azure**.</span><span class="sxs-lookup"><span data-stu-id="05b61-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="05b61-147">Selezionare l'icona a forma di ingranaggio e quindi selezionare **Crea profilo**.</span><span class="sxs-lookup"><span data-stu-id="05b61-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="05b61-148">Selezionare **Crea profilo**.</span><span class="sxs-lookup"><span data-stu-id="05b61-148">Select **Create Profile**.</span></span>

![Finestra di dialogo Pubblica](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="05b61-150">Creare risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="05b61-150">Create Azure resources</span></span>

<span data-ttu-id="05b61-151">Viene visualizzata la finestra di dialogo **Crea servizio app**:</span><span class="sxs-lookup"><span data-stu-id="05b61-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="05b61-152">Immettere la sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="05b61-152">Enter your subscription.</span></span>
* <span data-ttu-id="05b61-153">I campi di immissione **Nome dell'app**, **Gruppo di risorse** e **Piano di servizio app** vengono popolati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="05b61-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="05b61-154">È possibile mantenere questi nomi o modificarli.</span><span class="sxs-lookup"><span data-stu-id="05b61-154">You can keep these names or change them.</span></span>

![Finestra di dialogo Servizio app](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="05b61-156">Selezionare la scheda **Servizi** per creare un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="05b61-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="05b61-157">Selezionare l'icona verde **+** per creare un nuovo database SQL</span><span class="sxs-lookup"><span data-stu-id="05b61-157">Select the green **+** icon to create a new SQL Database</span></span>

![Nuovo database SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="05b61-159">Selezionare **Nuovo** nella finestra di dialogo **Configura database SQL** per creare un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="05b61-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Nuovo database SQL e server](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="05b61-161">Viene visualizzata la finestra di dialogo **Configura SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="05b61-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="05b61-162">Immettere nome utente e password di amministratore e selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="05b61-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="05b61-163">È possibile mantenere il **Nome server** predefinito.</span><span class="sxs-lookup"><span data-stu-id="05b61-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="05b61-164">La stringa "admin" non è consentita come nome utente di amministratore.</span><span class="sxs-lookup"><span data-stu-id="05b61-164">"admin" is not allowed as the administrator user name.</span></span>

![Finestra di dialogo Configura SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="05b61-166">Scegliere **OK**.</span><span class="sxs-lookup"><span data-stu-id="05b61-166">Select **OK**.</span></span>

<span data-ttu-id="05b61-167">Visual Studio torna alla finestra di dialogo **Crea servizio app**.</span><span class="sxs-lookup"><span data-stu-id="05b61-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="05b61-168">Selezionare **Crea** nella finestra di dialogo **Crea servizio app**.</span><span class="sxs-lookup"><span data-stu-id="05b61-168">Select **Create** on the **Create App Service** dialog.</span></span>

![Finestra di dialogo Configura database SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="05b61-170">Visual Studio crea l'app Web e SQL Server in Azure.</span><span class="sxs-lookup"><span data-stu-id="05b61-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="05b61-171">Questo passaggio potrebbe richiedere alcuni minuti.</span><span class="sxs-lookup"><span data-stu-id="05b61-171">This step can take a few minutes.</span></span> <span data-ttu-id="05b61-172">Per informazioni sulle risorse create, vedere [Risorse aggiuntive](#additonal-resources).</span><span class="sxs-lookup"><span data-stu-id="05b61-172">For information on the resources created, see [Additonal resources](#additonal-resources).</span></span>

<span data-ttu-id="05b61-173">Al termine della distribuzione, selezionare **impostazioni**:</span><span class="sxs-lookup"><span data-stu-id="05b61-173">When deployment completes, select **Settings**:</span></span>

![Finestra di dialogo Configura SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="05b61-175">Nella pagina **Impostazioni** della finestra di dialogo **Pubblica**:</span><span class="sxs-lookup"><span data-stu-id="05b61-175">On the **Settings** page of the **Publish** dialog:</span></span>

  * <span data-ttu-id="05b61-176">Espandere **Database** e selezionare **Usa questa stringa di connessione in fase di esecuzione**.</span><span class="sxs-lookup"><span data-stu-id="05b61-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
  * <span data-ttu-id="05b61-177">Espandere **Migrazioni Entity Framework** e selezionare **Applica questa migrazione in fase di pubblicazione**.</span><span class="sxs-lookup"><span data-stu-id="05b61-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="05b61-178">Selezionare **Salva**.</span><span class="sxs-lookup"><span data-stu-id="05b61-178">Select **Save**.</span></span> <span data-ttu-id="05b61-179">Visual Studio torna alla finestra di dialogo **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="05b61-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Finestra di dialogo Pubblica: pannello Impostazioni](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="05b61-181">Fare clic su **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="05b61-181">Click **Publish**.</span></span> <span data-ttu-id="05b61-182">Visual Studio pubblica l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="05b61-182">Visual Studio publishs your app to Azure.</span></span> <span data-ttu-id="05b61-183">Al termine della distribuzione, l'app viene aperta in un browser.</span><span class="sxs-lookup"><span data-stu-id="05b61-183">When the depoyment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="05b61-184">Testare l'app in Azure</span><span class="sxs-lookup"><span data-stu-id="05b61-184">Test your app in Azure</span></span>

* <span data-ttu-id="05b61-185">Eseguire il test dei collegamenti **About** (Informazioni su) e **Contact** (Contatto).</span><span class="sxs-lookup"><span data-stu-id="05b61-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="05b61-186">Registrare un nuovo utente</span><span class="sxs-lookup"><span data-stu-id="05b61-186">Register a new user</span></span>

![Applicazione Web aperta in Microsoft Edge in Servizio app di Azure](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="05b61-188">Aggiornare l'app</span><span class="sxs-lookup"><span data-stu-id="05b61-188">Update the app</span></span>

* <span data-ttu-id="05b61-189">Modificare la pagina Razor *Pages/About.cshtml* e modificarne il contenuto.</span><span class="sxs-lookup"><span data-stu-id="05b61-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="05b61-190">Ad esempio, è possibile modificare il paragrafo specificando "Hello ASP.NET Core!": [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]</span><span class="sxs-lookup"><span data-stu-id="05b61-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!": [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]</span></span>

* <span data-ttu-id="05b61-191">Fare clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="05b61-191">Right-click on the project and select **Publish...** again.</span></span>

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="05b61-193">Dopo la pubblicazione dell'app, verificare che le modifiche apportate siano disponibili in Azure.</span><span class="sxs-lookup"><span data-stu-id="05b61-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Verificare che l'attività sia stata completata](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="05b61-195">Eseguire la pulizia</span><span class="sxs-lookup"><span data-stu-id="05b61-195">Clean up</span></span>

<span data-ttu-id="05b61-196">Al termine del test dell'app accedere al [portale di Azure](https://portal.azure.com/) ed eliminare l'app.</span><span class="sxs-lookup"><span data-stu-id="05b61-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="05b61-197">Selezionare **Gruppi di risorse** e in seguito il gruppo di risorse che è stato creato.</span><span class="sxs-lookup"><span data-stu-id="05b61-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Portale di Azure: Gruppi di risorse nel menu laterale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="05b61-199">Nella pagina **Gruppi di risorse** selezionare **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="05b61-199">In the **Resource groups** page, select **Delete**.</span></span>

![Portale di Azure: pagina Gruppi di risorse](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="05b61-201">Immettere il nome del gruppo di risorse e selezionare **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="05b61-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="05b61-202">A questo punto l'app e tutte le altre risorse create in questa esercitazione vengono eliminate da Azure.</span><span class="sxs-lookup"><span data-stu-id="05b61-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="05b61-203">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="05b61-203">Next steps</span></span>

* [<span data-ttu-id="05b61-204">Distribuzione continua in Azure con Visual Studio e Git</span><span class="sxs-lookup"><span data-stu-id="05b61-204">Continuous Deployment to Azure with Visual Studio and Git</span></span>](xref:host-and-deploy/azure-apps/azure-continuous-deployment)

## <a name="additonal-resources"></a><span data-ttu-id="05b61-205">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="05b61-205">Additonal resources</span></span>

* [<span data-ttu-id="05b61-206">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="05b61-206">Azure App Service</span></span>](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="05b61-207">Gruppi di risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="05b61-207">Azure resource groups</span></span>](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="05b61-208">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="05b61-208">Azure SQL Database</span></span>](https://docs.microsoft.com/en-us/azure/sql-database/)
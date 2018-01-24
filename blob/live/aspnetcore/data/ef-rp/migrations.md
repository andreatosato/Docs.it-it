---
title: Pagine Razor con Entity Framework Core - Migrations - 4 di 8
author: rick-anderson
description: "In questa esercitazione, iniziare a usare la funzionalità di migrazioni EF Core per la gestione delle modifiche al modello di dati in un'applicazione ASP.NET MVC di base."
ms.author: riande
manager: wpickett
ms.date: 10/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/migrations
ms.openlocfilehash: 9a0fb52a1d1a62bce3f11c7e0394c00b9d544ab3
ms.sourcegitcommit: 3d512ea991ac36dfd4c800b7d1f8a27bfc50635e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="migrations---ef-core-with-razor-pages-tutorial-4-of-8"></a><span data-ttu-id="affb7-103">Migrazioni - Core EF esercitazione pagine Razor (4 di 8)</span><span class="sxs-lookup"><span data-stu-id="affb7-103">Migrations - EF Core with Razor Pages tutorial (4 of 8)</span></span>

<span data-ttu-id="affb7-104">Da [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="affb7-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="affb7-105">In questa esercitazione viene utilizzata la funzionalità di migrazioni EF Core per la gestione delle modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="affb7-105">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="affb7-106">Se si verificano problemi, è possibile risolvere, scaricare il [app completata per questa fase](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="affb7-106">If you run into problems you can't solve, download the [completed app for this stage](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span></span>

<span data-ttu-id="affb7-107">Quando è stata sviluppata un'app, i modello di dati spesso.</span><span class="sxs-lookup"><span data-stu-id="affb7-107">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="affb7-108">Ogni volta che le modifiche del modello, il modello Ottiene sincronizzato con il database.</span><span class="sxs-lookup"><span data-stu-id="affb7-108">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="affb7-109">In questa esercitazione avviata tramite la configurazione di Entity Framework per creare il database se non esiste.</span><span class="sxs-lookup"><span data-stu-id="affb7-109">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="affb7-110">Ogni volta che i modello di dati:</span><span class="sxs-lookup"><span data-stu-id="affb7-110">Each time the data model changes:</span></span>

* <span data-ttu-id="affb7-111">Il database viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="affb7-111">The DB is dropped.</span></span>
* <span data-ttu-id="affb7-112">EF crea una nuova istanza che corrisponde al modello.</span><span class="sxs-lookup"><span data-stu-id="affb7-112">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="affb7-113">L'app esegue il seeding del database con dati di test.</span><span class="sxs-lookup"><span data-stu-id="affb7-113">The app seeds the DB with test data.</span></span>

<span data-ttu-id="affb7-114">Questo approccio per mantenere sincronizzati con il modello di dati del database funziona bene fino a quando non si distribuisce l'app nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="affb7-114">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="affb7-115">Quando l'app è in esecuzione nell'ambiente di produzione, in genere l'archiviazione dei dati che devono essere conservate.</span><span class="sxs-lookup"><span data-stu-id="affb7-115">When the app is running in production, it is usually storing data that needs to be maintained.</span></span> <span data-ttu-id="affb7-116">L'app non può iniziare con un test di database ogni volta che viene apportata una modifica (ad esempio aggiungendo una nuova colonna).</span><span class="sxs-lookup"><span data-stu-id="affb7-116">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="affb7-117">La funzionalità di Entity Framework Core migrazioni risolve questo problema abilitando EF Core aggiornare lo schema di database anziché creare un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="affb7-117">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="affb7-118">Anziché eliminare e ricreare il database quando le modifiche del modello di dati, le migrazioni Aggiorna lo schema e mantiene i dati esistenti.</span><span class="sxs-lookup"><span data-stu-id="affb7-118">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="entity-framework-core-nuget-packages-for-migrations"></a><span data-ttu-id="affb7-119">Pacchetti NuGet di Entity Framework Core per le migrazioni</span><span class="sxs-lookup"><span data-stu-id="affb7-119">Entity Framework Core NuGet packages for migrations</span></span>

<span data-ttu-id="affb7-120">Per lavorare con le migrazioni, utilizzare il **Package Manager Console** (PMC) o l'interfaccia della riga di comando (CLI).</span><span class="sxs-lookup"><span data-stu-id="affb7-120">To work with migrations, use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span> <span data-ttu-id="affb7-121">Queste esercitazioni viene illustrato come utilizzare i comandi CLI.</span><span class="sxs-lookup"><span data-stu-id="affb7-121">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="affb7-122">Informazioni sul PMC, vedere [la fine di questa esercitazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="affb7-122">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

<span data-ttu-id="affb7-123">Vengono forniti gli strumenti di Entity Framework Core per l'interfaccia della riga di comando (CLI) in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span><span class="sxs-lookup"><span data-stu-id="affb7-123">The EF Core tools for the command-line interface (CLI) are provided in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span></span> <span data-ttu-id="affb7-124">Per installare questo pacchetto, aggiungerlo al `DotNetCliToolReference` insieme il *csproj* file, come illustrato.</span><span class="sxs-lookup"><span data-stu-id="affb7-124">To install this package, add it to the `DotNetCliToolReference` collection in the *.csproj* file, as shown.</span></span> <span data-ttu-id="affb7-125">**Nota:** il pacchetto deve essere installato mediante la modifica di *csproj* file.</span><span class="sxs-lookup"><span data-stu-id="affb7-125">**Note:** This package must be installed by editing the *.csproj* file.</span></span> <span data-ttu-id="affb7-126">Il`install-package` comando o la GUI di gestione di pacchetti non può essere utilizzata per installare questo pacchetto.</span><span class="sxs-lookup"><span data-stu-id="affb7-126">The`install-package` command or the package manager GUI cannot be used to install this package.</span></span> <span data-ttu-id="affb7-127">Modificare il *csproj* file facendo clic con il nome del progetto in **Esplora** e selezionando **ContosoUniversity.csproj modifica**.</span><span class="sxs-lookup"><span data-stu-id="affb7-127">Edit the *.csproj* file by right-clicking the project name in **Solution Explorer** and selecting **Edit ContosoUniversity.csproj**.</span></span>

<span data-ttu-id="affb7-128">Di seguito viene illustrato l'aggiornamento *csproj* file con gli strumenti di Entity Framework Core CLI evidenziato:</span><span class="sxs-lookup"><span data-stu-id="affb7-128">The following markup shows the updated *.csproj* file with the EF Core CLI tools highlighted:</span></span>

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?highlight=12)]
  
<span data-ttu-id="affb7-129">I numeri di versione nell'esempio precedente sono stati correnti quando l'esercitazione è stato scritto.</span><span class="sxs-lookup"><span data-stu-id="affb7-129">The version numbers in the preceding example were current when the tutorial was written.</span></span> <span data-ttu-id="affb7-130">Utilizzare la stessa versione per gli strumenti di Entity Framework Core CLI, vedere gli altri pacchetti.</span><span class="sxs-lookup"><span data-stu-id="affb7-130">Use the same version for the EF Core CLI tools found in the other packages.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="affb7-131">Modificare la stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="affb7-131">Change the connection string</span></span>

<span data-ttu-id="affb7-132">Nel *appSettings. JSON* file, modificare il nome del database nella stringa di connessione in ContosoUniversity2.</span><span class="sxs-lookup"><span data-stu-id="affb7-132">In the *appsettings.json* file, change the name of the DB in the connection string to ContosoUniversity2.</span></span>

[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="affb7-133">Se si modifica il nome di database nella stringa di connessione, la migrazione prima di creare un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="affb7-133">Changing the DB name in the connection string causes the first migration to create a new DB.</span></span> <span data-ttu-id="affb7-134">Poiché non ne esiste uno con lo stesso nome, viene creato un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="affb7-134">A new DB is created because one with that name does not exist.</span></span> <span data-ttu-id="affb7-135">Modifica la stringa di connessione non è necessaria per iniziare a usare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="affb7-135">Changing the connection string isn't required for getting started with migrations.</span></span>

<span data-ttu-id="affb7-136">Un'alternativa alla modifica del nome di database sta eliminando il database.</span><span class="sxs-lookup"><span data-stu-id="affb7-136">An alternative to changing the DB name is deleting the DB.</span></span> <span data-ttu-id="affb7-137">Utilizzare **Esplora oggetti di SQL Server** (sillaba SSOX) o `database drop` comando CLI:</span><span class="sxs-lookup"><span data-stu-id="affb7-137">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>

 ```console
 dotnet ef database drop
 ```

<span data-ttu-id="affb7-138">Nella sezione seguente viene illustrato come eseguire i comandi CLI.</span><span class="sxs-lookup"><span data-stu-id="affb7-138">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="affb7-139">Creazione di una migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="affb7-139">Create an initial migration</span></span>

<span data-ttu-id="affb7-140">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="affb7-140">Build the project.</span></span>

<span data-ttu-id="affb7-141">Aprire una finestra di comando e passare alla cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="affb7-141">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="affb7-142">La cartella di progetto contiene il *Startup.cs* file.</span><span class="sxs-lookup"><span data-stu-id="affb7-142">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="affb7-143">Nella finestra di comando, immettere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="affb7-143">Enter the following in the command window:</span></span>

```console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="affb7-144">La finestra di comando consente di visualizzare informazioni simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="affb7-144">The command window displays information similar to the following:</span></span>

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="affb7-145">Se la migrazione non riesce con il messaggio "*Impossibile accedere al file... ContosoUniversity.dll perché è utilizzato da un altro processo.* "</span><span class="sxs-lookup"><span data-stu-id="affb7-145">If the migration fails with the message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*"</span></span> <span data-ttu-id="affb7-146">viene visualizzato:</span><span class="sxs-lookup"><span data-stu-id="affb7-146">is displayed:</span></span>

* <span data-ttu-id="affb7-147">Arrestare IIS Express.</span><span class="sxs-lookup"><span data-stu-id="affb7-147">Stop IIS Express.</span></span>

   * <span data-ttu-id="affb7-148">Chiudere e riavviare Visual Studio, o</span><span class="sxs-lookup"><span data-stu-id="affb7-148">Exit and restart Visual Studio, or</span></span>
   * <span data-ttu-id="affb7-149">Nella barra delle applicazioni di Windows, individuare l'icona di IIS Express.</span><span class="sxs-lookup"><span data-stu-id="affb7-149">Find the IIS Express icon in the Windows System Tray.</span></span>
   * <span data-ttu-id="affb7-150">Fare doppio clic sull'icona di IIS Express e quindi fare clic su **ContosoUniversity > Arresta sito**.</span><span class="sxs-lookup"><span data-stu-id="affb7-150">Right-click the IIS Express icon, and then click **ContosoUniversity > Stop Site**.</span></span>

<span data-ttu-id="affb7-151">Se il messaggio di errore "generazione non riuscita."</span><span class="sxs-lookup"><span data-stu-id="affb7-151">If the error message "Build failed."</span></span> <span data-ttu-id="affb7-152">viene visualizzato, eseguire nuovamente il comando.</span><span class="sxs-lookup"><span data-stu-id="affb7-152">is displayed, run the command again.</span></span> <span data-ttu-id="affb7-153">Se questo errore si verifica, lasciare una nota nella parte inferiore di questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="affb7-153">If you get this error, leave a note at the bottom of this tutorial.</span></span>

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="affb7-154">Esaminare l'alto e verso il basso di metodi</span><span class="sxs-lookup"><span data-stu-id="affb7-154">Examine the Up and Down methods</span></span>

<span data-ttu-id="affb7-155">Il comando Core EF `migrations add` generato codice per il database da creare.</span><span class="sxs-lookup"><span data-stu-id="affb7-155">The EF Core command `migrations add` generated code to create the DB from.</span></span> <span data-ttu-id="affb7-156">Questo codice migrazioni è il *migrazioni\<timestamp > _InitialCreate.cs* file.</span><span class="sxs-lookup"><span data-stu-id="affb7-156">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="affb7-157">Il `Up` metodo la `InitialCreate` classe crea le tabelle di database che corrispondono al set di entità del modello di dati.</span><span class="sxs-lookup"><span data-stu-id="affb7-157">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="affb7-158">Il `Down` metodo eliminati, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="affb7-158">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[Main](intro/samples/cu/Migrations/20171026010210_InitialCreate.cs?range=8-24,77-)]

<span data-ttu-id="affb7-159">Chiamate di migrazioni di `Up` metodo per implementare le modifiche del modello di dati per la migrazione.</span><span class="sxs-lookup"><span data-stu-id="affb7-159">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="affb7-160">Quando si immette un comando per annullare l'aggiornamento, le chiamate di migrazioni di `Down` metodo.</span><span class="sxs-lookup"><span data-stu-id="affb7-160">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="affb7-161">Il codice precedente è per la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="affb7-161">The preceding code is for the initial migration.</span></span> <span data-ttu-id="affb7-162">Tale codice è stato creato quando il `migrations add InitialCreate` comando è stato eseguito.</span><span class="sxs-lookup"><span data-stu-id="affb7-162">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="affb7-163">Il parametro di nome migrazione ("InitialCreate" nell'esempio) viene utilizzato per il nome del file.</span><span class="sxs-lookup"><span data-stu-id="affb7-163">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="affb7-164">Il nome della migrazione può essere qualsiasi nome di file valido.</span><span class="sxs-lookup"><span data-stu-id="affb7-164">The migration name can be any valid file name.</span></span> <span data-ttu-id="affb7-165">È consigliabile scegliere una parola o frase che riepiloga le quali viene eseguita la migrazione.</span><span class="sxs-lookup"><span data-stu-id="affb7-165">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="affb7-166">Ad esempio, una migrazione aggiunto una tabella di reparto, denominata "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="affb7-166">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="affb7-167">Se la migrazione iniziale viene creata e il database viene chiuso:</span><span class="sxs-lookup"><span data-stu-id="affb7-167">If the initial migration is created and the DB exits:</span></span>

* <span data-ttu-id="affb7-168">Viene generato il codice di creazione del database.</span><span class="sxs-lookup"><span data-stu-id="affb7-168">The DB creation code is generated.</span></span>
* <span data-ttu-id="affb7-169">Il codice di creazione database non è necessario eseguire perché il database è già corrisponde al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="affb7-169">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="affb7-170">Se viene eseguito il codice di creazione del database, non apporta alcuna modifica perché il database è già corrisponde al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="affb7-170">If the The DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="affb7-171">Quando l'applicazione viene distribuita in un nuovo ambiente, è necessario eseguire il codice di creazione del database per creare il database.</span><span class="sxs-lookup"><span data-stu-id="affb7-171">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="affb7-172">La stringa di connessione in precedenza è stata modificata per l'utilizzo di un nuovo nome per il database.</span><span class="sxs-lookup"><span data-stu-id="affb7-172">Previously the connection string was changed to use a new name for the DB.</span></span> <span data-ttu-id="affb7-173">Il database specificato non esiste, pertanto le migrazioni creerà il database.</span><span class="sxs-lookup"><span data-stu-id="affb7-173">The specified DB doesn't exist, so migrations creates the DB.</span></span>

### <a name="examine-the-data-model-snapshot"></a><span data-ttu-id="affb7-174">Esaminare lo snapshot del modello di dati</span><span class="sxs-lookup"><span data-stu-id="affb7-174">Examine the data model snapshot</span></span>

<span data-ttu-id="affb7-175">Le migrazioni crea un *snapshot* dello schema del database corrente in *Migrations/SchoolContextModelSnapshot.cs*:</span><span class="sxs-lookup"><span data-stu-id="affb7-175">Migrations creates a *snapshot* of the current DB schema in *Migrations/SchoolContextModelSnapshot.cs*:</span></span>

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]

<span data-ttu-id="affb7-176">Poiché lo schema di database corrente è rappresentato nel codice, Core EF non deve interagire con il database per creare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="affb7-176">Because the current DB schema is represented in code, EF Core doesn't have to interact with the DB to create migrations.</span></span> <span data-ttu-id="affb7-177">Quando si aggiunge una migrazione, Core EF determina le modifiche confrontando il modello di dati del file di snapshot.</span><span class="sxs-lookup"><span data-stu-id="affb7-177">When you add a migration, EF Core determines what changed by comparing the data model to the snapshot file.</span></span> <span data-ttu-id="affb7-178">Core EF interagisce con il database solo quando è necessario aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="affb7-178">EF Core interacts with the DB only when it has to update the DB.</span></span>

<span data-ttu-id="affb7-179">Il file di snapshot deve essere sincronizzato con le migrazioni che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="affb7-179">The snapshot file must be in sync with the migrations that created it.</span></span> <span data-ttu-id="affb7-180">Impossibile rimuovere una migrazione eliminando il file denominato  *\<timestamp > _\<migrationname >. cs*.</span><span class="sxs-lookup"><span data-stu-id="affb7-180">A migration can't be removed by deleting the file named *\<timestamp>_\<migrationname>.cs*.</span></span> <span data-ttu-id="affb7-181">Se tale file viene eliminato, le migrazioni rimanenti sono sincronizzate con il file di snapshot di database.</span><span class="sxs-lookup"><span data-stu-id="affb7-181">If that file is deleted, the remaining migrations are out of sync with the DB snapshot file.</span></span> <span data-ttu-id="affb7-182">Per eliminare l'ultima migrazione aggiunto, usare il [migrazioni ef dotnet rimuovere](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) comando.</span><span class="sxs-lookup"><span data-stu-id="affb7-182">To delete the last migration added, use the [dotnet ef migrations remove](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="affb7-183">Rimuovere EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="affb7-183">Remove EnsureCreated</span></span>

<span data-ttu-id="affb7-184">Per lo sviluppo iniziale, il `EnsureCreated` comando è stato utilizzato.</span><span class="sxs-lookup"><span data-stu-id="affb7-184">For early development, the `EnsureCreated` command was used.</span></span> <span data-ttu-id="affb7-185">In questa esercitazione viene utilizzato le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="affb7-185">In this tutorial, migrations is used.</span></span> <span data-ttu-id="affb7-186">`EnsureCreated`presenta le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="affb7-186">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="affb7-187">Ignora le migrazioni e crea il database e lo schema.</span><span class="sxs-lookup"><span data-stu-id="affb7-187">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="affb7-188">Non crea una tabella delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="affb7-188">Does not create a migrations table.</span></span>
* <span data-ttu-id="affb7-189">Possibile *non* utilizzabile con le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="affb7-189">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="affb7-190">È progettato per prototipi di test o rapida in cui il database viene eliminato e ricreato frequentemente.</span><span class="sxs-lookup"><span data-stu-id="affb7-190">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="affb7-191">Rimuovere la riga seguente da `DbInitializer`:</span><span class="sxs-lookup"><span data-stu-id="affb7-191">Remove the following line from `DbInitializer`:</span></span>

```csharp
context.Database.EnsureCreated();
```

## <a name="apply-the-migration-to-the-db-in-development"></a><span data-ttu-id="affb7-192">Applicare la migrazione al database in fase di sviluppo</span><span class="sxs-lookup"><span data-stu-id="affb7-192">Apply the migration to the DB in development</span></span>

<span data-ttu-id="affb7-193">Nella finestra di comando, immettere il comando seguente per creare il database e tabelle.</span><span class="sxs-lookup"><span data-stu-id="affb7-193">In the command window, enter the following to create the DB and tables.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="affb7-194">Nota: Se il `update` comando restituisce l'errore "La compilazione non riuscita.":</span><span class="sxs-lookup"><span data-stu-id="affb7-194">Note: If the `update` command returns the error "Build failed.":</span></span>

* <span data-ttu-id="affb7-195">Eseguire nuovamente il comando.</span><span class="sxs-lookup"><span data-stu-id="affb7-195">Run the command again.</span></span>
* <span data-ttu-id="affb7-196">Se non viene completato, uscire da Visual Studio e quindi eseguire il `update` comando.</span><span class="sxs-lookup"><span data-stu-id="affb7-196">If it fails again, exit Visual Studio and then run the `update` command.</span></span>
* <span data-ttu-id="affb7-197">Lasciare un messaggio nella parte inferiore della pagina.</span><span class="sxs-lookup"><span data-stu-id="affb7-197">Leave a message at the bottom of the page.</span></span>

<span data-ttu-id="affb7-198">L'output del comando è simile al `migrations add` comando output.</span><span class="sxs-lookup"><span data-stu-id="affb7-198">The output from the command is similar to the `migrations add` command output.</span></span> <span data-ttu-id="affb7-199">Nel comando precedente, vengono visualizzati i registri per i comandi SQL che imposta backup del database.</span><span class="sxs-lookup"><span data-stu-id="affb7-199">In the preceding command, logs for the SQL commands that set up the DB are displayed.</span></span> <span data-ttu-id="affb7-200">La maggior parte dei log vengono omessi negli output di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="affb7-200">Most of the logs are omitted in the following sample output:</span></span>

```text
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (467ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (20ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20170816151242_InitialCreate', N'2.0.0-rtm-26452');
Done.
```

<span data-ttu-id="affb7-201">Per ridurre il livello di dettaglio nei messaggi di log, può modificare i livelli di log di *appsettings. Development.JSON* file.</span><span class="sxs-lookup"><span data-stu-id="affb7-201">To reduce the level of detail in log messages, can change the log levels in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="affb7-202">Per ulteriori informazioni, vedere [Introduzione a registrazione](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="affb7-202">For more information, see [Introduction to logging](xref:fundamentals/logging/index).</span></span>

<span data-ttu-id="affb7-203">Utilizzare **Esplora oggetti di SQL Server** per controllare il database.</span><span class="sxs-lookup"><span data-stu-id="affb7-203">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="affb7-204">Si noti l'aggiunta di un `__EFMigrationsHistory` tabella.</span><span class="sxs-lookup"><span data-stu-id="affb7-204">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="affb7-205">Il `__EFMigrationsHistory` tabella tiene traccia di quali migrazioni sono state applicate al database.</span><span class="sxs-lookup"><span data-stu-id="affb7-205">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="affb7-206">Visualizzare i dati di `__EFMigrationsHistory` tabella mostra una riga per la migrazione prima.</span><span class="sxs-lookup"><span data-stu-id="affb7-206">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="affb7-207">L'ultimo log nell'esempio di output CLI precedente mostra l'istruzione INSERT che consente di creare questa riga.</span><span class="sxs-lookup"><span data-stu-id="affb7-207">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="affb7-208">Eseguire l'applicazione e verificare che tutto funzioni.</span><span class="sxs-lookup"><span data-stu-id="affb7-208">Run the app and verify that everything works.</span></span>

## <a name="appling-migrations-in-production"></a><span data-ttu-id="affb7-209">Migrazioni di applicazione nell'ambiente di produzione</span><span class="sxs-lookup"><span data-stu-id="affb7-209">Appling migrations in production</span></span>

<span data-ttu-id="affb7-210">Si consiglia di App di produzione deve **non** chiamare [Database.Migrate](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) all'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="affb7-210">We recommend production apps should **not** call [Database.Migrate](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="affb7-211">`Migrate`non deve essere chiamato da un'app nella server farm.</span><span class="sxs-lookup"><span data-stu-id="affb7-211">`Migrate` should not be called from an app in server farm.</span></span> <span data-ttu-id="affb7-212">Ad esempio, se l'app è stato cloud distribuito con scalabilità orizzontale (più istanze dell'app sono in esecuzione).</span><span class="sxs-lookup"><span data-stu-id="affb7-212">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="affb7-213">Migrazione del database deve essere eseguita come parte della distribuzione in modo controllato.</span><span class="sxs-lookup"><span data-stu-id="affb7-213">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="affb7-214">Approcci di migrazione di database di produzione includono:</span><span class="sxs-lookup"><span data-stu-id="affb7-214">Production database migration approaches include:</span></span>

* <span data-ttu-id="affb7-215">Usando le migrazioni per creare script SQL e gli script SQL nella distribuzione.</span><span class="sxs-lookup"><span data-stu-id="affb7-215">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="affb7-216">Esecuzione `dotnet ef database update` da un ambiente controllato.</span><span class="sxs-lookup"><span data-stu-id="affb7-216">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="affb7-217">Core EF utilizza il `__MigrationsHistory` tabella per vedere se è necessario eseguire le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="affb7-217">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="affb7-218">Se il database viene aggiornato, non viene eseguita alcuna migrazione.</span><span class="sxs-lookup"><span data-stu-id="affb7-218">If the DB is up to date, no migration is run.</span></span>

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a><span data-ttu-id="affb7-219">Visual Studio interfaccia della riga di comando (CLI). Console di gestione pacchetti (PMC)</span><span class="sxs-lookup"><span data-stu-id="affb7-219">Command-line interface (CLI) vs. Package Manager Console (PMC)</span></span>

<span data-ttu-id="affb7-220">Il nucleo di Entity Framework gli strumenti per la gestione delle migrazioni sono disponibile da:</span><span class="sxs-lookup"><span data-stu-id="affb7-220">The EF Core tooling for managing migrations is available from:</span></span>

* <span data-ttu-id="affb7-221">Comandi di .NET core CLI.</span><span class="sxs-lookup"><span data-stu-id="affb7-221">.NET Core CLI commands.</span></span>
* <span data-ttu-id="affb7-222">I cmdlet di PowerShell in Visual Studio **Package Manager Console** finestra (PMC).</span><span class="sxs-lookup"><span data-stu-id="affb7-222">The PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span>

<span data-ttu-id="affb7-223">In questa esercitazione viene illustrato come utilizzare l'interfaccia CLI, alcuni sviluppatori preferiscono utilizzare PMC.</span><span class="sxs-lookup"><span data-stu-id="affb7-223">This tutorial shows how to use the CLI, some developers prefer using the PMC.</span></span>

<span data-ttu-id="affb7-224">I comandi di base di Entity Framework per PMC presenti il [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="affb7-224">The EF Core commands for the PMC are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="affb7-225">Questo pacchetto è incluso nel [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, pertanto non è necessario installarlo.</span><span class="sxs-lookup"><span data-stu-id="affb7-225">This package is included in the [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, so you don't have to install it.</span></span>

<span data-ttu-id="affb7-226">**Importante:** non è il pacchetto stesso di quello in cui si installa per l'interfaccia CLI modificando il *csproj* file.</span><span class="sxs-lookup"><span data-stu-id="affb7-226">**Important:** This is not the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="affb7-227">Il nome di questo termina `Tools`, a differenza del nome di pacchetto CLI che termina in `Tools.DotNet`.</span><span class="sxs-lookup"><span data-stu-id="affb7-227">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="affb7-228">Per ulteriori informazioni sui comandi CLI, vedere [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="affb7-228">For more information about the CLI commands, see [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="affb7-229">Per ulteriori informazioni sui comandi PMC, vedere [Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="affb7-229">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="affb7-230">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="affb7-230">Troubleshooting</span></span>

<span data-ttu-id="affb7-231">Scaricare il [app completata per questa fase](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="affb7-231">Download the [completed app for this stage](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span></span>

<span data-ttu-id="affb7-232">L'applicazione genera l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="affb7-232">The app generates the following exception:</span></span>

```text
`SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="affb7-233">Soluzione: eseguire`dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="affb7-233">Solution: Run `dotnet ef database update`</span></span>

<span data-ttu-id="affb7-234">Se il `update` comando restituisce l'errore "La compilazione non riuscita.":</span><span class="sxs-lookup"><span data-stu-id="affb7-234">If the `update` command returns the error "Build failed.":</span></span>

* <span data-ttu-id="affb7-235">Eseguire nuovamente il comando.</span><span class="sxs-lookup"><span data-stu-id="affb7-235">Run the command again.</span></span>
* <span data-ttu-id="affb7-236">Lasciare un messaggio nella parte inferiore della pagina.</span><span class="sxs-lookup"><span data-stu-id="affb7-236">Leave a message at the bottom of the page.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="affb7-237">[Precedente](xref:data/ef-rp/sort-filter-page)
[Successivo](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="affb7-237">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>
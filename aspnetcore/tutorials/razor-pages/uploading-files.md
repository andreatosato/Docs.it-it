---
title: Caricamento di file in una pagina Razor in ASP.NET Core
author: guardrex
description: Informazioni su come caricare i file in una pagina Razor.
keywords: ASP.NET Core, Razor, pagine Razor, IFormFile, caricamento di file, fileupload
ms.author: riande
manager: wpickett
ms.date: 09/12/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/uploading-files
ms.openlocfilehash: 3c5841f8c623f09530b60cc9997281dcb8e3c4f6
ms.sourcegitcommit: 94b7e0f95b92c98b182a93d2b3dc0287e5f97976
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2017
---
# <a name="uploading-files-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="9408c-104">Caricamento di file in una pagina Razor in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9408c-104">Uploading files to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="9408c-105">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9408c-105">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9408c-106">In questa sezione viene illustrato il caricamento di file con una pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="9408c-106">In this section, uploading files with a Razor Page is demonstrated.</span></span>

<span data-ttu-id="9408c-107">In questa esercitazione l'[app di esempio Razor Pages Movie](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) per caricare i file usa l'associazione di modelli semplice che funziona correttamente per file di piccole dimensioni.</span><span class="sxs-lookup"><span data-stu-id="9408c-107">The [Razor Pages Movie sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) in this tutorial uses simple model binding to upload files, which works well for uploading small files.</span></span> <span data-ttu-id="9408c-108">Per informazioni sulla trasmissione di file di grandi dimensioni, vedere [Caricamento di file di grandi dimensioni con il flusso](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="9408c-108">For information on streaming large files, see [Uploading large files with streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span></span>

<span data-ttu-id="9408c-109">Nella procedura descritta di seguito viene aggiunta una funzionalità di caricamento file di pianificazione dei film.</span><span class="sxs-lookup"><span data-stu-id="9408c-109">In the steps below, you add a movie schedule file upload feature to the sample app.</span></span> <span data-ttu-id="9408c-110">Ogni pianificazione di film è rappresentata da una classe `Schedule`.</span><span class="sxs-lookup"><span data-stu-id="9408c-110">A movie schedule is represented by a `Schedule` class.</span></span> <span data-ttu-id="9408c-111">La classe include due versioni della pianificazione.</span><span class="sxs-lookup"><span data-stu-id="9408c-111">The class includes two versions of the schedule.</span></span> <span data-ttu-id="9408c-112">La versione `PublicSchedule` viene messa a disposizione dei clienti.</span><span class="sxs-lookup"><span data-stu-id="9408c-112">One version is provided to customers, `PublicSchedule`.</span></span> <span data-ttu-id="9408c-113">L'altra versione, `PrivateSchedule`, viene usata per i dipendenti della società.</span><span class="sxs-lookup"><span data-stu-id="9408c-113">The other version is used for company employees, `PrivateSchedule`.</span></span> <span data-ttu-id="9408c-114">Le versioni vengono caricate come file separati.</span><span class="sxs-lookup"><span data-stu-id="9408c-114">Each version is uploaded as a separate file.</span></span> <span data-ttu-id="9408c-115">Nell'esercitazione viene illustrato come eseguire il caricamento di due file da una pagina con un solo invio al server.</span><span class="sxs-lookup"><span data-stu-id="9408c-115">The tutorial demonstrates how to perform two file uploads from a page with a single POST to the server.</span></span>

## <a name="add-a-helper-method-to-upload-files"></a><span data-ttu-id="9408c-116">Aggiungere un metodo helper per caricare file</span><span class="sxs-lookup"><span data-stu-id="9408c-116">Add a helper method to upload files</span></span>

<span data-ttu-id="9408c-117">Per evitare la duplicazione del codice per l'elaborazione dei file di pianificazione caricati, è necessario per prima cosa aggiungere un metodo helper statico.</span><span class="sxs-lookup"><span data-stu-id="9408c-117">To avoid code duplication for processing uploaded schedule files, add a static helper method first.</span></span> <span data-ttu-id="9408c-118">Creare una cartella *Utilità* nell'app e aggiungere un file *FileHelpers.cs* con il contenuto seguente.</span><span class="sxs-lookup"><span data-stu-id="9408c-118">Create a *Utilities* folder in the app and add a *FileHelpers.cs* file with the following content.</span></span> <span data-ttu-id="9408c-119">Il metodo helper, `ProcessFormFile`, accetta [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) e [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) e restituisce una stringa contenente le dimensioni e il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="9408c-119">The helper method, `ProcessFormFile`, takes an [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) and [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) and returns a string containing the file's size and content.</span></span> <span data-ttu-id="9408c-120">Il tipo di contenuto e la lunghezza vengono controllati.</span><span class="sxs-lookup"><span data-stu-id="9408c-120">The content type and length are checked.</span></span> <span data-ttu-id="9408c-121">Se il file non supera un controllo di convalida, viene aggiunto un errore a `ModelState`.</span><span class="sxs-lookup"><span data-stu-id="9408c-121">If the file doesn't pass a validation check, an error is added to the `ModelState`.</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Utilities/FileHelpers.cs)]

## <a name="add-the-schedule-class"></a><span data-ttu-id="9408c-122">Aggiungere la classe Schedule</span><span class="sxs-lookup"><span data-stu-id="9408c-122">Add the Schedule class</span></span>

<span data-ttu-id="9408c-123">Fare clic con il pulsante destro del mouse sulla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="9408c-123">Right click the *Models* folder.</span></span> <span data-ttu-id="9408c-124">Selezionare **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="9408c-124">Select **Add** > **Class**.</span></span> <span data-ttu-id="9408c-125">Denominare la classe **Schedule** e aggiungere le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="9408c-125">Name the class **Schedule** and add the following properties:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/Schedule.cs)]

<span data-ttu-id="9408c-126">La classe usa gli attributi `Display` e `DisplayFormat`, che producono titoli descrittivi e la formattazione quando viene eseguito il rendering dei dati di pianificazione.</span><span class="sxs-lookup"><span data-stu-id="9408c-126">The class uses `Display` and `DisplayFormat` attributes, which produce friendly titles and formatting when the schedule data is rendered.</span></span>

## <a name="update-the-moviecontext"></a><span data-ttu-id="9408c-127">Aggiornare MovieContext</span><span class="sxs-lookup"><span data-stu-id="9408c-127">Update the MovieContext</span></span>

<span data-ttu-id="9408c-128">Specificare un `DbSet` in `MovieContext` (*Models/MovieContext.cs*) per le pianificazioni:</span><span class="sxs-lookup"><span data-stu-id="9408c-128">Specify a `DbSet` in the `MovieContext` (*Models/MovieContext.cs*) for the schedules:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

## <a name="add-the-schedule-table-to-the-database"></a><span data-ttu-id="9408c-129">Aggiungere la tabella Schedule al database</span><span class="sxs-lookup"><span data-stu-id="9408c-129">Add the Schedule table to the database</span></span>

<span data-ttu-id="9408c-130">Aprire la Console di Gestione pacchetti: **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="9408c-130">Open the Package Manger Console (PMC): **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="9408c-132">Nella console di Gestione pacchetti eseguire i comandi seguenti.</span><span class="sxs-lookup"><span data-stu-id="9408c-132">In the PMC, execute the following commands.</span></span> <span data-ttu-id="9408c-133">In questo modo viene aggiunta una tabella `Schedule` al database:</span><span class="sxs-lookup"><span data-stu-id="9408c-133">These commands add a `Schedule` table to the database:</span></span>

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-fileupload-class"></a><span data-ttu-id="9408c-134">Aggiungere una classe FileUpload</span><span class="sxs-lookup"><span data-stu-id="9408c-134">Add a FileUpload class</span></span>

<span data-ttu-id="9408c-135">Successivamente, aggiungere una classe `FileUpload`, che è associata alla pagina per ottenere i dati di pianificazione.</span><span class="sxs-lookup"><span data-stu-id="9408c-135">Next, add a `FileUpload` class, which is bound to the page to obtain the schedule data.</span></span> <span data-ttu-id="9408c-136">Fare clic con il pulsante destro del mouse sulla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="9408c-136">Right click the *Models* folder.</span></span> <span data-ttu-id="9408c-137">Selezionare **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="9408c-137">Select **Add** > **Class**.</span></span> <span data-ttu-id="9408c-138">Denominare la classe **FileUpload** e aggiungere le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="9408c-138">Name the class **FileUpload** and add the following properties:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/FileUpload.cs)]

<span data-ttu-id="9408c-139">La classe ha una proprietà per il titolo della pianificazione e una proprietà per ognuna delle due versioni della pianificazione.</span><span class="sxs-lookup"><span data-stu-id="9408c-139">The class has a property for the schedule's title and a property for each of the two versions of the schedule.</span></span> <span data-ttu-id="9408c-140">Tutte e tre le proprietà sono obbligatorie e il titolo deve avere una lunghezza compresa fra 3 e 60 caratteri.</span><span class="sxs-lookup"><span data-stu-id="9408c-140">All three properties are required, and the title must be 3-60 characters long.</span></span>

## <a name="add-a-file-upload-razor-page"></a><span data-ttu-id="9408c-141">Aggiungere una pagina Razor di caricamento file</span><span class="sxs-lookup"><span data-stu-id="9408c-141">Add a file upload Razor Page</span></span>

<span data-ttu-id="9408c-142">Nella cartella *Pages* creare una cartella *Schedules*.</span><span class="sxs-lookup"><span data-stu-id="9408c-142">In the *Pages* folder, create a *Schedules* folder.</span></span> <span data-ttu-id="9408c-143">Nella cartella *Schedules* creare una pagina denominata *Index.cshtml* per il caricamento di una pianificazione con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="9408c-143">In the *Schedules* folder, create a page named *Index.cshtml* for uploading a schedule with the following content:</span></span>

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

<span data-ttu-id="9408c-144">Ogni gruppo di moduli include un oggetto **\<label>** ovvero un'etichetta che visualizza il nome di ogni proprietà della classe.</span><span class="sxs-lookup"><span data-stu-id="9408c-144">Each form group includes a **\<label>** that displays the name of each class property.</span></span> <span data-ttu-id="9408c-145">Gli attributi `Display` del modello `FileUpload` specificano i valori di visualizzazione per le etichette.</span><span class="sxs-lookup"><span data-stu-id="9408c-145">The `Display` attributes in the `FileUpload` model provide the display values for the labels.</span></span> <span data-ttu-id="9408c-146">Ad esempio, il nome visualizzato della proprietà `UploadPublicSchedule` è impostato con `[Display(Name="Public Schedule")]` e pertanto viene visualizzato "Public Schedule" nell'etichetta quando viene eseguito il rendering del modulo.</span><span class="sxs-lookup"><span data-stu-id="9408c-146">For example, the `UploadPublicSchedule` property's display name is set with `[Display(Name="Public Schedule")]` and thus displays "Public Schedule" in the label when the form renders.</span></span>

<span data-ttu-id="9408c-147">Ogni gruppo di moduli include una convalida **\<span>**.</span><span class="sxs-lookup"><span data-stu-id="9408c-147">Each form group includes a validation **\<span>**.</span></span> <span data-ttu-id="9408c-148">Se l'input dell'utente non soddisfa gli attributi della proprietà impostati nella classe `FileUpload` o se uno dei controlli della convalida file del metodo `ProcessFormFile` ha esito negativo, il modello non viene convalidato.</span><span class="sxs-lookup"><span data-stu-id="9408c-148">If the user's input fails to meet the property attributes set in the `FileUpload` class or if any of the `ProcessFormFile` method file validation checks fail, the model fails to validate.</span></span> <span data-ttu-id="9408c-149">Quando il modello non viene convalidato, viene eseguito il rendering di un messaggio di convalida utile per l'utente.</span><span class="sxs-lookup"><span data-stu-id="9408c-149">When model validation fails, a helpful validation message is rendered to the user.</span></span> <span data-ttu-id="9408c-150">Ad esempio, la proprietà `Title` viene annotata con `[Required]` e `[StringLength(60, MinimumLength = 3)]`.</span><span class="sxs-lookup"><span data-stu-id="9408c-150">For example, the `Title` property is annotated with `[Required]` and `[StringLength(60, MinimumLength = 3)]`.</span></span> <span data-ttu-id="9408c-151">Se l'utente non specifica un titolo, riceve un messaggio che indica che è necessario specificare un valore.</span><span class="sxs-lookup"><span data-stu-id="9408c-151">If the user fails to supply a title, they receive a message indicating that a value is required.</span></span> <span data-ttu-id="9408c-152">Se l'utente immette un valore inferiore a tre caratteri o superiore a sessanta, riceve un messaggio che indica che il valore ha una lunghezza non corretta.</span><span class="sxs-lookup"><span data-stu-id="9408c-152">If the user enters a value less than three characters or more than sixty characters, they receive a message indicating that the value has an incorrect length.</span></span> <span data-ttu-id="9408c-153">Se viene specificato un file che non presenta alcun contenuto, viene visualizzato un messaggio che indica che il file è vuoto.</span><span class="sxs-lookup"><span data-stu-id="9408c-153">If a file is provided that has no content, a message appears indicating that the file is empty.</span></span>

## <a name="add-the-code-behind-file"></a><span data-ttu-id="9408c-154">Aggiungere il file code-behind</span><span class="sxs-lookup"><span data-stu-id="9408c-154">Add the code-behind file</span></span>

<span data-ttu-id="9408c-155">Aggiungere il file code-behind, *Index.cshtml.cs*, nella cartella *Schedules*:</span><span class="sxs-lookup"><span data-stu-id="9408c-155">Add the code-behind file (*Index.cshtml.cs*) to the *Schedules* folder:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

<span data-ttu-id="9408c-156">Il modello di pagina (`IndexModel` in *Index.cshtml.cs*) associa la classe `FileUpload`:</span><span class="sxs-lookup"><span data-stu-id="9408c-156">The page model (`IndexModel` in *Index.cshtml.cs*) binds the `FileUpload` class:</span></span>

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="9408c-157">Il modello usa anche un elenco delle pianificazioni, `IList<Schedule>`, per visualizzare le pianificazioni archiviate nel database nella pagina:</span><span class="sxs-lookup"><span data-stu-id="9408c-157">The model also uses a list of the schedules (`IList<Schedule>`) to display the schedules stored in the database on the page:</span></span>

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="9408c-158">Quando la pagina viene caricata con `OnGetAsync`, la tabella `Schedules` viene popolata dal database e usata per generare una tabella HTML di pianificazioni caricate:</span><span class="sxs-lookup"><span data-stu-id="9408c-158">When the page loads with `OnGetAsync`, `Schedules` is populated from the database and used to generate an HTML table of loaded schedules:</span></span>

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

<span data-ttu-id="9408c-159">Quando il modulo viene pubblicato nel server, viene eseguito il controllo di `ModelState`.</span><span class="sxs-lookup"><span data-stu-id="9408c-159">When the form is posted to the server, the `ModelState` is checked.</span></span> <span data-ttu-id="9408c-160">Se la convalida dà esito negativo, `Schedule` viene ricompilata, e il rendering della pagina genera uno o più messaggi di convalida che indicano il motivo per cui la convalida non è riuscita.</span><span class="sxs-lookup"><span data-stu-id="9408c-160">If invalid, `Schedule` is rebuilt, and the page renders with one or more validation messages stating why page validation failed.</span></span> <span data-ttu-id="9408c-161">Se la convalida ha esisto positivo, le proprietà `FileUpload` vengono usate in *OnPostAsync* per completare il caricamento del file per le due versioni della pianificazione e per creare un nuovo oggetto `Schedule` per archiviare i dati.</span><span class="sxs-lookup"><span data-stu-id="9408c-161">If valid, the `FileUpload` properties are used in *OnPostAsync* to complete the file upload for the two versions of the schedule and to create a new `Schedule` object to store the data.</span></span> <span data-ttu-id="9408c-162">La pianificazione viene quindi salvata nel database:</span><span class="sxs-lookup"><span data-stu-id="9408c-162">The schedule is then saved to the database:</span></span>

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

## <a name="link-the-file-upload-razor-page"></a><span data-ttu-id="9408c-163">Aggiungere un collegamento alla pagina Razor di caricamento file</span><span class="sxs-lookup"><span data-stu-id="9408c-163">Link the file upload Razor Page</span></span>

<span data-ttu-id="9408c-164">Aprire *_Layout.cshtml* e aggiungere un collegamento alla barra di navigazione per accedere alla pagina di caricamento dei file:</span><span class="sxs-lookup"><span data-stu-id="9408c-164">Open *_Layout.cshtml* and add a link to the navigation bar to reach the file upload page:</span></span>

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=31-38&highlight=4)]

## <a name="add-a-page-to-confirm-schedule-deletion"></a><span data-ttu-id="9408c-165">Aggiungere una pagina per confermare l'eliminazione della pianificazione</span><span class="sxs-lookup"><span data-stu-id="9408c-165">Add a page to confirm schedule deletion</span></span>

<span data-ttu-id="9408c-166">Quando l'utente fa clic per eliminare una pianificazione, è necessario che abbia la possibilità di annullare l'operazione.</span><span class="sxs-lookup"><span data-stu-id="9408c-166">When the user clicks to delete a schedule, you want them to have a chance to cancel the operation.</span></span> <span data-ttu-id="9408c-167">Aggiungere una pagina di conferma dell'eliminazione (*Delete.cshtml*) nella cartella *Schedules*:</span><span class="sxs-lookup"><span data-stu-id="9408c-167">Add a delete confirmation page (*Delete.cshtml*) to the *Schedules* folder:</span></span>

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

<span data-ttu-id="9408c-168">Il file code-behind, *Delete.cshtml.cs*, carica una singola pianificazione identificata da `id` nei dati della route della richiesta.</span><span class="sxs-lookup"><span data-stu-id="9408c-168">The code-behind file (*Delete.cshtml.cs*) loads a single schedule identified by `id` in the request's route data.</span></span> <span data-ttu-id="9408c-169">Aggiungere il file *Delete.cshtml.cs* nella cartella *Schedules*:</span><span class="sxs-lookup"><span data-stu-id="9408c-169">Add the *Delete.cshtml.cs* file to the *Schedules* folder:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

<span data-ttu-id="9408c-170">Il metodo `OnPostAsync` gestisce l'eliminazione della pianificazione tramite `id`:</span><span class="sxs-lookup"><span data-stu-id="9408c-170">The `OnPostAsync` method handles deleting the schedule by its `id`:</span></span>

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

<span data-ttu-id="9408c-171">Dopo aver completato l'eliminazione della pianificazione, `RedirectToPage` invia nuovamente l'utente alla pagina delle pianificazioni *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9408c-171">After successfully deleting the schedule, the `RedirectToPage` sends the user back to the schedules *Index.cshtml* page.</span></span>

## <a name="the-working-schedules-razor-page"></a><span data-ttu-id="9408c-172">La pagina Razor Schedules operativa</span><span class="sxs-lookup"><span data-stu-id="9408c-172">The working Schedules Razor Page</span></span>

<span data-ttu-id="9408c-173">Quando la pagina viene caricata, il rendering delle etichette e degli input per il titolo della pianificazione, della pianificazione pubblica e della pianificazione privata viene eseguito con un pulsante di invio:</span><span class="sxs-lookup"><span data-stu-id="9408c-173">When the page loads, labels and inputs for schedule title, public schedule, and private schedule are rendered with a submit button:</span></span>

![La pagina Razor Schedules come viene visualizzata al caricamento iniziale in assenza di errori di convalida e con i campi vuoti](uploading-files/_static/browser1.png)

<span data-ttu-id="9408c-175">Se si seleziona il pulsante **Carica** senza aver popolato i campi, vengono violati gli attributi `[Required]` nel modello.</span><span class="sxs-lookup"><span data-stu-id="9408c-175">Selecting the **Upload** button without populating any of the fields violates the `[Required]` attributes on the model.</span></span> <span data-ttu-id="9408c-176">`ModelState` non è valido.</span><span class="sxs-lookup"><span data-stu-id="9408c-176">The `ModelState` is invalid.</span></span> <span data-ttu-id="9408c-177">I messaggi di errore di convalida vengono visualizzati all'utente:</span><span class="sxs-lookup"><span data-stu-id="9408c-177">The validation error messages are displayed to the user:</span></span>

![I messaggi di errore di convalida vengono visualizzati accanto a ogni controllo input](uploading-files/_static/browser2.png)

<span data-ttu-id="9408c-179">Digitare due lettere nel campo **Titolo**.</span><span class="sxs-lookup"><span data-stu-id="9408c-179">Type two letters into the **Title** field.</span></span> <span data-ttu-id="9408c-180">Il messaggio di convalida cambia e indica che il titolo deve avere una lunghezza compresa tra 3 e 60 caratteri:</span><span class="sxs-lookup"><span data-stu-id="9408c-180">The validation message changes to indicate that the title must be between 3-60 characters:</span></span>

![Messaggio di convalida titolo modificato](uploading-files/_static/browser3.png)

<span data-ttu-id="9408c-182">Quando vengono caricate una o più pianificazioni, la sezione **Loaded Schedules** (Pianificazioni caricate) esegue il rendering delle pianificazioni caricate:</span><span class="sxs-lookup"><span data-stu-id="9408c-182">When one or more schedules are uploaded, the **Loaded Schedules** section renders the loaded schedules:</span></span>

![Tabella delle pianificazioni caricate che include il titolo di ogni pianificazione, la data di caricamento con l'ora UTC, le dimensioni del file in versione pubblica e in versione privata](uploading-files/_static/browser4.png)

<span data-ttu-id="9408c-184">L'utente può scegliere il collegamento **Eliminazione** per accedere alla schermata di conferma dell'eliminazione, da dove è possibile confermare o annullare l'operazione.</span><span class="sxs-lookup"><span data-stu-id="9408c-184">The user can click the **Delete** link from there to reach the delete confirmation view, where they have an opportunity to confirm or cancel the delete operation.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="9408c-185">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="9408c-185">Troubleshooting</span></span>

<span data-ttu-id="9408c-186">Per risolvere i problemi relativi al caricamento di `IFormFile`, vedere [Caricamento di file in ASP.NET Core: Risoluzione dei problemi](xref:mvc/models/file-uploads#troubleshooting).</span><span class="sxs-lookup"><span data-stu-id="9408c-186">For troubleshooting information with `IFormFile` uploading, see the [File uploads in ASP.NET Core: Troubleshooting](xref:mvc/models/file-uploads#troubleshooting).</span></span>

<span data-ttu-id="9408c-187">Grazie aver completato questa introduzione alle pagine Razor.</span><span class="sxs-lookup"><span data-stu-id="9408c-187">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="9408c-188">Si apprezza l'invio di commenti.</span><span class="sxs-lookup"><span data-stu-id="9408c-188">We appreciate any comments you leave.</span></span> <span data-ttu-id="9408c-189">[Getting started with MVC and EF Core](xref:data/ef-mvc/intro) (Introduzione a MVC ed Entity Framework Core) è un ottimo completamento per questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="9408c-189">[Getting started with MVC and EF Core](xref:data/ef-mvc/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9408c-190">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9408c-190">Additional resources</span></span>

* [<span data-ttu-id="9408c-191">Caricamento di file in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9408c-191">File uploads in ASP.NET Core</span></span>](xref:mvc/models/file-uploads)
* [<span data-ttu-id="9408c-192">IFormFile</span><span class="sxs-lookup"><span data-stu-id="9408c-192">IFormFile</span></span>](/dotnet/api/microsoft.aspnetcore.http.iformfile)

>[!div class="step-by-step"]
[<span data-ttu-id="9408c-193">Articolo Precedente: Convalida</span><span class="sxs-lookup"><span data-stu-id="9408c-193">Previous: Validation</span></span>](xref:tutorials/razor-pages/validation)
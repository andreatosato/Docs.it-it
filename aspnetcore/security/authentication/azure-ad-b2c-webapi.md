---
title: Autenticazione cloud nell'API web con Azure Active Directory B2C in ASP.NET Core
author: camsoper
description: Informazioni su come configurare l'autenticazione di Azure Active Directory B2C con l'API Web ASP.NET Core. Testare l'API con Postman web autenticato.
ms.author: casoper
ms.date: 09/21/2018
ms.custom: mvc
uid: security/authentication/azure-ad-b2c-webapi
ms.openlocfilehash: 0efc95f508ef84d2728f503f1edd886ce6ae7a79
ms.sourcegitcommit: 4d5f8680d68b39c411b46c73f7014f8aa0f12026
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47028258"
---
# <a name="cloud-authentication-in-web-apis-with-azure-active-directory-b2c-in-aspnet-core"></a>Autenticazione cloud nell'API web con Azure Active Directory B2C in ASP.NET Core

Di [Cam Soper](https://twitter.com/camsoper)

[Azure Active B2C di Directory](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) è una soluzione di gestione identità cloud per le App per dispositivi mobili e web. Il servizio fornisce l'autenticazione per le app ospitate nel cloud e locali. Tipi di autenticazione includono account individuali, gli account di social network e account aziendali federati. Inoltre, Azure AD B2C fornisce l'autenticazione a più fattori con la configurazione minima.

> [!TIP]
> Azure Active Directory (Azure AD) e Azure AD B2C vengono offerti come prodotti separati. Un tenant di Azure AD rappresenta un'organizzazione, mentre un tenant di Azure AD B2C rappresenta una raccolta di identità da usare con le applicazioni relying party. Per altre informazioni, vedere [Azure Active Directory B2C: domande frequenti (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

Poiché le API web non dispone di alcuna interfaccia utente, risultano non è possibile reindirizzare l'utente a un servizio token di sicurezza, ad esempio Azure AD B2C. Al contrario, l'API viene passato un token di connessione da app chiamante, che ha già autenticato l'utente con Azure AD B2C. L'API viene quindi convalidato il token senza interazione diretta dell'utente.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un tenant di Azure Active Directory B2C.
> * Registrare un'API Web in Azure AD B2C.
> * Usare Visual Studio per creare un'API Web configurato per l'uso del tenant di Azure AD B2C per l'autenticazione.
> * Configurare i criteri di controllo del comportamento del tenant di Azure AD B2C.
> * Usare Postman per simulare un'app web che presenta una finestra di dialogo di accesso, recupera un token e lo usa per effettuare una richiesta all'API web.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono necessarie per questa procedura dettagliata:

* [Sottoscrizione di Microsoft Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) (qualsiasi edizione)
* [Postman](https://www.getpostman.com/postman)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Creare il tenant di Azure Active Directory B2C

Creare un tenant di Azure AD B2C [come descritto nella documentazione di](/azure/active-directory-b2c/active-directory-b2c-get-started). Quando richiesto, associare il tenant con una sottoscrizione di Azure è facoltativa per questa esercitazione.

## <a name="configure-a-sign-up-or-sign-in-policy"></a>Configurare un criterio di iscrizione o accesso

Usare i passaggi descritti nella documentazione di Azure AD B2C per [creare un criterio di iscrizione o accesso](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy). Denomina il criterio **SiUpIn**.  Usare i valori di esempio forniti nella documentazione relativa a **provider di identità**, **attributi di iscrizione**, e **attestazioni dell'applicazione**. Usando il **Esegui adesso** pulsante per testare il criterio, come descritto nella documentazione è facoltativo.

## <a name="register-the-api-in-azure-ad-b2c"></a>Registrare l'API in Azure AD B2C

Nel tenant di Azure AD B2C appena creato, registrare l'API usando [i passaggi nella documentazione sullo](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-api) sotto il **registrare un'API web** sezione.

Usare i valori seguenti:

| Impostazione                       | Valore               | Note                                                                                  |
|-------------------------------|---------------------|----------------------------------------------------------------------------------------|
| **Name**                      | *&lt;Nome dell'API&gt;*  | Immettere un **nome** per le app che descrive l'app agli utenti.                     |
| **Includi app web / API web** | Yes                 |                                                                                        |
| **Consenti flusso implicito**       | Yes                 |                                                                                        |
| **URL di risposta**                 | `https://localhost` | Gli URL di risposta sono gli endpoint in cui Azure AD B2C restituisce eventuali token richiesti dall'app. |
| **URI ID App**                | *api*               | L'URI non è necessario risolvere un indirizzo fisico. Solo deve essere univoco.     |
| **Includi client nativo**     | No                  |                                                                                        |

Dopo aver registrato l'API, viene visualizzato l'elenco di API e App nel tenant. Selezionare l'API che è stato appena registrato. Selezionare il **copia** a destra dell'icona le **ID applicazione** campo per copiarlo negli Appunti. Selezionare **ambiti pubblicati** e verificare il valore predefinito *user_impersonation* ambito è presente.

## <a name="create-an-aspnet-core-app-in-visual-studio-2017"></a>Creare un'app ASP.NET Core in Visual Studio 2017

Il modello di applicazione Web di Visual Studio può essere configurato per usare il tenant di Azure AD B2C per l'autenticazione.

In Visual Studio:

1. Creare una nuova applicazione Web ASP.NET Core. 
2. Selezionare **API Web** dall'elenco dei modelli.
3. Selezionare il **Modifica autenticazione** pulsante.

    ![Pulsante Modifica autenticazione](./azure-ad-b2c-webapi/change-auth-button.png)

4. Nel **Modifica autenticazione** finestra di dialogo, seleziona **account utente individuali**e quindi selezionare **Connetti a un archivio utente esistente nel cloud** nell'elenco a discesa. 

    ![Finestra di dialogo Modifica autenticazione](./azure-ad-b2c-webapi/change-auth-dialog.png)

5. Compilare il modulo con i valori seguenti:

    | Impostazione                       | Valore                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Nome di dominio**               | *&lt;il nome di dominio del tenant di B2C&gt;*          |
    | **ID dell'applicazione**            | *&lt;incollare l'ID dell'applicazione dagli Appunti&gt;* |
    | **Criteri di iscrizione o accesso** | `B2C_1_SiUpIn`                                        |

    Selezionare **OK** per chiudere la **Modifica autenticazione** finestra di dialogo. Selezionare **OK** per creare l'app web.

Visual Studio crea l'API web con un controller denominato *ValuesController.cs* che restituisce valori hardcoded per le richieste GET. La classe è decorata con il [attributo Authorize](xref:security/authorization/simple), in modo che tutte le richieste di richiedono l'autenticazione.

## <a name="run-the-web-api"></a>Eseguire l'API web

In Visual Studio, eseguire l'API. Visual Studio avvia un browser che puntano all'URL radice dell'API. Prendere nota dell'URL nella barra degli indirizzi e lasciare l'API in esecuzione in background.

> [!NOTE]
> Poiché non è presente alcun controller definito per l'URL radice, il browser potrebbe essere visualizzato un errore 404 (pagina non trovata). Si tratta di un comportamento previsto.

## <a name="use-postman-to-get-a-token-and-test-the-api"></a>Usare Postman per ottenere un token e testare l'API

[Postman](https://getpostman.com/postman) è uno strumento per testare le API web. Per questa esercitazione, Postman Simula un'app web che consente di accedere all'API web per conto dell'utente.

### <a name="register-postman-as-a-web-app"></a>Registrare Postman come un'app web

Poiché Postman Simula un'app web che può ottenere i token dal tenant Azure AD B2C, deve essere registrato nel tenant come app web. Registrare usando Postman [i passaggi nella documentazione sullo](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) sotto il **registrare un'app web** sezione. Termina la **creare un segreto client dell'app web** sezione. Un segreto client non è necessario per questa esercitazione. 

Usare i valori seguenti:

| Impostazione                       | Valore                            | Note                           |
|-------------------------------|----------------------------------|---------------------------------|
| **Name**                      | Postman                          |                                 |
| **Includi app web / API web** | Yes                              |                                 |
| **Consenti flusso implicito**       | Yes                              |                                 |
| **URL di risposta**                 | `https://getpostman.com/postman` |                                 |
| **URI ID App**                | *&lt;Lasciare vuoto&gt;*            | Non è obbligatorio per questa esercitazione. |
| **Includi client nativo**     | No                               |                                 |

L'app web appena registrata necessita dell'autorizzazione per accedere all'API web per conto dell'utente.  

1. Selezionare **Postman** nell'elenco di App e quindi selezionare **l'accesso all'API** dal menu a sinistra.
2. Selezionare **+ Aggiungi**.
3. Nel **seleziona API** elenco a discesa, selezionare il nome dell'API web.
4. Nel **Seleziona ambiti** elenco a discesa, assicurarsi che tutti gli ambiti sono selezionati.
5. Selezionare **accettabile**.

Notare l'ID applicazione dell'app Postman, perché verrà usata per ottenere un token di connessione.

### <a name="create-a-postman-request"></a>Creare una richiesta Postman

Avviare Postman. Per impostazione predefinita, Postman consente di visualizzare il **Crea nuovo** dialogo all'avvio. Se non viene visualizzata la finestra di dialogo, selezionare la **+ nuovo** pulsante in alto a sinistra.

Dal **Crea nuovo** finestra di dialogo:

1. Selezionare **richiedere**.

    ![Pulsante di richiesta](./azure-ad-b2c-webapi/postman-create-new.png)

2. Immettere *ottenere i valori* nel **nome richiesta** casella.
3. Selezionare **+ Create Collection** per creare una nuova raccolta per archiviare la richiesta. Nome insieme *esercitazioni di ASP.NET Core* e quindi selezionare il segno di spunta.

    ![Creare una nuova raccolta](./azure-ad-b2c-webapi/postman-create-collection.png)

4. Selezionare il **salvare alle esercitazioni di ASP.NET Core** pulsante.

### <a name="test-the-web-api-without-authentication"></a>Testare l'API web senza autenticazione

Per verificare che l'API web richiede l'autenticazione, effettuare prima di tutto una richiesta senza autenticazione.

1. Nel **immettere l'URL della richiesta** immettere l'URL per `ValuesController`. L'URL è identico a quello visualizzato nel browser con **api/valori** aggiunto. Un esempio sarebbe `https://localhost:44375/api/values`.
2. Selezionare il **inviare** pulsante.
3. Si noti lo stato della risposta viene *401 non autorizzato*.

    ![risposta 401 non autorizzato](./azure-ad-b2c-webapi/postman-401-status.png)

> [!IMPORTANT]
> Se si verifica un errore "Impossibile ottenere una risposta", potrebbe essere necessario disabilitare la verifica dei certificati SSL nel [impostazioni Postman](https://learning.getpostman.com/docs/postman/launching_postman/settings). 
 
### <a name="obtain-a-bearer-token"></a>Ottenere un token di connessione

Per eseguire una richiesta autenticata all'API web, è necessario un token di connessione. Postman rende più semplice accedere al tenant di Azure AD B2C e ottenere un token.

1. Nel **Authorization** nella scheda il **tipo** elenco a discesa, seleziona **OAuth 2.0**. Nel **dati di autorizzazione per aggiungere** elenco a discesa, seleziona **intestazioni di richiesta**. Selezionare **ottenere il Token di accesso nuovo**.

    ![Scheda di autorizzazione con le impostazioni](./azure-ad-b2c-webapi/postman-auth-tab.png)

2. Completare la **OTTIENI nuovo TOKEN di accesso** finestra di dialogo come indicato di seguito:


   |                Impostazione                 |                                             Valore                                             |                                                                                                                                    Note                                                                                                                                     |
   |----------------------------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |      <strong>Nome del token</strong>       |                                  <em>&lt;nome del token&gt;</em>                                  |                                                                                                                   Immettere un nome descrittivo per il token.                                                                                                                    |
   |      <strong>Tipo di concessione</strong>       |                                           implicito                                            |                                                                                                                                                                                                                                                                              |
   |     <strong>URL di callback</strong>      |                               `https://getpostman.com/postman`                                |                                                                                                                                                                                                                                                                              |
   |       <strong>URL autenticazione</strong>        | `https://login.microsoftonline.com/tfp/<tenant domain name>/B2C_1_SiUpIn/oauth2/v2.0/authorize` |                                                                                                  Sostituire <em>&lt;nome dominio tenant&gt;</em> con il nome di dominio del tenant.                                                                                                  |
   |       <strong>ID client</strong>       |                <em>&lt;Immettere l'app Postman <b>ID applicazione</b>&gt;</em>                 |                                                                                                                                                                                                                                                                              |
   |         <strong>Ambito</strong>         |         `https://<tenant domain name>/<api>/user_impersonation openid offline_access`         | Sostituire <em>&lt;nome dominio tenant&gt;</em> con il nome di dominio del tenant. Sostituire <em>&lt;api&gt;</em> con l'URI ID App assegnato all'API web al momento della prima registrazione (in questo caso, `api`). Il modello per l'URL è: <em>https://{tenant}.onmicrosoft.com/{api-id-uri}/{scope nome}</em>. |
   |         <strong>Stato</strong>         |                                 <em>&lt;Lasciare vuoto&gt;</em>                                  |                                                                                                                                                                                                                                                                              |
   | <strong>Autenticazione client</strong> |                                Inviare le credenziali del client nel corpo                                |                                                                                                                                                                                                                                                                              |


3. Selezionare il **richiedere Token** pulsante.

4. Postman consente di aprire una nuova finestra contenente una finestra di dialogo di accesso del tenant Azure AD B2C. Accedere con un account esistente (se ne è stato creato il test dei criteri) oppure selezionare **iscriversi adesso** per creare un nuovo account. Il **password dimenticata?** collegamento viene utilizzato per reimpostare una password dimenticata.

5. Dopo aver effettuato l'accesso, la finestra viene chiusa e il **gestire i token di accesso** viene visualizzata la finestra. Scorrere fino alla fine e selezionare il **utilizzo Token** pulsante.

    ![Dove trovare il pulsante "Usare Token"](./azure-ad-b2c-webapi/postman-access-token.png)

### <a name="test-the-web-api-with-authentication"></a>Testare l'API web con l'autenticazione

Selezionare il **inviare** pulsante per inviare nuovamente la richiesta. In questo caso, lo stato della risposta si *200 OK* e il payload JSON non è visibile sulla risposta **corpo** scheda.

![Stato del payload e operazione riuscita](./azure-ad-b2c-webapi/postman-success.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un tenant di Azure Active Directory B2C.
> * Registrare un'API Web in Azure AD B2C.
> * Usare Visual Studio per creare un'API Web configurato per l'uso del tenant di Azure AD B2C per l'autenticazione.
> * Configurare i criteri di controllo del comportamento del tenant di Azure AD B2C.
> * Usare Postman per simulare un'app web che presenta una finestra di dialogo di accesso, recupera un token e lo usa per effettuare una richiesta all'API web.

Continuare a sviluppare le API da learning per:

* [Proteggere un ASP.NET Core app web usando Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* [Chiamare un'API web .NET da un'app web .NET usando Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).
* [Personalizzare l'interfaccia utente di Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Configurare i requisiti di complessità delle password](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Abilitare multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Configurare altri provider di identità, ad esempio [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)e così via.
* [Usare l'API Graph di Azure AD](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) per recuperare informazioni aggiuntive sull'utente, ad esempio l'appartenenza al gruppo, dal tenant Azure AD B2C.

---
title: "Immutabilità chiave e la modifica delle impostazioni"
author: rick-anderson
description: "Questo documento descrive i dettagli di implementazione di ASP.NET Core dati protezione chiave immutabilità API."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8e46e634266fa5f082c47f3be306009eb54bcbcc
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="key-immutability-and-changing-settings"></a><span data-ttu-id="c38c1-103">Immutabilità chiave e la modifica delle impostazioni</span><span class="sxs-lookup"><span data-stu-id="c38c1-103">Key Immutability and changing settings</span></span>

<span data-ttu-id="c38c1-104">Una volta che un oggetto è persistente nell'archivio di backup, rappresentazione forever è fissa.</span><span class="sxs-lookup"><span data-stu-id="c38c1-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="c38c1-105">Nuovi dati possono essere aggiunti all'archivio di backup, ma i dati esistenti non possono essere modificati.</span><span class="sxs-lookup"><span data-stu-id="c38c1-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="c38c1-106">Lo scopo principale di questo comportamento è per evitare il danneggiamento dei dati.</span><span class="sxs-lookup"><span data-stu-id="c38c1-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="c38c1-107">Una conseguenza di questo comportamento è che una volta che una chiave viene scritta all'archivio di backup, non modificabile.</span><span class="sxs-lookup"><span data-stu-id="c38c1-107">One consequence of this behavior is that once a key is written to the backing store, it is immutable.</span></span> <span data-ttu-id="c38c1-108">Le date di creazione, attivazione e la scadenza non possono essere modificate, anche se è possibile revocare `IKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="c38c1-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="c38c1-109">Inoltre, il sottostante informazioni algoritmiche, materiale della chiave master e la crittografia a proprietà rest anche sono modificabili.</span><span class="sxs-lookup"><span data-stu-id="c38c1-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="c38c1-110">Se lo sviluppatore modifica qualsiasi impostazione che influisce sulla chiave persistenza, tali modifiche non diventeranno effettive fino alla successiva esecuzione viene generata una chiave, tramite una chiamata esplicita a `IKeyManager.CreateNewKey` o tramite dati protezione del sistema proprio [chiave automatico generazione](key-management.md#data-protection-implementation-key-management) comportamento.</span><span class="sxs-lookup"><span data-stu-id="c38c1-110">If the developer changes any setting that affects key persistence, those changes will not go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](key-management.md#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="c38c1-111">Le impostazioni che influiscono sulla persistenza chiave sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="c38c1-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="c38c1-112">Durata della chiave predefinita</span><span class="sxs-lookup"><span data-stu-id="c38c1-112">The default key lifetime</span></span>](key-management.md#data-protection-implementation-key-management)

* [<span data-ttu-id="c38c1-113">La crittografia della chiave al meccanismo di rest</span><span class="sxs-lookup"><span data-stu-id="c38c1-113">The key encryption at rest mechanism</span></span>](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)

* [<span data-ttu-id="c38c1-114">Le informazioni algoritmiche contenute all'interno della chiave</span><span class="sxs-lookup"><span data-stu-id="c38c1-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="c38c1-115">Se è necessario per avviare precedenti la successiva chiave automatica in sequenza ora queste impostazioni, prendere in considerazione una chiamata esplicita a `IKeyManager.CreateNewKey` per imporre la creazione di una nuova chiave.</span><span class="sxs-lookup"><span data-stu-id="c38c1-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="c38c1-116">Ricordarsi di fornire una data di attivazione esplicita ({ora + 2 giorni} è una buona regola pratica di tempo affinché la modifica si propaghi) e data di scadenza nella chiamata.</span><span class="sxs-lookup"><span data-stu-id="c38c1-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="c38c1-117">Tocca il repository tutte le applicazioni specifichino le stesse impostazioni con la `IDataProtectionBuilder` metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="c38c1-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="c38c1-118">In caso contrario, le proprietà della chiave persistente saranno dipende l'applicazione specifica che ha richiamato la routine di generazione delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="c38c1-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
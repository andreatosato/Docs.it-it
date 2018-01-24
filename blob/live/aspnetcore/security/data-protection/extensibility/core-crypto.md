---
title: "Estendibilità della crittografia core"
author: rick-anderson
description: Spiega IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer e la factory di primo livello.
ms.author: riande
manager: wpickett
ms.date: 8/11/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: b82c30fe40c4badc74645dafa9f0d13f6ffae031
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="core-cryptography-extensibility"></a><span data-ttu-id="3f93c-103">Estendibilità della crittografia core</span><span class="sxs-lookup"><span data-stu-id="3f93c-103">Core cryptography extensibility</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="3f93c-104">Tipi che implementano le interfacce seguenti devono essere thread-safe per più i chiamanti.</span><span class="sxs-lookup"><span data-stu-id="3f93c-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="3f93c-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="3f93c-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="3f93c-106">Il **IAuthenticatedEncryptor** interfaccia è il componente di base del sottosistema di crittografia.</span><span class="sxs-lookup"><span data-stu-id="3f93c-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="3f93c-107">È in genere uno IAuthenticatedEncryptor per ogni chiave e l'istanza IAuthenticatedEncryptor esegue il wrapping di tutte le chiavi crittografiche e algoritmiche informazioni necessarie per eseguire le operazioni di crittografia.</span><span class="sxs-lookup"><span data-stu-id="3f93c-107">There is generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="3f93c-108">Come suggerisce il nome, il tipo è responsabile di fornire servizi di crittografia e decrittografia autenticati.</span><span class="sxs-lookup"><span data-stu-id="3f93c-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="3f93c-109">Espone le seguenti due API.</span><span class="sxs-lookup"><span data-stu-id="3f93c-109">It exposes the following two APIs.</span></span>

* <span data-ttu-id="3f93c-110">Decrittografare (ArraySegment<byte> testo crittografato, ArraySegment<byte> additionalAuthenticatedData): byte]</span><span class="sxs-lookup"><span data-stu-id="3f93c-110">Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

* <span data-ttu-id="3f93c-111">Crittografare (ArraySegment<byte> in testo normale, ArraySegment<byte> additionalAuthenticatedData): byte]</span><span class="sxs-lookup"><span data-stu-id="3f93c-111">Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

<span data-ttu-id="3f93c-112">Il metodo di crittografia restituisce un blob che include il testo non crittografato crittografato e un tag di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3f93c-112">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="3f93c-113">Il tag di autenticazione deve includere i dati di autenticazione aggiuntivi (ad), anche se non devono essere ripristinabili dal payload finale di AAD.</span><span class="sxs-lookup"><span data-stu-id="3f93c-113">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="3f93c-114">Il metodo Decrypt convalida il tag di autenticazione e restituisce il payload deciphered.</span><span class="sxs-lookup"><span data-stu-id="3f93c-114">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="3f93c-115">Tutti gli errori (ad eccezione di ArgumentNullException e così via) devono essere omogeneizzati per CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="3f93c-115">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="3f93c-116">La stessa istanza di IAuthenticatedEncryptor non è effettivamente necessario contenere il materiale della chiave.</span><span class="sxs-lookup"><span data-stu-id="3f93c-116">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="3f93c-117">Ad esempio, l'implementazione può delegare a un modulo HSM per tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="3f93c-117">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="3f93c-118">Come creare un IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="3f93c-118">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="3f93c-119">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="3f93c-119">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="3f93c-120">Il **IAuthenticatedEncryptorFactory** interfaccia rappresenta un tipo in grado di creare un [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) istanza.</span><span class="sxs-lookup"><span data-stu-id="3f93c-120">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="3f93c-121">L'API è come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="3f93c-121">Its API is as follows.</span></span>

* <span data-ttu-id="3f93c-122">CreateEncryptorInstance (chiave IKey): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="3f93c-122">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="3f93c-123">Per un'istanza specificata di IKey, qualsiasi autenticazione rifiutati creati dal metodo CreateEncryptorInstance devono essere considerati equivalente, come nell'esempio di codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="3f93c-123">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="3f93c-124">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="3f93c-124">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="3f93c-125">Il **IAuthenticatedEncryptorDescriptor** interfaccia rappresenta un tipo in grado di creare un [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) istanza.</span><span class="sxs-lookup"><span data-stu-id="3f93c-125">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="3f93c-126">L'API è come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="3f93c-126">Its API is as follows.</span></span>

* <span data-ttu-id="3f93c-127">CreateEncryptorInstance(): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="3f93c-127">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="3f93c-128">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="3f93c-128">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="3f93c-129">Ad esempio IAuthenticatedEncryptor, un'istanza di IAuthenticatedEncryptorDescriptor equivale a eseguire il wrapping di una chiave specifica.</span><span class="sxs-lookup"><span data-stu-id="3f93c-129">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="3f93c-130">Ciò significa che per un'istanza di IAuthenticatedEncryptorDescriptor specificata, qualsiasi rifiutati autenticati creati dal metodo CreateEncryptorInstance devono essere considerati equivalenti, come l'esempio di codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="3f93c-130">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="3f93c-131">IAuthenticatedEncryptorDescriptor (ASP.NET Core solo 2. x)</span><span class="sxs-lookup"><span data-stu-id="3f93c-131">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="3f93c-132">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="3f93c-132">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="3f93c-133">Il **IAuthenticatedEncryptorDescriptor** interfaccia rappresenta un tipo in grado di esportare se stessa in formato XML.</span><span class="sxs-lookup"><span data-stu-id="3f93c-133">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="3f93c-134">L'API è come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="3f93c-134">Its API is as follows.</span></span>

* <span data-ttu-id="3f93c-135">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="3f93c-135">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="3f93c-136">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="3f93c-136">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="3f93c-137">Serializzazione XML</span><span class="sxs-lookup"><span data-stu-id="3f93c-137">XML Serialization</span></span>

<span data-ttu-id="3f93c-138">La differenza principale tra IAuthenticatedEncryptor e IAuthenticatedEncryptorDescriptor è che il descrittore di grado di creare il componente di crittografia e fornirlo con gli argomenti validi.</span><span class="sxs-lookup"><span data-stu-id="3f93c-138">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="3f93c-139">Si consideri un IAuthenticatedEncryptor la cui implementazione si basa su SymmetricAlgorithm e KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="3f93c-139">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="3f93c-140">Processo del componente di crittografia consiste nell'utilizzare questi tipi, ma non necessariamente conoscere provenienza questi tipi, in modo non è realmente scrivere una descrizione appropriata della procedura ricreare stesso se l'applicazione viene riavviata.</span><span class="sxs-lookup"><span data-stu-id="3f93c-140">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="3f93c-141">Il descrittore agisce come un livello superiore nella parte superiore di questa.</span><span class="sxs-lookup"><span data-stu-id="3f93c-141">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="3f93c-142">Poiché il descrittore sia in grado di creare l'istanza del componente di crittografia (ad esempio, da sapere come creare gli algoritmi necessari), in modo che l'istanza del componente di crittografia può essere ricreato dopo la reimpostazione di un'applicazione può serializzare tali informazioni in formato XML.</span><span class="sxs-lookup"><span data-stu-id="3f93c-142">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="3f93c-143">Il descrittore di può essere serializzato tramite la routine ExportToXml.</span><span class="sxs-lookup"><span data-stu-id="3f93c-143">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="3f93c-144">Questa routine restituisce un XmlSerializedDescriptorInfo che contiene due proprietà: la rappresentazione di XElement del descrittore e il tipo che rappresenta un [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) che può essere utilizzato per ripristinare il descrittore di base di XElement corrispondente.</span><span class="sxs-lookup"><span data-stu-id="3f93c-144">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="3f93c-145">Il descrittore serializzato può contenere informazioni riservate, ad esempio chiavi crittografiche.</span><span class="sxs-lookup"><span data-stu-id="3f93c-145">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="3f93c-146">Il sistema di protezione dati include il supporto incorporato per crittografare le informazioni prima di è resa persistente nell'archivio.</span><span class="sxs-lookup"><span data-stu-id="3f93c-146">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="3f93c-147">Per sfruttare i vantaggi di questo, è necessario il descrittore di contrassegnare l'elemento che contiene informazioni riservate con il nome dell'attributo "requiresEncryption" (xmlns "http://schemas.asp.net/2015/03/dataProtection"), valore "true".</span><span class="sxs-lookup"><span data-stu-id="3f93c-147">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "http://schemas.asp.net/2015/03/dataProtection"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="3f93c-148">È un'API di supporto per l'impostazione di questo attributo.</span><span class="sxs-lookup"><span data-stu-id="3f93c-148">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="3f93c-149">Chiamare il metodo di estensione che XElement.markasrequiresencryption() si trova nello spazio dei nomi Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span><span class="sxs-lookup"><span data-stu-id="3f93c-149">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="3f93c-150">Può anche essere casi in cui il descrittore serializzato non contiene informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="3f93c-150">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="3f93c-151">Si consideri di nuovo nel caso di una chiave di crittografia archiviata in un HSM.</span><span class="sxs-lookup"><span data-stu-id="3f93c-151">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="3f93c-152">Il descrittore non è possibile scrivere il materiale della chiave durante la serializzazione stesso, poiché il modulo di protezione non espone il materiale di riferimento in formato non crittografato.</span><span class="sxs-lookup"><span data-stu-id="3f93c-152">The descriptor cannot write out the key material when serializing itself since the HSM will not expose the material in plaintext form.</span></span> <span data-ttu-id="3f93c-153">Al contrario, il descrittore di può scrivere la versione di chiave con wrapping della chiave (se il modulo di protezione hardware consente l'esportazione in questo modo) o l'identificatore univoco dell'hardware per la chiave.</span><span class="sxs-lookup"><span data-stu-id="3f93c-153">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="3f93c-154">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="3f93c-154">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="3f93c-155">Il **IAuthenticatedEncryptorDescriptorDeserializer** interfaccia rappresenta un tipo in grado di deserializzare un'istanza di IAuthenticatedEncryptorDescriptor da un XElement.</span><span class="sxs-lookup"><span data-stu-id="3f93c-155">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="3f93c-156">Espone un singolo metodo:</span><span class="sxs-lookup"><span data-stu-id="3f93c-156">It exposes a single method:</span></span>

* <span data-ttu-id="3f93c-157">ImportFromXml (elemento XElement): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="3f93c-157">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="3f93c-158">Il metodo ImportFromXml accetta XElement che è stato restituito da [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) e crea un equivalente di IAuthenticatedEncryptorDescriptor l'originale.</span><span class="sxs-lookup"><span data-stu-id="3f93c-158">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="3f93c-159">I tipi che implementano IAuthenticatedEncryptorDescriptorDeserializer devono avere uno dei due costruttori pubblici seguenti:</span><span class="sxs-lookup"><span data-stu-id="3f93c-159">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="3f93c-160">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="3f93c-160">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="3f93c-161">.ctor()</span><span class="sxs-lookup"><span data-stu-id="3f93c-161">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="3f93c-162">L'IServiceProvider passato al costruttore può essere null.</span><span class="sxs-lookup"><span data-stu-id="3f93c-162">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="3f93c-163">La factory di primo livello</span><span class="sxs-lookup"><span data-stu-id="3f93c-163">The top-level factory</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="3f93c-164">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="3f93c-164">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="3f93c-165">Il **AlgorithmConfiguration** classe rappresenta un tipo che sia in grado di creare [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) istanze.</span><span class="sxs-lookup"><span data-stu-id="3f93c-165">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="3f93c-166">Espone una singola API.</span><span class="sxs-lookup"><span data-stu-id="3f93c-166">It exposes a single API.</span></span>

* <span data-ttu-id="3f93c-167">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="3f93c-167">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="3f93c-168">AlgorithmConfiguration può essere paragonato alla factory di primo livello.</span><span class="sxs-lookup"><span data-stu-id="3f93c-168">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="3f93c-169">La configurazione viene utilizzato come modello.</span><span class="sxs-lookup"><span data-stu-id="3f93c-169">The configuration serves as a template.</span></span> <span data-ttu-id="3f93c-170">Esegue il wrapping informazioni algoritmiche (ad esempio, questa configurazione genera descrittori con una chiave master di AES-128-GCM), ma non è ancora associata a una chiave specifica.</span><span class="sxs-lookup"><span data-stu-id="3f93c-170">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="3f93c-171">Quando viene chiamato CreateNewDescriptor, nuovo materiale della chiave viene creato esclusivamente per la chiamata e viene generato un nuovo IAuthenticatedEncryptorDescriptor che include il materiale della chiave e le informazioni algoritmiche necessarie per utilizzare il materiale di riferimento.</span><span class="sxs-lookup"><span data-stu-id="3f93c-171">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="3f93c-172">Il materiale della chiave potrebbe creato in software (e in memoria), viene creato e potrebbe essere contenuta all'interno di un modulo di protezione hardware e così via.</span><span class="sxs-lookup"><span data-stu-id="3f93c-172">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="3f93c-173">Il punto essenziale è che le due chiamate a CreateNewDescriptor mai devono essere create istanze IAuthenticatedEncryptorDescriptor equivalente.</span><span class="sxs-lookup"><span data-stu-id="3f93c-173">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="3f93c-174">Il tipo AlgorithmConfiguration funge da punto di ingresso per le routine di creazione della chiave, ad esempio [rollover automatico della chiave](../implementation/key-management.md#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="3f93c-174">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#key-expiration-and-rolling).</span></span> <span data-ttu-id="3f93c-175">Per modificare l'implementazione per tutte le chiavi future, impostare la proprietà AuthenticatedEncryptorConfiguration in KeyManagementOptions.</span><span class="sxs-lookup"><span data-stu-id="3f93c-175">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="3f93c-176">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="3f93c-176">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="3f93c-177">Il **IAuthenticatedEncryptorConfiguration** interfaccia rappresenta un tipo che sia in grado di creare [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) istanze.</span><span class="sxs-lookup"><span data-stu-id="3f93c-177">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="3f93c-178">Espone una singola API.</span><span class="sxs-lookup"><span data-stu-id="3f93c-178">It exposes a single API.</span></span>

* <span data-ttu-id="3f93c-179">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="3f93c-179">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="3f93c-180">IAuthenticatedEncryptorConfiguration può essere paragonato alla factory di primo livello.</span><span class="sxs-lookup"><span data-stu-id="3f93c-180">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="3f93c-181">La configurazione viene utilizzato come modello.</span><span class="sxs-lookup"><span data-stu-id="3f93c-181">The configuration serves as a template.</span></span> <span data-ttu-id="3f93c-182">Esegue il wrapping informazioni algoritmiche (ad esempio, questa configurazione genera descrittori con una chiave master di AES-128-GCM), ma non è ancora associata a una chiave specifica.</span><span class="sxs-lookup"><span data-stu-id="3f93c-182">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="3f93c-183">Quando viene chiamato CreateNewDescriptor, nuovo materiale della chiave viene creato esclusivamente per la chiamata e viene generato un nuovo IAuthenticatedEncryptorDescriptor che include il materiale della chiave e le informazioni algoritmiche necessarie per utilizzare il materiale di riferimento.</span><span class="sxs-lookup"><span data-stu-id="3f93c-183">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="3f93c-184">Il materiale della chiave potrebbe creato in software (e in memoria), viene creato e potrebbe essere contenuta all'interno di un modulo di protezione hardware e così via.</span><span class="sxs-lookup"><span data-stu-id="3f93c-184">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="3f93c-185">Il punto essenziale è che le due chiamate a CreateNewDescriptor mai devono essere create istanze IAuthenticatedEncryptorDescriptor equivalente.</span><span class="sxs-lookup"><span data-stu-id="3f93c-185">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="3f93c-186">Il tipo IAuthenticatedEncryptorConfiguration funge da punto di ingresso per le routine di creazione della chiave, ad esempio [rollover automatico della chiave](../implementation/key-management.md#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="3f93c-186">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#key-expiration-and-rolling).</span></span> <span data-ttu-id="3f93c-187">Per modificare l'implementazione per tutte le chiavi future, registrare un singleton IAuthenticatedEncryptorConfiguration nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="3f93c-187">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
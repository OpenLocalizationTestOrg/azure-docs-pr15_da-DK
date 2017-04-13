<properties 
    pageTitle="Opdater Media Services efter rullende lagerplads adgangstaster | Microsoft Azure" 
    description="I denne artikel giver dig vejledning til, hvordan du opdaterer Media Services efter rullende lagerplads adgangstaster." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="milangada;cenkdin;juliako"/>

#<a name="update-media-services-after-rolling-storage-access-keys"></a>Opdater Media Services efter rullende lagerplads access-taster

Når du opretter en ny konto Azure Media Services, bliver du også bedt om at vælge en Azure-lager-konto, der bruges til at gemme dine medieindhold. Bemærk, at du kan [tilføje mere end ét lagerplads konti](meda-services-managing-multiple-storage-accounts.md) til kontoen Media Services.

Når der oprettes en ny konto lagerplads, genererer Azure to 512-bit lagerplads adgangstaster, som bruges til at godkende adgang til kontoen lagerplads. Hvis du vil bevare dine lagerplads forbindelser mere sikker, skal anbefales det at genoprette og rotere din lagerplads adgangsnøgle med jævne mellemrum. Der findes to access-taster (primære og sekundære) for at gør det muligt at vedligeholde forbindelser til kontoen lagerplads ved hjælp af en access-tasten, mens du genoprette nøglen andre access. Denne fremgangsmåde kaldes også "rullende hurtigtaster".

Media Services, afhænger af en lagerplads nøgle, der er angivet til den. Nærmere betegnet kan afhænger locators, der bruges til at streame eller hente dine aktiver hurtigtast angivne lagerplads. Når der oprettes et AMS firma det tager en afhængighed på hurtigtast primære lagerplads som standard, men som en bruger, du kan opdatere nøglen lagerplads, som har AMS. Du skal sikre dig at lade Media Services ved, hvilken nøgle for at bruge ved at følge trin, der er beskrevet i dette emne. Også, når rullende lagerplads adgangstaster, skal du sørge for at opdatere din locators så der bliver ikke afbrudt i din streaming tjeneste (dette trin er også beskrevet i emnet).

>[AZURE.NOTE]Hvis du har flere lagerplads konti, skal du udføre denne procedure med hver lagerplads konto.
>
>Før du udfører trin som beskrevet i dette emne på en konto fremstilling, Sørg for at teste dem på en foreløbig fremstilling konto.


## <a name="step-1-regenerate-secondary-storage-access-key"></a>Trin 1: Genoprette sekundær lagerplads adgangsnøgle

Start med at genoprette sekundær lagerplads nøgle. En sekundær nøgle bruges som standard ikke af Media Services.  Finde oplysninger om, hvordan du rulle lagerplads taster [Sådan: få vist, Kopiér og Regenerer lagerplads hurtigtasterne](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Trin 2: Opdater Media Services til at bruge den nye nøgle sekundær lagerplads

Opdater Media Services til at bruge hurtigtast sekundær lagerplads. Du kan bruge en af følgende to metoder til at synkronisere tasten gendannede lagerplads med Media Services.

- Bruge Azure-portalen: for at finde navnet på og nøgle værdier, skal du gå til Azure-portalen og vælge din konto. Vinduet indstillinger vises til højre. Vælg taster i vinduet indstillinger. Afhængigt af hvilken lagerplads nøgle, du vil bruge til Media Services til at synkronisere med, skal du vælge Synkroniser primær nøgle eller Synkroniser sekundær knappen. I dette tilfælde bruge tasten sekundær.

- Bruge Media Services management REST-API.

Følgende kodeeksempel viser opbygningen af https://endpoint/***subscriptionId/services/mediaservices/konti/kontonavn*/StorageAccounts/*storageAccountName*installationsparameter anmodning for at synkronisere den angivne lagerplads nøgle med Media Services. I dette tilfælde bruges den sekundære lagerplads nøgleværdi. Du kan finde flere oplysninger, se [Sådan: Brug Media Services Management REST-API](http://msdn.microsoft.com/library/azure/dn167656.aspx).
    
    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);
        
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);
        
        
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }
        
        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

Når dette trin skal du opdatere eksisterende locators (som har afhængighed på tasten gamle lagerplads) som vist i følgende trin.

>[AZURE.NOTE]Vent på 30 minutter før du udfører handlinger med Media Services (for eksempel oprettelse af nye locators) for at forhindre en hvilken som helst virkning på afventende job.

##<a name="step-3-update-locators"></a>Trin 3: Opdater locators

>[AZURE.NOTE]Når rullende lagerplads adgangstaster, skal du sørge for at opdatere din eksisterende locators, så der ikke er uden afbrydelser i din streaming tjeneste.

Vent mindst 30 minutter efter synkronisering af den nye lagerplads nøgle med AMS. Derefter kan du gendanne din anmodet arbejdsproces locators, så de tager afhængighed på tasten angivne lager og bevare den eksisterende URL-adresse.

Bemærk, at når du opdaterer (eller genopret) en SAS locator, URL-adressen kan altid ændre.

>[AZURE.NOTE] For at sikre, at du bevare de eksisterende URL-adresserne på din anmodet arbejdsproces locators, skal du slette den eksisterende locator og oprette en ny med det samme-ID.

.NET eksemplet nedenfor viser, hvordan at genskabe en locator med det samme-ID.

privat statisk ILocator RecreateLocator(CloudMediaContext context, ILocator locator) {/ / Gem egenskaber for eksisterende locator.
var aktiv = locator. Aktiv. varians accessPolicy = locator. AccessPolicy; varians locatorId = locator. -Id. varians startdato = locator. Starttidspunkt; varians locatorType = locator. Skriv; varians locatorName = locator. Navn.

Slette gamle locator.
Locator. DELETE();

Hvis (locator. ExpirationDateTime < = DateTime.UtcNow) {Udløs nye undtagelse (String.Format ("ikke kan genoprette locator Id = {0} fordi dens locator udløbsdatoen er overskredet", locator. Id)); }
    
        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);
    
    
    
        return newLocator;
    }


##<a name="step-5-regenerate--primary-storage-access-key"></a>Trin 5: Genoprette primære lagerplads adgangsnøgle

Genoprette hurtigtast primære lagerplads. Finde oplysninger om, hvordan du rulle lagerplads taster [Sådan: få vist, Kopiér og Regenerer lagerplads hurtigtasterne](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##<a name="step-6-update-media-services-to-use-the-new-primary-storage-key"></a>Trin 6: Opdater Media Services til at bruge den nye lagerplads primære nøgle
    
Brug den samme fremgangsmåde som beskrevet i [trin 2](media-services-roll-storage-access-keys.md#step2) , men denne gang du synkronisere den nye primære lagerplads access nøgle med kontoen Media Services.

>[AZURE.NOTE]Vent på 30 minutter før du udfører handlinger med Media Services (for eksempel oprettelse af nye locators) for at forhindre en hvilken som helst virkning på afventende job.

##<a name="step-7-update-locators"></a>Trin 7: Opdater locators  

Efter 30 minutter kan du genskabe din anmodet arbejdsproces locators, så de tager afhængighed på den nye primære lagerplads nøgle og bevare den eksisterende URL-adresse.

Bruge den samme fremgangsmåde, som beskrevet i [trin 3](media-services-roll-storage-access-keys.md#step-3-update-locators).


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###<a name="acknowledgments"></a>Bekræftelse 

Vi vil gerne bekræfte de følgende personer, der har bidraget for at skabe dette dokument: Cenk Dingiloglu, Milano Gada Seva Titov.

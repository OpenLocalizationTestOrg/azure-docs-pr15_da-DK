<properties
    pageTitle="Azure Media Services fejlkoder | Microsoft Azure"
    description="Emnet giver et overblik over Azure Media Services fejlkoder."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>

# <a name="azure-media-services-error-codes"></a>Azure Media Services fejlkoder

Når du bruger Microsoft Azure Media Services, modtager du muligvis HTTP fejlkoder fra tjenesten afhængigt af problemer som godkendelsestokens udløber på Handlinger, der ikke understøttes i Media Services. Følgende er en liste over **HTTP fejlkoder** , der kan blive returneret af Media Services og de mulige årsager til dem.  
  
## <a name="400-bad-request"></a>400 forkert anmodning

Anmodningen indeholder ugyldige oplysninger og afvises på grund af en af følgende årsager:

- Er angivet en ikke-understøttede API-version. Se [konfiguration af til udvikling af Media Services RESTEN API](media-services-rest-how-to-use.md)til den nyeste version.
- API-version af Media Services er ikke angivet. Finde oplysninger om at angive API-version, i [tilslutning til Media Services med Media Services REST-API](media-services-rest-connect-programmatically.md). 
   
    >[AZURE.NOTE] Hvis du bruger .NET eller Java SDK'er at oprette forbindelse til Media Services, er API-version angivet for dig, når du prøver og udfører en handling mod Media Services.
- En udefineret egenskab er blevet angivet. Navnet på egenskaben er fejlmeddelelse. Kun de egenskaber, der er medlemmer af en bestemt enhed kan angives. Du kan se [Azure Media Services RESTEN API Reference](http://msdn.microsoft.com/library/azure/hh973617.aspx) til en liste over objekter og deres egenskaber.
- En ugyldig egenskabsværdi er blevet angivet. Navnet på egenskaben er fejlmeddelelse. Se forrige linket til gyldig egenskabstyper og deres værdier.
- En egenskabsværdi mangler og er påkrævet.
- Del af den angivne URL-adresse indeholder en forkert værdi.
- Blev forsøgt at opdatere en WriteOnce.
- Blev forsøgt at oprette en sag, der indeholder et input aktiv med en primær AssetFile, der ikke blev angivet eller blev ikke fundet.
- Blev forsøgt at opdatere en SAS Locator. SAS locators kan kun oprettes eller slettes. Streaming locators kan opdateres. Du kan finde flere oplysninger [Locators](http://msdn.microsoft.com/library/azure/hh974308.aspx).
- En ikke-understøttet handling eller forespørgsel blev sendt. 

## <a name="401-unauthorized"></a>401 uautoriseret

Anmodningen blev ikke godkendt (før den kan være godkendt) på grund af en af følgende årsager:

- Manglende godkendelse sidehoved.
- Ugyldig godkendelse sidehoved værdi.
    - Tokenet er udløbet. Hvis bruger REST API'er direkte, i [tilslutning til Media Services med Media Services REST-API](media-services-rest-connect_programmatically.md) til Lær at oprette en ny godkendelse token. Hvis du bruger .NET eller Java SDK'er skal du oprette et CloudMediaContext eller MediaContract objekt for at generere tokenet. Du kan finde flere oplysninger om, hvordan du gør dette, i [tilslutning til Media Services med Media Services SDK til .NET](media-services-dotnet-connect-programmatically.md).
    - Tokenet indeholder en ugyldig signatur.</li></ul></li></ul>

## <a name="403-forbidden"></a>403 forbudt

Anmodningen er ikke tilladt på grund af en af følgende årsager:

- Media Services kontoen blev ikke fundet eller er blevet slettet.
- Kontoen Media Services er deaktiveret, og anmodning om typen er ikke HTTP GET. Tjenestehandlinger vil returnere et 403 svar.
- Godkendelse tokenet indeholder ikke brugerens legitimationsoplysninger oplysninger: kontonavn og/eller SubscriptionId. Du kan finde disse oplysninger i filtypenavnet Media Services brugergrænseflade til kontoen Media Services på portalen Azure administration.
- Ressourcen kan ikke åbnes.
    - Blev forsøgt at bruge en MediaProcessor, der ikke er tilgængelig for din Media Services-konto.
    - Blev forsøgt at opdatere en JobTemplate, der er defineret af Media Services.
    - Blev forsøgt at overskrive nogle andre Media Services-kontoen Locator.
    - Blev forsøgt at overskrive nogle andre Media Services-kontoen ContentKey.

- Ressourcen kunne ikke oprettes på grund af en tjeneste kvote, som blev nået for kontoen Media Services. Se [kvoter og begrænsninger](media-services-quotas-and-limitations.md)for flere oplysninger om tjenesten kvoter.

## <a name="404-not-found"></a>404 ikke fundet

Anmodningen er ikke tilladt for en ressource på grund af en af følgende årsager:

- Blev forsøgt at opdatere et objekt, der ikke findes.
- Blev forsøgt at slette et objekt, der ikke findes.
- Blev forsøgt at oprette et objekt, der er knyttet til et objekt, der ikke findes.
- Blev forsøgt at få et objekt, der ikke findes.
- Blev forsøgt at angive en lagerplads-konto, der ikke er knyttet til kontoen Media Services.  

## <a name="409-conflict"></a>409 konflikt

Anmodningen er ikke tilladt på grund af en af følgende årsager:

- Mere end én AssetFile med det angivne navn inden for aktivet.
- Blev forsøgt at oprette en anden primær AssetFile inden for aktivet.
- Blev forsøgt at oprette en ContentKey med det angivne Id allerede i brug.
- Blev forsøgt at oprette en Locator med det angivne Id allerede i brug.
- Mere end én IngestManifestFile med det angivne navn i IngestManifest.
- Blev forsøgt at sammenkæde en anden lagerplads kryptering ContentKey med lagerplads-krypterede aktivet.
- Blev forsøgt at oprette et link i samme ContentKey til aktivet.
- Blev forsøgt at oprette en locator til et aktiv, hvis objektbeholder til lagring, mangler eller er ikke længere er knyttet til aktivet.
- Blev forsøgt at oprette en locator til et aktiv, som allerede har 5 locators i brug. (Azure-lager håndhæves grænsen på fem politikkerne for delt adgang i en objektbeholder til lagring).
- Sammenkæde lagerplads konto for et aktiv med en IngestManifestAsset er ikke den samme som kontoen lagerplads, der bruges af overordnet IngestManifest.  

## <a name="500-internal-server-error"></a>500 Intern serverfejl

Under behandling af anmodningen støder Media Services nogle fejl, der forhindrer behandling i at fortsætte. Det kan skyldes et af følgende årsager:

- Oprette en aktiv eller et Job mislykkes, fordi kontoen Media Services service kvote oplysninger er ikke tilgængelig i øjeblikket.
- Oprette en aktiv eller IngestManifest blob storage objektbeholder mislykkes, fordi firmaets lagerplads kontooplysninger er ikke tilgængelig i øjeblikket.
- Andre uventet fejl. 

## <a name="503-service-unavailable"></a>503 Tjenesten er ikke tilgængelig

Serveren er i øjeblikket ikke modtage anmodninger. Denne fejl kan være forårsaget af unødvendig forespørgsler til webtjenesten. Media Services, throttling ordning begrænser visningen Ressourceforbrug for programmer, der gør unødvendig anmodning til tjenesten.

>[AZURE.NOTE]Kontrollér fejlmeddelelse og fejl kodestreng for at få mere detaljerede oplysninger om årsagen, du har fået fejlen 503. Denne fejl betyder altid ikke (throttling).

Beskrivelser af mulige status er:

- "Serveren er optaget. Forrige kører af denne type af anmodning tog mere end {0} sekunder."
- "Serveren er optaget. Mere end {0} anmodninger sekundet kan være begrænset."
- "Serveren er optaget. Mere end {0} anmodninger inden for {1} sekunder kan være begrænset."

For at håndtere denne fejl, anbefaler vi, at du bruger eksponentiel tilbage fra forsøg. Det betyder, at ved hjælp af gradvist længere venter mellem nye forsøg for efterfølgende fejlsvar.  Du kan finde yderligere oplysninger finder [Midlertidige fejl håndtering af programmet Bloker](https://msdn.microsoft.com/library/hh680905.aspx). 

>[AZURE.NOTE]Hvis du bruger [Azure Media Services SDK til .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), er blevet implementeret forsøg for fejlen 503 ved SDK.  
  
## <a name="see-also"></a>Se også  

[Media Services Management fejlkoder](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

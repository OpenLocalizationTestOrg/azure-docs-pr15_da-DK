<properties 
    pageTitle="Oversigt over Media Services REST-API | Microsoft Azure" 
    description="Oversigt over Media Services REST-API" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako"/>


# <a name="media-services-rest-api-overview"></a>Oversigt over Media Services REST-API 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Microsoft Azure Media Services er en tjeneste, der accepterer OData-baserede HTTP-anmodninger og kan svare tilbage i detaljeret JSON eller atom + pub. Da Media Services er i overensstemmelse med retningslinjer for Azure design, er der en række nødvendige HTTP-headere, som hver klient skal bruge, når du opretter forbindelse til Media Services, samt et sæt af valgfri sidehoveder, der kan bruges. I nedenstående afsnit beskrives overskrifterne og HTTP-verber kan du bruge hvornår oprettes anmodninger om og modtage svar fra Media Services.

##<a name="considerations"></a>Overvejelser i forbindelse med 

Overvej følgende gælder, når du bruger RESTEN.

- Når forespørger på enheder, er der en grænse på 1000 objekter, der returneres ad gangen, fordi offentlige RESTEN v2 begrænser forespørgselsresultater 1000 resultater. Du skal bruge **Spring over** og **tage** (.NET) / **toppen** (RESTEN) som beskrevet i [dette eksempel .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) og [eksemplet REST-API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 

- Når bruge JSON og angive for at bruge nøgleordet **__metadata** i anmodningen (for eksempel til refererer til et sammenkædet objekt) skal du angive **Acceptér** sidehovedet til [JSON detaljeret format](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (se eksemplet nedenfor). OData forstår ikke egenskaben **__metadata** i anmodningen, medmindre du angiver den til detaljeret.  

        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
        
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 
        

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standard HTTP-anmodning om headere understøttes af Media Services

For hvert opkald, du foretager i Media Services, der er et sæt af nødvendige sidehoveder, skal du medtage i din anmodning og også en række valgfri sidehoveder du muligvis vil medtage. I følgende tabel vises de nødvendige sidehoveder:


Sidehoved|Type|Værdi
---|---|---
Godkendelse|Bæreren|Bæreren er den eneste accepterede godkendelse ordning. Værdien skal også indeholde adgangstoken leveres af ACS.
x-ms-version|Decimal|2.11
DataServiceVersion|Decimal|3.0
MaxDataServiceVersion|Decimal|3.0



>[AZURE.NOTE] Da Media Services bruger OData til at få vist dets underliggende aktiv metadata lager gennem RESTEN API'er, skal DataServiceVersion og MaxDataServiceVersion overskrifterne medtages i en forespørgsel men hvis det er tilfældet, derefter aktuelt Media Services antages DataServiceVersion værdien i brug er 3.0.

Følgende er en række valgfri sidehoveder:

Sidehoved|Type|Værdi
---|---|---
Dato|RFC 1123 dato|Tidsstempel for din anmodning
Acceptere|Indholdstype|Den ønskede indholdstype for svaret som følgende:<p> -programmet/json; odata = detaljeret<p> -programmet/atom + xml<p> Svar kan have en anden indholdstype, som en blob Hent, hvor en vellykket svar skal indeholde blob strømmen som data.
Acceptere-kode|Gzip, deflate|GZIP og DEFLATE kodning, når det er relevant. Bemærk: For store ressourcer Media Services kan ignorere denne overskrift og returnere noncompressed data.
Acceptere sprog|"da", "es", og så videre.|Angiver det foretrukne sprog for svaret.
Acceptere tegnsæt|Tegnsæt type som "UTF-8"|Standard er UTF-8.
X-HTTP-metoden|HTTP-metode|Kan klienter eller firewalls, der ikke understøtter HTTP-metoder som læg eller Slet for at bruge disse metoder, tunnelført via et GET-opkald.
Indholdstype|Indholdstype|Indholdstype for anmodningsteksten i læg eller INDLÆG anmodninger.
klient-anmodning-id|Streng|En opkalds-defineret værdi, der identificerer den angivne anmodning. Hvis angivet, skal denne værdi medtages i svarmeddelelsen som en metode til at overføre forespørgslen. <p><p>**Vigtige**<p>Værdier skal lukkes på 2096b (2k).

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standard HTTP-svarheadere understøttes af Media Services

Følgende er en række til overskrifter, kan returneres til dig, afhængigt af den ressource, du anmoder om og den handling, du skal udføre.


Sidehoved|Type|Værdi
---|---|---
anmodning om-id|Streng|Et entydigt id for den aktuelle operation, service, der er oprettet.
klient-anmodning-id|Streng|Et id, der er angivet af kalderen i den oprindelige forespørgsel, hvis de forefindes.
Dato|RFC 1123 dato|Den dato, anmodningen blev behandlet.
Indholdstype|Varierer|Den indholdstype for svar brødteksten.
Indhold-kode|Varierer|Gzip eller deflate, efter behov.


## <a name="standard-http-verbs-supported-by-media-services"></a>Standard HTTP-verber, der understøttes af Media Services

Følgende er en komplet liste over HTTP-verber, der kan bruges, når foretage HTTP anmoder om:


Verbum|Beskrivelse
---|---
HENT|Returnerer den aktuelle værdi af et objekt.
INDLÆG|Opretter et objekt, der er baseret på de data, der er angivet, eller sender en kommando.
LÆG|Erstatter et objekt, eller opretter et navngivet objekt (når det er relevant).
SLET|Sletter et objekt.
FLET|Opdaterer en eksisterende objekt med navngivne egenskabsændringer.
I AFSNIT|Returnerer metadataene i et objekt til et få svar.

##<a name="limitation"></a>Begrænsning

Når forespørger på enheder, er der en grænse på 1000 objekter, der returneres ad gangen, fordi offentlige RESTEN v2 begrænser forespørgselsresultater 1000 resultater. Du skal bruge **Spring over** og **tage** (.NET) / **toppen** (RESTEN) som beskrevet i [dette eksempel .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) og [eksemplet REST-API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 


## <a name="discovering-media-services-model"></a>Opdager Media Services model

Hvis du vil gøre Media Services enheder mere synlig, kan $metadata operationen bruges. Gør det muligt at hente alle gyldige objekttyper, objektegenskaber, tilknytninger, funktioner, handlinger og osv. Følgende eksempel viser, hvordan du kan oprette URI: https://media.windows.net/API/$ metadata.

Du skal føje "? api-version=2.x" til slutningen af URI, hvis du vil have vist metadata i en browser, eller du skal ikke medtage sidehovedet x-ms-version i din anmodning.



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





 

<properties 
    pageTitle="Sådan kode en aktiv, ved hjælp af Media Encoder Standard | Microsoft Azure" 
    description="Lær at bruge Media Encoder Standard til at kode medieindhold på Media Services. Kodeeksempler Brug REST-API." 
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
    ms.date="09/19/2016"
    ms.author="juliako"/>


#<a name="how-to-encode-an-asset-using-media-encoder-standard"></a>Sådan kode en aktiv, ved hjælp af Media Encoder Standard


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
- [RESTEN](media-services-rest-encode-asset.md)
- [Portal](media-services-portal-encode.md)

##<a name="overview"></a>Oversigt
For at kunne levere digital video over internettet skal du komprimere medier. Digitale videofiler er meget store og kan være for stor til at levere via internettet eller for dine kunders enheder, der vises korrekt. Processen med at komprimere video og lyd, så dine kunder kan få vist dine medier er kodning.

Kodning job er en af de mest almindelige behandling handlinger i Media Services. Du opretter kodning job for at konvertere mediefiler fra én kodning til en anden. Når du koder, kan du bruge Media Services indbyggede encoder (Media Encoder Standard). Du kan også bruge en koder, der leveres af en Media Services partner; fra tredjepart kodere er tilgængelige via Azure Marketplace. Du kan angive oplysninger om kodning opgaver ved hjælp af forudindstillede strenge, der er defineret for din encoder eller ved at bruge foruddefinerede konfigurationsfiler. For at se typerne forudindstillinger, der er tilgængelige, skal du se [Opgave forudindstillinger for Media Encoder Standard](http://msdn.microsoft.com/library/mt269960).

Hvert Job kan have en eller flere opgaver afhængigt af typen behandling, du vil udføre. Gennem REST-API, kan du oprette job og deres relaterede opgaver på en af to måder:

- Opgaver kan være defineret indbygget via egenskaben opgaver navigation på jobbet objekter, eller
- via OData batchbehandling.


Det anbefales at altid kode din mezzanine-filer i en tilpasset bithastighed MP4 angive og Konverter derefter sættet til det ønskede format ved hjælp af [Dynamiske emballagen](media-services-dynamic-packaging-overview.md). For at kunne udnytte dynamiske emballagen, skal du først få mindst én efter behov streaming enhed for streaming slutpunktet, som du planlægger at levering af dit indhold. Se, [hvordan du skala Media Services](media-services-portal-manage-streaming-endpoints.md)kan finde flere oplysninger.

Hvis din output aktiv er krypteret lagerplads, skal du konfigurere aktiv levering politik. Finde flere oplysninger under [konfiguration af aktiv levering politik](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE]Inden du starter refererer til medier processorer, kan du kontrollere, at du har det korrekte medie processor-ID. Se [Få medier processorer](media-services-rest-get-media-processor.md)kan finde flere oplysninger.

##<a name="create-a-job-with-a-single-encoding-task"></a>Oprette et job med en enkelt kodning opgave

>[AZURE.NOTE] Når du arbejder med Media Services REST-API, gælder følgende overvejelser:
>
>Når du åbner objekter i Media Services, skal du angive bestemte sidehoved felter og værdier i HTTP-anmodninger. Se [konfiguration for Media Services RESTEN API udvikling](media-services-rest-how-to-use.md)kan finde flere oplysninger.

>Når du har oprettet forbindelse til https://media.windows.net, får du en 301 Omdiriger, der angiver en anden Media Services URI. Du skal foretage efterfølgende opkald til den nye URI som beskrevet i [tilslutning til Media Services ved hjælp af REST-API](media-services-rest-connect-programmatically.md).
>
>Når bruge JSON og angive for at bruge nøgleordet **__metadata** i anmodningen (for eksempel til refererer til et sammenkædet objekt) skal du angive **Acceptér** sidehovedet til [JSON detaljeret format](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Acceptér: programmet/json; odata = detaljeret.

I følgende eksempel viser, hvordan til at oprette og sende en sag med en opgave, der er angivet til at kode en video på en bestemt opløsning og -kvalitet. Når kodning med Media Encoder Standard, kan du bruge opgave konfiguration forudindstillinger angivet [her](http://msdn.microsoft.com/library/mt269960).

Anmode om:

INDLÆG https://media.windows.net/API/Jobs HTTP/1.1 indholdstype: programmet/json; odata = detaljeret Acceptér: programmet/json; odata = detaljeret DataServiceVersion: 3.0 MaxDataServiceVersion: 3.0 x-ms-version: 2.11 godkendelse: bæreren <token value> 
 x-ms-klient-anmodning-id: 00000000-0000-0000-0000-000000000000 Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Svar:
    
    HTTP/1.1 201 Created

    . . . 

###<a name="set-the-output-assets-name"></a>Angive output aktivets navn

I følgende eksempel vises, hvordan du kan indstille egenskaben assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

##<a name="considerations"></a>Overvejelser i forbindelse med

- TaskBody egenskaber skal bruge ordret XML til at angive antallet input, eller Medtag aktiver, der anvendes af opgaven. Emnet opgave indeholder XML-skemadefinitionen for XML-filen.
- I definitionen TaskBody hver indre værdien af <inputAsset> og <outputAsset> skal være angivet som JobInputAsset(value) eller JobOutputAsset(value).
- En opgave kan have flere output aktiver. En JobOutputAsset(x) kan kun bruges én gang som output fra en opgave i en sag.
- Du kan angive JobInputAsset eller JobOutputAsset som et input aktiv for en opgave.
- Opgaver skal ikke udgør en cyklus.
- Parameteren value, du sender til JobInputAsset eller JobOutputAsset repræsenterer indeksværdien for et aktiv. De faktiske Aktiver er defineret i egenskaberne InputMediaAssets og OutputMediaAssets navigation på jobbet enhed definition. 
- Da Media Services er bygget på OData v3, de enkelte aktiver i InputMediaAssets og OutputMediaAssets navigation egenskabssamlinger der henvises til en "__metadata: uri" navn / værdi-par.
- InputMediaAssets knytter til en eller flere aktiver, du har oprettet i Media Services. OutputMediaAssets er oprettet af systemet. De ikke referere til et eksisterende aktiv.
- OutputMediaAssets kan navngives ved hjælp af attributten assetName. Hvis denne attribut er ikke til stede, og klik derefter på navnet på OutputMediaAsset bliver uanset tekstværdien af den <outputAsset> element er med et suffiks af værdien jobnavn eller værdien Job-Id (i de tilfælde, hvor egenskaben Name ikke er defineret). Eksempelvis hvis du angiver en værdi for assetName til "Eksempel", derefter OutputMediaAsset navn egenskaben ville være angivet til "Eksempel". Men hvis du ikke har angivet en værdi for assetName, men du angivet jobnavn til "NewJob", derefter navnet på OutputMediaAsset ville være "JobOutputAsset (værdi) _NewJob". 


##<a name="create-a-job-with-chained-tasks"></a>Oprette en sag med sammenkædede opgaver

I mange scenarier med programmet vil udviklere til at oprette en række behandling af opgaver. I Media Services, kan du oprette en række sammenkædede opgaver. Hver enkelt opgave udfører forskellige behandlingstrin og kan bruge forskellige medier processorer. De sammenkædede opgaver kan aflevere et aktiv fra én opgave til en anden, udfører en lineær serie af opgaver på aktivet. De opgaver, der er udført i et job behøver ikke at være i en sekvens. Når du opretter en sammenkædet opgave, oprettes de sammenkædede **ITask** objekter i et enkelt **IJob** objekt.

>[AZURE.NOTE] Der findes i øjeblikket en begrænsning på 30 opgaver hver sag. Hvis du har brug for til at sammenkæde mere end 30 opgaver, kan du oprette mere end ét job indeholde opgaverne.


    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


###<a name="considerations"></a>Overvejelser i forbindelse med

Sådan aktiverer du sammenkædning af opgave:

- Et job skal have mindst 2 opgaver
- Der skal være mindst én opgave, hvis input er output fra en anden opgave i jobbet.

## <a name="use-odata-batch-processing"></a>Brug af OData batchbehandling 

I følgende eksempel viser, hvordan du bruger OData batchbehandling til at oprette et job og opgaver. Finde oplysninger om batchbehandling, se [Open Data Protocol (OData) batchbehandling](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).
 
    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net
    
    
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## <a name="create-a-job-using-a-jobtemplate"></a>Oprette et Job fra en JobTemplate


Når behandling af flere aktiver ved hjælp af en fælles gruppe opgaver, er JobTemplates nyttige til at angive opgave forudindstillingerne, rækkefølgen af opgaverne, og så videre.

I følgende eksempel viser, hvordan du opretter en JobTemplate med en TaskTemplate, der er defineret i selve teksten. TaskTemplate bruger Media Encoder Standard som MediaProcessor til at kode filen aktiv; andre MediaProcessors kan dog blive brugt samt. 


    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]I modsætning til andre enheder Media Services, skal du definere et nyt GUID-id for hver TaskTemplate og placere det i taskTemplateId og id-egenskaben i din anmodningsteksten. Indhold identifikation farveskema skal følge det farveskema, der er beskrevet i identificere Azure Media Services enheder. Desuden kan JobTemplates ikke opdateres. I stedet skal du oprette en ny med ændringerne opdateret.
 

Hvis det lykkes, returneres følgende svaret:
    
    HTTP/1.1 201 Created
    
    . . .


I følgende eksempel viser, hvordan du opretter et Job, der refererer til et JobTemplate-Id:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
     

Hvis det lykkes, returneres følgende svaret:
    
    HTTP/1.1 201 Created
    
    . . . 



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Næste trin
Nu hvor du ved, hvordan du opretter et job for at kode en assset, gå til emnet [Hvordan til at kontrollere jobstatus fremskridt med Media Services](media-services-rest-check-job-progress.md) .


##<a name="see-also"></a>Se også

[Få medier processorer](media-services-rest-get-media-processor.md)

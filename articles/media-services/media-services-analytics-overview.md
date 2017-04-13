<properties
    pageTitle="Azure Media Services Analytics oversigt | Microsoft Azure"
    description="Azure Media Services indeholder prøveversionen af Azure Media Analytics, en samling af tale og computer syn-tjenester på enterprise skala, overholdelse af regler, sikkerhed og global rækkevidde. Azure Media Analytics services er oprettet ved hjælp af core Azure Media Services platformskomponenter og dermed er klar til at håndtere medier behandling skaleres til brug på én dag. "
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/24/2016"   
    ms.author="milanga;juliako;johndeu"/>

# <a name="azure-media-services-analytics-overview"></a>Azure Media Services Analytics oversigt

##<a name="overview"></a>Oversigt

Flere organisationer og virksomheder understøtte video som foretrukne mediet uddanne deres medarbejdere, skal du deltage deres kunder og dokument business funktioner. Cloud computing gør det effektivt at gemme, streame og få adgang til disse store mediefiler, men som firmaer vokser deres video indholdsbibliotek, de skal have et lige effektivt middel til og ny viden fra video for at oprette mere sigende tilpasset interaktion med deres målgrupper og tage deres virksomhed til et højere niveau.

Du kan løse dette voksende behov på markedet ved indeholder Azure Media Services medier Analytics, en samling af tale og syn komponenter (i enterprise-skalering, overholdelse af regler, sikkerhed og global rækkevidde), som gør det nemmere for organisationer og virksomheder at udlede handlings indsigt fra deres videofiler. Azure Media Analytics services er oprettet ved hjælp af core Azure Media Services platformskomponenter og dermed er klar til at håndtere medier behandling skaleres til brug på én dag.

Azure Media Analytics aktivere udviklere til hurtigt at komme i gang med syn-funktioner til video med begrænset omfang og sætter det avancerede funktioner til programmer. Azure Media Analytics er udviklet til at blive brugt af enterprise miljøer med fuld skala, overholdelse af regler, sikkerhed og global rækkevidde, der kræves af store virksomheder.

I det følgende diagram viser **Media analyser** og andre vigtigste dele af Media Services-platform. 

![VoD arbejdsproces](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

Medier Analytics medier processorer landbrugsprodukter MP4 filer eller JSON-filer. Hvis en medier processor produceret en MP4-fil, kan du gradvist hente filen. Hvis en medier processor produceret en JSON-fil, kan du hente filen fra Azure blob-lager. 

## <a name="azure-media-analytics-services"></a>Azure Media Analytics-tjenester

- **Indekseringsprogram** – Azure Media indekseringsprogram gør det muligt at finde indhold søgbare samt generere lukket undertekster numre. Azure Media Services udgivet **Azure Media indekseringsprogram 2 Preview** med hurtigere indeksering og mere omfattende understøttelse af sprog. Understøttede sprog omfatter engelsk, spansk, fransk, tysk, italiensk, kinesisk, portugisisk og arabisk. Detaljerede oplysninger og eksempler under [processen videoer med Azure Media indekseringsprogram 2](media-services-process-content-with-indexer2.md)
 
- **Hyperlapse** – Microsoft Hyperlapse er et resultat af over 20 års computer syn opslag på Microsoft Research (MSR), kombinerer video konstante og tid lapsing for at oprette hurtig, forbrugsvarer, flotte videoer fra formularen lang indhold. Udover at oprette tid udløber, kan du også bruge Hyperlapse til at oprette stabil videoer fra shaky videoer, der hentes via mobiltelefoner og videokameraer. Detaljerede oplysninger og eksempler under [Hyperlapse mediefiler med Azure Media Hyperlapse](media-services-hyperlapse-content.md)
 
- **Bevægelsessti registrering** – du kan bruge denne tjeneste til at registrere bevægelse i en video med brevpapir baggrunde. Dette er ideelt til kunder, der skal kontrolleres for forkerte forekomster bevægelsessti hændelser, der er fundet af overvågning kameraer på overvågning video feeds. Detaljerede oplysninger og eksempler under [Bevægelsessti registrering for Azure Media analyser](media-services-motion-detection.md).
 
- **Ansigt registrering og ansigt følelser** – via denne tjeneste, kan du registrere personers flader og deres følelser, herunder lykke, sadness, overraskelse, anger, contempt, frygt, foragt og ligegyldighed/neutrale. Dette har flere nyttige branche programmer, beskrevet nedenfor, herunder sammenlægning og analysere ved af personer, der deltager i en begivenhed. Yderligere oplysninger og eksempler i [ansigt og følelse registrering for Azure Media analyser](media-services-face-and-emotion-detection.md).
 
- **Video opsummering** – Video opsummering kan hjælpe dig med at oprette oversigter over lange videoer ved automatisk at vælge interessante kodestykker fra kilde videoen. Dette er nyttigt, når du vil give et hurtigt overblik over, hvad du kan forvente i en lang video. Detaljerede oplysninger og eksempler under [Brug Azure Media Video miniaturer til at oprette en Video opsummering](media-services-video-summarization.md)

- **Optisk tegngenkendelse** - Azure Media Analytics OCR (optisk tegngenkendelse) gør det muligt at konvertere tekst-indhold i videofiler til kan redigeres, søgbare digitale tekst. Dette kan du automatisere udtrækning af sigende metadata fra det video signal dine medier.
 
- **SVG ansigt redigering** - **Azure Media Redactor** er en Azure Media Analytics MP, der tilbyder SVG ansigt redigering i skyen. Ansigt redigering gør det muligt at ændre din video for at kunne sløring flader udvalgte personer. Du overveje at bruge tjenesten ansigt redigering i offentlige sikkerhed og nyheder medier scenarier. Et par minutter af optagelser, der indeholder flere flader kan tage timer at Rediger manuelt, men med denne tjeneste ansigt redigering processen kræver blot nogle få enkle trin. Se [denne](media-services-face-redaction.md) artikel kan finde flere oplysninger.

 
## <a name="common-scenarios"></a>Almindelige scenarier

Er nedenfor nogle scenarier, hvor Azure Media Analytics kan hjælpe organisationer og virksomheder på tværs af virksomheder glean ny viden fra video for at oprette mere personlig målgruppe og medarbejder aftaler samt mere effektivt administrere stor mængde videoindhold:

- **Opkald Centrerer** – lige med fremkomsten af sociale medier, kundeopkald centrum lette stadig en stor procentdel af tjenesten kundetransaktionerne. Kodet i disse lyd data er et væld af oplysninger om kunder, der kan analysere for at forbedre produktet vejvisere og også uddannelse af call center medarbejdere at opnå større kundetilfredshed. Via Azure Media indekseringsprogram kan kunder udtrække tekst og opbygge et søgeindeks og dashboards til at uddrage intelligence omkring mest almindelige klager, kilde klager og andre sådanne relevante data.

- **Bruger genererede beskeden om redigering af indhold** – fra nyheder medier udtag til politi afdelinger, mange organisationer har offentlige modstående portaler, hvor de accepterer UGC medier, som videoer og billeder. Mængde indhold kan forøgelse på grund af uventede begivenheder. I disse scenarier, er det nær umuligt Sådan udfører du en effektiv manuel anmeldelse af indhold til hensigtsmæssige. Kunder kan stole på tjenesten redigering af indhold til at fokusere på det indhold, der er relevant.

- **Overvågning** - med væksten af IP-kameraer, er der en cirkeladskillelse af overvågning videoer. Manuel gennemgang overvågning video er tid intensivt og giver risiko for mennesker fejl. Azure Media Analytics indeholder flere komponenter som bevægelsessti registrering, ansigt registrering og Hyperlapse at gøre processen med at gennemse, administrere og nemmere at oprette afledte produkter.

## <a name="media-services-analytics-media-processors"></a>Media Services Analytics medier processorer 

I dette afsnit vises alle Media Services Analytics medier processorer (MP) og viser hvordan bruge .NET eller RESTEN for at få et MP objekt.

### <a name="mp-names"></a>MP navne


- Azure Media indekseringsprogram 2 Preview
- Azure Media indekseringsprogram
- Azure Media Hyperlapse
- Azure Media ansigt registrering ressourcer
- Azure Media Bevægelsesdetektor
- Azure Media Video miniaturer
- Azure Media OCR

### <a name="net"></a>.NET

Følgende funktion har en af de angivne MP navne og returnere en MP objekt.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


## <a name="rest"></a>RESTEN

Anmode om:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net
    
Svar:
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

##<a name="demos"></a>Demoer

[Azure Media Analytics demoer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

##<a name="next-steps"></a>Næste trin

Gennemse Media Services læringsstier.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-articles"></a>Relaterede artikler

[Media Services Analytics meddelelse](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)
  

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

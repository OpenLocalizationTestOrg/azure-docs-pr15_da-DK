<properties 
    pageTitle="Sådan logger du begivenheder til Azure begivenhed hubber i Azure API Management | Microsoft Azure" 
    description="Lær at logføre hændelser til Azure begivenhed hubber i Azure API Management." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Sådan logger du begivenheder til Azure begivenhed hubber i Azure API Management

Azure begivenhed Hubs er en meget SVG data vandindtrængen tjeneste, der kan indtager millioner af begivenheder sekundet, så du kan behandle og analysere store mængder data, der er oprettet med din forbundne enheder og programmer. Begivenhed Hubs fungerer som "forside dør" for en begivenhed pipeline, og når data, der indsamles i en begivenhed-hub, det kan være transformeret og gemt ved hjælp af enhver realtid analytics-udbyder eller samling/lagerplads kort. Begivenhed Hubs decouples fremstilling af en strøm af begivenheder fra forbrug af hændelserne, så begivenhed forbrugere kan få adgang til hændelserne på deres egne tidsplan.

I denne artikel er en companion til [Integrere Azure API Management med begivenhed Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) videoen og beskriver, hvordan du logger API Management hændelser ved hjælp af Azure begivenhed Hubs.

## <a name="create-an-azure-event-hub"></a>Oprette en Azure begivenhed Hub

Hvis du vil oprette en ny begivenhed-Hub, logon til [Azure klassisk portal](https://manage.windowsazure.com) og klikke på **Ny**->**App Services**->**Service Bus**->**Begivenhed Hub**->**Hurtig oprettelse**. Angiv navnet på en begivenhed-Hub, region, Vælg et abonnement, og vælg et navneområde. Hvis du ikke tidligere har oprettet et navneområde, kan du oprette én ved at skrive et navn i tekstfeltet **Namespace** . Når alle egenskaber, der er konfigureret, kan du klikke på **Opret en ny begivenhed-Hub** for at oprette begivenhed hubben.

![Oprette begivenhed hub][create-event-hub]

Derefter gå til fanen **Konfigurer** for din nye begivenhed-Hub, og oprette to **politikker for delt adgang**. Navngive sektionen først én **afsendelse** og giver den **Send** -tilladelser.

![Afsendelse af politik][sending-policy]

Navngiv det andet én **modtager**, give den **lytter** tilladelser, og klik på **Gem**.

![Modtager politik][receiving-policy]

Hver delt adgangspolitik giver programmer til at sende og modtage hændelser til og fra begivenhed hubben. Gå til fanen **Dashboard** i hubben begivenhed for at få adgang til de forbindelsesstrenge for disse politikker, og klikke på **oplysninger om forbindelsen**.

![Forbindelsesstreng][event-hub-dashboard]

Forbindelsesstrengen **sender** bruges, når logføring af hændelser, og forbindelsesstrengen **modtager** bruges, når hente begivenheder fra begivenhed hubben.

![Forbindelsesstreng][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Oprette en API Management logføring

Nu hvor du har en begivenhed-Hub, er næste trin at konfigurere en [logføring](https://msdn.microsoft.com/library/azure/mt592020.aspx) i din API Management-tjenesten, så den kan logge begivenheder til begivenhed-Hub.

API Management loggers er konfigureret til brug af [API Management REST-API](http://aka.ms/smapi). Før du bruger REST-API for første gang, gennemse [forudsætninger](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites) og sikre, at du har [aktiveret adgang til REST-API](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI).

Kontrollér HTTP PLACERE en anmodning om en ved hjælp af skabelonen følgende URL-adresse for at oprette en logføring.

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-   Erstatte `{your service}` med navnet på din API Management service forekomst.
-   Erstatte `{new logger name}` med det ønskede navn til din nye logføring. Du vil henvise dette navn, når du konfigurerer politikken [log-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)

Tilføj følgende overskrifterne til anmodningen.

-   Indholdstype: programmet/json
-   Godkendelse: SharedAccessSignature uid =...
    -   Yderligere oplysninger om oprettelse af den `SharedAccessSignature` se [Azure API Management RESTEN API godkendelse](https://msdn.microsoft.com/library/azure/dn798668.aspx).

Angiv anmodningsteksten ved hjælp af følgende skabelon.

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-   `type`være indstillet til `AzureEventHub`.
-   `description`indeholder en valgfri beskrivelse af logføring af og kan være en streng med længden nul, hvis du ønsker.
-   `credentials`indeholder den `name` og `connectionString` af din Azure begivenhed Hub.

Når du har foretaget anmodningen, hvis logføring af oprettes en statuskode af `201 Created` returneres. 

>[AZURE.NOTE] Se [oprette en logføring](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT)til andre mulige returnerede koder og deres årsager. Se hvordan udføre andre handlinger, som liste, Opdater og Slet, i dokumentationen til [logføring](https://msdn.microsoft.com/library/azure/mt592020.aspx) enhed.

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurere politikker for log-eventhubs

Når din logføring er konfigureret i administration af API, kan du konfigurere din log-eventhubs politikker for at logge på ønskede begivenheder. Politikken log-eventhubs kan bruges i sektionen indgående politik eller afsnittet udgående politik.

For at konfigurere politikker logon på [Azure klassisk portal](https://manage.windowsazure.com), gå din API Management-tjenesten, og klik på **publisher-portalen** eller **Administrer** for at få adgang til portalen publisher.

![Publisher-portalen][publisher-portal]

Klik på **politikker** i menuen API administration i venstre side, Vælg den ønskede produkt og API og klik på **Tilføj politik**. I dette eksempel tilføjer vi en politik til **Ekkoet API** i **ubegrænset** produktet.

![Tilføje politik][add-policy]

Placer markøren i den `inbound` politik sektion, og klik på **logfil til EventHub** politikken til at indsætte den `log-to-eventhub` politikskabelon-sætning.

![Politik editor][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

Erstatte `logger-id` med navnet på logføring af API administration, som du har konfigureret i det forrige trin.

Du kan bruge et udtryk, der returnerer en streng som værdien for den `log-to-eventhub` element. I dette eksempel logføres en streng, der indeholder den dato og klokkeslæt, navnet på tjenesten, anmodnings-id, anmodning om IP-adresse og navn til handlingen.

Klik på **Gem** for at gemme den opdaterede politik konfiguration. Så snart den er gemt politikken er aktiv, og hændelser er logget udpegede begivenhed hubben.

## <a name="next-steps"></a>Næste trin

-   Lær mere om Azure begivenhed Hubs
    -   [Introduktion til Azure begivenhed Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Modtage meddelelser med EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Begivenhed Hubs programming vejledning](../event-hubs/event-hubs-programming-guide.md)
-   Få flere oplysninger om administration af API og begivenhed Hubs integration
    -   [Logføring enhedsreference](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [Log-eventhub politikreference](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    -   [Overvåge dit API'er med Azure API Management, begivenhed Hubs og Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Se en video gennemgang

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png







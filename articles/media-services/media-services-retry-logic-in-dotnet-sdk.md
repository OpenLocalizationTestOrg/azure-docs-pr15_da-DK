<properties
    pageTitle="Retry-logik i Media Services SDK til .NET | Microsoft Azure"
    description="Emnet giver et overblik over forsøg i Media Services SDK til .NET."
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


# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Retry-logik i Media Services SDK til .NET

Midlertidige fejl kan opstå, når du arbejder med Microsoft Azure-tjenester. Hvis en midlertidig fejl opstår, i de fleste tilfælde, efter et par Forsøg handlingen blev fuldført. Media Services SDK til .NET implementerer forsøg at håndtere midlertidige fejl, der er knyttet til undtagelser og fejl, der er forårsaget af webanmodninger udførelse af forespørgsler, gemme ændringer og lagerplads handlinger.  Som standard udfører Media Services SDK til .NET fire forsøg før den igen udløser undtagelse i dit program. Koden i dit program skal derefter håndtere denne undtagelse korrekt.  
  
 Følgende er en kort vejledning med anmode om Web, lagring, forespørgsel og SaveChanges politikker:  
  
-   Politikken lagerplads bruges til blob storage handlinger (overførsler eller download af aktiv filer).  
  
-   Politikken Web anmode om bruges til anmodninger om generisk web (for eksempel til at finde et godkendelse token og løse brugere klynge slutpunktet).  
  
-   Politikken forespørgsel bruges til at forespørge enheder fra RESTEN (for eksempel mediaContext.Assets.Where(...)).  
  
-   Politikken SaveChanges bruges til at gøre noget, der ændrer data i tjenesten (for eksempel, oprette et objekt ved at opdatere en enhed, ringe til en tjeneste funktion for en handling).  
  
 Dette emne viser undtagelse typer og fejlkoder, der håndteres af Media Services SDK til .NET retry-logik.  
  
## <a name="exception-types"></a>Typer af undtagelse  

I følgende tabel beskrives undtagelser, Media Services SDK til .NET håndterer eller håndterer ikke for nogle handlinger, der kan medføre midlertidige fejl.  
  
Undtagelse|Webforespørgsel|Lagerplads|Forespørgsel|SaveChanges
----|------|----|---|---
WebException<br/>Yderligere oplysninger finder du i afsnittet [WebException status-koder](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) .|Ja|Ja|Ja|Ja  
DataServiceClientException<br/> Du kan finde yderligere oplysninger finder [HTTP fejlkoder status](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Nej|Ja|Ja|Ja
DataServiceQueryException<br/> Du kan finde yderligere oplysninger finder [HTTP fejlkoder status](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Nej|Ja|Ja|Ja  
DataServiceRequestException<br/> Du kan finde yderligere oplysninger finder [HTTP fejlkoder status](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Nej|Ja|Ja|Ja  
DataServiceTransportException|Nej|Nej|Ja|Ja
TimeoutException|Ja|Ja|Ja|Nej
SocketException|Ja|Ja|Ja|Ja  
StorageException|Nej|Ja|Nej|Nej 
IOException|Nej|Ja|Nej|Nej
  
###  <a name="WebExceptionStatus"></a>WebException status-koder  

Tabellen nedenfor viser, hvilke WebException fejlkoder forsøg gennemføres. [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) optælling definerer statuskoderne.  
  
Status|Webforespørgsel|Lagerplads|Forespørgsel|SaveChanges  
-----|-----------------|-------------|-----------|----------  
ConnectFailure|Ja|Ja|Ja|Ja
NameResolutionFailure|Ja|Ja|Ja|Ja  
ProxyNameResolutionFailure|Ja|Ja|Ja|Ja  
SendFailure|Ja|Ja|Ja|Ja
PipelineFailure|Ja|Ja|Ja|Nej  
ConnectionClosed|Ja|Ja|Ja|Nej  
KeepAliveFailure|Ja|Ja|Ja|Nej  
UnknownError|Ja|Ja|Ja|Nej 
ReceiveFailure|Ja|Ja|Ja|Nej  
RequestCanceled|Ja|Ja|Ja|Nej  
Timeout|Ja|Ja|Ja|Nej
En protokolfejl <br/>Gentagne forsøg for en protokolfejl styres af HTTP status kode håndtering. Du kan finde yderligere oplysninger finder [HTTP fejlkoder status](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Ja|Ja|Ja|Ja|  
  
###  <a name="HTTPStatusCode"></a>HTTP status fejlkoder  

Når forespørgslen og SaveChanges handlinger Udløs DataServiceClientException, DataServiceQueryException eller DataServiceQueryException, returneres HTTP Statusfejlkoden i egenskaben statuskode.  Tabellen nedenfor viser, hvilke fejlkoder forsøg gennemføres.  
  
 
Status|Webforespørgsel|Lagerplads|Forespørgsel|SaveChanges 
---|----|----|----|----
401|Nej|Ja|Nej|Nej
403|Nej|Ja<br/>Håndtering af forsøg med længere venter.|Nej|Nej  
408|Ja|Ja|Ja|Ja
429|Ja|Ja|Ja|Ja  
500|Ja|Ja|Ja|Nej  
502|Ja|Ja|Ja|Nej  
503|Ja|Ja|Ja|Ja  
504|Ja|Ja|Ja|Nej  
  
Hvis du vil tage et kig på faktiske implementeringen af Media Services SDK til .NET retry-logik, skal du se [azure-sdk-til-media-tjenester](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

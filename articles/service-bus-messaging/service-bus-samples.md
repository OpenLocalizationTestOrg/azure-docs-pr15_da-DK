<properties 
    pageTitle="Service Bus messaging eksempler oversigt | Microsoft Azure"
    description="Kategoriserer og beskriver Service Bus messaging eksempler med links til hver."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/07/2016"
    ms.author="sethm" />

# <a name="service-bus-messaging-samples"></a>Service Bus SMS eksempler

Service Bus SMS Eksemplerne viser de vigtigste funktioner i [Service Bus messaging](https://azure.microsoft.com/services/service-bus/) (skytjeneste) og [Service Bus til Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). I denne artikel kategoriserer og beskriver de eksempler, der er tilgængelige med links til hver.

>[AZURE.NOTE] Service Bus eksempler er ikke installeret med SDK. For at hente eksemplerne, skal du besøge [Azure SDK eksempelsiden](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>Desuden er der en opdateret sæt Service Bus messaging eksempler [her](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (og skrive, de er ikke beskrevet i denne artikel).  

Relay eksempler, skal du se [Service Bus relay eksempler](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Service Bus messaging

I følgende eksempler viser, hvordan du kan skrive programmer, der bruger Service Bus messaging.

Bemærk, at SMS eksemplerne kræver en forbindelsesstreng for at få adgang til din Service Bus navneområde.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Du får en forbindelsesstreng til Azure Service Bus

1. Log på [Azure-portalen](http://portal.azure.com).

1. Klik på **Service Bus**i venstre kolonne.

1. Klik på navnet på din navneområde på listen.

3. Klik på **delt access politikker**i bladet navneområde.

4. Klik på **RootManageSharedAccessKey**i bladet **delt access politikker** .

6. Kopiere forbindelsesstrengen til Udklipsholder.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Du får en forbindelsesstreng til tjenesten Bus til Windows Server

1. Kør følgende PowerShell-cmdlet:

    ```
    get-sbClientConfiguration
    ```

2. Indsæt forbindelsesstrengen i App.config-filen til eksemplet.

### <a name="getting-started-samples"></a>Få Introduktion eksempler

Disse eksempler beskriver grundlæggende SMS funktionalitet.

|Eksempel navn|Beskrivelse|Mindste SDK Version|Tilgængelighed|
|---|---|---|---|
|[Introduktion: Messaging med køer](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Viser, hvordan du bruger Microsoft Azure Service Bus til at sende og modtage meddelelser fra en kø.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Introduktion: Messaging med emner](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|Viser, hvordan du bruger Microsoft Azure Service Bus til at sende og modtage meddelelser fra et emne med flere abonnementer.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|

### <a name="exploring-features"></a>Udforskning af funktioner

I følgende eksempler viser forskellige funktioner i Service Bus.

|Eksempel navn|Beskrivelse|Mindste SDK Version|Tilgængelighed|
|---|---|---|---|
|[HTTP-Token udbydere](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|Viser de forskellige måder for at godkende en HTTP/hvile klient med Service Bus.|2.1|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Tjenesten Bus HTTP-klient](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|Viser, hvordan du sender meddelelser til og modtage meddelelser fra Service Bus via HTTP/RESTEN.|2.3|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Tjenesten Bus Autoforwarding](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|Viser, hvordan for automatisk at videresende meddelelser fra en kø, abonnement eller inaktive kø til en anden kø eller emne. Det viser også, hvordan du kan sende en meddelelse til en kø eller emne via en overførsel kø.|2.3|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: WCF kanal Session eksempel](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Viser, hvordan du bruger Microsoft Azure Service Bus ved hjælp af Windows Communication Foundation (WCF) kanaler. Eksemplet viser brugen af WCF kanaler til at sende og modtage meddelelser via en Service Bus kø. Eksemplet viser både session og ikke-session kommunikation over Service Bus.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: transaktioner](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|Viser, hvordan du bruger den Microsoft Azure Service Bus messaging funktioner i et transaktion omfang for at kontrollere, at batches med messaging handlinger er anvendt atomically.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: Administration af handlinger ved hjælp af REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|Viser, hvordan du udfører handlinger i administration på Service Bus ved hjælp af REST.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Ressource udbyder REST API'er](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|Viser, hvordan du bruger de nye Service Bus RDFE REST API'er til at administrere navneområder og SMS enheder.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: WCF-tjenesten Session eksempel](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|Viser, hvordan du bruger Microsoft Azure Service Bus ved hjælp af WCF-tjenesten modellen. Eksemplet viser brugen af WCF-tjenesten modellen til at udføre session-baseret kommunikation via en Service Bus kø.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: Anmodning om svar](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Viser, hvordan du bruger Microsoft Azure Service Bus og funktionerne for anmodning/svar. Eksemplet viser simple klienter og servere kommunikere via en Service Bus kø.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: inaktive bogstav kø](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Viser, hvordan du bruger Microsoft Azure Service Bus og funktionen SMS "blind bogstav kø". Eksemplet viser en simpel afsenderen og modtageren kommunikere via en Service Bus kø.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: Udskudt meddelelser](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Viser, hvordan du bruger funktionen meddelelse udelukkelse af Microsoft Azure Service Bus. Eksemplet viser en simpel afsenderen og modtageren kommunikere via en Service Bus kø.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: Session meddelelser](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Viser, hvordan du bruger Microsoft Azure Service Bus og funktionerne for Messaging Session. Eksemplet viser simple afsendere og modtagere kommunikere via en Service Bus kø.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: Anmodning om svar emne](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|Viser, hvordan at implementere mønstret anmodning/svar ved hjælp af Microsoft Azure Service Bus emner og abonnementer. Eksemplet viser simple klienter og servere kommunikere via et Service Bus emne.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: Anmodning om svarkø](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Viser, hvordan du bruger Microsoft Azure Service Bus og funktionerne for anmodning/svar. Eksemplet viser simple klienter og servere kommunikere via to Service Bus køer.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: Dubletter](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|Viser, hvordan du bruger Microsoft Azure Service Bus dublerede meddelelse registrering med køer. Det opretter to køer, hvoraf den ene bruger aktiveret registrering af dubletter og andre én uden dubletter.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: Asynkron Messaging](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Viser, hvordan du bruger Microsoft Azure Service Bus til at sende og modtage meddelelser asynkront fra en kø. Køen indeholder Afkoblet, asynkron kommunikation mellem en afsender og en hvilken som helst antal modtagere (her en enkelt modtager).|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: Avancerede filtre](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Viser, hvordan du bruger Microsoft Azure Service Bus Udgiv/Abonner avancerede filtre. Det opretter et emne og 3 abonnementer med forskellige filterdefinitioner, sender meddelelser til emnet eller modtager alle meddelelser fra abonnementer.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Formidlet Messaging: Meddelelser Prefetch](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Viser, hvordan du bruger funktionen Microsoft Azure Service Bus meddelelser prefetch. Det viser, hvordan du bruger funktionen meddelelser prefetch på Modtag.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|

## <a name="service-bus-reference-tools"></a>Service Bus referenceværktøjer

I følgende eksempler viser forskellige andre funktioner til tjenesten.

|Eksempel navn|Beskrivelse|Mindste SDK Version|Tilgængelighed|
|---|---|---|---|
|[Tjenesten Bus Explorer](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|Tjenesten Bus Explorer kan brugerne oprette forbindelse til et Service Bus service navneområde og administrere SMS objekter på en nem måde. Værktøjet indeholder avancerede funktioner som Importér/Eksportér funktionalitet og muligheden for at teste SMS enheder og relay-tjenester.|1,8|Microsoft Azure Service Bus; Service Bus til Windows Server|
|[Godkendelse: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|Dette eksempel demonstrerer at oprette og administrere tjenesteidentiteter i Microsoft Azure Active Directory adgangskontrol (også kendt som Access Control Service eller ACS) til brug sammen med tjenesten Bus.|I/T.|Microsoft Azure Service Bus|

## <a name="next-steps"></a>Næste trin

Se følgende artikler for begrebsmæssig oversigt over Service Bus.

- [Service Bus SMS oversigt](service-bus-messaging-overview.md)
- [Service Bus arkitektur](service-bus-architecture.md)
- [Service Bus grundlæggende](service-bus-fundamentals-hybrid-solutions.md)

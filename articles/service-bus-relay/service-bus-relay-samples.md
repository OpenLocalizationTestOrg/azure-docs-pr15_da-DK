<properties 
    pageTitle="Service Bus relay eksempler oversigt | Microsoft Azure"
    description="Kategoriserer og beskriver Service Bus relay eksempler med links til hver."
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

# <a name="service-bus-relay-samples"></a>Service Bus relay eksempler

Service Bus relay Eksemplerne viser de vigtigste funktioner i [Service Bus relay](https://azure.microsoft.com/services/service-bus/). I denne artikel kategoriserer og beskriver de eksempler, der er tilgængelige med links til hver.

>[AZURE.NOTE] Service Bus eksempler er ikke installeret med SDK. For at hente eksemplerne, skal du besøge [Azure SDK eksempelsiden](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>Desuden er der en opdateret sæt Service Bus relay eksempler [her](https://github.com/Azure-Samples/azure-servicebus-relay-samples) (og skrive, de er ikke beskrevet i denne artikel).  

SMS eksempler, skal du se [Service Bus messaging eksempler](../service-bus-messaging/service-bus-samples.md).

## <a name="service-bus-relay"></a>Service Bus relay

I følgende eksempler viser, hvordan du kan skrive programmer, der bruger Service Bus relay-tjenesten.

Bemærk, at relay eksemplerne kræver en forbindelsesstreng for at få adgang til din Service Bus navneområde.

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

## <a name="service-bus-relay"></a>Service Bus relay

Eksempler, der viser Service Bus relay.

### <a name="getting-started"></a>Kom godt i gang

|Eksempel navn|Beskrivelse|Mindste SDK Version|Tilgængelighed|
|---|---|---|---|
|[Videresendt Messaging: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Viser, hvordan du kører en Service Bus klient og tjeneste på Azure. Dette eksempel konfigurerer Service Bus fra et program. Kun miljø og sikkerhed oplysninger gemmes i dialogboksen konfigurationsfiler.|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS godkendelse: Delt hemmeligt](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|Viser, hvordan du bruger en udstedernavn og udsteder hemmeligt til at godkende med Service Bus.|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS godkendelse: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|Viser, hvordan du få vist en HTTP-webtjeneste, der ikke kræver klienten brugergodkendelse.|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS bindinger: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Viser, hvordan du bruger **WebHttpRelayBinding** binding til at returnere binære data ved hjælp af internettet programming model.|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS bindinger: NetTcp videresendt](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|Viser, hvordan du bruger **NetTcpRelayBinding** binding.|1,8|Microsoft Azure Service Bus|

### <a name="exploring-features"></a>Udforskning af funktioner

Eksempler, der viser forskellige Service Bus relay-funktioner.

|Eksempel navn|Beskrivelse|Mindste SDK Version|Tilgængelighed|
|---|---|---|---|
|[Videresendt SMS godkendelse: Enkel WebToken](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|Viser, hvordan du bruger en simpel web token legitimationsoplysninger til at godkende med Service Bus. Eksemplet ligner eksemplet ekkoet med nogle få ændringer. Nærmere betegnet kan tilføjes i dette eksempel funktioner i ChannelFactory (klienten) programmerne og ServiceHost (service).|1,8|Microsoft Azure Service Bus|
|[Videresendt Messaging: Belastning](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Viser, hvordan du bruger Microsoft Azure Service Bus til at dirigere meddelelser til flere modtagere. Der vises flere forekomster af en simpel tjeneste kommunikere med en klient via **NetTcpRelayBinding** binding|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS bindinger: Net begivenhed](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Demonstrerer brugen af **NetEventRelayBinding** binding på Microsoft Azure Service Bus.|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS bindinger: WS2007Http Session](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|Demonstrerer brugen af **WS2007HttpRelayBinding** binding med pålidelige sessioner, der er aktiveret. Det viser også at angive Service Bus legitimationsoplysninger i konfigurationsfil i stedet for fra et program.|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS bindinger: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|Viser, hvordan du bruger **WS2007HttpRelayBinding** binding med meddelelsen sikkerhed til at sikre til slut meddelelser, mens du stadig kræve klienter mulighed for at godkende med Service Bus.|1,8|Microsoft Azure Service Bus|
|[Videresendt Messaging: Udveksling af Metadata](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|Viser, hvordan du få vist et slutpunkt for metadata, der bruger relay binding. **MetadataExchange** understøttes i de følgende relay bindinger: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding**og **WS2007HttpRelayBinding**.|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS bindinger: NetTcp direkte](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|Viser, hvordan du konfigurerer **NetTcpRelayBinding** binding til at understøtte forbindelsestilstanden **Hybrid** som først opretter en relayed forbindelse, og hvis det er muligt, skifter automatisk til en direkte forbindelse mellem en klient og en tjeneste.|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS bindinger: NetTcp MsgSec brugernavn](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|Viser, hvordan du bruger **NetTcpRelayBinding** binding med meddelelsen sikkerhed.|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS bindinger: Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|Viser, hvordan du få vist og bruge en Tjenesteslutpunktet, ved hjælp af **NetOnewayRelayBinding** binding.|1,8|Microsoft Azure Service Bus|
|[Videresendt SMS bindinger: WS2007Http enkle](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|Demonstrerer brugen af **WS2007HttpRelayBinding** binding. Det viser en enkelt tjeneste, der bruges ingen sikkerhedsindstillinger og kræver ikke klienter mulighed for at godkende.|1,8|Microsoft Azure Service Bus|

## <a name="next-steps"></a>Næste trin

Se følgende artikler for begrebsmæssig oversigt over Service Bus.

- [Oversigt over Service Bus relay](service-bus-relay-overview.md)
- [Service Bus arkitektur](../service-bus-messaging/service-bus-architecture.md)
- [Service Bus grundlæggende](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
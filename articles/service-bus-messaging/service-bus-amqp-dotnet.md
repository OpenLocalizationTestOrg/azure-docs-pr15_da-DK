<properties 
    pageTitle="Service Bus med .NET og AMQP 1.0 | Microsoft Azure"
    description="Brug af Service Bus fra .NET med AMQP"
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-net-with-amqp-10"></a>Brug af Service Bus fra .NET med AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>Hente Service Bus SDK

Understøttelse af AMQP 1.0 er tilgængelig i Service Bus SDK version 2.1 eller nyere. Du kan sikre, at du har den nyeste version ved at hente Service Bus bit fra [NuGet][].

## <a name="configuring-net-applications-to-use-amqp-10"></a>Konfigurere .NET-programmer til at bruge AMQP 1.0

Som standard kommunikerer biblioteket Service Bus .NET klienten med Service Bus tjenesten ved hjælp af en dedikeret SOAP-baseret protokol. Hvis du vil bruge AMQP 1.0 i stedet for standard kræver protocol eksplicitte konfiguration på forbindelsesstreng Service Bus, som beskrevet i næste afsnit. End denne ændring er programkode forbliver uændret, når du bruger AMQP 1.0.

I den aktuelle udgave er der et par API-funktioner, der ikke understøttes, når du bruger AMQP. Disse ikke-understøttede funktioner vises senere i sektionen [ikke-understøttede funktioner, begrænsninger, og funktionsmæssige forskelle](#unsupported-features-restrictions-and-behavioral-differences). Nogle af de avancerede konfigurationsindstillinger også have en anden betydning, når du bruger AMQP.

### <a name="configuration-using-appconfig"></a>Konfiguration med App.config

Det er god praksis til programmer for at bruge filen App.config konfiguration til at gemme indstillingerne. Du kan bruge App.config for Service Bus programmer til at gemme indstillingerne for værdien Service Bus **ConnectionString** . Et eksempel App.config-filen er som følger:

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
        </appSettings>
    </configuration>

Værdien af den `Microsoft.ServiceBus.ConnectionString` indstilling er forbindelsesstrengen Service Bus, der bruges til at konfigurere forbindelsen til Service Bus. Formatet er som følger:

    Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Hvor `[namespace]` og `SharedAccessKey` hentes fra [Azure-portalen][]. Se [Introduktion til Service Bus køer][]kan finde flere oplysninger.

Når du bruger AMQP, tilføje forbindelsesstrengen med `;TransportType=Amqp`. Denne notation oplyser biblioteket klienten på computeren for at foretage forbindelsen Service Bus bruger AMQP 1.0.

## <a name="message-serialization"></a>Meddelelse serialisering

Når du bruger standard-protokol, er serialisering standardfunktionsmåden for biblioteket .NET klienten at bruge typen [DataContractSerializer][] ordne sekventielt en forekomst af [BrokeredMessage][] til transport mellem biblioteket klienten og Service Bus-tjenesten. Når du bruger tilstanden AMQP transport, bruger biblioteket klient AMQP Typesystemet for serialisering af [formidlet meddelelse][BrokeredMessage] i en AMQP meddelelse. Denne serialisering gør det muligt for meddelelsen til modtaget og fortolket af en modtagelse program, der potentielt kører på en anden platform, for eksempel en Java-program, der bruger JMS API til at få adgang til tjenesten Bus.

Når du opbygger en forekomst af [BrokeredMessage][] , kan du angive et .NET objekt som en parameter til parametre skal fungere som brødteksten i meddelelsen. Objekter, som kan knyttes til AMQP enkle typer skal serialiseres brødteksten til AMQP datatyper. Hvis objektet ikke kan tilknyttes direkte til en AMQP basistype Det vil sige, sendes en brugerdefineret type, der er defineret af programmet, og klik derefter på objektet er serialiseres ved hjælp af [DataContractSerializer][]og de fortløbende byte i meddelelsen AMQP data.

Brug kun .NET-datatyper, der kan serialiseres direkte i AMQP typer til brødteksten i meddelelsen for at lette kompatibilitet med ikke-.NET-klienter. Den følgende tabel indeholder oplysninger om disse filtyper og de tilsvarende tilknytning til AMQP type system.

| .NET brødtekst objekttype          | Tilknyttede AMQP Type                          | AMQP brødtekst sektionstype                                                                                                                                    |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Boolesk                           | Boolesk værdi                                   | AMQP værdi                                                                                                                                                |
| byte                           | ubyte                                     | AMQP værdi                                                                                                                                                |
| Double                         | Double                                    | AMQP værdi                                                                                                                                                |
| UInt                           | UInt                                      | AMQP værdi                                                                                                                                                |
| ulong                          | ulong                                     | AMQP værdi                                                                                                                                                |
| SByte                          | byte                                      | AMQP værdi                                                                                                                                                |
| kort                          | kort                                     | AMQP værdi                                                                                                                                                |
| heltal                            | heltal                                       | AMQP værdi                                                                                                                                                |
| lang                           | lang                                      | AMQP værdi                                                                                                                                                |
| slæk                          | slæk                                     | AMQP værdi                                                                                                                                                |
| dobbelt                         | dobbelt                                    | AMQP værdi                                                                                                                                                |
| decimal                        | decimal128                                | AMQP værdi                                                                                                                                                |
| tegn                           | tegn                                      | AMQP værdi                                                                                                                                                |
| Dato og klokkeslæt                       | tidsstempel                                 | AMQP værdi                                                                                                                                                |
| GUID                           | UUID                                      | AMQP værdi                                                                                                                                                |
| byte]                         | binært tal                                    | AMQP værdi                                                                                                                                                |
| streng                         | streng                                    | AMQP værdi                                                                                                                                                |
| System.Collections.IList       | liste                                      | AMQP værdi: elementerne i gruppen af websteder kan kun være dem, der er defineret i denne tabel.                                                             |
| System.Array                   | matrix                                     | AMQP værdi: elementerne i gruppen af websteder kan kun være dem, der er defineret i denne tabel.                                                             |
| System.Collections.IDictionary | kort                                       | AMQP værdi: elementerne i gruppen af websteder kan kun være dem, der er defineret i denne tabel. Bemærk: understøttes kun streng taster.                        |
| URI                            | Beskrevet streng (se tabellen nedenfor) | AMQP værdi                                                                                                                                                |
| DateTimeOffset                 | Beskrevet længe (se tabellen nedenfor)   | AMQP værdi                                                                                                                                                |
| TimeSpan                       | Beskrevet længe (se følgende)         | AMQP værdi                                                                                                                                                |
| Værdistrøm                         | binært tal                                    | AMQP Data (kan være flere). Data sektioner indeholder de rå byte, der læses fra Stream-objektet.                                                           |
| Andet objekt                   | binært tal                                    | AMQP Data (kan være flere). Indeholder det fortløbende binær af det objekt, der bruger DataContractSerializer eller en serialiseringsfunktion, der leveres af programmet. |

| .NET type      | Tilknyttede AMQP beskrevet Type                                                                                              | Noter                   |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| URI            | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>`                       | Uri.AbsoluteUri         |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan       | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> `              | TimeSpan.Ticks          |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Ikke-understøttede funktioner, begrænsninger og funktionsmæssige forskelle

Følgende Service Bus .NET API-funktioner understøttes ikke i øjeblikket, når du bruger AMQP:

-   Transaktioner

-   Send via filoverførsel destination

Der er også nogle mindre forskelle i funktionsmåden for tjenesten Bus .NET API, når du bruger AMQP, sammenlignet med standard-protokol:

-   Egenskaben [OperationTimeout][] ignoreres.

-   `MessageReceiver.Receive(TimeSpan.Zero)`implementeres som `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

-   Fuldfør meddelelser ved at låse tokens kan kun udføres af de meddelelse modtagere, der har modtaget meddelelser.

## <a name="controlling-amqp-protocol-settings"></a>Styre AMQP protocol indstillinger

.NET-API'er få vist flere indstillinger for at styre funktionsmåden for AMQP protokollen:

-   **MessageReceiver.PrefetchCount**: styrer den indledende kredit, der er anvendt på et link. Standard er 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: kontrolelementer ramme maksimumstørrelsen AMQP tilbydes under forhandling på forbindelse åbne tid. Standard er 65.536 byte.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: Hvis overførsler er batchable, denne værdi angiver den maksimale forsinkelse for at sende dispositions. Nedarves af afsendere/modtagere som standard. Individuelle afsender/modtager kan tilsidesætte standardindstillingen, som er 20 millisekunder.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: kontrollerer, om AMQP forbindelser er etableret via en SSL-forbindelse. Standard er **Sand**.

## <a name="next-steps"></a>Næste trin

Klar til at få mere at vide? Besøg følgende links:

- [Tjenesten Bus AMQP oversigt]
- [AMQP 1.0 understøttelse af Service Bus opdelt køer og emner]
- [AMQP i Service Bus til Windows Server]

  [Introduktion til Service Bus køer]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure-portalen]: https://portal.azure.com
[Tjenesten Bus AMQP oversigt]: service-bus-amqp-overview.md
[AMQP 1.0 understøttelse af Service Bus opdelt køer og emner]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP i Service Bus til Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

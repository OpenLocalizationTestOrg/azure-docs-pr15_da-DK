<properties 
    pageTitle="Service Bus asynkron messaging | Microsoft Azure"
    description="Beskrivelse af tjenesten Bus asynkron messaging."
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
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynkron SMS mønstre og høj tilgængelighed

Asynkron messaging kan implementeres i en række forskellige måder. Med køer, emner og abonnementer understøtter Azure Service Bus asynchrony via en store og videresende ordning. I normal (synkron) handling, du sender meddelelser til køer og emner og modtage meddelelser fra køer og abonnementer. Programmer, du skriver, afhænger af disse objekter, der altid er tilgængelig. Når enhed tilstanden ændres, på grund af en række forskellige omstændigheder, skal du en måde at give en reduceret egenskab enhed, der kan opfylder de fleste behov.

Programmer bruger typisk asynkron SMS mønstre til at aktivere et antal kommunikationsscenarier. Du kan oprette programmer, hvori klienter kan sende meddelelser til tjenesterne, selv når tjenesten ikke kører. Denne indstilling kommunikation, en kø kan hjælpe oplevelse niveau for programmer afkrydsningsfeltet Indlæs ved at give et sted på bufferen kommunikation. Til sidst, kan du få enkle, men effektive belastningsjustering til at distribuere meddelelser på tværs af flere computere.

For at bevare tilgængeligheden af nogen af disse objekter skal du overveje en række forskellige måder, kan disse objekter vises ikke tilgængelig for et robust meddelelsessystem. Generelt skal se vi enheden, bliver utilgængelige til programmer vi skrive på følgende måder:

- Der kan ikke sende meddelelser.

- Ikke muligt at modtage meddelelser.

- Det er ikke muligt at administrere objekter (oprette, hente, opdatere eller slette enheder).

- Kan ikke kontaktes tjenesten.

For hver af disse fejl skal findes forskellige fejl funktionsmåder, der aktiverer et program kan fortsætte med at udføre arbejdet på nogle niveau i reduceret egenskab. For eksempel kan stadig modtage ordrer fra kunder et system, der kan sende meddelelser, men ikke modtaget dem, men kan ikke behandle disse ordrer. I dette emne beskrives potentielle problemer, der kan opstå, samt hvordan disse problemer mindskes. Service Bus har indført et antal afhjælpninger, som du skal vælge til, og i dette emne beskrives også reglerne for brug af disse tilmelding afhjælpninger.

## <a name="reliability-in-service-bus"></a>Pålidelighed i Service Bus

Der er flere måder til at håndtere problemer med meddelelse og enhed, og der er retningslinjerne for den korrekte brug af disse afhjælpninger. Du kan finde retningslinjerne, skal du først forstå, hvad kan mislykkes i Service Bus. På grund af designet af Azure systemer typisk alle disse problemer er forbigående. På et højt niveau vises de forskellige årsager til utilgængelighed på følgende måde:

-   (Throttling) fra et eksternt system, som Service Bus afhænger af. (Throttling) opstår fra interaktion med lager og Beregn ressourcer.

-   Problem for et system, som Service Bus afhænger af. En bestemt del af lagerplads kan for eksempel støder på problemer.

-   Fejl i tjenesten Bus på enkelt undersystem. I dette tilfælde en beregningsnode får adgang til en inkonsekvent tilstand og skal genstarte sig selv medfører, at alle enheder det fungerer justering af belastning til andre noder. Dette kan også medføre en kort periode behandling af langsom meddelelse.

-   Fejl i tjenesten Bus inden for en Azure datacenter. Dette er "opstår nedbrud" hvor systemet er ikke tilgængelig for mange minutter eller et par timer.

> [AZURE.NOTE] Ord **lagerplads** kan betyde både Azure-lager og SQL Azure.

Service Bus indeholder et antal afhjælpninger for disse problemer. I følgende afsnit beskrives de enkelte problemer og deres respektive afhjælpninger.

### <a name="throttling"></a>(Throttling)

Med Service Bus, throttling mulighed for samarbejde meddelelse rente administration. Hver enkelt Service Bus node indeholder mange enheder. Hver af disse enheder gør krav på systemet med hensyn til CPU, hukommelse, lager og andre aspekter. Når en af disse facetter registrerer brugen, der overskrider defineret tærskler, Service Bus kan nægte en given anmodning. Kalderen modtager en [ServerBusyException][] og nye forsøg efter 10 sekunder.

Som en afhjælpning skal koden læse fejlen og standse en hvilken som helst forsøg meddelelsens for mindst 10 sekunder. Da fejlen kan forekomme på tværs af dele af programmet, forventes, at hvert enkelt uafhængigt udfører forsøg. Koden kan reducere sandsynligheden for, at der begrænset ved at aktivere partitionering på en kø eller emne.

### <a name="issue-for-an-azure-dependency"></a>Problemet for en Azure afhængighed

Andre komponenter i Azure kan nogle gange har problemer med tjenesten. For eksempel, når der opgraderes et system, der bruger Service Bus, opleve systemet midlertidigt reduceret funktioner. Du kan løse disse typer problemer ved Service Bus regelmæssigt undersøger og implementerer afhjælpninger. Side virkningerne af disse afhjælpninger vises. Hvis du vil håndtere midlertidige problemer med lagerplads, implementerer Service Bus eksempelvis et system, der gør det muligt for meddelelse send handlinger til at fungere konsekvent. På grund af hvilke Afhjælpningen, kan en sendt meddelelse tage op til 15 minutter skal vises i den pågældende kø eller et andet abonnement og er klar til en modtag-handling. De fleste objekter påvirkes generelt ikke dette problem. Dog bruges ud fra antallet af objekter i Service Bus i Azure, denne afhjælpning nogle gange kan til en lille del af Service Bus kunder.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Service Bus fejl på et enkelt undersystem

Omstændigheder kan medføre en intern komponent i at blive inkonsekvent Service Bus med et program. Når Service Bus registrerer dette, indsamler data fra programmet på computeren som hjælp til diagnosticering Hvad skete der med. Når dataene er indsamlet, genstartes programmet i et forsøg på at vende tilbage til en konsistent tilstand. Denne proces foregår ret hurtigt, og resultaterne i et objekt, der vises ikke er tilgængelig for op til et par minutter, selvom typisk ned gange er meget kortere.

I disse tilfælde genererer klientprogrammet en [System.TimeoutException][] eller [MessagingException][] undtagelse. Service Bus indeholder en afhjælpning til dette problem i form af automatiseret klient forsøg. Når perioden forsøg er opbrugt, og at meddelelsen ikke er leveret, kan du undersøge ved hjælp af andre funktioner som [parvis navneområder][]. Parvis navneområder har andre advarsler, der er beskrevet i denne artikel.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Fejl i forbindelse med Service Bus inden for en Azure datacenter

Den mest sandsynlige årsag til en fejl i en Azure datacenter er mislykket opgradering installation af Service Bus eller et afhængige system. Sandsynligheden for, at denne type fejl er faldet, som har været lagret platformen. En datacenter fejl kan også ske årsager, der omfatter følgende:

-   Elektrisk afbrydelse (power levering og generere power forsvinder).
-   Forbindelse (internet afbrydelse mellem dine kunder og Azure).

I begge tilfælde forårsagede naturlige eller menneskeskabte nedbrud problemet. For at løse dette problem, og Sørg for, at du stadig kan sende meddelelser, kan du bruge [parvis navneområder][] til at aktivere meddelelser skal sendes til en anden placering, mens den primære placering er gjort sund igen. Få mere at vide i [bedste fremgangsmåder til Isolations programmer mod Service Bus afbrydelser og nedbrud][].

## <a name="paired-namespaces"></a>Parvis navneområder

Funktionen [parvis navneområder][] understøtter scenarier, hvor en tjeneste Bus enhed eller installation i et datacenter bliver utilgængeligt. Mens denne hændelse indtræffer sjældent, skal distribuerede systemer stadig være forberedt på håndtere værste-fald scenarier. Denne hændelse sker typisk, fordi nogle element, som Service Bus afhænger af har en kortvarig problem. Hvis du vil bevare programmet tilgængelighed under en afbrydelse, kan Service Bus brugere bruge to separate navneområder, helst i separate datacentre, til at hoste deres SMS enheder. Resten af dette afsnit bruger den terminologi, der er følgende:

-   Primære navneområde: navneområdet, som dit program interagerer til send og modtag handlinger.

-   Sekundær navneområde: navneområdet, der fungerer som en sikkerhedskopi til den primære navneområde. Programmet logik kommunikerer ikke med dette navneområde.

-   Failover interval: mængden af tid til at acceptere almindelig fejl, før programmet skifter fra det primære navneområde til navneområdet for den sekundære.

Parvis navneområder support *send tilgængelighed*. Send tilgængelighed bevarer mulighed for at sende meddelelser. Hvis du vil bruge send tilgængelighed, skal dit program opfylde følgende krav:

1.  Kun der modtages meddelelser fra det primære navneområde.

2.  Meddelelser sendes til en given kø eller emne kan modtages defekt.

3.  Hvis dit program bruger sessioner, kan meddelelser i en session ender defekt. Dette er et sideskift i normal funktionaliteten i sessioner. Det betyder, at dit program bruger sessioner logisk gruppemeddelelser. Det primære navneområde vedligeholdes kun sessionstilstand.

4.  Meddelelser i en session kan ender defekt. Dette er et sideskift i normal funktionaliteten i sessioner. Det betyder, at dit program bruger sessioner logisk gruppemeddelelser.

5.  Det primære navneområde vedligeholdes kun sessionstilstand.

6.  Den primære kø kan er online og begynde at acceptere meddelelser, før den sekundære kø leverer alle meddelelser i den primære kø.

I følgende afsnit beskrives API'erne, hvordan API'erne er implementeret, og viser eksempler på kode, der bruger funktionen. Bemærk, at der er fakturering konsekvenserne, der er knyttet til denne funktion.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>MessagingFactory.PairNamespaceAsync API

Funktionen parvis navneområder indeholder metoden [PairNamespaceAsync][] i klassen [Microsoft.ServiceBus.Messaging.MessagingFactory][] :

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Når opgaven er afsluttet navneområde parring også er fuldført og klar til at handle ud fra for en hvilken som helst [MessageReceiver][], [QueueClient][]eller [TopicClient][] oprettet med [MessagingFactory][] forekomst. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] er den grundlæggende klasse for de forskellige typer af parring, der er tilgængelige med et [MessagingFactory][] objekt. Den eneste afledte klasse er i øjeblikket, et navngivet [SendAvailabilityPairedNamespaceOptions][], som anvender send tilgængelighedskrav. [SendAvailabilityPairedNamespaceOptions][] har et sæt af konstruktører, der bygger på hinanden. Se på parametre med de fleste parametre, kan du forstå funktionsmåden for de andre konstruktører.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Disse parametre har følgende betydning:

-   *secondaryNamespaceManager*: en initialiseret [NamespaceManager][] forekomst for det sekundære navneområde, metoden [PairNamespaceAsync][] kan bruge til at konfigurere navneområdet sekundær. Navneområdet manager bruges til at se en liste over køer i navneområdet og at sikre dig, at de nødvendige ordrebeholdning køer findes. Hvis disse køer ikke findes, oprettes de. [NamespaceManager][] kræver mulighed for at oprette et token med **Administrer** kravet.

-   *messagingFactory*: [MessagingFactory][] forekomsten af navneområdet sekundær. Objektet [MessagingFactory][] bruges til at sende og modtage meddelelser fra ordrebeholdningen køerne, hvis egenskaben [EnableSyphon][] er indstillet til **Sand**.

-   *backlogQueueCount*: antallet ordrebeholdningen køer til at oprette. Denne værdi skal være mindst 1. Når du sender meddelelser til ordrebeholdningen, vælges en af disse køer tilfældigt. Hvis du angiver værdien til 1, kan derefter kun én kø nogensinde bruges. Når dette sker, og én ordrebeholdningen køen genererer fejl, klienten er ikke selv kan prøve en anden ordrebeholdningen kø og muligvis ikke at sende meddelelsen. Vi anbefaler, at indstille denne værdi til nogle større værdi og standard værdien til 10. Du kan ændre dette til et højere eller lavere værdi afhængigt af hvor meget data dit program sender om dagen. Hver ordrebeholdningen kø kan indeholde op til 5 GB meddelelser.

-   *failoverInterval*: mængden tid, hvor du vil acceptere mislykkede forsøg på den primære navneområde inden du skifter et enkelt objekt navneområdet sekundær. Der opstår failover på grundlag af enhed efter enhed. Objekter i et enkelt navneområde direkte ofte i forskellige noder i Service Bus. En fejl i én enhed indebærer ikke en fejl i en anden. Du kan angive denne værdi til [System.TimeSpan.Zero][] Sådan flytter du til sekundært umiddelbart efter din første, permanent fejl. Fejl, der udløser failover timer er en hvilken som helst [MessagingException][] , hvor egenskaben [IsTransient][] er falsk eller en [System.TimeoutException][]. Andre undtagelser, som [UnauthorizedAccessException][] få ikke failover, fordi de angiver, at klienten er konfigureret forkert. En [ServerBusyException][] ikke forårsager failover fordi det korrekte mønster er at vente ti sekunder, send meddelelsen igen.

-   *enableSyphon*: Angiver, at denne bestemt parring bør også syphon meddelelser fra den sekundære navneområde tilbage til det primære navneområde. Generelt skal programmer, som sender meddelelser indstille denne værdi til **Falsk**. programmer, der modtage meddelelser skal angive denne værdi til **Sand**. Årsagen til det, at ofte, der er færre meddelelse modtagere end afsendere. Afhængigt af antallet modtagere, kan du vælge at have en enkelt programforekomst af håndtere syphon opgaver. Ved hjælp af mange modtagere har fakturering konsekvenser for hver enkelt ordrebeholdningen kø.

For at bruge koden, skal du oprette en primær [MessagingFactory][] -forekomst, en sekundær [MessagingFactory][] -forekomst, en sekundær [NamespaceManager][] forekomst og en forekomst af [SendAvailabilityPairedNamespaceOptions][] . Opkaldet kan være noget så enkelt som følgende:

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Når den opgave, der returneres af metoden [PairNamespaceAsync][] er fuldført, er alt konfigureret og klar til brug. Før der returneres opgaven, kan du ikke har fuldført alle det baggrund arbejde, der er nødvendige for den parring til at arbejde højre. Du skal derfor ikke startet afsendelse af meddelelser, indtil opgaven returnerer. Hvis der opstod fejl som forkert legitimationsoplysninger eller manglende oprettelse af køer ordrebeholdningen vil disse undtagelser udløst, når opgaven er fuldført. Når opgaven returnerer, Kontrollér, at køerne blev fundet eller oprettet ved at undersøge egenskaben [BacklogQueueCount][] på din [SendAvailabilityPairedNamespaceOptions][] forekomst. Til den foregående kode vises denne handling på følgende måde:

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Næste trin

Nu, hvor du har lært grundlæggende omkring asynkron messaging i Service Bus, kan du få flere oplysninger om [parvis navneområder][].

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Bedste fremgangsmåder til Isolations programmer mod Service Bus afbrydelser og nedbrud]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]:https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [parvis navneområder]: service-bus-paired-namespaces.md
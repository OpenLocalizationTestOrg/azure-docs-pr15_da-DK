<properties
    pageTitle="Azure begivenhed Hubs arkiv | Microsoft Azure"
    description="Oversigt over funktionen Azure begivenhed Hubs arkiv."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="azure-event-hubs-archive"></a>Azure begivenhed Hubs arkiv

Azure begivenhed Hubs arkiv gør det muligt at levere automatisk streaming dataene i din begivenhed Hubs til en Blob storage konto efter eget valg med større fleksibilitet til at angive et klokkeslæt eller tilpasse størrelsen på interval efter eget valg. Konfiguration af arkiv er hurtig, der er ingen administrative omkostninger til at køre den, og det skaleres automatisk med begivenhed Hubs [overførselshastighed enheder](event-hubs-overview.md#capacity-and-security). Begivenhed Hubs arkiv er den nemmeste måde at indlæse streaming data til Azure og gør det muligt at fokusere på databehandling i stedet for hentning af data.

Azure begivenhed Hubs arkiv gør det muligt at behandle realtid og batchen-baserede rørledninger på samme strømmen. Dette gør det muligt at bygge løsninger, der kan vokse med dine behov over tid. Uanset om du opbygger batchen-baserede systemer i dag med øje med kommende realtid behandling, eller du vil føje en effektiv kolde sti til en eksisterende realtid løsning, gør begivenhed Hubs arkiv arbejde med streaming data nemmere.

## <a name="how-event-hubs-archive-works"></a>Hvordan fungerer begivenhed Hubs arkiv

Begivenhed Hubs er en tid opbevaring robust bufferen til telemetri vandindtrængen, svarende til en logfil over fordelt. Nøglen skalere i begivenhed Hubs er [partitioneret consumer model](event-hubs-overview.md#partition-key). Hver partition er en uafhængig segment af data og er consumed uafhængigt af hinanden. Over tid alderen disse data fra baseret på konfigurerbar opbevaringsperioden. Som et resultat, bliver en given begivenhed Hub aldrig "for fuld."

Begivenhed Hubs arkiv gør det muligt at angive dine egne Azure Blob-lager konto og beholder, der bruges til at gemme de arkiverede data. Denne konto, kan være i samme område som begivenhed centrum eller i et andet område, tilføjes fleksibilitet af funktionen begivenhed Hubs arkiv.

Arkiverede data er skrevet i [Apache Avro][] format. et kompakt, hurtig, binært format, der giver omfattende datastrukturer indbyggede skema. Dette format der anvendes i Hadoop økosystemet samt ved Stream analyser og Azure Data Factory. Du kan finde flere oplysninger om at arbejde med Avro findes senere i denne artikel.

### <a name="archive-windowing"></a>Arkiv Windowing

Begivenhed Hubs arkiv giver dig mulighed at konfigurere et vindue til at styre arkivering. Dette vindue er en minimumstørrelse og konfiguration af med en "første wins politik," dvs., at den første udløser er stødt på medfører handlingen arkiv. Hvis du har et 15-minutters/100 MB arkiv vindue og sende 1 MB/s, udløser vinduet størrelse før en tidsramme. Hver partition arkiverer uafhængigt af hinanden og skriver en færdige Bloker blob på tidspunktet for arkiv, navnet for den tid, når arkiv intervallet blev fundet. Navnekonventionen er som følger:

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### <a name="scaling-to-throughput-units"></a>Skalering til overførselshastighed enheder

Begivenhed Hubs trafik styres af [overførselshastighed enheder](event-hubs-overview.md#capacity-and-security). En enkelt overførselshastighed enhed gør det muligt for 1 MB/s eller 1000 begivenheder sekundet af indgangs- og to gange mængden udgangspunkt. Standard begivenhed Hubs kan konfigureres med 1-20 overførselshastighed enheder, og mere kan købes via en kvote øge [anmode om support][]. Brugen ud over købte overførselshastighed enheder er begrænset. Begivenhed Hubs arkiv kopierer data direkte fra den interne begivenhed Hubs lagerplads springe overførselshastighed enhed udgangspunkt kvoter og gemme dit udgangspunkt for andre behandling læsere som Stream Analytics eller gnister.

Når konfigureret, kører begivenhed Hubs arkiv automatisk, så snart du sender din første hændelse. Den stadig køre til enhver tid. Hvis du vil gøre det nemmere at til din efterfølgende behandling for at vide, at processen fungerer, skriver begivenhed Hubs tomme filer, når der er ingen data. Dette giver en mere forudsigelige cadence og markør, der kan feed batchen-processorer.

## <a name="setting-up-event-hubs-archive"></a>Konfiguration af begivenhed Hubs arkiv

Begivenhed Hubs arkiv kan konfigureres på klokkeslættet for oprettelsen af begivenhed Hub via portalen eller Azure ressourcestyring. Du skal blot aktivere arkiv ved at klikke på knappen **på** . Du kan konfigurere en lagerplads konto og beholder ved at klikke på **objektbeholder** del af bladet. Da begivenhed Hubs arkiv bruger godkendelse tjenester med lagerplads, behøver du ikke at angive en forbindelsesstreng lagerplads. Datovælgeren ressource markerer ressourcen URI for kontoen lagerplads automatisk. Hvis du bruger Azure Ressourcestyring, skal du angive denne URI eksplicit som en streng.

Vinduet standard klokkeslæt er fem minutter. Den mindste værdi er 1, de maksimale 15. Vinduet **størrelse** indeholder en række 10-500 MB.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Tilføje arkiv til en eksisterende begivenhed-Hub

Arkiver kan konfigureres på eksisterende begivenhed Hubs, der er i en begivenhed Hubs navneområde. Funktionen er ikke tilgængelig på ældre meddelelser eller blandet type navneområder. Aktivere arkiv på en eksisterende begivenhed-Hub, eller ændre indstillingerne for arkiv, skal du klikke på din navneområde for at indlæse bladet **Essentials** og derefter klikke på hubben begivenhed, du vil aktivere eller ændre indstillingen arkiv. Til sidst, klikke på i afsnittet **Egenskaber** i den åbne blade, som vist i følgende figur.

![][2]

Du kan også konfigurere begivenhed Hubs arkiv via Azure ressourcestyring skabeloner. Du kan finde flere oplysninger [i denne artikel](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Udforskning af arkivet og arbejde med Avro

Når konfigureret, opretter begivenhed Hubs arkivere filer i Azure-lager konto og beholder, der er angivet i vinduet konfigurerede tidsrum. Du kan få vist disse filer i et værktøj som [Azure Storage Explorer][]. Du kan downloade filer lokalt til at arbejde på dem.

De filer, der er oprettet med begivenhed Hubs arkiv har følgende Avro skema:

![][3]

Der er en nem måde at udforske Avro filer ved hjælp af [Avro værktøjer][] glas fra Apache. Når du overfører denne glas, kan du se skemaet for en bestemt Avro-fil ved at køre følgende kommando:

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

Denne kommando returnerer

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Du kan også bruge Avro værktøjer til at konvertere filen til JSON-format og udføre andre behandling.

Hvis du vil udføre mere avancerede behandling, downloade og installere Avro til dit valg af platform. På tidspunktet for denne skrivning, der findes installationer til C, C++, C\#, Java, NodeJS, Perl, PHP, Python og fonetisk.

Apache Avro har fuldført Introduktion hjælpelinjer til [Java][] og [Python][]. Du kan også læse artiklen [Introduktion til begivenhed Hubs arkiv](event-hubs-archive-python.md) .

## <a name="how-event-hubs-archive-is-charged"></a>Hvordan begivenhed Hubs arkiv faktureres

Begivenhed Hubs arkiv måles på samme måde at overførselshastighed enheder, som satsbaserede ressourceomkostninger. Det er direkte proportional med antallet af købte til navneområdet overførselshastighed enheder. Som overførselshastighed enheder er større og mindre, begivenhed Hubs arkiv øges og mindskes for at opnå tilsvarende ydeevne. Målere ske sammen. Gebyr for begivenhed Hubs arkiv er $0.10 timen per overførselshastighed enhed, der tilbydes i rabat 50% i perioden, preview.

Begivenhed Hubs arkiv er helt igennem den nemmeste måde at hente data til Azure. Brug af Azure Data sø, Azure Data Factory og Azure HDInsight, kan du udføre batchbehandling og andre analytics efter eget valg ved hjælp af velkendte værktøjer og platforme på en hvilken som helst skala, du har brug for.

## <a name="next-steps"></a>Næste trin

Du kan få mere at vide om begivenhed Hubs ved at besøge følgende links:

- En komplet [Northwind, der bruger begivenhed Hubs][].
- Eksemplet [Skaler ud begivenhed behandling med begivenhed Hubs][] .
- [Oversigt over Hubs begivenhed][]

[Apache Avro]: http://avro.apache.org/
[anmodning om support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Azure-lager Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Avro værktøjer]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Oversigt over Hubs begivenhed]: event-hubs-overview.md
[eksempelprogram, der bruger begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Skalere ud begivenhed behandling med begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
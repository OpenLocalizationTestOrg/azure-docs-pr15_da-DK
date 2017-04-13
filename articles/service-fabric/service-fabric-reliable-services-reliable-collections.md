<properties
   pageTitle="Pålidelige samlinger | Microsoft Azure"
   description="Tjenesten strukturerede med høj sikkerhed services indeholder pålidelig samlinger, der gør det muligt at skrive meget tilgængelige, SVG og lav ventetid skyen programmer."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduktion til pålidelig samlinger i Azure Service strukturerede med høj sikkerhed services

Pålidelige samlinger gør det muligt at skrive meget tilgængelige, SVG og lav ventetid skyen programmer, som hvis du er ved at skrive enkelt computerprogrammer. Klasser i navneområdet **Microsoft.ServiceFabric.Data.Collections** indeholder et sæt af out box af websteder, som gør automatisk din stat meget tilgængelig. Udviklere skal program kun for den pålidelige websteder API'er og lade pålidelig samlinger administrere tilstanden replikerede og lokale.

Vigtige forskellen mellem pålidelig af websteder og andre teknologier til høj tilgængelighed (såsom Redis, Azure Table service og Azure kø service) er, at tilstanden gemmes lokalt i forekomsten af mens også gøres meget tilgængelige. Det betyder, at:

- Alle læser er lokale, hvilket resulterer i lav ventetid og høj overførselshastighed læser.
- Alle skriver betale det mindste antal netværk IOs, hvilket resulterer i lav ventetid og høj overførselshastighed skriver.

![Billede af udviklingen af af websteder.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Pålidelige af websteder kan betragtes som neutralt udviklingen af **System.Collections** klasser: et nyt sæt af websteder, som er udviklet til skyen og med flere computerprogrammer uden at øge kompleksitet for udvikleren. Så er pålidelig af websteder:

- Replikeres: Tilstandsændringer replikeres til høj tilgængelighed.
- Bevaret: Data bevares til disken til holdbarhed mod store udfald (for eksempel datacenter strømafbrydelse).
- Asynkron: API'er er asynkrone at sikre, at tråde ikke blokeres, når skulle betale EY.
- Transaktions: API'er anvender fremstilling af transaktioner så du nemt kan administrere flere pålidelige websteder i en tjeneste.

Pålidelige samlinger giver stærke konsistens garanterer af feltet for at gøre Kognitiv om programtilstanden nemmere.
Stærke konsistens opnås ved at sikre transaktion anvendelser Udfør, når hele posteringen er logget på beslutningsdygtig størstedelen af replikaer, herunder primært.
For at opnå lavere konsistens, bekræftelse programmer tilbage til klient/anmoderen, før den asynkrone bekræftelse returnerer.

De pålidelige samlinger API'er er en udviklingen af samtidige samlinger API'er (findes i navneområdet **System.Collections.Concurrent** ):

- Asynkron: Returnerer en opgave, da i modsætning til samtidige samlinger, handlingerne replikeres og bevaret.
- Ikke ud-parametre: bruger `ConditionalValue<T>` til at returnere en boolesk værdi og en værdi i stedet for ud-parametre. `ConditionalValue<T>`er som `Nullable<T>` , men ikke kræver T for at være en struktur.
- Transaktioner: Bruger et transaction-objekt til at give gruppen Handlinger for flere pålidelige websteder i en transaktion brugeren.

I dag, indeholder **Microsoft.ServiceFabric.Data.Collections** to af websteder:

- [Pålidelige ordbog](https://msdn.microsoft.com/library/azure/dn971511.aspx): repræsenterer en replikerede, transaktioner og asynkron samling af nøgle/værdi-par. Svarer til **ConcurrentDictionary**, både tasten og værdien, der kan være af enhver type.
- [Pålidelige kø](https://msdn.microsoft.com/library/azure/dn971527.aspx): repræsenterer en replikerede, transaktioner og asynkron faste første-i first-out (FIFO) kø. Svarer til **ConcurrentQueue**værdien kan være af enhver type.

## <a name="isolation-levels"></a>Isolationsniveauet niveauer
Isolationsniveau definerer graden som posteringen isolation fra ændringer foretaget af andre transaktioner.
Der er to isolationsniveauet niveauer, der understøttes i pålidelig af websteder:

- **Gentaget læsning**: Angiver, at sætninger ikke kan læse data, der er blevet ændret, men endnu ikke er sendt af andre transaktioner og, at ingen andre transaktioner kan ændre data, der er blevet læst af den aktuelle post, før den aktuelle transaktion er afsluttet. Du kan finde flere oplysninger, [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
- **Øjebliksbillede**: Angiver, at data, der læses af enhver sætning i en transaktion medtages en transaktion ensartet version af de data, der var i starten af posteringen.
Posteringen kan genkendes kun dataændringer, som blev anvendt før starten af posteringen.
Dataændringer, der er foretaget af andre transaktioner efter starten af den aktuelle post er ikke synlige for sætninger, der er udført i den aktuelle transaktion.
Effekten er, som om sætninger i en transaktion få et øjebliksbillede af anvendt data, som fandtes i starten af posteringen.
Snapshots er ensartet på tværs af pålidelig af websteder.
Du kan finde flere oplysninger, [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Pålidelige samlinger Vælg automatisk isolationsniveauet skal bruges til en given Læs handling afhængigt af handlingen, og replikaen rolle på tidspunktet for oprettelse af transaktion.
Følgende er den tabel, hvor vises isolationsniveauet niveau standardindstillinger for pålidelig ordbog og kø handlinger.

| Handlingen \ rolle      | Primære          | Sekundær        |
| --------------------- | :--------------- | :--------------- |
| Enkelt objekt: Læs    | Gentaget læsning  | Øjebliksbillede         |
| Optælling \ Tæl   | Øjebliksbillede         | Øjebliksbillede         |

>[AZURE.NOTE] Almindelige eksempler for enkelt objekt handlinger er `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.

Både den pålidelige ordbog og pålidelig køen understøtter læse din skriver.
Med andre ord, kan en hvilken som helst skriveadgang i en transaktion ses til et følgende læses, der hører til samme transaktion.

## <a name="locking"></a>Låse
I pålidelige samlinger, alle transaktioner er to-tidsfaseinddelt: en transaktion ikke slip låse det har hentet, indtil posteringen afsluttes med en afbrydelse eller en bekræftelse.

Pålidelige ordbog bruger rækkeniveau låsning, så alle handlinger i ét objekt.
Pålidelige kø driver fra på dokumentsammenfald for faste transaktions FIFO egenskab.
Pålidelige kø bruger handlingen niveau låse tillade én transaktion med `TryPeekAsync` og/eller `TryDequeueAsync` og én transaktion med `EnqueueAsync` ad gangen.
Bemærk, at hvis du vil bevare FIFO, hvis en `TryPeekAsync` eller `TryDequeueAsync` nogensinde overholder pålidelig køen er tom, de også vil låse `EnqueueAsync`.

Skriv handlinger altid henter eksklusivt låse.
For få handlinger afhænger den låsning af flere faktorer.
En hvilken som helst få handling færdig ved hjælp af øjebliksbillede isolationsniveauet er Lås gratis.
En gentaget læsning handlingen som standard tager delt låse.
Dog for en hvilken som helst få handling, der understøtter gentaget læsning, kan brugeren bede om en opdateringslås i stedet for delt låsen.
En opdateringslås er en asymmetrisk Lås, der bruges til at forhindre en fælles formular for Baglåssituationer, der opstår, når flere transaktioner låser ressourcer til potentielle opdateringer på et senere tidspunkt.

Lås kompatibilitet matrixen kan ses nedenfor:

| Anmode om \ tildelt | Ingen         | Delt       | Opdater      | Med udelt adgang    |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Delt            | Ingen konflikt  | Ingen konflikt  | Konflikt    | Konflikt     |
| Opdater            | Ingen konflikt  | Ingen konflikt  | Konflikt    | Konflikt     |
| Med udelt adgang         | Ingen konflikt  | Konflikt     | Konflikt    | Konflikt     |

Bemærk, at et timeout argument i de pålidelige samlinger API'er bruges til registrering af baglås.
For eksempel forsøger to transaktioner (T1 og T2) at læse og opdatere K1.
Det er muligt for at de kan deadlock, fordi de begge ender har delt låsen.
I dette tilfælde vil den ene eller begge handlinger timeout.

Bemærk, at eksemplet ovenfor baglås er et godt eksempel på, hvordan en opdateringslås kan forhindre baglåse.

## <a name="persistence-model"></a>Brugerdata model
Stabil tilstand Manager og pålidelige websteder at følge en brugerdata model, der kaldes Log og kontrolpunkt.
Dette er en model, hvor hver enkelt tilstand ændring er logget på disken og anvendes kun i hukommelsen.
Tilstanden fuldført selve bevares kun nogle gange (kaldes også Kontrolpunkt).
Fordelen er, at deltaer er slået til sekventielle ændringer, der kun skriver på disk til forbedret ydeevne.

For bedre at forstå Log og kontrolpunkt modellen, Lad os først se på scenariet uendelig disk.
Stabil tilstand leder logger hver operation, før den replikeres.
Dette giver mulighed for pålidelig samlingen for at anvende handlingen i hukommelsen.
Da logfiler er bevaret, selvom replikaen mislykkes, og skal genstartes, har stabil tilstand leder nok oplysninger i dens logfiler til genafspilning alle handlinger, der har mistet replikaen.
Disken er uendelig, logposter behøver ikke at blive fjernet og samlingen pålidelig skal administrere tilstanden i hukommelsen.

Nu Lad os se på scenariet endelig disk.
Som logposter point, der kræves, vil stabil tilstand leder løbe tør for plads på harddisken.
Før, der sker, skal stabil tilstand leder afkorter sin logfil for at skabe plads til de nyere poster.
Det kan anmode om den pålidelige samlinger kontrolpunkt deres i hukommelsen tilstand på disken.
Det er den pålidelige samlinger ansvar at fastholdes dens tilstand op til dette punkt.
Når pålidelig af websteder har fuldført deres kontrolpunkter, kan stabil tilstand leder afkorte Logfør i ledig diskplads.
Denne måde, når replikaen skal genstartes, gendanne pålidelig samlinger deres tildelt et kontrolpunkt tilstand, og vil gendanne og afspille alle tilstandsændringer, som opstået efter kontrolpunktet stabil tilstand leder.

>[AZURE.NOTE] Tilføje en anden værdi af checkpointing er, at det forbedrer gendannelsesydeevne i almindelige tilfælde.
Dette skyldes, at kontrolpunkter indeholder kun de nyeste versioner.

## <a name="recommendations"></a>Anbefalinger

- Rediger ikke et objekt af brugerdefineret type, der returneres af Læs handlinger (f.eks., `TryPeekAsync` eller `TryGetValueAsync`). Pålidelige samlinger, ligesom samtidige samlinger, returnerer en reference til objekterne og ikke en kopi.
- Gøre deep Kopiér det objekt, der returneres af en brugerdefineret type før du foretager ændringer i den. Da strukturer og indbyggede typer gang efter værdi, skal du ikke gøre en deep kopi på dem.
- Brug ikke `TimeSpan.MaxValue` for timeout. Timeout skal bruges til at registrere baglåse.
- Brug ikke en transaktion, når den er blevet anvendt, afbrudt eller fjernet.
- Brug ikke en optælling uden for transaktionsområdet det blev oprettet i.
- Opret ikke en transaktion i en anden transaktion `using` sætningen fordi det kan medføre baglåse.
- Sørg for, at din `IComparable<TKey>` implementering er korrekt. Systemet tager afhængighed på dette til fletning kontrolpunkter.
- Brug Update Lås ved læsning af et element med en har til hensigt for at opdatere den for at forhindre en bestemt kategori af baglåse.
- Overvej at bruge Sikkerhedskopiering og gendannelse funktionalitet for at få nedbrud.
- Undgå at blande enkelt enhed handlinger og flere enhed handlinger (f.eks `GetCountAsync`, `CreateEnumerableAsync`) i samme transaktion på grund af de forskellige isolationsniveauet niveauer.

Her er nogle ting, du skal huske på:

- Standardværdien for timeout er 4 sekunder til alle pålidelige websteder API'er. De fleste brugere skal ikke tilsidesættes.
- Standard annulleringen Tokenet er `CancellationToken.None` i alle pålidelige samlinger API'er.
- Parameteren vigtige type (*TKey*) til en pålidelig ordbog skal korrekt implementere `GetHashCode()` og `Equals()`. Nøgler skal være fast.
- For at opnå høj tilgængelighed for pålidelig samlinger, skal hver tjeneste have mindst et destinationsprogram og mindste replikasæt størrelse af 3.
- Læsehandlinger på sekundært kan læse versioner, der ikke er quorum anvendt.
Det betyder, at en version af data, der læses fra en enkelt sekundær kan være kommet false.
Læser fra primær er naturligvis altid stabil: kan aldrig være falsk kommet.

## <a name="next-steps"></a>Næste trin

- [Pålidelige Services Hurtig start](service-fabric-reliable-services-quick-start.md)
- [Arbejde med pålidelig af websteder](service-fabric-work-with-reliable-collections.md)
- [Pålidelige Services-beskeder](service-fabric-reliable-services-notifications.md)
- [Pålidelige Services sikkerhedskopiering og gendannelse (nedbrud)](service-fabric-reliable-services-backup-restore.md)
- [Pålidelig tilstand Manager konfiguration](service-fabric-reliable-services-configuration.md)
- [Introduktion til tjenesten strukturen Web API-tjenester](service-fabric-reliable-services-communication-webapi.md)
- [Avancerede brugen af pålidelig tjenesterne programming model](service-fabric-reliable-services-advanced-usage.md)
- [Udviklerreference for pålidelig af websteder](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

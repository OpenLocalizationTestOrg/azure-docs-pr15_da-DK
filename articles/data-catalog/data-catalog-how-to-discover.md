<properties
   pageTitle="Hvordan du kan registrere datakilder | Microsoft Azure"
   description="Vejledning til artikel fremhævning af hvordan du kan finde registrerede dataaktiver med Azure datakatalog, herunder søgning og filtrering og brug af den forekomst af fremhævning af funktionerne i portalen Azure datakatalog."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>

# <a name="how-to-discover-data-sources"></a>Hvordan du kan registrere datakilder

## <a name="introduction"></a>Introduktion
**Microsoft Azure datakatalog** er en fuldt administreret skybaseret tjeneste, der fungerer som et system af registrering og system af registrering til enterprise-datakilder. Med andre ord handler **Azure datakatalog** om hjælpe personer opdage, forstå og bruge datakilder, og hjælp organisationer til at få flere værdi fra deres eksisterende data. Når en datakilde, der er registreret med **Azure datakatalog**, er dens metadata indekseret af tjenesten, så brugerne kan nemt søge for at se de ønskede data.

## <a name="searching-and-filtering"></a>Søgning og filtrering

Registrering i **Azure datakatalog** bruger to primære mekanismer: søgning og filtrering.

Søgning er udviklet til at være både intuitiv og effektive – som standard, søgeord sammenlignes med en egenskab i kataloget, herunder leveret anmærkninger.

Filtrering er udviklet supplement til søgning. Brugere kan vælge bestemte egenskaber som eksperter, datakildetype, objekttype og mærker, til at få vist kun matchende dataaktiver samt til at begrænse søgeresultaterne til tilsvarende Aktiver samt.

Ved hjælp af en kombination af søgning og filtrering, kan brugere hurtigt navigere de datakilder, der er registreret med **Azure datakatalog** at finde de datakilder, de skal bruge.

## <a name="search-syntax"></a>Syntaksen for Søg

Brugere kan også bruge **Azure datakatalog**Søg syntaksen for at få bedre kontrol over søgeresultaterne, selvom søgningen registrerer fri tekst som standard er simpel og intuitiv. **Azure datakatalog** søgning understøtter en af følgende fremgangsmåder:

| Teknik                 | Brug                                                                                                                                     | Eksempel                                                   |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Grundlæggende søgning              | Grundlæggende søgning ved hjælp af en eller flere søgeord. Resultaterne er en hvilken som helst aktiver, der svarer til en egenskab med en eller flere af de betingelser, der er angivet. | salgsdata                                                |
| Egenskaben angivelse af område          | Kun returnere datakilder, hvor søgeordet sammenlignes med den angivne egenskab                                                   | navn: økonomi                                              |
| Booleske operatorer         | Udvide eller begrænse en søgning ved hjælp af boolesk handlinger                                                                                     | økonomi ikke virksomhedens                                     |
| Gruppering med parentes | Bruge parenteser til gruppen dele af forespørgslen til at opnå logiske isolationsniveauet, især i forbindelse med booleske operatorer              | navn: økonomi og (mærker: Kvt1 eller mærker: K2) |
| Sammenligningsoperatorer      | Brug sammenligninger end lige til egenskaber, der har numeriske dato og klokkeslæt-datatyper                                                | modifiedTime > "11-05-2014"                                 |

Du kan finde flere oplysninger om **Azure datakatalog** søgning, [https://msdn.microsoft.com/library/azure/mt267594.aspx](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## <a name="hit-highlighting"></a>Tryk på fremhævning
Når du får vist søgeresultater, bliver alle viste egenskaber, der opfylder de angivne søgeord – som data aktiv navn, beskrivelse og mærker – fremhævet for at gøre det nemmere at identificere, hvorfor en given data aktiv blev returneret af en bestemt søgning.

> [AZURE.NOTE] Brugere kan slå besøg fremhævning fra, hvis det er nødvendigt, brug af parameteren "Fremhævning" i portalen **Azure datakatalog** .

Når du får vist søgeresultater, den altid muligvis ikke indlysende hvorfor et data aktiv er inkluderet, selv med besøg fremhævning aktiveret. Fordi der søges i alle egenskaber, der som standard, kan et data aktiv returneres på grund af et match på en egenskab i kolonnen niveau. Og fordi flere brugere kan oprette anmærkninger i registrerede dataaktiver med deres egne mærker og -beskrivelser, ikke alle metadata kan ikke vises på listen over søgeresultater.

I standard flisen visning, hvert felt, der vises i søgeresultaterne indeholder et "Vis søgeord er svarer til" ikon, som gør det muligt for brugeren til hurtigt at få vist antallet af forekomster og deres placering, og at springe til dem, hvis du ønsker.

 ![Tryk på fremhævning og søgefunktionen matcher i portalen Azure datakatalog](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Oversigt
Registrering af en datakilde med **Azure datakatalog** gør datakilden lettere at registrere og forstå ved at kopiere strukturelle og beskrivende metadata fra datakilden til tjenesten katalog. Når der er registreret en datakilde, kan brugerne finde den ved hjælp af filtrering og søgning fra i portalen **Azure datakatalog** .

## <a name="see-also"></a>Se også
- [Introduktion til Azure datakatalog](data-catalog-get-started.md) selvstudium trinvise få mere at vide om, hvordan du registrere datakilder.

<properties
   pageTitle="Azure datakatalog terminologi | Microsoft Azure"
   description="Denne artikel indeholder en introduktion til begreber og ord, der bruges i dokumentationen til Azure datakatalog."
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
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-terminology"></a>Azure datakatalog terminologi

## <a name="catalog"></a>Katalog

Datakatalog Azure er en skybaseret metadata lager, hvor dataene kan være registreret kilder og data aktiver. Kataloget fungerer som en central lagerplacering strukturel metadata, der er hentet fra datakilder og beskrivende metadata, der er tilføjet af brugere.

## <a name="data-source"></a>Datakilde

En datakilde er et system eller en objektbeholder, der administrerer dataaktiver. Som eksempler kan nævnes SQL Server-databaser, Oracle-databaser, SQL Server Analysis Services-databaser (tabelformat eller Multidimensionelle) og SQL Server Reporting Services-servere.

## <a name="data-asset"></a>Data aktiv

Dataaktiver er objekter, der er indeholdt i datakilder, der kan være registreret hos kataloget. Som eksempler kan nævnes SQL Server-tabeller og visninger, Oracle tabeller og visninger, SQL Server Analysis Services målinger, mål og KPI'er, og SQL Server Reporting Services-rapporter.

## <a name="data-asset-location"></a>Placering af aktiver

Kataloget gemmer placeringen af en datakilde eller data aktiv, så der kan bruges til at oprette forbindelse til datakilden ved hjælp af en-klientprogrammet. Formatet og oplysninger om placeringen varierer afhængigt af datakildetypen. En SQL Server-tabel kan for eksempel være identificeret med navnet fire del – servernavn, databasenavnet, skemanavn, objektnavn –, mens en SQL Server Reporting Services-rapport kan blive identificeret af dens URL-adresse.

## <a name="structural-metadata"></a>Strukturel metadata

Strukturel metadata er de metadata, der er hentet fra en datakilde, der beskriver strukturen i et data aktiv. Dette omfatter Aktiver placeringen, dens objektnavn og type og yderligere type-specifikke egenskaber. For eksempel indeholder strukturel metadataene for tabeller og visninger navne og datatyper for objektets kolonner.

## <a name="descriptive-metadata"></a>Beskrivende metadata

Beskrivende metadata er metadata, der beskriver formål eller formålet med en data aktiv. Typisk beskrivende metadata er tilføjet af kataloget brugere ved hjælp af portalen Azure datakatalog, men det kan også udtrækkes fra datakilden under registrering. For eksempel kan værktøjet til Azure datakatalog registrering udtrække beskrivelser fra egenskaben beskrivelse i SQL Server Analysis Services og SQL Server Reporting Services og fra [ms_description udvidet egenskab](https://technet.microsoft.com/library/ms190243.aspx) i SQL Server-databaser, hvis disse egenskaber er blevet udfyldt med værdier.

## <a name="request-access"></a>Anmode om adgang

Et data aktiv beskrivende metadata kan indeholde oplysninger om, hvordan du anmoder om adgang til data aktiv eller datakilde. Disse oplysninger vises med aktiv placeringen af data og kan indeholde et eller flere af følgende indstillinger:

- E-mail-adressen på den bruger eller gruppe, der er ansvarlig for at give adgang til datakilden.
- URL-adressen til dokumenterede processen, som brugerne skal følge for at få adgang til datakilden.
- URL-adressen for en identitets- og administration af værktøjet (som Microsoft identitet Manager), der kan bruges til at få adgang til datakilden.
- Gratis-teksten i en værdi, der beskriver, hvordan brugere kan få adgang til datakilden.

## <a name="preview"></a>Vis udskrift

Et eksempel i Azure datakatalog er et øjebliksbillede af op til 20 poster, der kan udtrækkes fra datakilden under registrering og gemt i kataloget med data aktiv metadata. Eksemplet kan hjælpe brugere, der finde et data aktiv bedre at forstå funktionalitet og formål. Det vil sige, at få vist eksempeldata kan være mere værdifuldt end se lige præcis de kolonnenavne og datatyper.
Eksempelvisning af understøttes kun til tabeller og visninger, og skal være eksplicit valgt af brugeren under registrering.

## <a name="data-profile"></a>Data-profil

En data profil i Azure datakatalog er et øjebliksbillede af tabel-niveau og kolonne metadata om et aktiv i registrerede data, der kan udtrækkes fra datakilden under registrering og gemt i kataloget med data aktiv metadata. Profilen data kan hjælpe brugere, der finde et data aktiv bedre at forstå funktionalitet og formål. Svarer til eksempelvisning, data profiler skal eksplicit vælges af brugeren under registrering.

> [AZURE.NOTE] Hente en data profil kan være en kostbar handling for store tabeller og visninger, og kan øge den tid, der kræves for at registrere en datakilde.

## <a name="user-perspective"></a>Bruger perspektiv

I Azure-Datakataloget, kan enhver bruger udgør beskrivende metadata for en registrerede data aktiv. Hver enkelt bruger har et entydigt perspektiv på data og dets anvendelse. For eksempel administratoren ansvarlig for en server, der muligvis give oplysninger om dets serviceniveauaftale (SLA) eller sikkerhedskopiering windows; en databehandler muligvis give links til dokumentation for virksomheden behandler dataene understøtter; og en analyse kan angive en beskrivelse i betingelser, der er mest relevante for andre analytikere, og som kan være mest værdifulde for de brugere, som har brug for at registrere og forstå dataene.

Hver af disse perspektiver er fuldstændig værdifulde, og med Azure datakatalog hver bruger kan angive oplysninger, der giver mening i dem, mens alle brugere kan bruge disse oplysninger til at forstå dataene og sit formål.

## <a name="expert"></a>Ekspert

En ekspert er en bruger, der er identificeret som har en informeret "expert" perspektiv på data anlægsaktiver. Alle brugere kan tilføje selv eller en anden bruger som en ekspert for et aktiv. Der er angivet som en ekspert ikke formidle ekstra rettigheder i Azure-Datakataloget; Det kan nemt finde disse perspektiver, der er mest sandsynlige skal være nyttig, når du gennemser en aktiv beskrivende metadata-brugere.

## <a name="owner"></a>Ejer

En ejer er en bruger, der har flere rettigheder til at administrere en data aktiv i Azure-Datakataloget. Brugere kan overtage ejerskabet af aktiver registrerede oplysninger og ejere kan tilføje andre brugere som samtidig ejere. Flere oplysninger under [Sådan administreres over dataaktiver](data-catalog-how-to-manage.md)  
> [AZURE.NOTE] Ejerskab og styring er kun tilgængelig i Standard Edition af Azure datakatalog.

## <a name="registration"></a>Registrering

Registrering er kunsten at uddrage data aktiv metadata fra en datakilde og kopiere den til Azure datakatalog-tjenesten. Dataaktiver, der er registreret kan derefter kommenterede og opdaget.

## <a name="see-also"></a>Se også

- [Hvad er Azure datakatalog?](data-catalog-what-is-data-catalog.md) -I denne artikel giver et overblik over tjenesten Azure datakatalog, den værdi, der giver og scenarier understøtter.

- [Introduktion til Azure datakatalog](data-catalog-get-started.md) - i denne artikel indeholder en til slut-selvstudium, der viser, hvordan du bruger Azure datakatalog til dataregistrering kilde.  

<properties
    pageTitle="Sådan Data profil-datakilder"
    description="Vejledning til artikel fremhævning hvordan medtage tabel og kolonne niveau data profiler, når du registrerer datakilder i Azure datakatalog, og hvordan du bruger data profiler til at forstå datakilder."
    services="data-catalog"
    documentationCenter=""
    authors="spelluru"
    manager="NA"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/13/2016"
    ms.author="spelluru"/>

# <a name="data-profile-data-sources"></a>Data profil-datakilder

## <a name="introduction"></a>Introduktion

**Microsoft Azure datakatalog** er en fuldt administreret skybaseret tjeneste, der fungerer som et system af registrering og system af registrering til enterprise-datakilder. Med andre ord handler **Azure datakatalog** om hjælpe personer opdage, forstå og bruge datakilder, og hjælp organisationer til at få flere værdi fra deres eksisterende data. Når en datakilde er registreret med **Azure datakatalog**, dens metadata er kopieret og indekseret af tjenesten, men tekstenheden afslutte ikke der.

Funktionen **Data profiler** i **Azure datakatalog** undersøger data fra understøttede datakilder i kataloget og indsamler statistik og oplysninger om disse data. Det er nemt at medtage en profil af dine dataaktiver. Når du har registreret et data aktiv, kan du vælge **Medtag Data profil** i værktøjet til datakilde registrering.

## <a name="what-is-data-profiling"></a>Hvad er Data profiler

Data profiler undersøger data i den datakilde, der er registreret og indsamler statistik og oplysninger om disse data. Disse statistikker kan hjælpe dig med at bestemme egnet til dataene til at løse problemet deres business under kilde dataregistrering.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Følgende datakilder understøtter data profiler:

- SQL Server (herunder Azure SQL DB og Azure SQL Data Warehouse) tabeller og visninger
- Oracle tabeller og visninger
- Teradata tabeller og visninger
- Hive tabeller

Herunder data profiler, når registrering dataaktiver hjælper brugerne med at besvare spørgsmål om datakilder, herunder:

-   Kan den bruges til at løse problemet min business?
-   Dataene, der overholder bestemte standarder eller mønstre?
-   Hvad er nogle af afvigelser af datakilden?
-   Hvad er mulige udfordringer i forbindelse med integrering af disse data til mit program?

> [AZURE.NOTE] Du kan også føje dokumentation til et aktiv til at beskrive, hvordan data kan være integreret i et program. Se, [hvordan du dokument datakilder](data-catalog-how-to-documentation.md).


<a name="howto"/>
## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Sådan medtages en data-profil, når du registrerer en datakilde

Det er nemt at medtage en profil i datakilden. Når du har registreret en datakilde i panelet **objekter for at være registreret** i værktøjet til datakilde registrering, kan du vælge **Medtag Data profil**.

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

Hvis du vil vide mere om, hvordan du registrere datakilder, skal du se, [hvordan til at registrere datakilder](data-catalog-how-to-register.md) og [komme i gang med Azure datakatalog](data-catalog-get-started.md).


## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrering af dataaktiver, der indeholder data profiler
For at finde dataaktiver, der indeholder en data-profil, du kan medtage `has:tableDataProfiles` eller `has:columnsDataProfiles` som et af dine søgeord.

> [AZURE.NOTE] Vælge **Medtag Data profil** i værktøjet til datakilde registrering omfatter både tabel og kolonne niveau profiloplysninger. Data katalog API kan imidlertid data anlægsaktiver, der skal være registreret hos kun ét sæt profiloplysninger inkluderet.

## <a name="viewing-data-profile-information"></a>Få vist oplysninger om profil

Når du finder en passende datakilde med en profil, kan du få vist profildetaljer data. Vælg et aktiv data for at få vist profilen data, og vælg **Data profil** i vinduet datakatalog portalen.

![](media\data-catalog-data-profile\data-catalog-view.png)

En data profil i **Azure datakatalog** viser tabel og kolonne profil oplysninger, herunder:

### <a name="object-data-profile"></a>Objekt data profil

-   Antallet af rækker
-   Tabellens størrelse
-   Hvor objektet sidst blev opdateret

### <a name="column-data-profile"></a>Kolonnen data profil

- Kolonne-datatype
- Antallet af entydige værdier
- Antallet af rækker med NULL-værdier
- Minimum, maksimum, gennemsnit og standardafvigelse for kolonneværdier

## <a name="summary"></a>Oversigt
Data profiler indeholder statistik og oplysninger om Aktiver registrerede oplysninger kan hjælpe dig med at bestemme er egnet til dataene til at løse problemer. Sammen med anmærkninger, og dokumentere datakilder, kan data profiler give brugerne en dybere forståelse af dine data.


## <a name="see-also"></a>Se også
-   [Sådan registreres datakilder](data-catalog-how-to-register.md)
-   [Introduktion til Azure datakatalog](data-catalog-get-started.md)

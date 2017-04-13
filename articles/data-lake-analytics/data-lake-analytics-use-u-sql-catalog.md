<properties
   pageTitle="Introducere Azure Data sø Analytics U-SQL-katalog | Azure"
   description="Introducere Azure Data sø Analytics U-SQL-katalog"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="use-u-sql-catalog"></a>Brug U-SQL-katalog

U-SQL-kataloget bruges til at strukturere data og kode, så de kan deles af U-SQL-scripts. Kataloget gør det muligt med data i Azure Data sø højeste ydeevne.

Hver Azure Data sø Analytics-konto har ét U-SQL-katalog, der er knyttet til den. Du kan ikke slette kataloget U-SQL. U-SQL kataloger kan aktuelt ikke deles mellem sø datalager konti.

Hvert U-SQL-katalog indeholder en database, der kaldes **Master**. Master for databasen kan ikke slettes.  Hvert U-SQL-katalog kan indeholde flere flere databaser.

U-SQL-database indeholder:

- Assemblies – dele .NET kode mellem U-SQL-scripts.
- Resultatværdier funktioner – dele U-SQL-koden mellem U-SQL-scripts.
- Tabeller – dele data mellem U-SQL-scripts.
- Ved hjælp af skemaer - dele tabel skemaer mellem U-SQL-scripts.

## <a name="manage-catalogs"></a>Administrere kataloger
Hver Azure Data sø Analytics-konto har en standardkonto for Azure sø datalager, der er knyttet til den. Denne sø datalager konto kaldes sø datalager standardkontoen. U-SQL-katalog er gemt i sø datalager standardkontoen under mappen /catalog. Slet ikke alle filer i mappen /catalog.

### <a name="use-azure-portal"></a>Bruge Azure-portalen

Se [administrere Data sø analyser ved hjælp af portalen](data-lake-analytics-manage-use-portal.md#view-u-sql-catalog)


### <a name="use-data-lake-tools-for-visual-studio"></a>Bruge Data sø Tools til Visual Studio.

Du kan bruge Data sø Tools til Visual Studio til at administrere kataloget.  Du kan finde flere oplysninger om værktøjerne, [Ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

**Til at administrere kataloget**

1. Åbne Visual Studio, og oprette forbindelse til azure. Du kan finde instruktioner, [Opret forbindelse til Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
1. Åbn **Server Explorer** ved at trykke på **CTRL + ALT + S**.
2. Udvid **Azure**fra **Server Explorer**, udvide **Data sø Analytics**, udvide din Data sø Analytics-konto, udvid **databaser**, og udvid derefter **master**.



    - Tilføje en ny Database, skal du højreklikke på **databasen**, og klik derefter på **Opret Database**.
    - Tilføje en ny samling, højreklik på **Assemblies**, og klik derefter på **Registrer samling**.
    - Tilføje et nyt skema, skal du højreklikke på **ved hjælp af skemaer**, og klik derefter på "Opret skema **.
    - Hvis du vil tilføje en ny tabel, skal du højreklikke på **tabeller**, og klik derefter på "" oprette tabel **.
    - For at tilføje en ny værdi af tabelfunktion skal du se [udvikle U-SQL brugerdefinerede operatorer for Data sø Analytics sager](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Gennemse kataloger U-SQL Visual Studio](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## <a name="see-also"></a>Se også

- Komme i gang
    - [Komme i gang med Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-get-started-portal.md)
    - [Komme i gang med Data sø analyser ved hjælp af Azure PowerShell](data-lake-analytics-get-started-powershell.md)
    - [Komme i gang med Data sø analyser ved hjælp af Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
    - [Udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Introduktion til Azure Data sø Analytics U-SQL-sprog](data-lake-analytics-u-sql-get-started.md)

- U-SQL og udvikling
    - [Introduktion til Azure Data sø Analytics U-SQL-sprog](data-lake-analytics-u-sql-get-started.md)
    - [Bruge U-SQL-vinduet funktioner til Azure Data sø Analytics-job](data-lake-analytics-use-window-functions.md)
    - [Udvikle U-SQL brugerdefinerede operatorer til Data sø Analytics-job](data-lake-analytics-u-sql-develop-user-defined-operators.md)

- Administration
    - [Administrere Azure Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-manage-use-portal.md)
    - [Administrere Azure Data sø analyser ved hjælp af Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
    - [Overvåge og foretage fejlfinding af Azure Data sø Analytics job ved hjælp af Azure portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Selvstudium til slut
    - [Brug Azure Data sø Analytics interaktive selvstudier](data-lake-analytics-use-interactive-tutorials.md)
    - [Analysere websted logfiler, der bruger Azure Data sø Analytics](data-lake-analytics-analyze-weblogs.md)

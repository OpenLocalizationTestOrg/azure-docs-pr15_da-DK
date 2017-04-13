<properties
   pageTitle="Analysere websted logfiler, der bruger Azure Data sø Analytics | Azure"
   description="Lær at analysere websted logfiler, der bruger Data sø analyser. "
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

# <a name="tutorial-analyze-website-logs-using-azure-data-lake-analytics"></a>Selvstudium: Analysere websted logfiler, der bruger Azure Data sø Analytics

Lær at analysere websted logfiler, der bruger Data sø Analytics, især til at finde ud af, hvilke henvisninger opstod fejl, når de har forsøgt at besøge webstedet.

>[AZURE.NOTE] Hvis du blot ønsker at se det program, der arbejder, gemmer tid til at gennemgå [Brug Azure Data sø Analytics interaktive selvstudier](data-lake-analytics-use-interactive-tutorials.md). Dette selvstudium er baseret på det samme scenario og den samme kode. Formålet med dette selvstudium er at give udviklere erfaring med at oprette og køre en Data sø Analytics-tjenesteprogrammet fra start til slut.

## <a name="prerequisites"></a>Forudsætninger for:

- **Visual Studio 2015, Visual Studio 2013 opdatere 4, eller Visual Studio 2012 med Visual C++ installeret**.
- **Microsoft Azure SDK til .NET version 2.5 eller nyere**.  Installere det ved hjælp af [Web platform installer](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Data sø Tools til Visual Studio](http://aka.ms/adltoolsvs)**.

    Når Data sø Tools til Visual Studio er installeret, vises der en **Data sø** menu i Visual Studio:

    ![U-SQL Visual Studio-menuen](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Grundlæggende kendskab til Data sø analyser og sø Data Tools til Visual Studio**. For at komme i gang skal du se:

    - [Introduktion til Azure Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-get-started-portal.md).
    - [Udvikle U-SQL-script ved hjælp af Data sø værktøjer til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

- **En Data sø Analytics-konto.**  Se [oprette en Azure Data sø Analytics-konto](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

    Sø Dataværktøjer understøtter ikke oprettelse af Data sø Analytics-konti.  Så skal du oprette den med Azure-portalen, Azure PowerShell, .NET SDK eller Azure CLI.
- **Overføre eksempeldataene til Data sø Analytics-kontoen.** Se [Overføre SearchLog.tsv til lagring af Data sø standardkonto](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    Hvis du vil køre et Data sø Analytics-job, skal du nogle data. Selvom sø Dataværktøjer understøtter blive overført data, skal bruge du portalen til at overføre eksempeldata for at gøre det nemmere at følge dette selvstudium.

## <a name="connect-to-azure"></a>Oprette forbindelse til Azure

Før du kan oprette og teste eventuelle U-SQL-scripts, skal du først opretter forbindelse til Azure.

**Oprette forbindelse til Data sø Analytics**

1. Åbne Visual Studio.
2. Klik på **Indstillinger og indstillinger**i menuen **Data sø** .
4. Klik på **Log på**eller **Skift bruger** , hvis nogen har logget på, og følg vejledningen.
5. Klik på **OK** for at lukke dialogboksen Indstillinger og indstillinger.

**Gennemse dine Data sø Analytics-konti**

1. Fra Visual Studio, skal du åbne **Server Explorer** ved at trykke på **CTRL + ALT + S**.
2. Udvid **Azure**fra **Server Explorer**, og udvid derefter **Data sø Analytics**. Du skal se en liste over dine Data sø Analytics-konti, hvis der ikke er nogen. Du kan ikke oprette Data sø Analytics konti fra studio. Se [Introduktion til Azure Data sø analyser ved hjælp af Azure Portal](data-lake-analytics-get-started-portal.md) eller [Komme i gang med Azure Data sø analyser ved hjælp af Azure PowerShell](data-lake-analytics-get-started-powershell.md)for at oprette en konto.

## <a name="develop-u-sql-application"></a>Udvikle U-SQL-program

En U-SQL-programmet er hovedsageligt et U-SQL-script. Hvis du vil vide mere om U-SQL, kan du se [komme i gang med U-SQL](data-lake-analytics-u-sql-get-started.md).

Du kan tilføje tilføjelse brugerdefinerede operatorer til programmet.  Du kan finde flere oplysninger, se [udvikle U-SQL brugerdefinerede operatorer for Data sø Analytics sager](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Oprette og sende et Data sø Analytics-job**

1. Klik på **Ny**i menuen **filer** , og klik derefter på **projekt**.
2. Vælg projekttype U-SQL.

    ![nyt U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Klik på **OK**. Visual studio opretter en løsning med en Script.usql fil.
4. Angiv følgende script i filen Script.usql:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    For at forstå U-SQL-Sætningen, kan du se [komme i gang med Data sø Analytics U-SQL-sprog](data-lake-analytics-u-sql-get-started.md).    

5. Tilføje et nyt U-SQL-script til dit projekt, og Angiv følgende oplysninger:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();

6. Skifte tilbage til det første U-SQL-script og ud for knappen **Send** , skal du angive din Analytics-konto.
7. Højreklik på **Script.usql**fra **Solution Explorer**, og klik derefter på **Opret Script**. Bekræfte resultaterne i ruden Output.
8. Højreklik på **Script.usql**fra **Solution Explorer**, og klik derefter på **Send Script**.
9. Kontrollere **Analytics-konto** er samme sted, hvor du vil køre jobbet, og klik derefter på **Send**. Resultater for afsendelse og joblink er tilgængelige i sø Data Tools til Visual Studio, medfører det vindue, når bidrag er fuldført.
10. Vent, indtil jobbet er fuldført.  Hvis jobbet mislykkedes, det sandsynligvis mangler kildefilen.  Se afsnittet nødvendige i dette selvstudium. Du kan finde flere oplysninger om fejlfinding, under [skærm og foretage fejlfinding af Azure Data sø Analytics job](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Når jobbet er fuldført, skal du se følgende skærmbillede:

    ![data sø analytics analysere weblogs websted logfiler](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Gentag trin 7 - 10 nu for **Script1.usql**.

>[AZURE.NOTE]Du kan ikke læse fra eller skrive til en U-SQL-tabel, der er oprettet eller ændret i det samme script.  Det er grunden Brug Brug to scripts i dette eksempel.

**Se tingene output**

1. Udvid **Azure**fra **Server Explorer**, udvide **Data sø Analytics**, udvide din Data sø Analytics-konto, udvide **Lagerplads konti**, skal du højreklikke på sø datalagring standardkontoen, og klik derefter på **Stifinder**.
2.  Dobbeltklik på **eksempler** for at åbne mappen, og dobbeltklik derefter på **output**.
3.  Dobbeltklik på **UnsuccessfulResponsees.log**.
4.  Du kan også dobbeltklikke outputfilen i visningen graph for jobbet for at gå direkte til output.

## <a name="see-also"></a>Se også

For at komme i gang med Data sø analyser ved hjælp af forskellige værktøjer skal du se:

- [Komme i gang med Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-get-started-portal.md)
- [Komme i gang med Data sø analyser ved hjælp af Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Komme i gang med Data sø analyser ved hjælp af .NET SDK](data-lake-analytics-get-started-net-sdk.md)

For at se flere udviklingsemner i:

- [Udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Introduktion til Azure Data sø Analytics U-SQL-sprog](data-lake-analytics-u-sql-get-started.md)
- [Udvikle U-SQL brugerdefinerede operatorer til Data sø Analytics-job](data-lake-analytics-u-sql-develop-user-defined-operators.md)

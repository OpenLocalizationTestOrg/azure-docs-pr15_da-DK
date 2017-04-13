<properties 
   pageTitle="Oversigt over Microsoft Azure Data sø Analytics | Azure" 
   description="Data sø Analytics er en Azure Big Data beregning tjeneste, hvor du kan bruge data til at drive din virksomhed bruger indsigt erfaringer fra dine data i skyen, uanset hvor det er og uanset dets størrelse. Data sø Analytics aktiverer dette i den enkleste mest SVG, og så økonomisk som muligt. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Oversigt over Microsoft Azure Data sø Analytics

## <a name="what-is-azure-data-lake-analytics"></a>Hvad er Azure Data sø Analytics?

Azure Data sø Analytics er en ny tjeneste, der er udviklet til at overskueligt stor data analytics. Denne tjeneste kan du fokusere på skrivning, kører og administrere jobs i stedet for operativsystem distribueret infrastruktur. I stedet for installation, konfiguration og justering hardware, skal skrive du forespørgsler for at transformere dataene og udtrække værdifuld indsigt. Tjenesten analytics kan håndtere job i løbet af en hvilken som helst skala med det samme ved at angive blot opkald til hvor meget power, du har brug for. Du betaler kun til dit arbejde, når den kører gør det økonomisk. Tjenesten analytics understøtter Azure Active Directory, så du kan blot administrere adgang og roller, der er integreret med dit lokale identitetssystem. Den indeholder også U-SQL, et sprog, der samler fordelene ved SQL med udtryksfuld fordelene ved brugerkode. U-SQL SVG fordelt runtime gør det muligt til effektivt at analysere data i store og på tværs af SQL-servere i Azure, Azure SQL-Database og Azure SQL Data Warehouse.


## <a name="key-capabilities"></a>Vigtige funktioner

- **Dynamisk skalering** 

    Data sø Analytics er designet fra bunden til skyen skala og ydeevne.  Dynamisk bestemmelser ressourcer og kan du gøre analytics på TB eller lige Exabyte af data. Når jobbet er fuldført, den vinde ned ressourcer automatisk, og du betaler kun for behandling power bruges. Når du forøge eller formindske størrelsen på data, der gemmes eller mængden Beregn bruges, kan du ikke behøver at omskrive kode. På denne måde kan du fokusere på dine forretningslogik kun og ikke om, hvordan du behandle og gemme store datasæt. 

- **Udvikle hurtigere, fejlfinding og optimere smartere ved hjælp af velkendte værktøjer**

    Data sø Analytics har tæt integration med Visual Studio, så du kan bruge velkendte værktøjer til at køre, teste og finjustere din kode. Visualiseringer af din U-SQL-job kan du se, hvordan din kode kører skaleres, så du nemt kan identificere ydeevne flaskehalse og optimere omkostninger. 

- **U-SQL: fra enkle og velkendte, effektive og extensible**

    Data sø Analytics omfatter U-SQL, et forespørgselssprog, der udvider SQL velkendte, enkle, deklarative art med udtryksfuld styrken i C#. U-SQL-sproget er bygget på den samme fordelt runtime, der styrer stor datasystemer i Microsoft. Millioner af SQL og .NET udviklere kan nu behandle og analysere alle deres data med de færdigheder, de allerede har.

- **Integreres problemfrit sammen med din IT-investeringer**

    Data sø Analytics kan bruge din eksisterende IT-investeringer til identitet, administration, sikkerhed og opbevaring af data. Dette forenkler data styring og gør det nemt at udvide dine aktuelle dataprogrammer. Data sø Analytics er integreret med Active Directory til brugeradministration og tilladelser og leveres med indbygget overvågning og overvågning.

- **Økonomisk og rentable**

    Data sø Analytics er en økonomisk løsning til kørsel af arbejdsbelastninger, som stor data. Du betaler på grundlag-job, når data behandles. Der kræves ingen hardware, licenser eller tjenestens supportaftaler. Systemet skaleres automatisk op eller ned som jobbet starter og er fuldført, hvilket betyder, at du aldrig betale for mere end det, du har brug for. 

- **Fungerer med alle dine Azure-Data**

    Data sø Analytics kan arbejde med et antal Azure datakilder: Azure Blob-lager, Azure SQL-database og naturligvis Data sø Analytics er særligt optimeret til at arbejde med Azure sø datalager - give det højeste niveau af ydeevne, overførselshastighed og parallelization for dig stor data arbejdsmængder.

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
    - [Overvåge og foretage fejlfinding af Azure Data sø Analytics job ved hjælp af Azure-portalen](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
    - [Få adgang til diagnosticering logfiler for Azure Data sø analyser](data-lake-analytics-diagnostic-logs.md)

- Selvstudium til slut
    - [Brug Azure Data sø Analytics interaktive selvstudier](data-lake-analytics-use-interactive-tutorials.md)
    - [Analysere websted logfiler, der bruger Azure Data sø Analytics](data-lake-analytics-analyze-weblogs.md)

- Fortæl os, hvad du mener
    - [Skrive en kommentar til vores dokumentation ordrebeholdning](data-lake-analytics-documentation-backlog.md)
    - [Sende en anmodning om funktion](http://aka.ms/adlafeedback)
    - [Få hjælp i forummerne](http://aka.ms/adlaforums)



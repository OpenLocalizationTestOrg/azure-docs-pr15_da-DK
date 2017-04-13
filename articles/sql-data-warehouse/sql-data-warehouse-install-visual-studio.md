<properties
   pageTitle="Installere Visual Studio og SSDT til SQL datawarehouse | Microsoft Azure"
   description="Installere Visual Studio og SQL Server Development Tools (SSDT) til Azure SQL datawarehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Installere Visual Studio-2015 og SSDT til SQL datawarehouse

For at udvikle programmer til SQL Data Warehouse, anbefaler vi, at du bruger Visual Studio-2015 med den seneste version af SQL Server Data Tools (SSDT).  Visual Studio 2013 Update 5 med SSDT understøttes også for at opnå bagudkompatibilitet.  

Brug af Visual Studio med SSDT, så du kan bruge SQL Server Object Explorer til visuelt udforske tabeller, visninger, lagrede procedurer og mange flere objekter i dine SQL Data Warehouse samt køre forespørgsler.

> [AZURE.NOTE] SQL Data Warehouse understøtter ikke Visual Studio databaseprojekter endnu.  Denne funktion tilføjes i fremtidige versioner.

## <a name="step-1-install-visual-studio-2015"></a>Trin 1: Installer Visual Studio 2015

Følg disse links for at hente og installere Visual Studio-2015. Hvis du allerede har Visual Studio 2013 eller 2015 installeret, kan du gå videre til trin 2, skal du installere SSDT.

1. [Hente Visual Studio-2015][].
2. Følg guiden [Installation af Visual Studio][] på MSDN og vælge standardkonfigurationer.

## <a name="step-2-install-ssdt"></a>Trin 2: Installer SSDT

Hvis du vil installere SSDT til Visual Studio blot søge efter en SSDT opdatering fra i Visual Studio ved at følge disse trin.

1. Klik på **Funktioner**i Visual Studio / **Extensions og opdateringer...**  /  **Opdateringer**
2. Vælg **Produktopdateringer** og find derefter frem til **Microsoft SQL Server-opdatering til database værktøjer**

Hvis der ikke findes en opdatering, bør du have den nyeste version, der er installeret.  Klik på **Hjælp**for at bekræfte SSDT er installeret, / **Om Microsoft Visual Studio** og se efter SQL Server Data Tools på listen.  Den seneste version af SSDT er 14.0.60525.0.  Hvis mulighed for at installere ikke er tilgængelig fra Visual Studio, kan du også besøge siden [SSDT Hent][] for at hente og installere SSDT manuelt.

## <a name="next-steps"></a>Næste trin

Nu hvor du har den nyeste version af SSDT, er du klar til at [oprette forbindelse][] til din SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[oprette forbindelse]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Hente Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Installation af Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Hent]: https://msdn.microsoft.com/library/mt204009.aspx

<properties
    pageTitle="Lav sikkerhedskopier af Stræk-kompatible databaser | Microsoft Azure"
    description="Lær, hvordan du sikkerhedskopiere Stræk\-aktiveret databaser."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="douglasl"/>

# <a name="backup-stretch-enabled-databases"></a>Sikkerhedskopiering Stræk-kompatible databaser

Databasesikkerhedskopier hjælpe dig med at gendanne fra mange forskellige typer af mislykkede forsøg, fejl og nedbrud.  

-   Du skal sikkerhedskopiere din Stræk\-aktiveret SQL Server-databaser.  

-   Microsoft Azure sikkerhedskopierer automatisk de eksterne data, Stræk Database er overført fra SQL Server til Azure.  

>    [AZURE.NOTE] Sikkerhedskopi er kun én del af en komplet høj tilgængelighed og business løsning. Du kan finde flere oplysninger om høj tilgængelighed, [Høj tilgængelighed løsninger](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="back-up-your-sql-server-data"></a>Sikkerhedskopiere dine SQL Server-data  

Sikkerhedskopiere din Stræk\-aktiveret SQL Server-databaser, du kan fortsætte med at bruge de SQL Server sikkerhedskopiering metoder, du aktuelt bruger. Flere oplysninger i [sikkerhedskopiere og gendanne af SQL Server-databaser](https://msdn.microsoft.com/library/ms187048.aspx).

Sikkerhedskopier af en Stræk aktiverede SQL Server-database indeholde kun lokale data og modtage data overførsel på sted i gang, når sikkerhedskopieringen kører. \(Gyldigt data er data, der endnu ikke er blevet overført, men der overføres til Azure baseret på indstillingerne for overførsel af tabellerne.\) Dette kaldes en **shallow** sikkerhedskopi og omfatter ikke dataene, der allerede er overført til Azure.  

## <a name="back-up-your-remote-azure-data"></a>Sikkerhedskopiere dine remote Azure data   

Microsoft Azure sikkerhedskopierer automatisk de eksterne data, Stræk Database er overført fra SQL Server til Azure.  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure reducerer risikoen for datatab med automatisk sikkerhedskopiering  
Tjenesten SQL Server-Stræk Database på Azure beskytter dine remote databaser med automatisk lagring snapshots mindst én gang otte timer. Hver øjebliksbillede 7 dage at give dig en række mulige gendannelsespunkter bevares.  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure reducerer risikoen for datatab med geografisk\-redundans  
Azure databasen er gemt på geografisk\-overflødige Azure-lager (RA\-GRS) og er derfor geografisk\-overflødige som standard. Geografisk\-overflødige lagerplads kopieres dataene til en sekundær område, der er hundredvis af sømil væk fra det primære område. I både primære og sekundære områder replikeres dataene tre gange hver, på tværs af separat fejl domæner og opgradering domæner. Dette sikrer, at dine data er robust selv i tilfælde af en komplet internationale afbrydelse eller nedbrud, der gengiver en af de Azure områder ikke tilgængelig.

### <a name="stretchRPO"></a>Stræk Database reducerer risikoen for tab af data for dine Azure data ved at beholde overførte rækker midlertidigt
Når Stræk Database overfører berettiget rækker fra SQL Server til Azure, bevares rækkerne i tabellen midlertidige for et minimum på 8 timer. Hvis du gendanner en sikkerhedskopi af databasen Azure, anvender Stræk databasen de rækker, der er gemt i tabellen midlertidige afstemme SQL Server og Azure-databaser.

Når du gendanner en sikkerhedskopi af dine Azure data, du skal køre den lagrede procedure [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) for at genoprette forbindelsen på Stræk\-aktiveret SQL Server-database til Azure-fjerndatabase. Når du kører **sys.sp_rda_reauthorize_db**, afstemmer Stræk Database automatisk SQL Server og Azure-databaser.

Kør lagret procedure [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) for at øge antallet af overførte data, der Stræk Database bevarer midlertidigt i tabellen midlertidige timer, og Angiv et antal timer, der er større end 8. For at afgøre, hvor meget data til at bevare, skal du overveje følgende faktorer:
-   Hyppigheden for automatisk Azure sikkerhedskopier (mindst én gang 8 timer).
-   Den tid, når du har problemer med at genkende problemet og beslutter dig for at gendanne en sikkerhedskopi.
-   Varigheden af Azure gendannelsen.

> [AZURE.NOTE] Øge mængden af data, Stræk Database bevarer midlertidigt i tabellen midlertidige øger mængden af plads på SQL Server.

Køre den lagrede procedure [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx)for at kontrollere antallet timer med data, som Stræk Database i øjeblikket bevarer midlertidigt i tabellen midlertidige.

## <a name="see-also"></a>Se også

[Administrere og foretage fejlfinding af Stræk Database](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Sikkerhedskopiere og gendanne af SQL Server-databaser](https://msdn.microsoft.com/library/ms187048.aspx)

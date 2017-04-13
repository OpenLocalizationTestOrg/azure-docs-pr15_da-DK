<properties
    pageTitle="Gendanne Stræk-kompatible databaser | Microsoft Azure"
    description="Lær, hvordan du kan gendanne Stræk\-aktiveret databaser."
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
    ms.date="08/01/2016"
    ms.author="douglasl"/>

# <a name="restore-stretch-enabled-databases"></a>Gendanne Stræk-kompatible databaser

Gendanne en sikkerhedskopierede database, når det er nødvendigt til at gendanne fra mange forskellige typer af mislykkede forsøg, fejl og nedbrud.

Du kan finde flere oplysninger om sikkerhedskopiering, [Sikkerhedskopiering Stræk-kompatible databaser](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] Sikkerhedskopi er kun én del af en komplet høj tilgængelighed og business løsning. Du kan finde flere oplysninger om høj tilgængelighed, [Høj tilgængelighed løsninger](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="restore-your-sql-server-data"></a>Gendanne dine SQL Server-data
Hvis du vil gendanne fra hardwarefejl eller beskadigelse, gendanne Stræk\-aktiveret SQL Server-database fra en sikkerhedskopi. Du kan fortsætte med at bruge de SQL Server Gendan metoder, du aktuelt bruger. Flere oplysninger i [Oversigt over gendannelse og Gendan](https://msdn.microsoft.com/library/ms191253.aspx).

Når du gendanner SQL Server-database, du skal køre den lagrede procedure **sys.sp_rda_reauthorize_db** for at genoprette forbindelsen mellem Stræk\-aktiveret SQL Server-database og Azure fjerndatabasen. Du finder flere oplysninger i [Gendan forbindelsen mellem SQL Server-databasen og Azure fjerndatabasen](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database).

## <a name="restore-your-remote-azure-data"></a>Gendanne din Azure fjerndata

### <a name="recover-a-live-azure-database"></a>Gendanne en live Azure-database
SQL Server-databasen Stræk tjeneste på Azure snapshots alle dynamiske data mindst én gang ved hjælp af Azure lagerplads Snapshots otte timer. Disse snapshots vedligeholdes for 7 dage. Dette giver dig mulighed at gendanne dataene til en af mindst 21 punkter tidspunkt inden for de seneste 7 dage op til det tidspunkt, hvor det sidste snapshot blev taget.

Hvis du vil gendanne en live Azure-database til et tidligere tidspunkt ved hjælp af portalen Azure, skal du gøre følgende ting.

1. Log på portalen Azure.
2. Vælg **Gennemse** , og vælg derefter **SQL-databaser**i venstre side af skærmen.
3. Gå til din database, og vælg det.
4. Klik på **Gendan**øverst i bladet database.
5. Angive et nyt **Databasenavn**, Vælg et **Gendan punkt** , og klik derefter på **Opret**.
6. Gendannelsen database påbegyndes og kan overvåges ved hjælp af **meddelelser**.

### <a name="recover-a-deleted-azure-database"></a>Gendanne en slettet Azure database
Tjenesten SQL Server-Stræk Database på Azure tager et snapshot af en database, før en database er sluppet og bevarer for 7 dage. Når dette sker, bevarer den ikke længere snapshots fra direkte databasen. Her kan du gendanne en slettet database til punktet, når den er blevet slettet.

For at gendanne en slettet Azure database til punktet, når den er blevet slettet ved hjælp af portalen Azure skal du gøre følgende ting.

1. Log på portalen Azure.
2. Vælg **Gennemse** , og vælg derefter **SQL-servere**i venstre side af skærmen.
3. Gå til din server og vælge den.
4. Rul ned til handlinger på din server blade, klik på feltet **Slettet databaser** .
5. Vælg den slettede database, du vil gendanne.
5. Angiv et nyt **Databasenavn** , og klik på **Opret**.
6. Gendannelsen database påbegyndes og kan overvåges ved hjælp af **meddelelser**.

## <a name="restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database"></a>Gendanne forbindelsen mellem SQL Server-databasen og Azure fjerndatabasen

1.  Hvis du vil oprette forbindelse til en gendannede Azure-database med et andet navn eller i et andet område, du Kør den lagrede procedure [sys.sp_rda_deauthorize_db](https://msdn.microsoft.com/library/mt703716.aspx) til at afbryde forbindelsen fra den forrige Azure database.  

2.  Køre den lagrede procedure [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) for at forbinde lokale Stræk\-aktiveret database til Azure-databasen.  

    -   Angiver de eksisterende database, der er fastsat legitimationsoplysninger som en sysnavn eller en varchar\(128\) værdi. \(Brug ikke varchar\(Maks\).\) Du kan søge efter navnet på legitimationsoplysninger i visningen **sys.database\_fastsat\_legitimationsoplysninger**.  

    -   Angiv, om du vil oprette en kopi af dataene og oprette forbindelse til den kopi (anbefales).  

    ```tsql  
    USE <Stretch-enabled database name>;
    GO
    EXEC sp_rda_reauthorize_db
        @credential = N'<existing_database_scoped_credential_name>',
        @with_copy = 1 ;  
    GO
    ```  

## <a name="see-also"></a>Se også

[Administrere og foretage fejlfinding af Stræk Database](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Sikkerhedskopiere og gendanne af SQL Server-databaser](https://msdn.microsoft.com/library/ms187048.aspx)

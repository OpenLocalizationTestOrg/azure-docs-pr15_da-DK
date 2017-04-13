<properties
   pageTitle="Sikre en database i SQL Data Warehouse | Microsoft Azure"
   description="Tip til sikring af en database i Azure SQL Data Warehouse til udvikling af løsninger."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="secure-a-database-in-sql-data-warehouse"></a>Sikre en database i SQL Data Warehouse

> [AZURE.SELECTOR]
- [Oversigt over sikkerhed](sql-data-warehouse-overview-manage-security.md)
- [Godkendelse](sql-data-warehouse-authentication.md)
- [Kryptering (Portal)](sql-data-warehouse-encryption-tde.md)
- [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

I denne artikel vejledes gennem de grundlæggende regler for sikring af databasen Azure SQL Data Warehouse. Især i denne artikel får du i gang med ressourcer for at begrænse adgangen, beskyttelse af data og overvåge aktiviteter på en database.

## <a name="connection-security"></a>Sikre forbindelser

Sikre forbindelser refererer til hvordan du begrænse og sikre forbindelser til databasen ved hjælp af firewallregler og forbindelse kryptering.

Firewallregler bruges af både serveren og databasen til at afvise forsøg forbindelser fra IP-adresser, der ikke er eksplicit whitelisted. Hvis du vil tillade forbindelser fra dit program eller klient maskine offentlige IP-adresse, skal du først oprette en serverniveau firewallregel, der bruger Azure-portalen, REST-API eller PowerShell. Som en bedste fremgangsmåde, skal du begrænse IP-adresseområder tilladt via din server firewall som muligt.  For at få adgang til Azure SQL Data Warehouse fra din lokale computer, Kontrollér, at firewallen på dit netværk og din lokale computer tillader udgående kommunikation på TCP-port 1433.  Se [Azure SQL-Database firewall][], [sp_set_firewall_rule][]og [sp_set_database_firewall_rule][]kan finde flere oplysninger.

Forbindelser til din SQL Data Warehouse er krypteret som standard.  Ændre indstillinger for forbindelsen til at deaktivere kryptering ignoreres.

## <a name="authentication"></a>Godkendelse

Godkendelse refererer til, hvordan du bevise din identitet, når du opretter forbindelse til databasen. SQL Data Warehouse understøtter i øjeblikket SQL Server-godkendelse med et brugernavn og adgangskode samt en Azure Active Directory. 

Når du har oprettet logiske serveren for din database, du har angivet et "server admin" logon med et brugernavn og din adgangskode. Ved hjælp af disse legitimationsoplysninger, kan du godkende til en database på serveren som databaseejer eller "dbo" via SQL Server-godkendelse.

Men som en bedste fremgangsmåde organisationens brugere skal bruge en anden konto til at godkende. Denne måde, som du kan begrænse tilladelserne til programmet og reducere risikoen for skadelig aktivitet, i tilfælde af, at din programkode er udsatte til en SQL-injektionsangreb. 

For at oprette en SQL Server-godkendt bruger, skal du oprette forbindelse til **master** databasen på din server med din server administrator logon og oprette en ny server-logon.  Desuden er det en god ide at oprette en bruger i den overordnede database til Azure SQL Data Warehouse brugere. Oprette en bruger i master gør det muligt for en bruger til at logge på ved hjælp af værktøjer som SSMS uden at angive et navn på databasen.  Det gør det også dem om at bruge objekt Stifinder til at få vist alle databaser på en SQL server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Derefter oprette forbindelse til **SQL Data Warehouse database** med din server administrator logon og oprette databasebruger baseret på den server-logon, du lige har oprettet.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Hvis en bruger skal udføre flere handlinger som oprette logon eller oprette nye databaser, skal de også skal tildeles den `Loginmanager` og `dbmanager` roller i den overordnede database. Se [administrere databaser og logon i Azure SQL-Database][]kan finde flere oplysninger om disse yderligere roller og godkendelse til en SQL-Database.  Flere oplysninger om Azure AD for SQL Data Warehouse, i [tilslutning til SQL Data Warehouse ved brug af Azure Active Directory-godkendelse][].


## <a name="authorization"></a>Godkendelse

Godkendelse refererer til hvad du kan gøre i en Data Warehouse Azure SQL-database, og dette styres af din brugerkonto medlemskab og tilladelser. Som en bedste fremgangsmåde, skal du give brugerne de nødvendige færrest rettigheder. Azure SQL Data Warehouse gør det nemt at administrere med roller i T-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Administratorkonto server, du opretter forbindelse med er medlem af db_owner, som har tilladelse til at gøre noget i databasen. Gem denne konto til implementering af skema opgraderinger og andre handlinger i administration. Bruge kontoen "ApplicationUser" med mere begrænsede tilladelser til at oprette forbindelse fra dit program til databasen med de færreste rettigheder, der kræves af programmet.

Der er måder til yderligere at begrænse, hvad en bruger kan gøre med Azure SQL-Database:

- Detaljerede [tilladelser][] kan du styre, hvilke handlinger, du kan gøre på enkelte kolonner, tabeller, visninger, procedurer og andre objekter i databasen. Bruge detaljerede tilladelser til at have de fleste kontrolelementet, og give de nødvendige mindste tilladelser. Findelt tilladelse systemet er lidt komplicerede og kræver, at nogle prøve at bruge effektivt.
- [Databaseroller][] end db_datareader og db_datawriter kan bruges til at oprette en mere effektiv metode program-brugerkonti eller mindre effektiv management-konti. De indbyggede faste databaseroller en nem måde at give tilladelser, men kan medføre give flere tilladelser end nødvendigt.
- [Lagrede procedurer][] kan bruges til at begrænse de handlinger, der kan udføres på databasen.

Administration af databaser og logiske servere fra portalen Azure klassisk eller bruge Azure ressourcestyring API styres af din portalen brugerkonto rolletildelinger. Du kan finde flere oplysninger om dette emne, [Rollebaseret adgangskontrol Azure-portalen][].

## <a name="encryption"></a>Kryptering

Azure SQL Data Warehouse gennemsigtig Data kryptering (TDE) hjælper med at beskytte mod truslen om skadelig aktivitet ved at udføre realtid kryptering og dekryptering af dine data på resten.  Når du krypterer databasen, krypteres tilknyttede sikkerhedskopier og transaktionslogfiler uden at ændringerne i dine programmer. TDE krypterer opbevaring af en hel database ved hjælp af en symmetriske nøgle, kaldes krypteringsnøglen database. Krypteringsnøglen database er beskyttet af et indbygget servercertifikat i SQL-Database. Den indbyggede servercertifikat er entydige for hver SQL-databaseserver. Microsoft roterer automatisk certifikaterne mindst én gang 90 dage. Den krypteringsalgoritme, der bruges af SQL Data Warehouse er AES 256. Du kan finde en generel beskrivelse af TDE [Gennemsigtig kryptering af Data][].

Du kan kryptere databasen ved hjælp af [Azure Portal] [ Encryption with Portal] eller [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Næste trin

Detaljer og eksempler på at oprette forbindelse til din SQL Data Warehouse med forskellige protokoller, under [oprette forbindelse til SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Oprette forbindelse til SQL datawarehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Oprette forbindelse til SQL datawarehouse ved hjælp af Azure Active Directory-godkendelse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL-Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Databaseroller]: https://msdn.microsoft.com/library/ms189121.aspx
[Administration af databaser og logon i Azure SQL-Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Tilladelser]: https://msdn.microsoft.com/library/ms191291.aspx
[Lagrede procedurer]: https://msdn.microsoft.com/library/ms190782.aspx
[Kryptering af gennemsigtig Data]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Rollebaseret adgangskontrol Azure-portalen]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure

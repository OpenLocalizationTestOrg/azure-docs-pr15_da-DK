<properties
   pageTitle="Retningslinjer for Azure SQL Database sikkerhed og begrænsninger | Microsoft Azure"
   description="Få mere at vide om Microsoft Azure SQL Database retningslinjer og begrænsninger i forbindelse med sikkerhed."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/18/2016"
   ms.author="rickbyh"/>

# <a name="azure-sql-database-security-guidelines-and-limitations"></a>Retningslinjer for Azure SQL-Database sikkerhed og begrænsninger

Dette emne beskrives Microsoft Azure SQL Database retningslinjer og begrænsninger i forbindelse med sikkerhed. Overvej følgende punkter, når du administrerer sikkerheden for dine Azure SQL-databaser.

## <a name="access-to-the-virtual-master-database"></a>Adgang til den virtuelle master database

Typisk have er kun administratorer adgang til master databasen. Rutinemæssige adgang til brugerdatabasen hver skal være gennem uden administratorrettigheder indeholdte databasebrugere, der er oprettet i hver database. Når du bruger indeholdte databasebrugere, behøver du ikke at oprette logon i den overordnede database. Du kan finde flere oplysninger, se [Indeholdt databasebrugere - gøre din Database flytbare](https://msdn.microsoft.com/library/ff929188.aspx).


## <a name="firewall"></a>Firewall

SQL Server-firewall, som er fastsat dynamisk for hele Azure SQL Server er som regel konfigureret via portalen og kun skal modtage de IP-adresser, der bruges af administratorer. Oprette forbindelse til en brugerdatabase for at oprette en database-fastsat firewallregel, der åbner de nødvendige område af IP-adresser for hver database, og brug derefter [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) Transact-SQL-sætning.

Tjenesten Azure SQL-Database er kun tilgængelig via TCP-port 1433. Sørg for, at din klient computerfirewall tillader udgående TCP-kommunikation på TCP-port 1433 for at få adgang til en SQL-Database fra din computer. Hvis det er ikke nødvendigt til andre programmer, kan du blokere indgående forbindelser på TCP-port 1433. 

Som en del af forbindelsesprocessen omdirigeres forbindelser fra Azure virtuelle maskiner til en anden IP-adresse og port, entydige for hver arbejder rolle. Portnummeret er i intervallet fra 11000 til 11999. Du kan finde flere oplysninger om TCP-porte, [porte ud over 1433 til ADO.NET 4.5 og version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="authentication"></a>Godkendelse

Brug Active Directory-godkendelse (integreret sikkerhed) når det er muligt. Se oplysninger om konfiguration af AD authentication [tilslutning til SQL Database ved hjælp af Azure Active Directory Authentication](sql-database-aad-authentication.md)og [vælge en anden godkendelsestilstand](https://msdn.microsoft.com/library/ms144284.aspx) i SQL Server bøger Online. 

Bruge indeholdte databasebrugere til at forbedre skalerbarhed. Se [Indeholdt databasebrugere - gøre din Database Portable](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx)og [Indeholdt databaser](https://technet.microsoft.com/library/ff929071.aspx)kan finde flere oplysninger.

Database Engine lukker forbindelser, der forbliver inaktiv i mere end 30 minutter. Forbindelsen skal login igen, før den kan bruges.

Løbende aktive forbindelser til SQL-Database kræver reauthorization (udført af Database Engine) mindst én gang 10 timer. Database Engine forsøger reauthorization ved hjælp af oprindeligt sendte adgangskoden, og der kræves ingen brugerinput. For at forbedre ydeevnen når en adgangskode er nulstillet i SQL-Database, der ikke forbindelse være skulle godkendes igen, selvom forbindelsen nulstilles på grund af gruppering af forbindelse. Dette er anderledes end funktionsmåden for lokal SQL Server. Hvis adgangskoden er blevet ændret, da forbindelsen blev oprindeligt godkendt, forbindelsen skal være afsluttet, og en ny forbindelse foretaget ved hjælp af den nye adgangskode. En bruger med tilladelse til at AFBRYDE DATABASEFORBINDELSE kan eksplicit afslutte en forbindelse til SQL-Database ved hjælp af kommandoen [Slet](https://msdn.microsoft.com/library/ms173730.aspx) .

## <a name="logins-and-users"></a>Logon og brugere

Når du administrerer logon og brugere i SQL-Database, er der begrænsninger.


- Du skal være forbundet med **master** databasen, når du kører den ``CREATE/ALTER/DROP DATABASE`` sætninger. -Databasebruger i den overordnede database, der svarer til det vigtigste serverniveau logon ikke ændres eller fjernet. 
- Engelsk (USA) er standardsproget for serverniveau vigtigste logon.
- Kun administratorer (serverniveau vigtigste login eller Azure AD-administrator) og medlemmer af rollen **dbmanager** database i den **overordnede** database har tilladelse til at udføre de ``CREATE DATABASE`` og ``DROP DATABASE`` sætninger.
- Du skal være forbundet med master databasen, når du kører den ``CREATE/ALTER/DROP LOGIN`` sætninger. Men ved hjælp af logon er ikke bør. I stedet bruge indeholdte databasebrugere.
- Hvis du vil oprette forbindelse til en brugerdatabase, skal du angive navnet på databasen i forbindelsesstrengen.
- Kun serverniveau vigtigste logon og medlemmer af rollen **loginmanager** database i **master** databasen har tilladelse til at udføre de ``CREATE LOGIN``, ``ALTER LOGIN``, og ``DROP LOGIN`` sætninger.
- Når du kører den ``CREATE/ALTER/DROP LOGIN`` og ``CREATE/ALTER/DROP DATABASE`` sætninger i et ADO.NET-program ved hjælp af parameteriseret kommandoer er ikke tilladt. Se [Kommandoer og parametre](https://msdn.microsoft.com/library/ms254953.aspx)kan finde flere oplysninger.
- Når du kører den ``CREATE/ALTER/DROP DATABASE`` og ``CREATE/ALTER/DROP LOGIN`` sætninger, hver af disse sætninger skal være kun sætning i Transact-SQL batch. Ellers, opstår der en fejl. For eksempel kontrollerer følgende Transact-SQL, om databasen findes. Hvis den findes, en ``DROP DATABASE`` sætningen kaldes for at fjerne databasen. Da den ``DROP DATABASE`` sætningen er ikke kun sætningen i batchen, udfører følgende Transact-SQL-sætning medfører en fejl.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Under udførelse af de ``CREATE USER`` sætningen med den ``FOR/FROM LOGIN`` indstilling, den skal være kun sætning i Transact-SQL batch.
- Under udførelse af de ``ALTER USER`` sætningen med den ``WITH LOGIN`` indstilling, den skal være kun sætning i Transact-SQL batch.
- Til ``CREATE/ALTER/DROP`` kræver, at en bruger på ``ALTER ANY USER`` tilladelse i databasen.
- Følgende fejl kan opstå, når ejeren af en databaserolle forsøger at tilføje eller fjerne en anden databasebruger til eller fra database rollen,: **bruger eller rolle "Navn" findes ikke i denne database.** Denne fejl opstår, fordi brugeren, ikke er synlige for ejeren. Du kan løse dette problem ved at give ejeren af rolle i ``VIEW DEFINITION`` tilladelse på brugeren. 

Du kan finde flere oplysninger om logon og brugere, skal du se [administrere databaser og logon i Azure SQL-Database](sql-database-manage-logins.md).


## <a name="see-also"></a>Se også

[Azure SQL Database Firewall](sql-database-firewall-configure.md)

[Sådan: konfigurere Firewall-indstillinger (Azure SQL-Database)](sql-database-configure-firewall-settings.md)

[Administration af databaser og logon i Azure SQL-Database](sql-database-manage-logins.md)

[Sikkerhedscenter til SQL Server-Database Engine og Azure SQL-Database](https://msdn.microsoft.com/library/bb510589)

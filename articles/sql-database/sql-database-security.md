<properties
   pageTitle="Oversigt over SQL Database-sikkerhed"
   description="Få mere at vide om Azure SQL-Database og SQL Server sikkerhed, herunder forskellene mellem i skyen og SQL Server lokalt når det drejer sig om godkendelse, godkendelse, forbindelsessikkerhed, kryptering og overholdelse af angivne standarder."
   services="sql-database"
   documentationCenter=""
   authors="tmullaney"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="thmullan;jackr"/>


# <a name="securing-your-sql-database"></a>Sikring af SQL-Database

## <a name="overview"></a>Oversigt

I denne artikel vejledes gennem de grundlæggende regler for sikring af data niveau i et program med Azure SQL-Database. Især i denne artikel får du i gang med ressourcer for at begrænse adgangen, beskyttelse af data, og overvågning aktiviteter på en database oprettet i [komme i gang med SQL-Database selvstudium](sql-database-get-started.md). Du kan finde en komplet oversigt over sikkerhedsfunktioner, der er tilgængelige på alle typer af SQL [Sikkerhedscenter til SQL Server Database Engine og Azure SQL-Database](https://msdn.microsoft.com/library/bb510589). Yderligere oplysninger er også tilgængelig i [sikkerhed og Azure SQL-Database tekniske hvidbog](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="connection-security"></a>Sikre forbindelser

Sikre forbindelser refererer til hvordan du begrænse og sikre forbindelser til databasen ved hjælp af firewallregler og forbindelse kryptering.

Firewallregler bruges af både serveren og databasen til at afvise forsøg forbindelser fra IP-adresser, der ikke er eksplicit whitelisted. Hvis du vil tillade dit program eller klient maskine offentlige IP-adresse for at forsøge at oprette forbindelse til en ny database, skal du først oprette en serverniveau firewallregel, der bruger Azure klassisk Portal, REST-API eller PowerShell. Som en bedste fremgangsmåde, skal du begrænse IP-adresseområder tilladt via din server firewall som muligt. Du kan finde yderligere oplysninger finder [Azure SQL Database-firewallen](https://msdn.microsoft.com/library/ee621782).

Alle forbindelser til Azure SQL-Database kræver kryptering (SSL/TLS) på hele tiden mens data er "undervejs" til og fra databasen. I dit program forbindelsesstreng, skal du angive parametre for at kryptere forbindelsen og *ikke* tillid til servercertifikatet (dette er gjort for dig Hvis du kopierer din forbindelsesstreng af portalen Azure klassisk), ellers forbindelsen kan ikke bekræfte identiteten på serveren, og vil være udsatte for "mand i feltet midt" angreb. For ADO.NET-driveren parametrene forbindelse streng er f.eks **Krypter = True** og **TrustServerCertificate = falsk**. Se [Azure SQL Database-forbindelse kryptering og certifikat validering](https://msdn.microsoft.com/library/azure/ff394108#encryption)kan finde flere oplysninger.


## <a name="authentication"></a>Godkendelse

Godkendelse refererer til, hvordan du bevise din identitet, når du opretter forbindelse til databasen. SQL-Database understøtter to typer godkendelse:

 - **SQL-godkendelse**, som bruger et brugernavn og adgangskode
 - **Azure Active Directory-godkendelse**, som anvender identiteter administreres af Azure Active Directory og understøttes for administrerede og integreret domæner

Når du har oprettet logiske serveren for din database, du har angivet et "server admin" logon med et brugernavn og din adgangskode. Ved hjælp af disse legitimationsoplysninger, kan du godkende til en database på serveren som databaseejer eller "dbo." Hvis du vil bruge Azure Active Directory-godkendelse, skal du oprette en anden server administrator kaldet "Azure AD administratoren," som har tilladelse til at administrere Azure AD-brugere og grupper. Denne administrator kan også udføre alle handlinger, som en almindelig server-administrator kan. I [tilslutning til SQL Database ved hjælp af Azure Active Directory Authentication](sql-database-aad-authentication.md) for en gennemgang af, hvordan du opretter en Azure AD-administrator for at aktivere Azure Active Directory-godkendelse.

Som en bedste fremgangsmåde dit program skal du bruge en konto til at godkende – denne måde, du kan begrænse tilladelserne til programmet og reducere risikoen for skadelig aktivitet, i tilfælde af, at din programkode er udsatte til en SQL-injektionsangreb. Den anbefalede tilgang er at oprette en [indeholdt databasebruger](https://msdn.microsoft.com/library/ff929188), som gør det muligt for din app til at godkende direkte til en enkelt database. Du kan oprette en indeholdte databasebruger, der bruger SQL-godkendelse ved at udføre følgende T-SQL-kommando mens forbindelse til brugerdatabasen som server administrator logon:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Hvis du har oprettet en Azure AD-administrator, kan du oprette en indeholdte databasebruger, der bruger Azure Active Directory-godkendelse ved at udføre følgende T-SQL-kommando mens forbindelse til brugerdatabasen som Azure AD-administrator:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

I begge tilfælde, skal dit program forbindelsesstreng angive disse legitimationsoplysninger i stedet for server administrator logon til at oprette forbindelse til databasen.

Se [administrere databaser og logon i Azure SQL-Database](sql-database-manage-logins.md)kan finde flere oplysninger om godkendelse til en SQL-Database.


## <a name="authorization"></a>Godkendelse
Godkendelse refererer til hvad du kan gøre i Azure SQL-Database, og dette styres af din brugerkonto medlemskab og tilladelser. Som en bedste fremgangsmåde, skal du give brugerne de nødvendige færrest rettigheder. Azure SQL-Database gør det nemt at administrere med roller i T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Administratorkonto server, du opretter forbindelse med er medlem af db_owner, som har tilladelse til at gøre noget i databasen. Gem denne konto til implementering af skema opgraderinger og andre handlinger i administration. Bruge kontoen "ApplicationUser" med mere begrænsede tilladelser til at oprette forbindelse fra dit program til databasen med de færreste rettigheder, der kræves af programmet.

Der er måder til yderligere at begrænse, hvad en bruger kan gøre med Azure SQL-Database:

* [Databaseroller](https://msdn.microsoft.com/library/ms189121) end db_datareader og db_datawriter kan bruges til at oprette en mere effektiv metode program-brugerkonti eller mindre effektiv management-konti.
* Detaljerede [tilladelser](https://msdn.microsoft.com/library/ms191291) kan du styre, hvilke handlinger, du kan gøre på enkelte kolonner, tabeller, visninger, procedurer og andre objekter i databasen.
* [Repræsentation](https://msdn.microsoft.com/library/vstudio/bb669087) og [modul signering](https://msdn.microsoft.com/library/bb669102) kan bruges til at udvide sikkert rettigheder midlertidigt.
* [Række-sikkerhed på brugerniveau](https://msdn.microsoft.com/library/dn765131) kan bruges grænseværdi, som rækker en bruger kan få adgang til.
* [Data Masking](sql-database-dynamic-data-masking-get-started.md) kan bruges til at begrænse visning af følsomme data.
* [Lagrede procedurer](https://msdn.microsoft.com/library/ms190782) kan bruges til at begrænse de handlinger, der kan udføres på databasen.

Administration af databaser og logiske servere fra portalen Azure klassisk eller bruge Azure ressourcestyring API styres af din portalen brugerkonto rolletildelinger. Du kan finde flere oplysninger om dette emne, [Rollebaseret adgangskontrol Azure-portalen](../active-directory./role-based-access-control-configure.md).


## <a name="encryption"></a>Kryptering

Azure SQL-Database kan hjælpe med at beskytte dine data ved at kryptere dine data, når det er "på resten" eller gemt i databasefiler og sikkerhedskopier, ved hjælp af [Gennemsigtig kryptering af Data](http://go.microsoft.com/fwlink/?LinkId=526242). Hvis du vil kryptere databasen, Tilslut som databaseejer af en og udføre:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Andre måder til at kryptere hemmeligheder dine data, kan du overveje:

* [Celleniveau kryptering](https://msdn.microsoft.com/library/ms179331.aspx) til at kryptere bestemte kolonner eller lige celler med data med forskellige kryptering taster.
* Hvis du har brug for et Hardware sikkerhedsmodul eller en central administration af det kryptering vigtige hierarki, kan du overveje at bruge [Azure nøgle samling med SQL Server på en Azure VM](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Altid krypteret](https://msdn.microsoft.com/library/mt163865.aspx) (i eksemplet) gør kryptering gennemsigtig til programmer og gør det klienter til at kryptere følsomme data i klientprogrammer uden at dele tasterne kryptering med SQL-Database.

## <a name="auditing"></a>Overvågning

Overvågning og registrering database begivenheder kan hjælpe dig med at bevare overholdelse af lovgivningen og identificere mistænkelig aktivitet. SQL-Database overvågning gør det muligt at registrere hændelser i din database til en revision log på kontoen Azure-lager. SQL-Database overvågning også integrerer med Microsoft Power BI til det er nemmere at analysere ned rapporter og analyser. Yderligere oplysninger finder du se [Introduktion til SQL Database overvågning](sql-database-auditing-get-started.md).

SQL-Database truslen registrering indeholder en ekstra sikkerhedslag oven på overvågning. Det gør det muligt at besvare trusler, efterhånden som de opstår ved at angive sikkerhedsadvarsler under uoverensstemmende aktiviteter. Yderligere oplysninger finder du se [Introduktion til SQL Database truslen registrering](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Overholdelse af angivne standarder

Ud over ovenstående funktioner og funktionalitet, der kan hjælpe dit program, der opfylder forskellige overholdelse af krav til sikkerhed, Azure SQL-Database også deltager i almindelige revisioner og er blevet certificeret mod et antal overholdelse standarder. Se [Microsoft Azure Sikkerhedscenter](https://azure.microsoft.com/support/trust-center/), hvor du kan finde de mest aktuelle liste over [SQL-Database overholdelse certificering](https://azure.microsoft.com/support/trust-center/services/)kan finde flere oplysninger.

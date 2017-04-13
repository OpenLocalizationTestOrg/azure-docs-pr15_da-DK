<properties
   pageTitle="Konfigurere en SQL server firewall oversigt | Microsoft Azure"
   description="Lær, hvordan du konfigurerer en SQL-database firewall med serverniveau og database niveau firewallregler til at administrere adgang."
   keywords="database firewall"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# <a name="configure-azure-sql-database-firewall-rules---overview"></a>Konfigurere Azure SQL-Database firewallregler \- oversigt


> [AZURE.SELECTOR]
- [Oversigt](sql-database-firewall-configure.md)
- [Azure-portalen](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST-API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database indeholder en relationsdatabase tjeneste til Azure og andre Internetbaserede programmer. For at beskytte dine data, forhindre firewalls alle adgang til din-databaseserver, indtil du angiver, hvilke computere der har tilladelse. Firewallen giver adgang til databaser, der er baseret på den oprindelige IP-adresse for hver enkelt anmodning.

For at konfigurere din firewall skal oprette du firewallregler, som angiver celleområder acceptable IP-adresser. Du kan oprette firewallregler på server og database niveauerne.

- **Serverniveau firewallregler:** Disse regler aktivere klienter mulighed for at få adgang til din hele Azure SQL-server, det vil sige, alle databaserne i den samme logiske server. Disse regler gemmes i den **overordnede** database. Serverniveau firewallregler kan konfigureres ved hjælp af portalen eller ved hjælp af Transact-SQL-sætninger.
- **Database niveau firewallregler:** Disse regler aktivere klienter mulighed for at få adgang til individuelle databaser i Azure SQL-databaseserver. Du kan oprette disse regler for hver database og de er gemt i de enkelte databaser. (Du kan oprette databasen niveau firewallregler for **master** databasen). Disse regler kan være nyttig i begrænse adgang til bestemte (sikker) databaser i den samme logiske server. Database niveau firewallregler kan kun konfigureres ved hjælp af Transact-SQL-sætninger.

**Anbefaling:** Microsoft anbefaler at bruge databasen niveau firewallregler, når det er muligt at forbedre sikkerheden og gøre databasen mere flytbare. Brug serverniveau firewallregler for administratorer og, når du har mange databaser, der har samme access krav, og du ikke vil bruge tid på at konfigurere hver database individuelt.


## <a name="firewall-overview"></a>Oversigt over Firewall

I første omgang er alle Transact-SQL-adgang til din Azure SQL server blokeret af firewallen. Hvis du vil begynde at bruge din Azure SQL-server, skal du gå til Azure-portalen og angive én eller flere serverniveau firewallregler, som giver adgang til din Azure SQL-server. Brug firewallregler til at angive, hvilke IP-adresseområder fra internettet er tilladt, og om Azure-programmer kan forsøge at oprette forbindelse til din Azure SQL-server.

Hvis du vil selektivt give adgang til kun én af databaserne i din Azure SQL-server, skal du oprette en database på brugerniveau regel for den påkrævede database. Angiv en IP-adresseområder for den database firewallregel, der ligger uden for den IP-adresseområder, der er angivet i serverniveau firewallreglen, og sikre, at IP-adressen på klienten ligger i det område, der er angivet i databasen niveau reglen.

Forbindelse forsøg fra internettet og Azure skal først passerer gennem firewallen før de kan få fat i din Azure SQL server eller SQL-Database, som vist i følgende diagram.

   ![Diagram, der beskriver konfiguration af firewall.][1]

## <a name="connecting-from-the-internet"></a>Oprette forbindelse fra internettet

Når en computer, der forsøger at oprette forbindelse til din databaseserver fra internettet, kontrollerer firewallen først oprindelige IP-adressen for din anmodning mod det samlede sæt af firewallregler:

- Hvis IP-adressen for din anmodning er placeret i et af de områder, der er angivet i serverniveau firewallreglerne, tildeles forbindelsen til din server Azure SQL-Database.

- Hvis IP-adressen for din anmodning ikke er i et af de områder, der er angivet i serverniveau firewallreglen, er database niveau firewallregler markeret. Hvis IP-adressen for din anmodning er placeret i et af de områder, der er angivet i databasen niveau firewallregler, tildeles forbindelsen kun til den database, der har en tilsvarende database-niveau-regel.

- Hvis IP-adressen for din anmodning ikke er i feltet områder, der er angivet i én af firewallreglerne for server eller database-niveau, forbindelse anmodning opstår.

> [AZURE.NOTE] For at få adgang til Azure SQL-Database fra din lokale computer, Kontrollér, at firewallen på dit netværk og din lokale computer tillader udgående kommunikation på TCP-port 1433.


## <a name="connecting-from-azure"></a>Oprette forbindelse fra Azure

Når et program fra Azure forsøger at oprette forbindelse til din-databaseserver, kontrollerer firewallen, Azure forbindelser er tilladt. En firewall, med start- og slutdatoer adresse, der er lig med 0.0.0.0 indstillingen angiver disse forbindelser er tilladt. Hvis forbindelsen ikke er tilladt, når anmodningen ikke Azure SQL-databaseserveren.

> [AZURE.IMPORTANT] Denne indstilling konfigurerer firewallen for at tillade alle forbindelser fra Azure herunder forbindelser fra andre kunders abonnementer. Når du vælger denne indstilling, og Sørg for, at dit logon og brugertilladelser begrænse adgangen til kun godkendte brugere.

Du kan aktivere forbindelser fra Azure på to måder:

- Vælg afkrydsningsfeltet **Tillad azure services til access-server** på [Microsoft Azure-portalen](https://portal.azure.com/), når du opretter en ny server.

- Klik på **Ja** i [Klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=161793), under fanen **konfigurere** på en server, i afsnittet **Tilladte tjenester** til **Microsoft Azure Services**.

## <a name="creating-the-first-server-level-firewall-rule"></a>Oprette den første firewallregel serverniveau

Indstillingen første serverniveau firewall kan oprettes ved hjælp af [Azure portal](https://portal.azure.com/) eller fra et program ved hjælp af REST-API eller Azure PowerShell. Efterfølgende serverniveau firewallregler kan oprettes og administreres ved hjælp af disse metoder som gennem Transact-SQL. For at forbedre ydeevnen, cachelagres serverniveau firewallregler midlertidigt på niveauet for databasen. For at opdatere cachen skal du se [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Finde flere oplysninger på serverniveau firewallregler [Sådan: konfigurere en Azure SQL server-firewall ved hjælp af portalen Azure](sql-database-configure-firewall-settings.md).

## <a name="creating-database-level-firewall-rules"></a>Oprettelse af database-niveau firewallregler

Når du har konfigureret første serverniveau firewallen, kan du vil begrænse adgangen til bestemte databaser. Hvis du angiver en IP-adresseområder i firewallreglen database niveau, der er uden for det område, der er angivet i serverniveau firewallreglen, kan kun disse klienter, der har IP-adresser i området database niveau adgang til databasen. Du kan have op til 128 database niveau firewallregler for en database. Database niveau firewallregler for master- og bruger databaser kan oprettes og administreres via Transact-SQL. Du kan finde flere oplysninger om konfiguration af database-niveau firewallregler [sp_set_database_firewall_rule (Azure SQL-databaser)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Administrere firewallregler fra et program

Ud over portalen Azure kan firewallregler administreres fra et program ved hjælp af Transact-SQL, REST-API og Azure PowerShell. I følgende tabel beskrives sæt kommandoer, der er tilgængelige for hver enkelt metode.


### <a name="transact-sql"></a>Transact-SQL

| Få vist katalog eller en lagret Procedure                                                           | Niveau     | Beskrivelse                                          |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx)                   | Server    | Viser de aktuelle serverniveau firewallregler     |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx)             | Server    | Opretter eller opdaterer serverniveau firewallregler       |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)          | Server    | Fjerner serverniveau firewallregler                  |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx)        | Database  | Viser de aktuelle database niveau firewallregler   |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx)    | Database  | Opretter eller opdaterer database niveau firewallregler |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Databaser | Fjerner database niveau firewallregler                |

### <a name="rest-api"></a>REST-API

| API                  | Niveau  | Beskrivelse                                                      |
|----------------------|--------|------------------------------------------------------------------|
| [Listen firewallregler](https://msdn.microsoft.com/library/azure/dn505715.aspx)  | Server | Viser de aktuelle serverniveau firewallregler                 |
| [Oprette firewallregel](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Server | Opretter eller opdaterer serverniveau firewallregler                   |
| [Angive firewallregel](https://msdn.microsoft.com/library/azure/dn505707.aspx)    | Server | Opdaterer egenskaberne for en eksisterende serverniveau firewallregel |
| [Slette firewallregel](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Server | Fjerner serverniveau firewallregler                              |


### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet                                                                                              | Niveau  | Beskrivelse                                                      |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)    | Server | Returnerer de aktuelle serverniveau firewallregler                  |
| [Ny AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)    | Server | Opretter en ny firewallregel for serverniveau                         |
| [Angiv AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)    | Server | Opdaterer egenskaberne for en eksisterende serverniveau firewallregel |
| [Fjern AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Server | Fjerner serverniveau firewallregler                              |

> [AZURE.NOTE] Der kan være op som nogenlunde samme måde som en fem minutters forsinke for ændringer af firewall indstillingerne træder i kraft.

## <a name="troubleshooting-the-database-firewall"></a>Fejlfinding af database-firewallen

Overvej følgende punkter, når adgang til tjenesten Microsoft Azure SQL Database ikke fungerer som forventet:

- **Konfigurationen af lokal firewall:** Før din computer kan få adgang til Azure SQL-Database, skal du kan oprette en firewall-undtagelse på din computer for TCP-port 1433. Hvis du laver forbindelser inden for Azure skyen kanten, skal du muligvis åbne flere porte. Finde flere oplysninger i **version 12 af SQL-Database: uden for vs i** sektion af [porte ud over 1433 til ADO.NET 4.5 og version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Netværk NAT (oversættelse af):** På grund af NAT, kan IP-adresse bruges af computeren til at oprette forbindelse til Azure SQL-Database være forskellig fra den IP-adresse, der vises i computerens IP-konfiguration af indstillinger. Log på portalen for at få vist din computer bruger til at oprette forbindelse til Azure IP-adressen, og gå til fanen **Konfigurer** på den server, hvor din database. I afsnittet **Tilladte IP-adresser** vises den **Aktuelle klient IP-adresse** . Klik på **Føj** til **IP-adresser** til at tillade denne computer for at få adgang til serveren.

- **Ændringer til listen over tilladte er ikke trådt i kraft endnu:** Der kan være så meget som en fem minutters forsinke for ændringer af konfigurationen af Azure SQL-Database firewallen kan træde i kraft.

- **Logon er ikke godkendt eller en forkert adgangskode blev brugt:** Hvis et logon ikke har tilladelser til Azure SQL-databaseserver, eller bruges adgangskode er forkert, nægtet forbindelsen til Azure SQL-databaseserveren. Oprette en firewallindstilling kun indeholder klienter mulighed for at forsøge at oprette forbindelse til din server hver klient skal angive de nødvendige legitimationsoplysninger. Du kan finde flere oplysninger om at forberede logon, skal du se administration af databaser, logon og brugere i Azure SQL-Database.

- **Dynamisk IP-adresse:** Hvis du har forbindelse til internettet med dynamisk IP-adresser, og du har problemer med at få via firewallen, kan du prøve et af følgende løsninger:

 - Spørg din internetudbyder (ISP) til den IP-adresseområder, der er tildelt til din klientcomputere, få adgang til Azure SQL-databaseserver, og Tilføj derefter IP-adresseområder som en firewallregel.

 - Få statiske IP-adresser i stedet for dine klientcomputere, og derefter tilføje IP-adresserne som firewallregler.

## <a name="next-steps"></a>Næste trin

Til, hvordan til artikler om oprettelse af serverniveau og database niveau firewallregler, se:

- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af portalen Azure](sql-database-configure-firewall-settings.md)
- [Konfigurere ved hjælp af T-SQL Azure SQL-Database serverniveau og database niveau firewallregler](sql-database-configure-firewall-settings-tsql.md)
- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af REST-API](sql-database-configure-firewall-settings-rest.md)

Se [oprette en SQL-database i minutter ved hjælp af portalen Azure](sql-database-get-started.md)selvstudium til oprettelse af en database.
Du kan finde hjælp til at oprette forbindelse til en Azure SQL-database fra Åbn kilde eller tredjepartsprogrammer, [klienten Hurtig start kodeeksempler til SQL-Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
For at forstå, hvordan du navigerer til databaser, skal du se [administrere database access og logon-sikkerhed](https://msdn.microsoft.com/library/azure/ee336235.aspx).



## <a name="additional-resources"></a>Yderligere ressourcer

- [Sikring af din database](sql-database-security.md)
- [Sikkerhedscenter til SQL Server-Database Engine og Azure SQL-Database](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

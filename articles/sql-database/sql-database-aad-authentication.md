<properties
   pageTitle="Oprette forbindelse til SQL-Database eller SQL datawarehouse ved hjælp af Azure Active Directory godkendelse | Microsoft Azure"
   description="Få mere at vide, hvordan du opretter forbindelse til SQL-Database ved hjælp af Azure Active Directory-godkendelse."
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
   ms.date="09/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="connecting-to-sql-database-or-sql-data-warehouse-by-using-azure-active-directory-authentication"></a>Oprette forbindelse til SQL-Database eller SQL datawarehouse ved hjælp af Azure Active Directory-godkendelse

Azure Active Directory-godkendelse er en funktion af oprette forbindelse til Microsoft Azure SQL-Database og [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ved hjælp af identiteter i Azure Active Directory (Azure AD). Med Azure Active Directory-godkendelse administrerer du centralt identiteter for brugere og andre Microsoft-tjenester på en central placering. Central administration af ID kan samle for at administrere brugere og forenkler administrationen af tilladelser. Følgende: fordele

- Det er et alternativ til SQL Server-godkendelse.
- Hjælper med at stoppe udbredelsen af bruger-id'er på tværs af databaseservere.
- Gør det muligt for adgangskode rotation på ét sted
- Kunder kan administrere databasetilladelser ved hjælp af eksterne (AAD) grupper.
- Det kan eliminere lagring af adgangskoder ved at aktivere integreret Windows-godkendelse og andre former for godkendelse, der understøttes af Azure Active Directory.
- Azure Active Directory-godkendelse bruger indeholdt databasebrugere for at godkende identiteter på niveauet for databasen.
- Azure Active Directory understøtter token-baseret godkendelse for at oprette forbindelse til SQL Database-programmer.
- Azure Active Directory-godkendelse understøtter ADFS (domænesammenslutning) eller oprindelige bruger/adgangskode til godkendelse til en lokal Azure Active Directory uden synkronisering af domæne.  
- Azure Active Directory understøtter forbindelser fra SQL Server Management Studio, der bruger Active Directory Universal godkendelse, som omfatter Multi-Factor Authentication (MFA).  MFA omfatter stærk godkendelse med en række nemme bekræftelsesindstillinger – telefonopkald, SMS-besked, chipkort med PIN-kode eller -mobilappen vises. Se [SSMS support til Azure AD MFA med SQL-Database og SQL Data Warehouse](sql-database-ssms-mfa-authentication.md)kan finde flere oplysninger.

Konfigurationstrinnene omfatter følgende procedurer for at konfigurere og bruge Azure Active Directory-godkendelse.

1. Oprette og udfylde en Azure Active Directory.
2. Kontrollér, at din database er version 12 Azure SQL-Database. (Ikke er nødvendige for SQL Data Warehouse.)
3. Valgfrit: Knytte eller ændre active directory, der er knyttet til abonnementet Azure.
4. Oprette en Azure Active Directory-administrator for Azure SQL Server eller [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
5. Konfigurere din klientcomputere.
6. Oprette indeholdte databasebrugere i din database, der er knyttet til Azure AD identiteter.
7. Oprette forbindelse til din database ved hjælp af Azure AD identiteter.


## <a name="trust-architecture"></a>Hav tillid til arkitektur

I følgende overordnet diagram opsummerer løsningsarkitektur ved at bruge Azure AD-godkendelse med Azure SQL-Database. De samme begreber gælder for SQL Data Warehouse. For at understøtte Azure Active Directory oprindelig brugeradgangskode, betragtes kun skyen del og Azure AD/Azure SQL-Database. Til at understøtte Federated godkendelse (eller bruger/adgangskode til Windows-legitimationsoplysninger), kommunikation med ADFS Bloker er påkrævet. Pilene angiver kommunikation reaktionsveje.

![aad auth diagram][1]

I det følgende diagram angiver sammenslutning, sikkerhed og rettighedsadministration og hosting relationer, der tillader en klient til at oprette forbindelse til en database ved at sende et token. Tokenet er godkendt af en Azure AD og har tillid til databasen. Kunden 1 kan repræsentere en Azure Active Directory med lokale brugere eller en Azure Active Directory med organisationsnetværket brugere. Kunde 2 repræsenterer en mulig løsning, herunder importerede brugere. i dette eksempel, der kommer fra et medlem af organisationsnetværket Azure Active Directory med ADFS synkroniseres med Azure Active Directory. Det er vigtigt at forstå, adgang til en database ved hjælp af Azure AD godkendelse kræver, at vært abonnementet er knyttet til Azure Active Directory. Det samme abonnement skal bruges til at oprette SQL Server-Azure SQL-Database eller SQL Data Warehouse.

![abonnement relation][2]

## <a name="administrator-structure"></a>Administratoren struktur

Når du bruger Azure AD-godkendelse, er der to administratorkonti for SQL-databaseserveren; oprindelige SQL Server-administratoren og Azure AD-administrator. De samme begreber gælder for SQL Data Warehouse. Kun administratoren baseret på en Azure AD-konto kan oprette første databasebruger til Azure AD, der er indeholdt i en brugerdatabase. Azure AD-administrator login kan være en Azure AD-bruger eller en Azure AD-gruppe. Når administratoren er konto for en gruppe, kan den bruges af et gruppemedlem, aktivere flere Azure AD-administratorer for SQL Server-forekomsten. Ved hjælp af gruppekonto som administrator forbedrer administration ved at tilføje og fjerne gruppemedlemmer i Azure AD uden at ændre de brugere eller tilladelser i SQL-Database centralt. Kun én Azure AD-administrator (en bruger eller gruppe) kan konfigureres når som helst.

![administrator struktur][3]

## <a name="permissions"></a>Tilladelser

Hvis du vil oprette nye brugere, skal du have den `ALTER ANY USER` tilladelse i databasen. Den `ALTER ANY USER` tilladelser kan tildeles til en hvilken som helst databasebruger. Den `ALTER ANY USER` tilladelse holdes også server administratorkonti, og databasebrugere med de `CONTROL ON DATABASE` eller `ALTER ON DATABASE` tilladelse for den pågældende database, og af medlemmer af den `db_owner` databaserolle.

Hvis du vil oprette en indeholdte databasebruger i Azure SQL-Database eller SQL Data Warehouse, skal du oprette forbindelse til databasen ved hjælp af en Azure AD-identitet. Hvis du vil oprette den første indeholdte databasebruger, skal du oprette forbindelse til databasen ved hjælp af en Azure Active Directory-administrator (som er ejeren af databasen). Dette er vist i trin 4 og 5 nedenfor. Azure Active Directory-godkendelse er kun muligt Hvis Azure Active Directory-administrator er blevet oprettet til Azure SQL-Database eller Data Warehouse SQL server. Hvis administratoren Azure Active Directory er blevet fjernet fra serveren, eksisterende Azure Active Directory-brugere, der er oprettet tidligere i SQL Server kan ikke længere oprette forbindelse til databasen ved hjælp af deres Azure Active Directory-legitimationsoplysninger.

## <a name="azure-ad-features-and-limitations"></a>Azure AD-funktioner og begrænsninger

Nedenstående medlemmer af Azure Active Directory kan være klargjort i Azure SQL Server eller SQL Data Warehouse:

- Oprindelig medlemmer: et medlem, der er oprettet i Azure AD i administrerede domænet eller i et kunde-domæne. Se [tilføje dit eget domænenavn til Azure AD](../active-directory/active-directory-add-domain.md)kan finde flere oplysninger.

- Samlet domænemedlemmer: et medlem, der er oprettet i Azure AD med et medlem af organisationsnetværket domæne. Yderligere oplysninger finder du se [Microsoft Azure nu understøtter sammenslutning med Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).

- Importerede medlemmer fra andre Azure Active mapper der er medlem af et oprindelige eller medlem af organisationsnetværket domæne.

- Active Directory-grupper, der oprettes som sikkerhedsgrupper.

Microsoft-konti (for eksempel outlook.com, hotmail.com, live.com) eller andre gæstekonti (for eksempel gmail.com, yahoo.com) understøttes ikke. Hvis du kan logge på [https://login.live.com](https://login.live.com) ved hjælp af den konto og adgangskode, bruger du en Microsoft-konto, som ikke understøttes til Azure AD-godkendelse til Azure SQL-Database eller Azure SQL-datalager.

### <a name="additional-considerations"></a>Yderligere overvejelser

- For at forbedre administration, anbefaler vi du klargøre en dedikeret Azure Active Directory-gruppe som administrator.
- Kun én Azure AD-administrator (en bruger eller gruppe) kan konfigureres til en Azure SQL Server eller Azure SQL Data Warehouse når som helst.
- Kun en Azure Active Directory-administrator for SQL Server kan først oprette forbindelse til Azure SQL Server eller Azure SQL Data Warehouse ved hjælp af en Azure Active Directory-konto. Active Directory-administrator kan konfigurere efterfølgende Azure Active Directory-database-brugere.
- Vi anbefaler, at forbindelsen afbrydes efter til 30 sekunder.
- SQL Server 2016 Management Studio og SQL Server Data Tools til Visual Studio 2015 (version 14.0.60311.1April 2016 eller nyere) understøtter Azure Active Directory-godkendelse. (Godkendelse af azure Active Directory understøttes af **.NET Framework Data-Provider til SQL Server**; på minimum version .NET Framework 4.6). De nyeste versioner af disse værktøjer og data lag programmer (DAC og .bacpac) kan derfor bruge Azure Active Directory-godkendelse.
- [ODBC-version 13.1](https://www.microsoft.com/download/details.aspx?id=53339) understøtter Azure Active Directory authentication dog `bcp.exe` kan ikke oprette forbindelse ved hjælp af Azure Active Directory-godkendelse, fordi den bruger en ældre ODBC-udbyder.
- `sqlcmd`understøtter Azure Active Directory-godkendelse, der begynder med version 13.1, der er tilgængelige fra [Overførselscenter](http://go.microsoft.com/fwlink/?LinkID=825643).  
- SQL Server Data Tools til Visual Studio 2015 kræver mindst April 2016-version af Dataværktøjer (version 14.0.60311.1). Azure Active Directory-brugere vises i øjeblikket ikke i SSDT Object Explorer. Få vist brugerne i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)som en løsning.
- [Microsoft JDBC Driver 6.0 til SQL Server](https://www.microsoft.com/en-us/download/details.aspx?id=11774) understøtter Azure Active Directory-godkendelse. Se også [angive egenskaber for forbindelse](https://msdn.microsoft.com/library/ms378988.aspx).
- PolyBase godkende ikke ved hjælp af Azure Active Directory-godkendelse.
- Nogle funktioner som BI og Excel understøttes ikke.
- Azure Active Directory-godkendelse understøttes af de Azure portalen **Importere** og **Eksportere databasen** blade for SQL-Database. Importere og eksportere ved hjælp af Azure Active Directory authentication er også understøttet fra PowerShell-kommandoen.


## <a name="1-create-and-populate-an-azure-ad"></a>1. oprette og udfylde en Azure AD

Oprette en Azure Active Directory og udfylde den med brugere og grupper. Azure Active Directory kan være det indledende domæne Azure AD administrerede domæne. Azure Active Directory kan også være et lokalt Active Directory Domain Services, der er knyttet til Azure Active Directory.

Du kan finde flere oplysninger, se [integrere dine lokale identiteter med Azure Active Directory](../active-directory/active-directory-aadconnect.md), [tilføje dit eget domænenavn til Azure AD](../active-directory/active-directory-add-domain.md), [Microsoft Azure nu understøtter sammenslutning med Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [administrere adresselisten Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx)og [Administrer Azure AD ved hjælp af Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

## <a name="2-ensure-your-sql-database-is-version-12"></a>2. Kontroller SQL-Database er version 12

Azure Active Directory-godkendelse er understøttet i den nyeste SQL-Database version 12. Se [Hvad er nyt i den nyeste SQL Database Update-version 12](sql-database-v12-whats-new.md)du kan finde oplysninger om SQL-Database version 12 og for at få mere at vide om den er tilgængelig i dit område. Dette trin er ikke nødvendigt til Azure SQL-datalager, fordi SQL Data Warehouse er kun tilgængelig i version 12.

Hvis du har en eksisterende database, kan du kontrollere, at den er hostet i SQL-Database version 12 ved at oprette forbindelse til databasen (for eksempel med SQL Server Management Studio) og afholde `SELECT @@VERSION;`. Den forventede afgang til en database i SQL-Database version 12 er mindst **Microsoft SQL Azure (RTM) - 12.0**. Se, hvis din database ikke er hostes i SQL-Database version 12 [planlægge og forberede dig på at opgradere til SQL-Database version 12](sql-database-v12-plan-prepare-upgrade.md), og gå derefter til Azure klassisk portalen for at overføre databasen til version 12 SQL-Database.

Alternativt kan du oprette en ny database i SQL-Database version 12 ved at følge trinnene i [oprette din første Azure SQL-database](sql-database-get-started.md). **Tip**: Læs næste trin, før du vælger et abonnement til den nye database.

## <a name="3-optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>3. Valgfrit: Knytte eller ændre active directory, der er knyttet til abonnementet Azure

For at knytte din database til mappen Azure AD for din organisation, skal du mappen en pålidelig mappe til Azure abonnementet vært for databasen. Se, [hvordan Azure abonnementer er knyttet til Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx)kan finde flere oplysninger.

**Yderligere oplysninger:** Hver Azure abonnement har et tillidsforhold til en Azure AD-forekomst. Det betyder, at det har tillid til mappen for at godkende brugere, tjenester og enheder. Flere abonnementer kan have tillid til den samme mappe, men kun én mappe har tillid til et abonnement. Du kan se, hvilke directory har tillid til dit abonnement under fanen **Indstillinger** på [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Dette tillidsforhold, der har et abonnement med en mappe er i modsætning til relationen, som har et abonnement med alle andre ressourcer i Azure (websteder, databaser og osv.), der er flere som underordnet ressourcer på et abonnement. Hvis et abonnement udløber, stopper adgang til disse andre ressourcer, der er knyttet til abonnementet også derefter. Men mappen forbliver i Azure, og du kan knytte et andet abonnement med den pågældende mappe og fortsætte med at administrere directory-brugere. Du kan finde flere oplysninger om ressourcer, [forstå ressource access i Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Følgende procedurer indeholder trinvis vejledning i at ændre den tilknyttede mappe for et bestemt abonnement.

1. Oprette forbindelse til din [Azure klassisk Portal](https://manage.windowsazure.com/) ved hjælp af en Azure abonnement administrator.
2. Vælg **Indstillinger**på den venstre-banner.
3. Dine abonnementer vises på skærmbilledet indstillinger. Hvis det ønskede abonnement ikke vises, skal du klikke på **abonnementer** øverst, rullelisten **FILTRER ved DIRECTORY** og vælg den mappe, der indeholder dine abonnementer og klik derefter på **Anvend**.

    ![Vælg abonnement][4]
4. Klik på dit abonnement i området **Indstillinger** , og klik derefter på **Rediger DIRECTORY** nederst på siden.

    ![ad-indstillinger-portal][5]
5. Vælg Azure Active Directory, der er knyttet til din SQL Server eller SQL Data Warehouse i boksen **Rediger mappe** , og klik derefter på pilen til næste.

    ![Rediger directory valg][6]
6. I dialogboksen **Bekræft** directory tilknytning bekræfte, at "**fjernes alle medadministratorer.**"

    ![Rediger directory bekræfte][7]
7. Klik på Søg for at genindlæse på portalen.

> [AZURE.NOTE] Når du ændrer mappen, adgang til alle medadministratorer, Azure AD-brugere og grupper, og directory sikkerhedskopier ressourcen brugere fjernes og de ikke længere har adgang til dette abonnement eller dets ressourcer. Kun kan, som tjenesteadministrator af en, du konfigurere adgangen til principper, der er baseret på den nye mappe. Denne ændring kan tage et omfattende stykke tid at sprede til alle de ressourcer. Ændre mappen, også ændres Azure AD-administratoren for SQL-Database og SQL Data Warehouse og afvise databaseadgang for alle eksisterende Azure AD-brugere. Azure AD-administratoren skal være nulstilling af (som beskrevet nedenfor) og nye Azure AD-brugere skal oprettes.

## <a name="4-create-an-azure-ad-administrator-for-azure-sql-server"></a>4. Opret administrator Azure AD til Azure SQL Server

Hver Azure SQL-Server (som vært for en SQL-Database eller SQL Data Warehouse) starter med en administratorkonto for enkelt server, der er administratoren af hele Azure SQL Server. En anden SQL Server-administratoren skal oprettes, der er en Azure AD-konto. Denne hovedstolen oprettes som databasebruger indeholdt i den overordnede database. Som administratorer, administratorkonti server er medlemmer af **db_owner i hver brugerdatabase** , og Angiv hver brugerdatabase som brugeren **dbo** . Du kan finde flere oplysninger om administratorkonti server, se [Administration af databaser og logon i Azure SQL-Database](sql-database-manage-logins.md) og sektionen **logon og brugere** i [Azure SQL Database retningslinjer for sikkerhed og begrænsninger](sql-database-security-guidelines.md).

Når du bruger Azure Active Directory med geografisk gentagelse, skal administratoren af Azure Active Directory konfigureres til både primært og sekundære servere. Hvis en server, der ikke har en Azure Active Directory-administrator, får Azure Active Directory-logon og brugere en "kan ikke oprette forbindelse" til serverfejl.

> [AZURE.NOTE] Brugere, der ikke er baseret på en Azure AD-konto (herunder administratorkontoen Azure SQL Server), kan ikke oprette Azure AD-baseret brugere, fordi de ikke har tilladelse til at validere brugere foreslåede navn med Azure AD.

### <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server-by-using-the-azure-portal"></a>Klargøre en Azure Active Directory-administrator for din Azure SQL Server ved hjælp af portalen Azure

1. Klik på din forbindelse til rulleliste med en liste over mulige aktive mapper i [Azure-portalen](https://portal.azure.com/), i det øverste højre hjørne. Vælg den korrekte Active Directory som standard Azure AD. Dette trin links tilknytningen abonnement med Active Directory med Azure SQL Server, og Sørg for, at det samme abonnement bruges til begge Azure AD og SQL Server. (Azure SQL Server kan vært for Azure SQL-Database eller Azure SQL Data Warehouse.)

    ![Vælg ad][8]
2. I venstre banneret Vælg **SQL-servere**, Vælg **SQL server**og klik derefter på **Indstillinger**øverst i bladet **SQL Server** .

    ![ad-indstillinger][9]
3. Klik på i bladet **Indstillinger** ** Active Directory-administrator.
4. Klik på **Active Directory-administrator**i bladet **Active Directory-administrator** , og klik derefter på **Angiv administrator**øverst.
5. Vælg den bruger eller gruppe skal være administrator i bladet **Tilføj administrator** , søge efter en bruger, og klik derefter på **Vælg**. (Active Directory-administrator blade viser alle medlemmer og grupper af din Active Directory. Kan ikke vælges brugere eller grupper, der er nedtonet, fordi de ikke understøttes som Azure AD-administratorer. (Se listen over understøttede administratorer i **Azure AD-funktioner og begrænsninger** ovenfor). Rollebaseret adgangskontrol (RBAC) gælder kun for portalen og overføres ikke til SQL Server.
6. Klik på **Gem**øverst i bladet **Active Directory-administrator** .
    ![Vælg administrator][10]

    Processen med at ændre administratoren kan tage flere minutter. Derefter vises den nye administrator i feltet **Active Directory-administrator** .

> [AZURE.NOTE] Når du konfigurerer Azure AD-administrator, kan ikke det nye administratornavn (bruger eller gruppe) allerede være til stede i den virtuelle overordnede database som en bruger, SQL Server-godkendelse. Azure AD-administrator installationen mislykkes, hvis de findes, Annullerer oprettelsen og angiver den sådanne administrator (navn) allerede findes. Da Sådan SQL Server-godkendelsesbruger ikke er en del af Azure AD, mislykkes et forsøg på at oprette forbindelse til serveren ved hjælp af Azure AD-godkendelse.

Klik på **Fjern administrator**for at fjerne senere administrator øverst på bladet **Active Directory-administrator** , og klik derefter på **Gem**.

### <a name="provision-an-azure-ad-administrator-for-azure-sql-server-by-using-powershell"></a>Klargøre administrator Azure AD til Azure SQL Server ved hjælp af PowerShell

For at køre PowerShell-cmdletter, skal du have Azure PowerShell installeret og kører. Du kan finde detaljerede oplysninger, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

Hvis du vil klargøre en Azure AD-administrator, ved at udføre følgende Azure PowerShell-kommandoer:

- Tilføje AzureRmAccount
- Vælg AzureRmSubscription


Cmdletter bruges til at klargøre og administrere Azure AD-administrator:

| Navn på cmdlet                                       | Beskrivelse                                                                                                     |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Angiv AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx)    | Danner en Azure Active Directory-administrator for Azure SQL Server eller Azure SQL Data Warehouse. (Skal være fra det aktuelle abonnement.) |
| [Fjern AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Fjerner en Azure Active Directory-administrator for Azure SQL Server eller Azure SQL Data Warehouse. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx)    | Returnerer oplysninger om en Azure Active Directory-administrator, der er konfigureret i øjeblikket til Azure SQL Server eller Azure SQL Data Warehouse. |

Bruge PowerShell kommandoen get-hjælp til at få vist flere detaljer for hver af disse kommandoer, for eksempel ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Følgende script bestemmelser en Azure AD-administratorgruppen med navnet **DBA_Group** (objekt-id `40b79501-b343-44ed-9ce7-da4c8cc7353f`) til **demo_server** server i en ressourcegruppe med navnet **gruppe-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

**Vist navn** Inputparameteren accepterer visningsnavnet Azure AD eller brugerens hovednavn. For eksempel ``DisplayName="John Smith"`` og ``DisplayName="johns@contoso.com"``. For Azure AD grupper kun Azure AD understøttes visningsnavn.

> [AZURE.NOTE] Kommandoen Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` forhindrer dig ikke fra klargøring Azure AD-administratorer for ikke-understøttede brugere. En ikke-understøttede bruger kan klargøres, men kan ikke oprette forbindelse til en database. (Se listen over understøttede administratorer i **Azure AD-funktioner og begrænsninger** ovenfor).

I følgende eksempel bruges valgfrit **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] Azure AD **ObjectID** er påkrævet, når **vist navn** ikke er entydige. Brug afsnittet Active Directory i Azure klassisk Portal for at hente værdierne **ObjectID** og **vist navn** , og få vist egenskaberne for en bruger eller gruppe.

I følgende eksempel returnerer oplysninger om aktuelt Azure AD-administrator for Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

I følgende eksempel fjerner en Azure AD-administrator:
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

Du kan også klargøre en Azure Active Directory-Administrator ved hjælp af REST API'er. Få mere at vide under [Service Management RESTEN API Reference og handlinger til Azure SQL-databaser handlinger til Azure SQL-databaser](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="5-configure-your-client-computers"></a>5. Konfigurer din klientcomputere

På alle klientmaskinen, hvorfra programmerne eller brugerne oprette forbindelse til Azure SQL-Database eller Azure SQL Data Warehouse ved hjælp af Azure AD identiteter, skal du installere følgende software:

- .NET framework 4.6 eller nyere fra [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory Authentication Library til SQL Server (**ADALSQL. DLL**) er tilgængelig på flere sprog (både x86 og amd64) fra downloadcenter på [Microsoft Active Directory Authentication Library til Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### <a name="tools"></a>Værktøjer

- Installation af [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) eller [SQL Server Data Tools til Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) opfylder kravet om .NET Framework 4.6.
- SSMS installationer på x86 version af **ADALSQL. DLL**.
- SSDT installeres amd64-versionen af **ADALSQL. DLL**.
- Den seneste Visual Studio fra [Visual Studio overførsler](https://www.visualstudio.com/downloads/download-visual-studio-vs) opfylder kravet om .NET Framework 4.6, men installere ikke den nødvendige amd64 version af **ADALSQL. DLL**.

## <a name="6-create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>6. oprette indeholdte databasebrugere i din database, der er knyttet til Azure AD identiteter

### <a name="about-contained-database-users"></a>Om indeholdte databasebrugere

Azure Active Directory-godkendelse kræver, at databasen brugerne skal have oprettet som indeholdte databasebrugere. Indeholdte databasebruger baseret på en Azure AD-identitet er databasebruger, der ikke har et logon i den overordnede database, og som er tilknyttet en identitet i den Azure AD-mappe, der er knyttet til databasen. Azure AD-identitet kan være enten en enkelt brugerkonto eller en gruppe. Du kan finde flere oplysninger om indeholdte databasebrugere se [Indeholdt databasebrugere og foretage dine Database flytbare](https://msdn.microsoft.com/library/ff929188.aspx).

> [AZURE.NOTE] Databasebrugere (med undtagelse af administratorer) kan ikke oprettes ved hjælp af portalen. RBAC roller, der ikke overføres til SQL Server, SQL-Database eller SQL Data Warehouse. Azure RBAC roller er brugt til administration af Azure ressourcer og gælder ikke for databasetilladelser. For eksempel giver rollen **SQL Server bidragyder** ikke adgang til at oprette forbindelse til SQL-Database eller SQL Data Warehouse. Tilladelsen access skal tildeles direkte i databasen ved hjælp af Transact-SQL-sætninger.

### <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>Oprette forbindelse til bruger database eller data warehouse ved hjælp af SQL Server Management Studio eller SQL Server Data Tools

For at bekræfte Azure AD-administratoren er korrekt konfigurere, oprette forbindelse til **master** databasen med Azure AD-administratorkonto.
Hvis du vil klargøre en Azure AD-baseret indeholdt databasebruger (end serveradministratoren af, der ejer databasen), oprette forbindelse til databasen med en Azure AD-identitet, der har adgang til databasen.

> [AZURE.IMPORTANT] Support til Azure Active Directory authentication leveres med [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) i Visual Studio-2015 og [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) . August 2016 udgave af SSMS indeholder også understøttelse af Active Directory Universal godkendelse, som giver administratorer mulighed for at kræve Multi-Factor Authentication ved hjælp af et telefonopkald, SMS-besked, chipkort med PIN-kode eller -mobilappen vises.

#### <a name="connect-using-active-directory-integrated-authentication"></a>Oprette forbindelse ved hjælp af Active Directory integreret godkendelse

Brug denne metode, hvis du er logget på Windows ved hjælp af Azure Active Directory-legitimationsoplysninger fra et medlem af organisationsnetværket domæne.

1. Start Management Studio eller Dataværktøjer, og vælg **Active Directory integreret godkendelse**i dialogboksen **Opret forbindelse til Server** (eller **oprette forbindelse til Database Engine**) i feltet **godkendelse** . Ingen adgangskode skal bruges eller kan angives, fordi din eksisterende legitimationsoplysninger der præsenteres for forbindelsen.
    ![Vælg AD integreret godkendelse][11]

2. Klik på knappen **Indstillinger** , og skriv navnet på den brugerdatabase, du vil oprette forbindelse til i feltet **Opret forbindelse til databasen** på siden **Egenskaber for forbindelse** .
    ![Vælg navnet på databasen][13]


#### <a name="connect-using-active-directory-password-authentication"></a>Oprette forbindelse ved hjælp af Active Directory adgangskodegodkendelse

Brug denne metode, når du opretter forbindelse til en Azure AD hovednavn ved hjælp af Azure AD administrerede domæne. Du kan også bruge den til medlem af organisationsnetværket konto uden adgang til domænet, for eksempel, når du arbejder fra en fjernplacering.

Brug denne metode, hvis du er logget på Windows ved hjælp af legitimationsoplysninger fra et domæne, der ikke er i organisationsnetværk med Azure, eller når ved hjælp af Azure AD-godkendelse ved hjælp af Azure AD baseret på første eller klient domænet.

1. Start Management Studio eller Dataværktøjer, og vælg **Active Directory Password Authentication**i dialogboksen **Opret forbindelse til Server** (eller **oprette forbindelse til Database Engine**) i feltet **godkendelse** .
2. Skriv dit brugernavn til Azure Active Directory i formatet i feltet **brugernavn** **username@domain.com**. Det skal være en konto fra Azure Active Directory eller en konto fra et domæne i organisationsnetværk med Azure Active Directory.
3. I feltet **adgangskode** , Skriv dit brugernavn din adgangskode for kontoen Azure Active Directory eller samlet domænekonto.
    ![Vælg AD Password Authentication][12]

4. Klik på knappen **Indstillinger** , og skriv navnet på den brugerdatabase, du vil oprette forbindelse til i feltet **Opret forbindelse til databasen** på siden **Egenskaber for forbindelse** . (Se grafik i den forrige indstilling).


### <a name="create-an-azure-ad-contained-database-user-in-a-user-database"></a>Oprette en databasebruger Azure AD, der er indeholdt i en brugerdatabase

Hvis du vil oprette en Azure AD-baseret indeholdt databasebruger (foruden serveradministratoren, der ejer databasen), oprette forbindelse til databasen med en Azure AD-identitet, som en bruger med mindst **Ændre alle brugeren** tilladelse. Brug derefter følgende Transact-SQL-syntaks:

    CREATE USER <Azure_AD_principal_name>
    FROM EXTERNAL PROVIDER;


*Azure_AD_principal_name* kan være brugerens hovednavn for en Azure AD-bruger eller det viste navn for en Azure AD-gruppe.

**Eksempler:** Bruger, der repræsenterer en Azure AD del af et organisationsnetværk eller administreret domænebruger til at oprette en indeholdte database:

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

For at oprette en indeholdte databasebruger, der repræsenterer en Azure AD eller samlet domænegruppe skal du angive det viste navn på en sikkerhedsgruppe:

    CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

Sådan oprettes en indeholdte databasebruger, der repræsenterer et program, der forbinder ved hjælp af en Azure AD-token:

    CREATE USER [appName] FROM EXTERNAL PROVIDER;

Du kan finde flere oplysninger om oprettelse af indeholdte databasebrugere på baggrund af Azure Active Directory identiteter [CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).


> [AZURE.NOTE] Fjerne Azure Active Directory-administrator for Azure SQL Server forhindrer alle Azure AD-godkendelse brugere i at oprette forbindelse til serveren. Hvis det er nødvendigt, ubrugelig Azure AD-brugere kan afbrydes manuelt ved at administrator af en SQL-Database.

Når du opretter en databasebruger, brugeren modtager tilladelsen **Opret forbindelse** og kan oprette forbindelse til den pågældende database som medlem af den **offentlige** rolle. De kun tilladelser, der er tilgængelige for brugeren er først eventuelle tilladelser, der er tildelt den **offentlige** rolle, eller en hvilken som helst tilladelser til en hvilken som helst Windows-grupper, som de er medlem af. Når du klargør en Azure AD-baseret indeholdt databasebruger, kan du tildele brugeren yderligere tilladelser, på samme måde som du give tilladelser til en anden type bruger. Typisk give tilladelser til databaseroller og føje brugere til roller. Du kan finde yderligere oplysninger finder [Grundlæggende oplysninger om databaser program tilladelse](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Du kan finde flere oplysninger om særlige SQL-Database roller, se [Administration af databaser og logon i Azure SQL-Database](sql-database-manage-logins.md).
En samlet domænebruger, der importeres til et Administrer domæne, skal bruge den administrerede domæne identitet.

> [AZURE.NOTE] Azure AD-brugere er markeret i databasen metadataene med type E (EXTERNAL_USER) og for grupper med type X (EXTERNAL_GROUPS). Du kan finde flere oplysninger [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## <a name="7-connect-by-using-azure-ad-identities"></a>7. forbinde ved hjælp af Azure AD identiteter

Azure Active Directory-godkendelse understøtter følgende metoder til at oprette forbindelse til en database ved hjælp af Azure AD identiteter:

- Ved hjælp af integreret Windows-godkendelse
- Ved hjælp af en Azure AD-hovednavn og en adgangskode
- Ved hjælp af programmet token godkendelse

### <a name="71-connecting-using-integrated-windows-authentication"></a>7.1. Oprette forbindelse via integreret (Windows)-godkendelse

Hvis du vil bruge integreret Windows-godkendelse, skal være samlet domænets Active Directory med Azure Active Directory. Klientprogrammet (eller en tjeneste) oprette forbindelse til databasen skal køre på et medlem af et domæne maskine under en brugers domænelegitimationsoplysninger.

For at oprette forbindelse til en database ved hjælp af integreret godkendelse og en Azure AD-identitet, skal være indstillet nøgleordet godkendelse i forbindelsesstrengen til databasen til Active Directory integreret. Følgende C#-kode prøven bruger ADO .NET.

    string ConnectionString =
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Bemærk, at forbindelsen streng nøgleord ``Integrated Security=True`` understøttes ikke til at oprette forbindelse til Azure SQL-Database.
Bemærk, at når du foretager en ODBC-forbindelse skal du fjerne mellemrum og indstille godkendelse til 'ActiveDirectoryIntegrated'.

### <a name="72-connecting-with-an-azure-ad-principal-name-and-a-password"></a>7.2. Oprette forbindelse med en Azure AD-hovednavn og en adgangskode
Hvis du vil oprette forbindelse til en database ved hjælp af integreret godkendelse og en Azure AD-identitet, skal nøgleordet godkendelse indstilles til Active Directory-adgangskode. Forbindelsesstrengen skal indeholde bruger-ID/UID og adgangskode/PWD nøgleord og værdier. Følgende C#-kode stikprøvernes bruger ADO .NET.

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Lær mere om Azure AD godkendelsesmetoder ved hjælp af de demo kodeeksempler, der er tilgængelige på [Azure AD godkendelse GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).


### <a name="73-connecting-with-an-azure-ad-token"></a>7.3 tilslutning med Azure AD token
Denne godkendelsesmetode kan midterste niveau services til at oprette forbindelse til Azure SQL-Database eller Azure SQL Data Warehouse ved at hente et token fra Azure Active Directory (AAD). Det giver mulighed for avancerede scenarier, herunder certifikat-baseret godkendelse. Du skal udføre fire grundlæggende trin at bruge Azure AD-token godkendelse:

1. Registrere dit program med Azure Active Directory og få klient-id i din kode. 
2. Opret en databasebruger, der repræsenterer programmet. (Fuldføres tidligere i trin 6).
3. Oprette et certifikat på klient computer kørt programmet.
4. Tilføje certifikatet som en nøgle til dit program.

Eksempel forbindelsesstreng:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Få mere at vide under [SQL Server sikkerhed Blog](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/).

### <a name="connecting-with-sqlcmd"></a>Oprette forbindelse med sqlcmd  
Følgende udtalelser forbindelse via version 13.1 af sqlcmd, som er tilgængelig fra [Overførselscenter](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="see-also"></a>Se også

[Administration af databaser og logon i Azure SQL-Database](sql-database-manage-logins.md)

[Indeholdte databasebrugere](https://msdn.microsoft.com/library/ff929071.aspx)

[OPRETTE bruger (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png


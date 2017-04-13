<properties
    pageTitle="Introduktion til synkronisering af SQL-databaser Data"
    description="Dette selvstudium hjælper dig med at komme i gang med Azure SQL Data Sync (Preview)."
    services="sql-database"
    documentationCenter=""
    authors="jennieHubbard"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/11/2016"
    ms.author="jhubbard"/>


#<a name="getting-started-with-azure-sql-data-sync-preview"></a>Introduktion til Azure SQL Data Sync (Preview)
I dette selvstudium lærer du grundlæggende Azure SQL Data Sync ved hjælp af portalen Azure klassisk.

Dette selvstudium antages det minimale forudgående oplevelse med SQL Server og Azure SQL-Database. I dette selvstudium opretter du en hybrid (SQL Server og SQL-Database forekomster) Synkroniser gruppe fuldt konfigureret og synkronisere på den angivne tidsplan.

> [AZURE.NOTE] Den komplette tekniske dokumentation, der er angivet for Azure SQL Data Sync, der tidligere er placeret på MSDN, er tilgængelige som .PDF-fil. Kan du hente det [her](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

## <a name="step-1-connect-to-the-azure-sql-database"></a>Trin 1: Oprette forbindelse til Azure SQL-Database

1. Log på [Klassisk Portal](http://manage.windowsazure.com).

2. Klik på **SQL-DATABASER** i venstre rude.

3. Klik på **SYNKRONISER** nederst på siden. Når du klikker på SYNKRONISER, vises en liste, der viser de ting, du kan føje - **Ny Synkroniser gruppe** og **Ny Synkroniser Agent**.

4. Klik på **Ny synkronisering Agent**for at starte guiden Ny SQL Data Synkroniser Agent.

5. Hvis du ikke har tilføjet en agent før, **Klik på Hent den her**.

    ![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## <a name="step-2-add-a-client-agent"></a>Trin 2: Føje en Client Agent
Dette trin er påkrævet, kun, hvis du skal have en lokal SQL Server-database inkluderet i din Synkroniser gruppe. Gå videre til trin 4, hvis din Synkroniser gruppe er kun forekomster af SQL-Database.

<a id="InstallRequiredSoftware"></a>
### <a name="step-2a-install-the-required-software"></a>Trin 2a: installere software, der kræves
Sørg for, at du har installeret på den computer, hvor du har installeret Client Agent følgende.

- **.NET framework 4.0**

 Du kan installere .NET Framework 4.0 fra [her](http://go.microsoft.com/fwlink/?linkid=205836).

- **Microsoft SQL Server 2008 R2 SP1 System CLR datatyper (x86)**

 Installere Microsoft SQL Server 2008 R2 SP1 System CLR typerne (x86) fra [her](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Microsoft SQL Server 2008 R2 SP1 delt Management Objects (x86)**

 Installere Microsoft SQL Server 2008 R2 SP1 delt Management Objects (x86) fra [her](http://www.microsoft.com/download/en/details.aspx?id=26728)



<a id="InstallClient"></a>
### <a name="step-2b-install-a-new-client-agent"></a>Trin 2b: installere en ny klient-Agent

Følg vejledningen i [installere en Client Agent (SQL Data Sync)](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf) for at installere agenten.



<a id="RegisterSSDb"></a>
### <a name="step-2c-finish-the-new-sql-data-sync-agent-wizard"></a>Trin 2c: har afsluttet guiden Ny SQL Data Synkroniser Agent

1.  Gå tilbage til guiden Ny SQL Data Synkroniser Agent.
2.  Giv agenten et beskrivende navn.
3.  På rullelisten Vælg **område** (datacenter) til at hoste denne agent.
4.  Vælg **ABONNEMENTET** til at hoste denne agent på rullelisten.
5.  Klik på højre pil.



## <a name="step-3-register-a-sql-server-database-with-the-client-agent"></a>Trin 3: Registrere en SQL Server-database med Client Agent

Når Client Agent er installeret, skal du registrere hver lokal SQL Server-database, du vil medtage i en synkronisering gruppe med agenten.
Følg vejledningen på [registrere en SQL Server-Database med en Client Agent](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)for at registrere en database med agenten.



## <a name="step-4-create-a-sync-group"></a>Trin 4: Oprette en synkronisering af gruppe


<a id="StartNewSGWizard"></a>
### <a name="step-4a-start-the-new-sync-group-wizard"></a>Trin 4a: starte guiden Ny Synkroniser gruppe

1.  Gå tilbage til [Klassisk Portal](http://manage.windowsazure.com).
2.  Klik på **SQL-DATABASER**.
3.  Klik på **Tilføj SYNKRONISER** nederst på siden skal du vælge Ny Synkroniser gruppe fra kassen.

    ![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### <a name="step-4b-enter-the-basic-settings"></a>Trin 4b: Angiv de grundlæggende indstillinger


1.  Angiv et beskrivende navn til gruppen synkronisering.
2.  På rullelisten Vælg **område** (Data Center) til at hoste denne Synkroniser gruppe.
3. Klik på højre pil.

    ![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### <a name="step-4c-define-the-sync-hub"></a>Trin 4c: definere Synkroniser hub

1. Vælg forekomsten af SQL-Database til at fungere som Synkroniser gruppe hubben på rullelisten.
2. Angiv legitimationsoplysningerne for denne forekomst af SQL-Database - **HUB brugernavn** og **Din adgangskode HUB**.
3. Vent på SQL Data synkronisering at bekræfte det brugernavn og adgangskode. Du får vist et grønt flueben vises til højre for ADGANGSKODEN, når legitimationsoplysningerne, der er bekræftet.
4. Vælg **KONFLIKTLØSNING** politikken på rullelisten.

 **Hub vinder** - enhver ændring, der er skrevet til hub database skrive reference databaser, overskrivning ændringer i den samme henviser til post i databasen. Funktionelt, betyder, at den første ændring, der er skrevet til hubben overfører til de andre databaser.


 **Klient vinder** - ændringer, der er skrevet til hubben overskrives af ændringer i reference databaser. Funktionelt, betyder, at den seneste ændring, der er skrevet til hubben den, bevares og overføres til de andre databaser.

5.  Klik på højre pil.

    ![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### <a name="step-4d-add-a-reference-database"></a>Trin 4d: tilføje en referencedatabase


Gentag dette trin for hver ekstra database, du vil føje til gruppen synkronisering.

1. Vælg databasen, der skal tilføjes, på rullelisten.

    Databaser i rullemenuen omfatter både SQL Server-databaser, der er registreret med agent og forekomster af SQL-Database.
2.  Angiv legitimationsoplysninger for denne database - **brugernavn** og **din adgangskode**.
3.  Vælg **SYNKRONISER retning** for denne database på rullelisten.

    **Tovejs-** - ændringer i reference databasen er skrevet til hub databasen, og ændringer til databasen hub skrives til reference-databasen.

    **Synkroniser fra Hub** - databasen modtager opdateringer fra Hub. Ændringer sender til hubben ikke.

    **Synkroniser med hubben** - databasen sender opdateringer til hubben. Ændringer i hubben skrives ikke til denne database.

4.  For at afslutte oprettelsen af gruppen synkronisering, skal du markere afkrydsningsfeltet nederst til højre i guiden. Vent på SQL Data synkronisering at bekræfte legitimationsoplysningerne. En grøn markering angiver, at legitimationsoplysningerne, der er bekræftet.

5.  Klik på fluebenet endnu en gang. Du kommer tilbage til siden **SYNKRONISERING** under SQL-databaser. Denne synkronisering gruppe er nu opført i dine andre Synkroniser grupper og supportmedarbejdere.

    ![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## <a name="step-5-define-the-data-to-sync"></a>Trin 5: Definere dataene til at synkronisere

Synkronisering af Azure SQL-Data kan du vælge tabeller og kolonner til at synkronisere. Hvis du vil også filtrere en kolonne, så, at kun rækker med specifikke værdier (f.eks., alder > = 65) synkronisere skal bruge SQL Data Sync-portalen på Azure og i dokumentationen på vælge de tabeller, kolonner og rækker skal synkroniseres til at definere dataene til at synkronisere.

1.  Gå tilbage til [Klassisk Portal](http://manage.windowsazure.com).
2.  Klik på **SQL-DATABASER**.
3.  Klik på fanen **SYNKRONISER** .
4.  Klik på navnet på denne Synkroniser gruppe.
5.  Klik på fanen **SYNKRONISER regler** .
6.  Vælg den database, du vil give Synkroniser gruppe skemaet.
7.  Klik på højre pil.
8.  Klik på **Opdater skema**.
9.  Vælg kolonnerne, der skal medtages i synkroniseringen for hver tabel i databasen.
    - Kolonner med ikke-understøttede datatyper kan ikke vælges.
    - Hvis ingen kolonner i en tabel er markeret, og er tabellen ikke medtaget i gruppen synkronisering.
    - Vælg/fravælg alle tabellerne, klikke på Vælg nederst på skærmen.
10. Klik på **Gem**, og derefter vente gruppen synkronisering for at afslutte klargøring.
11. For at vende tilbage til landingssiden synkronisering af Data, klik på tilbage-pilen øverst til venstre på skærmen (over Synkroniser gruppens navn).

    ![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## <a name="step-6-configure-your-sync-group"></a>Trin 6: Konfigurere din Synkroniser gruppe

Du kan altid synkronisere en synkronisering gruppe ved at klikke på SYNKRONISER nederst på landingssiden synkronisering af Data.
Hvis du vil synkronisere efter en tidsplan, kan du konfigurere gruppen til synkronisering.

1.  Gå tilbage til [Klassisk Portal](http://manage.windowsazure.com).
2.  Klik på **SQL-DATABASER**.
3.  Klik på fanen **SYNKRONISER** .
4.  Klik på navnet på denne Synkroniser gruppe.
5.  Klik på **KONFIGURER** .
6.  **AUTOMATISK SYNKRONISERING**
    - Hvis du vil konfigurere gruppen synkronisering for at synkronisere på et sæt hyppighed, skal du klikke på **til**. Du kan stadig synkronisere efter behov ved at klikke på SYNKRONISER.
    - Klik på **fra** for at konfigurere gruppen synkronisering for at synkronisere, når du klikker på SYNKRONISER.
7.  **SYNKRONISER FREKVENS**
    - Hvis automatisk SYNKRONISERING er slået til, kan du angive hyppigheden synkronisering. Hyppigheden skal være mellem 5 minutter og 1 måned.
8.  Klik på **Gem**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Tillykke. Du har oprettet en synkronisering gruppe, der omfatter både en forekomst af SQL-Database og en SQL Server-database.

## <a name="next-steps"></a>Næste trin
Kan finde flere oplysninger om SQL-Database og SQL Data synkronisering i:

* [Hente den komplette SQL Data Sync tekniske dokumentation](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [Oversigt over SQL-Database](sql-database-technical-overview.md)
* [Administration af database livscyklus](https://msdn.microsoft.com/library/jj907294.aspx)
 

 

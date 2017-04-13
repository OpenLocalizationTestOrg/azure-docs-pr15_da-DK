<properties
    pageTitle="Optimere dit miljø med løsningen SQL vurdering i Log Analytics | Microsoft Azure"
    description="Du kan bruge SQL vurdering løsningen til at vurdere risikoen og tilstand for din server-miljøer, jævnligt."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="optimize-your-environment-with-the-sql-assessment-solution-in-log-analytics"></a>Optimere dit miljø med løsningen SQL vurdering i Log Analytics


Du kan bruge SQL vurdering løsningen til at vurdere risikoen og tilstand for din server-miljøer, jævnligt. Denne artikel kan hjælpe dig med at installere løsningen, så du kan foretage korrigerende handlinger for eventuelle problemer.

Denne løsning indeholder en prioriteret liste over anbefalinger, der er specifikke for serverinfrastrukturen udløst. Anbefalinger kategoriseres på tværs af seks fokusområder, så du hurtigt forstå risikoen og løs problemet.

Anbefalinger, der er foretaget er baseret på den viden og erfaringer med Microsoft teknikere fra tusindvis af kunde besøg. Hver anbefaling giver vejledning om grunden et problem, der muligvis er vigtige for dig, og hvordan du implementerer de foreslåede ændringer.

Du kan vælge fokusområder, der er vigtigst for din organisation og registrere status for opgaver mod anvender et risikoen gratis og sund-miljø.

Når du har tilføjet løsningen, og en vurdering er færdige, oversigt vises oplysninger for fokusområder på dashboardet i **SQL vurdering** til infrastruktur i dit miljø. I nedenstående afsnit beskrives, hvordan du bruger oplysningerne på dashboardet **SQL vurdering** , hvor du kan få vist og derefter tage handlinger, der anbefales til SQL server-infrastruktur.

![Billede af SQL vurdering felt](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![Billede af SQL vurdering dashboard](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>Installation og konfiguration af løsningen
SQL vurdering fungerer med alle aktuelt understøttede versioner af SQL Server til Standard, udvikler og Enterprise editions.

Brug følgende oplysninger til at installere og konfigurere løsningen.

- Supportmedarbejdere skal være installeret på servere, der har SQL Server er installeret.
- SQL vurdering løsning kræver .NET Framework 4 installeret på alle computere, der indeholder en OMS agent.
- Når du bruger Operations Manager agent med SQL vurdering, skal du bruge en Operations Manager Run-As-konto. Yderligere oplysninger finder du i [Operations Manager Kør som konti til OMS](#operations-manager-run-as-accounts-for-oms) nedenfor.

    >[AZURE.NOTE] MMA agent understøtter ikke Operations Manager Run-As konti.

- Føje SQL vurdering løsningen til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md). Der er ingen yderligere konfiguration, der er påkrævet.

>[AZURE.NOTE] Når du har tilføjet løsningen, føjes filen AdvisorAssessment.exe til servere med supportmedarbejdere. Af konfigurationsdata læses og derefter sendes til tjenesten OMS i skyen for behandling. Logik anvendes på modtager data og skytjenesten registrerer dataene.

## <a name="sql-assessment-data-collection-details"></a>Detaljer om SQL vurdering data af websteder

SQL vurdering indsamler WMI-data, data fra registreringsdatabasen, ydelsesdata og SQL Server dynamisk management visningsresultater ved hjælp af supportmedarbejdere, som du har aktiveret.

Følgende tabel viser data samling metoder til supportmedarbejdere, om Operations Manager (SCOM) er påkrævet, og hvordan ofte data indsamles af en agent.

| platform | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Windows|![Ja](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Ja](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Nej](./media/log-analytics-sql-assessment/oms-bullet-red.png)|    ![Nej](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![Ja](./media/log-analytics-sql-assessment/oms-bullet-green.png)|   7 dage|

## <a name="operations-manager-run-as-accounts-for-oms"></a>Operations Manager Kør som konti for OMS

Log Analytics i OMS bruger gruppen Operations Manager agent og administration til at indsamle og sende data til tjenesten OMS. OMS builds på management packs til arbejdsbelastninger til at levere Tilføj værdi-tjenester. Hver arbejdsbelastningen kræver arbejdsbelastningen-specifikke rettigheder til at køre management packs i en anden sikkerhedskontekst, som en domænekonto. Du skal angive oplysninger om brugerlegitimationsoplysninger ved at konfigurere en Operations Manager Kør som konto.

Brug følgende oplysninger til at angive Operations Manager Kør som konto til SQL vurdering.

### <a name="set-the-run-as-account-for-sql-assessment"></a>Angive Kør som kontoen for SQL vurdering

 Hvis du allerede bruger SQL Server management pack, skal du bruge kontoen, der kører som.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Du kan konfigurere kontoen SQL Kør som i konsollen handlinger

>[AZURE.NOTE] Hvis du bruger den OMS direkte agent i stedet for SCOM agent, kører management pack altid i sikkerhedsindstillingerne for den lokale systemkonto. Springe trin 1-5 nedenfor, og kør den T-SQL eller Powershell eksempel, der angiver NT AUTHORITY\SYSTEM som brugernavnet.

1. Åbn konsollen handlinger i Operations Manager, og klik derefter på **Administration**.

2. Klik på **profiler**under **Køre som konfiguration**, og Åbn **OMS SQL vurdering køre som profil**.

3. Klik på **Tilføj**på siden **Køre som konti** .

4. Vælg en Kør som i Windows-konto, der indeholder de legitimationsoplysninger, der bruges til SQL Server, eller klik på **Ny** for at oprette en.
    >[AZURE.NOTE] Kontotype Kør som skal være Windows. Kør som kontoen skal også være en del af lokaladministratorgruppe på alle Windows-servere, der er vært for forekomster af SQL Server.

5. Klik på **Gem**.

6. Rediger og derefter udføre følgende eksempel på T-SQL for hver forekomst af SQL Server til at give mindste tilladelse til at køre som konto til at udføre SQL vurdering. Men du ikke behøver at gøre dette, hvis en køre som konto er allerede en del af rollen systemadministrator server på forekomster af SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Konfigurere kontoen SQL Kør som ved hjælp af Windows PowerShell

Åbne en PowerShell-vinduet, og Kør følgende script, når du har opdateret med dine oplysninger:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Forstå, hvordan anbefalinger er prioriteret

Hver anbefaling, der er foretaget gives en vægtning værdi, der identificerer den relative vigtighed af anbefalingen. Kun de ti vigtigste anbefalinger vises.

### <a name="how-weights-are-calculated"></a>Hvordan tykkelser beregnes

Vægtning er aggregatværdierne baseret på tre vigtige faktorer:

- *Sandsynligheden* for, at et problem identificeret skaber problemer. En højere sandsynlighed passer til en større overordnede point for anbefalingen.

- *Virkning* af problemet på din organisation, hvis den medfører et problem. Større indflydelse passer til en større overordnede point for anbefalingen.

- *Indsats* , kræves for at implementere anbefalingen. Et højere, der passer til en mindre overordnede point for anbefalingen.

Vægtning for hver anbefaling udtrykt som en procentdel af det samlede antal point, der er tilgængelige for hvert område i fokus. Eksempelvis hvis anbefaling i området sikkerhed og overholdelse fokus har et resultat på 5%, implementere denne anbefaling, øger din overordnede sikkerhed og overholdelse resultat ved 5%.

### <a name="focus-areas"></a>Fokusområder

**Sikkerhed og overholdelse** - dette fokus område viser anbefalinger til potentielle sikkerhedsrisici og brud på, firmapolitikker og tekniske, juridiske og lovmæssige krav.

**Tilgængelighed og Forretningskontinuitet** - dette fokus område viser anbefalinger til tjenesten kører, fleksibilitet dine infrastruktur og business beskyttelse.

**Ydeevne og skalerbarhed** - dette fokus område viser anbefalinger, der hjælper organisationens IT-infrastruktur vokse, sikre, at din IT-miljø opfylder aktuelle krav til ydeevne og kan reagere på ændrede infrastruktur behov.

**Opgradere, migrering og udrulning** - dette fokus område viser anbefalinger, der hjælper dig med at opgradere, overføre og installere SQL Server på den eksisterende infrastruktur.

Viser anbefalinger til at strømline dine IT-handlinger, implementere forebyggende vedligeholdelse og maksimere ydeevnen, **Handlinger og overvågnings** - dette fokus område.

Viser anbefalinger til at beskytte hverdagens, skal du sikre, at ændringer ikke negativt påvirker infrastrukturen, etablere Skift kontrolelement procedurer, og til at registrere og overvåge systemkonfigurationer, **ændring og konfigurationsstyring** - dette fokus område.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Skal du har til formål at resultat 100% i hver fokus område?

Ikke nødvendigvis. Anbefalinger er baseret på den viden og erfaringer ved Microsoft teknikere på tværs af tusindvis af kunde besøg. Men ingen to server-infrastruktur er den samme, og specifikke anbefalinger kan være mere eller mindre relevante for dig. Nogle sikkerhedsanbefalinger kan for eksempel være mindre relevant, hvis din virtuelle maskiner ikke vises på internettet. Nogle tilgængelighed anbefalinger kan være mindre relevant for tjenester, som giver lav prioritet ad hoc-dataindsamling og -rapportering. Problemer, der er vigtige for en fuldt udviklet virksomhed kan være mindre vigtigt til en opstart. Du vil identificere, hvilke fokusområder er dine prioriteter og derefter se på, hvordan dine resultater ændrer sig med tiden.

Hver anbefaling indeholder vejledning til, hvorfor det er vigtigt. Du skal bruge denne vejledning til at evaluere om implementere anbefalingen er relevante for dig, givet karakteren af din IT-tjenester, og de forretningsmæssige behov i din organisation.

## <a name="use-assessment-focus-area-recommendations"></a>Bruge vurdering fokus område anbefalinger

Før du kan bruge en vurdering løsning i OMS, skal du have den løsning, der er installeret. Læse mere om installation af løsninger, se [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md). Når den er installeret, kan du se oversigt over anbefalinger ved hjælp af feltet SQL vurdering på siden Oversigt i OMS.

Få vist opsummerede overholdelse bedømmelser til infrastruktur og derefter analysere i anbefalinger.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Til at få vist anbefalinger til et område med fokus og korrigerende handle

1. Klik på feltet **SQL vurdering** på siden **Oversigt** .
2. Gennemgå oversigtsoplysningerne i en af fokus område blade, og klik derefter på en for at få vist anbefalinger for det pågældende fokus område på siden **SQL vurdering** .
3. På en af siderne fokus område, kan du se de prioriterede anbefalinger, der er foretaget for dit miljø. Klik på en anbefaling under **Påvirkes objekter** at få vist oplysninger om, hvorfor denne meddelelse er foretaget.  
    ![Billede af SQL vurdering anbefalinger](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. Du kan foretage korrigerende handlinger, der foreslås i **Foreslåede handlinger**. Når Elementet er blevet løst, registrerer nyere bedømmelse, anbefales handlinger er taget og dit overholdelse resultat øges. Rettede elementer vises som **Overføres objekter**.

## <a name="ignore-recommendations"></a>Ignorere anbefalinger

Hvis du har anbefalinger, som du vil ignorere, kan du oprette en tekstfil, OMS vil bruge til at forhindre anbefalinger vises vurdering i søgeresultaterne.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Til at identificere anbefalinger, som du vil ignorere

1.  Log på dit arbejdsområde, og Åbn Log søgning. Bruge følgende forespørgsel til listen anbefalinger, der har mislykkedes til computere i dit miljø.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Her er et skærmbillede, som viser Log søgeforespørgsel: ![mislykkedes anbefalinger](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.  Vælg anbefalinger, som du vil ignorere. Du skal bruge værdierne for RecommendationId i den næste procedure.


### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Oprette og bruge en IgnoreRecommendations.txt tekstfil

1.  Oprette en fil med navnet IgnoreRecommendations.txt.
2.  Indsæt eller Skriv hver RecommendationId for hver anbefaling, du vil OMS ignoreret i en separat linje, Gem og Luk filen.
3.  Anbringe filen i følgende mappe på hver enkelt computer, hvor du vil OMS at ignorere anbefalinger.
    - På computere med Microsoft overvågning Agent (forbindelse direkte eller gennem Operations Manager) - *systemdrev*: \Programmer\Microsoft overvågnings Agent\Agent
    - På Operations Manager management server - *systemdrev*: \Programmer\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>At bekræfte, at anbefalinger, ignoreres

1.  Når næste planlagte vurdering kører som standard alle 7 dage, de angivne anbefalinger markeres ignoreret og vises ikke på dashboardet til vurdering.
2.  Du kan bruge følgende Log søgeforespørgsler til at få vist alle ignorerede anbefalinger.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.  Hvis du senere beslutter, at du vil have vist ignoreres anbefalinger, skal du fjerne alle IgnoreRecommendations.txt filer, eller du kan fjerne RecommendationIDs fra dem.

## <a name="sql-assessment-solution-faq"></a>SQL vurdering løsning ofte stillede spørgsmål

*Hvor ofte kører en vurdering?*
- Vurdering kører hver 7 dage.

*Er der en metode til at konfigurere, hvor ofte vurdering kører?*
- Ikke på nuværende tidspunkt.

*Hvis en anden server for ud, når jeg har tilføjet SQL vurdering løsningen, vil den blive vurderet?*
- Ja, når man finder ud af det vurderes fra og derefter på hver 7 dage.

*Hvis en server, der ikke er i funktion, når det bliver fjernet fra vurdering?*
- Hvis en server ikke fremlægger data til 3 uger, er den fjernet.

*Hvad er navnet på den proces, der indeholder data indsamles?*
- AdvisorAssessment.exe

*Hvor lang tid tager det for data indsamles?*
- Den faktiske dataindsamling på serveren tager cirka 1 time. Det kan tage længere tid på servere, der har et stort antal forekomster af SQL eller databaser.

*Hvilken type data, der indsamles?*
- De følgende typer data indsamles:
    - WMI
    - Registreringsdatabasen
    - Tællere i ydeevne
    - SQL dynamiske management visninger (DMV).

*Er der en metode til at konfigurere, når data indsamles?*
- Ikke på nuværende tidspunkt.

*Hvorfor skal jeg konfigurere en køre som konto?*
- For SQL Server skal køre et lille antal SQL-forespørgsler. En køre som konto med de SQL SERVER-VISNINGSTILSTAND tilladelser skal bruges i rækkefølge at køre.  Desuden for at forespørge WMI, lokal administratorlegitimationsoplysninger er påkrævet.

*Hvorfor vises kun de 10 vigtigste anbefalinger?*
- I stedet for hvorved du får en omfattende forvirrende liste over opgaver, anbefaler vi, at du fokusere på at løse prioriterede anbefalinger først. Når du løse dem, så flere anbefalinger bliver tilgængelig. Hvis du foretrækker at få vist en detaljeret liste, kan du få vist alle anbefalinger ved hjælp af OMS log søgning.

*Er der en måde at ignorerer en meddelelse?*
- Ja, se [Ignorer anbefalinger](#ignore-recommendations) ovenstående afsnit.



## <a name="next-steps"></a>Næste trin

- [Logfiler over Søg](log-analytics-log-searches.md) til at få vist detaljerede SQL vurdering data og anbefalinger.

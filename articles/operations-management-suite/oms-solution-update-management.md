<properties
    pageTitle="Opdatere Management-løsning i OMS | Microsoft Azure"
    description="I denne artikel henvender sig til at hjælpe dig med at forstå, hvordan du bruger denne løsning til at administrere opdateringer til Windows og Linux-computere."
    services="operations-management-suite"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
    />
<tags
    ms.service="operations-management-suite"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="magoedte"/>

# <a name="update-management-solution-in-omsmediaoms-solution-update-managementupdate-management-solution-iconpng-update-management-solution-in-oms"></a>![Opdatere Management-løsning i OMS](./media/oms-solution-update-management/update-management-solution-icon.png) Opdatere Management-løsning i OMS

Opdater Management-løsning i OMS tillader dig at administrere opdateringer til Windows og Linux-computere.  Du kan hurtigt vurdere status for tilgængelige opdateringer på alle agentcomputere og starte installationen af nødvendige opdateringer for servere. 

## <a name="prerequisites"></a>Forudsætninger

-   Windows supportmedarbejdere skal enten være konfigureret til at kommunikere med en Windows Server Update Services (WSUS) server eller have adgang til Microsoft Update.  

    >[AZURE.NOTE] Windows-agent kan ikke administreres samtidig ved System Center Configuration Manager.  
  
-   Linux supportmedarbejdere skal have adgang til en update-lager.  OMS Agent for Linux kan hentes fra [GitHub](https://github.com/microsoft/oms-agent-for-linux). 

## <a name="configuration"></a>Konfiguration

Udfør følgende trin for at føje Update Management-løsning til arbejdsområdet OMS og tilføje Linux supportmedarbejdere.  Windows supportmedarbejdere føjes der automatisk med ingen yderligere konfiguration.

1.  Føje Update Management-løsning til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje OMS løsninger](../log-analytics/log-analytics-add-solutions.md) fra løsningsgalleriet.  
2.  Vælg **Indstillinger** og derefter **Forbindelse datakilder**i portalen OMS.  Bemærk **arbejdsområde-ID** og enten **primær nøgle** eller **sekundær nøgle**.
3.  Udfør følgende trin for hver Linux-computer.

    en.  Installere den nyeste version af OMS Agent for Linux ved at køre følgende kommandoer.  Erstatte <Workspace ID> med arbejdsområde-ID'ET og <Key> med den primære eller sekundære nøgle.

        cd ~
        wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.2.0-75/omsagent-1.2.0-75.universal.x64.sh
        sudo bash omsagent-1.2.0-75.universal.x64.sh --upgrade -w <Workspace ID> -s <Key>

     b. Hvis du vil fjerne agenten, skal du køre følgende kommando.

        sudo bash omsagent-1.2.0-75.universal.x64.sh --purge

## <a name="management-packs"></a>Management packs

Hvis din System Center Operations Manager management gruppe er forbundet til arbejdsområdet OMS, skal de følgende management packs installeres i Operations Manager når du tilføjer denne løsning. Der er ingen konfiguration eller vedligeholdelse af disse management packs påkrævet. 

-   Microsoft System Center Advisor Update vurdering Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
-   Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
-   Opdatere installation MP

Du kan finde flere oplysninger om hvordan løsning management packs opdateres, [Forbinde Operations Manager til Log analyser](../log-analytics/log-analytics-om-agents.md).

## <a name="data-collection"></a>Dataindsamling

### <a name="supported-agents"></a>Understøttede supportmedarbejdere

I følgende tabel beskrives de tilknyttede kilder, der understøttes af denne løsning.

Forbundne kilde | Understøttes | Beskrivelse|
----------|----------|----------|
Windows supportmedarbejdere | Ja | Løsningen indsamler oplysninger om systemopdateringer fra Windows supportmedarbejdere og starter installation af nødvendige opdateringer.|
Linux supportmedarbejdere | Ja | Løsningen indsamler oplysninger om systemopdateringer fra Linux supportmedarbejdere.|
Operations Manager Administration af gruppe | Ja | Løsningen indsamler oplysninger om systemopdateringer fra supportmedarbejdere i en gruppe af forbundne administration.<br>En direkte forbindelse fra en agent for Operations Manager til Log Analytics er ikke påkrævet. Data er videresendt fra gruppen management til OMS-lager.|
Azure-lager-konto | Nej | Azure-lager omfatter ikke kan finde oplysninger om opdateringer.|  

### <a name="collection-frequency"></a>Frekvens af websteder

For hver administrerede Windows-computer, der udføres en scanning to gange om dagen.  Når en opdatering er installeret, skal opdateres mødeoplysningerne inden for 15 minutter.  

For hver administrerede Linux-computer udføres en scanning hver 3 timer.  

## <a name="using-the-solution"></a>Ved hjælp af løsningen

Når du tilføjer Update Management-løsning til arbejdsområdet OMS, tilføjes feltet **Administration af opdateringer** til dit OMS dashboard. Dette felt viser en Tæl og grafisk repræsentation af antallet computere i dit miljø, der aktuelt kræver opdateringer.<br><br>
![Opdater oversigt Management-felt](media/oms-solution-update-management/update-management-summary-tile.png)  

## <a name="viewing-update-assessments"></a>Visning Opdater bedømmelser

Klik på feltet **Update Management** for at åbne dashboardet for **Administration af opdateringer** . Dashboardet indeholder kolonnerne, i den følgende tabel. Hver kolonne vises op til ti elementer, der matcher den kolonne kriterier for det angivne område og tidsinterval. Du kan køre en log søgning, der returnerer alle poster ved at klikke på **se alle** nederst i kolonnen eller ved at klikke på kolonneoverskriften.

Kolonne | Beskrivelse|
----------|----------|
**Computere manglende opdateringer** ||
Kritiske eller sikkerhedsopdateringer | Lister over den øverste ti computere, der mangler opdaterer sorteret efter antallet opdateringer de er mangler. Klik på navnet på en computer til at køre en log søgning, der returnerer alle opdatere poster for den pågældende computer.|
Kritisk eller sikkerhedsopdateringer, der er ældre end 30 dage| Identificerer antallet af computere, der mangler kritiske eller sikkerhedsopdateringer grupperet efter tidsrummet, siden opdateringen blev publiceret. Klik på en af posterne, der kører en log søgning, der returnerer alle de manglende og vigtige opdateringer.|
**Påkrævet manglende opdateringer**||
Kritiske eller sikkerhedsopdateringer | Viser klassificeringer af opdateringer, at computere der mangler sorteret efter antallet computere manglende opdateringer i kategorien. Klik på en klassifikation for at køre en log søgning, der returnerer alle opdatere poster for klassificeringen.|
**Opdatere installationer**||
Opdatere installationer | Antallet af planlagte opdatering installationer og varighed indtil næste planlagte kørsel.  Klik på feltet til at få vist tidsplaner, i øjeblikket er aktive og færdige opdateringer eller til at planlægge en ny installation.|  
<br>  
![Opdater oversigt-Administrationsdashboard](./media/oms-solution-update-management/update-management-deployment-dashboard.png)<br>  
<br>
![Opdater administration Dashboard Computer visning](./media/oms-solution-update-management/update-management-assessment-computer-view.png)<br>  
<br>
![Opdater administration Dashboard pakke visning](./media/oms-solution-update-management/update-management-assessment-package-view.png)<br>  

## <a name="installing-updates"></a>Installation af opdateringer

Når opdateringer er blevet vurderet for alle Windows-computere i dit miljø, kan du har bedt om opdateringer, der er installeret ved at oprette en *Installation af opdateringer*.  En opdatering installation er en planlagt installation af nødvendige opdateringer til en eller flere Windows-computere.  Du angiver den dato og klokkeslæt for installationen ud over en computer eller en gruppe af computere, der skal medtages.  

Opdateringer er installeret af runbooks i Azure Automation.  Du kan ikke i øjeblikket får vist disse runbooks, og de kræver ikke en konfiguration.  Når der oprettes en opdatere installation, oprettes der en tidsplan i, der startes en master opdatering runbook på det angivne tidspunkt for de inkluderede computere.  Denne master runbook starter en underordnet runbook på hver Windows agent, som udfører installationen af nødvendige opdateringer.  

### <a name="viewing-update-deployments"></a>Visning Opdater installationer

Klik på feltet **Installation af opdateringer** for at få vist listen over eksisterende opdatering installationer.  De er grupperet efter status – **planlagt**, **kører**og **fuldført**.<br><br> ![Opdatere siden installationer tidsplan](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

De egenskaber, der vises for hver installation af opdateringer er beskrevet i følgende tabel.

Egenskaben | Beskrivelse|
----------|----------|
Navn | Navnet på installationen af opdateringer.|
Tidsplan | Type af tidsplan.  *OneTime* er i øjeblikket kun mulige værdi.|
Starttidspunktet|Dato og klokkeslæt, som den Update Installation er planlagt til at starte.|
Varighed | Antallet af minutter opdatering installationen har tilladelse til at køre.  Hvis alle opdateringer ikke installeres i varigheden, skal de resterende opdateringer vente til den næste opdatering-installation.|
Servere | Antallet af computere, der påvirkes af den Update Installation.|
Status | Aktuelle status for installation af opdateringer.<br><br>Mulige værdier er:<br>-Ikke startet<br>-Kører<br>-Færdig|  

Klik på en installation af opdateringer til at få vist dets detaljeret skærmen som indeholder kolonnerne i tabellen nedenfor.  Disse kolonner udfyldes ikke, hvis den installation af opdateringer ikke er startet endnu.<br>

Kolonne | Beskrivelse|
----------|----------|
**Computer resultater**||
Blev fuldført | Viser antallet af computere i opdatering installationen efter status.  Klik på en status for at køre en log søgning, der returnerer alle opdatere poster med denne status for den opdatere installation.|
Status for Installation af computer| Oversigt over de computere, der er involveret i den installation af opdateringer og procentdelen af opdateringer, der blev installeret. Klik på en af posterne, der kører en log søgning, der returnerer alle de manglende og vigtige opdateringer.|
**Opdatere forekomst resultater**||
Status for Installation af forekomst | Viser klassificeringer af opdateringer, at computere der mangler sorteret efter antallet computere manglende opdateringer i kategorien. Klik på en computer til at køre en log søgning, der returnerer alle opdatere poster for den pågældende computer.|  
<br><br> ![Oversigt over opdatering installation resultater](./media/oms-solution-update-management/update-la-updaterunresults-page.png)

### <a name="creating-an-update-deployment"></a>Oprette en installation af opdateringer

Oprette en ny opdatering installation ved at klikke på knappen **Tilføj** øverst på skærmen for at åbne siden **Ny installation af opdateringer** .  Du skal angive værdier for egenskaberne i den følgende tabel.

Egenskaben | Beskrivelse|
----------|----------|
Navn | Entydigt navn til at identificere update installationen.|
Tidszone | Tidszone skal bruges til starttidspunktet.|
Starttidspunktet | Dato og klokkeslæt for at starte installationen opdatering.|
Varighed | Antallet af minutter opdatering installationen har tilladelse til at køre.  Hvis alle opdateringer ikke installeres i varigheden, skal de resterende opdateringer vente til den næste opdatering-installation.|
Computere | Navne på computere eller computergrupper, der skal medtages i den installation af opdateringer.  Vælg en eller flere poster på rullelisten.|
<br><br> ![Ny opdateringssiden installation](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Tidsinterval

Som standard er omfanget af de data, analysere Update Management-løsning fra alle forbundne management grupper, der oprettes i den sidste 1 dag. 

Hvis du vil ændre tidsintervallet for dataene, Vælg **Data baseret på** øverst i dashboardet. Du kan vælge poster oprettet eller opdateret i de seneste 7 dage, 1 dag eller 6 timer. Eller du kan vælge **brugerdefineret** og angive et brugerdefineret datointerval.<br><br> ![Indstillingen Brugerdefineret tidsinterval](./media/oms-solution-update-management/update-la-time-range-scope-databasedon.png)  

## <a name="log-analytics-records"></a>Log Analytics poster

Opdater Management-løsning opretter to typer af poster i OMS-lager.

### <a name="update-records"></a>Opdatere poster

Der oprettes en post af typen **opdatere** for hver opdatering, er installeret, eller det er nødvendigt på alle computere. Opdateringsposter har egenskaber i den følgende tabel.

Egenskaben | Beskrivelse|
----------|----------|
Type | *Opdater*|
SourceSystem | Den kilde, godkendt installationen af opdateringen.<br>Mulige værdier er:<br>-Microsoft Update<br>-Windows Update<br>-SCCM<br>-Linux servere (hentet fra pakke administratorer)|
Godkendt | Angiver, om opdateringen er blevet godkendt til installation.<br> Dette er i øjeblikket valgfrit som for Linux servere retter ikke styres af OMS.|
Klassifikation til Windows | Klassifikation af opdateringen.<br>Mulige værdier er:<br>-Programmer<br>-Vigtige opdateringer<br>-Definition af opdateringer<br>-Funktion Packs<br>-Sikkerhedsopdateringer<br>-Servicepakker<br>-Opdateringer<br>-Opdateringer|
Klassifikation for Linux | Cassification af opdateringen.<br>Mulige værdier er:<br>-Vigtige opdateringer<br>-Sikkerhedsopdateringer<br>-Andre opdateringer|
Computer | Navnet på computeren.|
InstallTimeAvailable | Angiver, om installationstiden er tilgængelig fra andre supportmedarbejdere, der har installeret den samme opdatering.|
InstallTimePredictionSeconds | Anslået installationstid i sekunder, der er baseret på andre supportmedarbejdere, der har installeret den samme opdatering.|
KBID | KB-artiklen, der beskriver opdateringen ID.|
ManagementGroupName | Navnet på gruppen management for SCOM supportmedarbejdere.  I forbindelse med andre supportmedarbejdere er AOI -<workspace ID>.|
MSRCBulletinID | ID i Microsoft-sikkerhedsbulletin, der beskriver opdateringen.|
MSRCSeverity | Alvorlighed af sikkerhedsbulletin fra Microsoft.<br>Mulige værdier er:<br>-Kritiske<br>-Vigtige<br>-Moderat|
Valgfri | Angiver, om opdateringen er valgfrit.|
Produkt | Navnet på det produkt, der opdateres til.  Klik på **Vis** for at åbne en artikel i en browser.|
PackageSeverity | Alvorlighed af sikkerhedsrisikoen fast i denne opdatering, som har rapporteret Linux distro leverandører. | 
PublishDate | Dato og klokkeslæt, der blev installeret opdateringen.|
RebootBehavior | Angiver, hvis opdateringen fremtvinger en genstart.<br>Mulige værdier er:<br>-canrequestreboot<br>-neverreboots|
RevisionNumber | Versionsnummeret af opdateringen.|
SourceComputerId | GUID til entydigt identificerer computeren.|
TimeGenerated | Dato og klokkeslæt, posten sidst blev opdateret.|
Titel | Titlen på opdateringen.|
Tidspunkt | GUID til entydigt identificerer opdateringen.|
UpdateState | Angiver, om opdateringen er installeret på denne computer.<br>Mulige værdier er:<br>-Installeres - er opdateringen installeret på denne computer.<br>-Det er nødvendigt - opdateringen ikke er installeret og bruges på denne computer.|  

<br>
Når du udfører en hvilken som helst log søgning, der returnerer poster med **en opdateringstype** kan du vælge visningen **opdateringer** , som viser et sæt af fliser summering af de opdateringer, der returneres af søgningen. Du kan klikke på posterne i **mangler og anvendte opdateringer** og **krævede og valgfrie opdateringer** felter for at begrænse visningen til, der er angivet af opdateringer. Vælg den **liste** eller **tabel** visning til at returnere de enkelte poster.<br> 

![Opdater Vis log søgning med posttype opdatering](./media/oms-solution-update-management/update-la-view-updates.png)  

I visningen **tabel** kan du klikke på **KBID** for en hvilken som helst post til at åbne en browser med KB-artiklen. Dette kan du hurtigt kan du læse om oplysninger om bestemte opdatering.<br> 

![Log Søg tabelvisning med felter posttype opdateringer](./media/oms-solution-update-management/update-la-view-table.png)

Klik på linket **Vis** ud for KBID til at åbne KB-artiklen i **listevisningen** .<br>

![Log Søg listevisning med felter posttype opdateringer](./media/oms-solution-update-management/update-la-view-list.png)


###<a name="updatesummary-records"></a>UpdateSummary poster

Der oprettes en post af typen **UpdateSummary** for hver Windows agent computer. Denne post opdateres, hver gang computeren scannes efter opdateringer. **UpdateSummary** poster har egenskaber i den følgende tabel.

Egenskaben | Beskrivelse|
----------|----------|
Type | UpdateSummary|
SourceSystem | OpsManager |
Computer | Navnet på computeren.|
CriticalUpdatesMissing | Antallet af vigtige opdateringer mangler på computeren.|
ManagementGroupName | Navnet på gruppen management for SCOM supportmedarbejdere. I forbindelse med andre supportmedarbejdere er AOI -<workspace ID>.|
NETRuntimeVersion | Version af .NET runtime installeret på computeren.|
OldestMissingSecurityUpdateBucket | Malerbøtte kategorisere klokkeslættet, da den ældste manglende sikkerhedsopdateringer opdatere på denne computer er blevet publiceret.<br>Mulige værdier er:<br>-Ældre<br>-180 dage siden<br>-150 dage siden<br>-120 dage siden<br>-90 dage siden<br>-60 dage siden<br>-Gå 30 dage<br>-Seneste|
OldestMissingSecurityUpdateInDays | Antallet af dage siden ældste manglende sikkerheden på denne computer er blevet publiceret.|
OsVersion | Version af operativsystemet er installeret på computeren.|
OtherUpdatesMissing | Antallet af andre opdateringer mangler på computeren.|
SecurityUpdatesMissing | Antallet af sikkerhedsopdateringer mangler på computeren.|
SourceComputerId | GUID til entydigt identificerer computeren.|
TimeGenerated | Dato og klokkeslæt, posten sidst blev opdateret.|
TotalUpdatesMissing |Samlet antal opdateringer mangler på computeren.|
WindowsUpdateAgentVersion | Versionsnummeret for Windows Update-agent på computeren.|
WindowsUpdateSetting | Indstilling for hvordan computeren skal installere vigtige opdateringer.<br>Mulige værdier er:<br>-Deaktiveret<br>-Besked, før installation<br>-Planlagt installation|
WSUSServer | URL-adressen på WSUS server, hvis computeren er konfigureret til at bruge en.|  

## <a name="sample-log-searches"></a>Eksempel log søgninger

Den følgende tabel indeholder eksempel log søger efter opdateringsposter, der indsamles via denne løsning. 

Forespørgsel | Beskrivelse|
----------|----------|
Alle computere med manglende opdateringer | Skriv = opdatering UpdateState = behov valgfrit = false & #124; Vælg Computer, titel, KBID, klassifikation, UpdateSeverity og PublishedDate|
Manglende opdateringer til computer "COMPUTER01.contoso.com" (Erstat med navnet på din egen computer) | Skriv = opdatering UpdateState = behov valgfrit = falsk Computer = "COMPUTER01.contoso.com" & #124; Vælg Computer, titel, KBID, produkt, UpdateSeverity, PublishedDate|
Alle computere med manglende kritiske eller sikkerhedsopdateringer | Skriv = opdatering UpdateState = behov valgfrit = false (klassifikation = "Sikkerhedsopdateringer" eller klassifikation = "Vigtige opdateringer")|
Kritisk eller sikkerhed opdateringer, der bruges til computere, hvor opdateringer manuelt er anvendt | Skriv = opdatering UpdateState = behov valgfrit = false (klassifikation = "Sikkerhedsopdateringer" eller klassifikation = "Vigtige opdateringer") Computer i {Type = UpdateSummary WindowsUpdateSetting = manuel & #124; Entydige computeren} & #124; Forskellige KBID|
Hændelser for computere, der mangler kritiske eller sikkerhed påkrævet opdateringer | Skriv = begivenhed EventLevelName = fejl Computer i {Type = opdatering (klassifikation = "Sikkerhedsopdateringer" eller klassifikation = "Vigtige opdateringer") UpdateState = behov valgfrit = false & #124; Entydige computeren}|
Alle computere med manglende opdateringer | Skriv = opdatering valgfrit = falsk klassifikation = "Samlede opdateringer" UpdateState = behov & #124; Vælg Computer, titel, KBID, klassifikation, UpdateSeverity og PublishedDate|
Entydige manglende opdateringer på tværs af alle computere | Skriv = opdatering UpdateState = behov valgfrit = false & #124; Entydige titel|
WSUS computer medlemskab | Skriv = UpdateSummary & #124; måle count() ved WSUSServer|
Konfiguration af automatiske opdateringer | Skriv = UpdateSummary & #124; måle count() ved WindowsUpdateSetting|
Computere med automatiske opdateringer, der er deaktiveret | Skriv = UpdateSummary WindowsUpdateSetting = manuel|  
Liste over alle de Linux maskiner, som har en pakke-opdatering, der er tilgængelige | Skriv = opdatering og OSType = Linux og UpdateState! = "Ikke er påkrævet" & #124; måle count() ved Computer|
Liste over alle de Linux maskiner, som har en pakke-opdatering, der er tilgængelige som adresser kritisk eller sikkerhed sikkerhedsrisiko | Skriv = opdatering og OSType = Linux og UpdateState! = "Ikke er påkrævet" og (klassifikation = "Vigtige opdateringer" eller klassifikation = "Sikkerhedsopdateringer") & #124; måle count() ved Computer|
Liste over alle pakker, som har en tilgængelig opdatering | Skriv = opdatering og OSType = Linux og UpdateState! = "Ikke er påkrævet"|
Liste over alle pakker, som har en tilgængelig opdatering, hvilke adresser kritisk eller sikkerhed sikkerhedsrisiko | Skriv = opdatering og OSType = Linux og UpdateState! = "Ikke er påkrævet" og (klassifikation = "Vigtige opdateringer" eller klassifikation = "Sikkerhedsopdateringer")|
Liste over alle maskiner "Ubuntu" med alle tilgængelige opdateringer | Skriv = opdatering og OSType = Linux og OSName = Ubuntu & #124; måle count() ved Computer|

## <a name="next-steps"></a>Næste trin

- Brug Log søgninger i [Log Analytics](../log-analytics/log-analytics-log-searches.md) til at få vist detaljerede opdateringsdata.

- [Oprette dine egne dashboards](../log-analytics/log-analytics-dashboards.md) viser opdatering overholdelse af angivne standarder for administrerede computere.

- [Opret beskeder](../log-analytics/log-analytics-alerts.md) , når der registreres kritiske opdateringer som mangler på computere eller en computer har automatiske opdateringer, der er deaktiveret.  





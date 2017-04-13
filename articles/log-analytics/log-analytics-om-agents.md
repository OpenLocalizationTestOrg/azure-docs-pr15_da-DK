<properties
    pageTitle="Forbinde Operations Manager til Log Analytics | Microsoft Azure"
    description="Hvis du vil bevare din eksisterende investering i System Center Operations Manager, og bruge udvidede funktioner med Log Analytics, skal integrere du Operations Manager med arbejdsområdet OMS."
    services="log-analytics"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="magoedte"/>

# <a name="connect-operations-manager-to-log-analytics"></a>Tilsluttes Log Analytics Operations Manager

Hvis du vil bevare din eksisterende investering i System Center Operations Manager, og bruge udvidede funktioner med Log Analytics, skal integrere du Operations Manager med arbejdsområdet OMS.  Dette kan du udnytte salgsmuligheder af OMS mens du fortsætter med at bruge Operations Manager for at:

- Fortsætte med at overvåge tilstanden for din IT-tjenester med Operations Manager
- Vedligeholde integration med dine ITSM løsninger understøttende hændelsen og problemet
- Administrere supportmedarbejdere installeres lokalt og offentlige skyen IaaS virtuelle maskiner, som du overvåge med Operations Manager livscyklus

Integration med System Center Operations Manager tilføjer værdi til din strategi for tjenesten handlinger ved at udnytte hastighed og effektivitet af OMS i indsamling, opbevaring og analyse af data fra Operations Manager.  OMS hjælper med at koordinere og arbejde med at identificere fejlene på problemer og fremhævelse reoccurrences overholde din eksisterende problem management proces.   Fleksibilitet af søgemaskinen om at undersøge ydeevne, begivenheden og beskeder om data med omfattende dashboards og rapporteringsfunktioner til at vise disse data i sigende måder, demonstrerer styrken OMS samler på færdiggørelse Operations Manager.

Supportmedarbejdere rapportering til gruppen Operations Manager management Indsaml data fra dine servere, der er baseret på de Log Analytics-datakilder og løsninger, du har aktiveret i abonnementet OMS.  Afhængigt af den løsning, du har aktiveret, data fra disse løsninger enten sendes direkte fra en Operations Manager management server til webtjenesten OMS eller på grund af mængden data indsamles på systemet agent-administreret, der sendes direkte fra en agent til OMS webtjeneste. Management-serveren videresender direkte OMS dataene til webtjenesten OMS, er den aldrig skrevet til OperationsManager eller OperationsManagerDW databasen.  Når en indholdsstyringsserver mister-forbindelse med webtjeneste OMS, fanger data lokalt, indtil kommunikation er genoprettet med OMS.  Hvis management-serveren er offline på grund af planlagt vedligeholdelse eller ikke-planlagt afbrydelse, genoptage en anden indholdsstyringsserver i gruppen management-forbindelse med OMS.  

Følgende diagram viser forbindelsen mellem styring af servere og supportmedarbejdere i en gruppe til administration af System Center Operations Manager og OMS, herunder retning og porte.   

![OMS-handlinger-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

## <a name="system-requirements"></a>Systemkrav
Før du starter, skal du gennemgå følgende oplysninger til at kontrollere du opfylder nødvendige forudsætninger.

- OMS understøtter kun Operations Manager 2012 SP1 UR6 og større, og Operations Manager 2012 R2 UR2 og større.  Proxyunderstøttelse af blev tilføjet i Operations Manager 2012 SP1 UR7 og Operations Manager 2012 R2 UR3.
- Alle Operations Manager supportmedarbejdere skal opfylde mindste supportkrav. Sikre, at supportmedarbejdere er op til den mindste opdatering, ellers Windows agent trafik mislykkes og mange fejl kan udfylde hændelseslog Operations Manager.
- Et OMS-abonnement.  Yderligere oplysninger finder du gennemse [komme i gang med Log Analytics](log-analytics-get-started.md).

## <a name="connecting-operations-manager-to-oms"></a>Tilsluttet OMS Operations Manager
Udføre den følgende række trin til at konfigurere din Operations Manager management gruppe for at oprette forbindelse til en af dine OMS arbejdsområder.

1. Vælg **Administration af** arbejdsområdet i konsollen Operations Manager.
2. Udvid noden handlinger Management-pakken, og klik på **forbindelse**.
3. Klik på linket **registrere til handlinger Management pakke** .
4. På den **Handlinger Management pakke Onboarding guiden: godkendelse** side, indtast mailadresse eller telefonnummer og adgangskoden til administratorkontoen, der er knyttet til abonnementet OMS, og klik på **Log på**.
5. Når du er blevet godkendt, på den **Handlinger Management pakke Onboarding guiden: Vælg arbejdsområde** side, bliver du bedt om at vælge dit OMS arbejdsområde.  Hvis du har mere end én arbejdsområde, Vælg det arbejdsområde, du vil registrere med gruppen Operations Manager management fra på rullelisten, og klik derefter på **Næste**.

    >[AZURE.NOTE] Operations Manager understøtter kun én OMS arbejdsområde ad gangen. Forbindelsen og de computere, der blev registreret til OMS til det forrige arbejdsområde er fjernet fra OMS.

6. På den **Handlinger Management pakke Onboarding guiden: Oversigt over** skal du bekræfte dine indstillinger og hvis de er korrekte, skal du klikke på **Opret**.
7. På den **Handlinger Management pakke Onboarding guiden: afslutte** skal du klikke på **Luk**.

### <a name="add-agent-managed-computers"></a>Føje en agent-administreret computere
Når du har konfigureret integration med arbejdsområdet OMS, dette kun opretter forbindelse til OMS, ikke data, der indsamles fra supportmedarbejdere rapportering til din gruppe i administration. Dette ske ikke indtil, når du har konfigureret hvilken bestemte agent-administreret computere indsamler data for Log analyser. Du kan enten vælge computerobjekterne enkeltvis, eller du kan vælge en gruppe, som indeholder Windows-computerobjekter. Du kan vælge en gruppe, som indeholder forekomster af en anden klasse, f.eks logiske SQL-databaser.

1. Åbne konsollen Operations Manager, og vælg **Administration af** arbejdsområdet.
2. Udvid noden handlinger Management-pakken, og klik på **forbindelse**.
3. Klik på linket **Tilføj en Computer/gruppe** under handlinger overskrift på i højre side af ruden.
4. I dialogboksen **Søg Computer** kan du søge efter computere eller grupper overvåges af Operations Manager. Vælg computere eller grupper til indbyggede til OMS, klik på **Tilføj**, og klik derefter på **OK**.

Du kan få vist computere og grupper, der er konfigureret til at indsamle data fra noden administrerede computere under handlinger Management Suite i arbejdsområdet **Administration** af konsollen handlinger.  Her kan du tilføje eller fjerne computere og grupper efter behov.

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>Konfigurere indstillinger for proxyserver OMS i konsollen handlinger
Udfør følgende trin, hvis en intern proxyserver er mellem management gruppe og OMS webtjeneste.  Disse indstillinger er centralt administrerede fra gruppen administration og fordeles agent-administreret systemer, der er inkluderet i området til at indsamle data for OMS.  Dette er vigtigt, når bestemte løsninger, der springer management-serveren og sende data direkte til OMS webtjeneste.

1. Åbne konsollen Operations Manager, og vælg **Administration af** arbejdsområdet.
2. Udvid handlinger Management-pakken, og klik derefter på **forbindelser**.
3. Klik på **Konfigurer proxyserver**i visningen OMS forbindelse.
4. På **Handlinger Management Suite guiden: proxyserver** siden, Vælg **Brug en proxyserver for at få adgang til handlinger Management-pakken**, og derefter skrive URL-adresse med portnummeret, for eksempel, http://corpproxy:80 og klik derefter på **Udfør**.

Hvis din proxy-server kræver godkendelse, skal du udføre følgende trin for at konfigurere legitimationsoplysninger og indstillinger, der skal overføres til administrerede computere, som rapporterer til OMS i gruppen administration.

1. Åbne konsollen Operations Manager, og vælg **Administration af** arbejdsområdet.
2. Vælg **profiler**under **RunAs konfiguration**.
3. Åbn den **System Center Advisor køre som profil Proxy** -profil.
4. Klik på Kør som profil guiden Tilføj for at bruge en konto, der kører som. Du kan oprette en ny [konto Kør som](https://technet.microsoft.com/library/hh321655.aspx) eller brug en eksisterende konto. Denne konto skal have tilladelse til at gå gennem proxy-server.
5. Hvis du vil angive kontoen til at administrere, Vælg **en valgte klasse, gruppen eller et objekt**, skal du klikke på **Vælg...** og derefter klikke på **gruppe...** for at åbne feltet **Gruppe Søg** .
6. Søge efter og derefter vælge **Microsoft System Center Advisor overvågning Server gruppe**.  Klik på **OK** , når du har valgt gruppen for at lukke **Gruppe** søgefeltet.
7.  Klik på **OK** for at lukke feltet **Tilføj en konto, der kører som** .
8.  Klik på **Gem** for at fuldføre guiden og gemme dine ændringer.

Når forbindelsen er oprettet, og du har konfigureret hvilken supportmedarbejdere som indsamling og rapporteringsdata til OMS, anvendes følgende konfiguration i gruppen management ikke nødvendigvis i rækkefølge:

- Køre som konto **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** oprettes.  Der er knyttet til profilen Kør som **Microsoft System Center Advisor køre som profil Blob** og målretning af to klasser - **Samling Server** og **Operations Manager Management Group**.
- Der oprettes to forbindelser.  Først hedder **Microsoft.SystemCenter.Advisor.DataConnector** og er automatisk konfigureret med et abonnement, der videresender alle beskeder, der er dannet ud fra forekomster af alle klasser i gruppen management til OMS Log analyser. Anden forbindelsen er **Advisor forbindelse**, som er ansvarlig for at kommunikere med OMS webtjeneste og deling af data.
- Supportmedarbejdere og grupper, du har valgt til at indsamle data i gruppen management, føjes til **Microsoft System Center Advisor overvågning Server gruppe**.

## <a name="management-pack-updates"></a>Management pack opdateringer
Når konfigurationen er fuldført, opretter gruppen Operations Manager management forbindelse til tjenesten OMS.  Management server vil synkronisere med webtjenesten og modtage opdaterede konfigurationsoplysninger i form af management packs til de løsninger, du har aktiveret, der integreres med Operations Manager.   Operations Manager skal søge efter opdateringer til disse management packs til automatisk at hente og importere dem, når de er tilgængelige.  Der er to regler især som kontrollere denne funktionsmåde:

- **Microsoft.SystemCenter.Advisor.MPUpdate** - opdaterer base OMS management packs. Kører hver 12 (12) timer som standard.
- **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - opdateringer løsning management packs er aktiveret i arbejdsområdet. Kører hvert fem (5) som standard.

Du kan tilsidesætte disse to regler for at forhindre automatisk download ved at deaktivere dem, eller ændre hyppigheden for hvor ofte management-serveren synkroniserer med OMS til at se, om en ny management pack er tilgængelig og skal hentes.  Følg trinnene, [hvordan du kan tilsidesætte en regel eller skærmen](https://technet.microsoft.com/library/hh212869.aspx) til at ændre parameteren **frekvens** med en værdi i sekunder for at ændre tidsplan for synkronisering, eller ændre parameteren **aktiveret** for at deaktivere reglerne.  Målrette tilsidesætter til alle objekter i klasse Operations Manager Management Group.

Hvis du vil fortsætte med at følge din eksisterende proces for at styre management pack versioner i din fremstilling management gruppe, kan du deaktivere reglerne og aktiverer dem på bestemte tidspunkter, når opdateringer er tilladt. Hvis du har en udvikling eller QA management gruppe i dit miljø, og den har forbindelse til internettet, kan du konfigurere gruppen management til et arbejdsområde til OMS til at understøtte dette scenario.  Dette kan du gennemse og evaluere gentagen versioner af OMS management servicepakker før du slipper dem til din fremstilling administration af gruppe.

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>Skifte en Operations Manager gruppe til et nyt OMS arbejdsområde
1. Log på abonnementet OMS, og Opret nyt arbejdsområde i [Microsoft Operations Management Suite](http://oms.microsoft.com/).
2. Åbn konsollen Operations Manager med en konto, der er medlem af rollen Operations Manager administratorer, og vælg **Administration af** arbejdsområdet.
3. Udvid handlinger Management-pakken, og vælg **forbindelser**.
4. Vælg linket **Omkonfigurere handlingen Management Suite** i midten side i ruden.
5. Følg **Handlinger Management pakke Onboarding guiden** , og Angiv den mailadresse eller telefonnummer og adgangskoden til administratorkontoen, der er knyttet til det nye OMS arbejdsområde.

    > [AZURE.NOTE] Den **Handlinger Management pakke Onboarding guiden: Vælg arbejdsområde** side skal præsentere det eksisterende arbejdsområde, der er i brug.


## <a name="validate-operations-manager-integration-with-oms"></a>Validere Operations Manager Integration med OMS
Der er et par forskellige måder, du kan kontrollere, at din OMS til Operations Manager-integration er gået igennem.

### <a name="to-confirm-integration-from-the-oms-portal"></a>For at bekræfte integration fra portalen OMS

1.  Klik på feltet **Indstillinger** i portalen OMS
2.  Vælg **forbundet kilder**.
3.  I tabellen i afsnittet System Center Operations Manager, skal du se navnet på gruppen administration vises med antallet af supportmedarbejdere og status, når data sidst blev modtaget.

    ![OMS-indstillinger-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

4.  Bemærk værdien **Arbejdsområde-ID** under i venstre side af siden Indstillinger.  Du validerer den mod Operations Manager management gruppen nedenfor.  

### <a name="to-confirm-integration-from-the-operations-console"></a>For at bekræfte integration fra konsollen handlinger

1.  Åbne konsollen Operations Manager, og vælg **Administration af** arbejdsområdet.
2.  Vælg **Management Packs** og i den **øje:** tekstfeltet skrive **Advisor** eller **Intelligence**.
3.  Afhængigt af de løsninger, du har aktiveret, får du vist en tilsvarende management pack vises i søgeresultaterne.  Eksempelvis hvis du har aktiveret beskeder om Management-løsning, management pack Microsoft System Center Advisor beskeder om Management bliver på listen.
4.  Gå til visningen **Handlinger Management Suite\Health tilstand** fra visningen **overvågnings** .  Vælg en indholdsstyringsserver under ruden **Management Server tilstand** , og Bekræft i ruden **Detaljevisning** værdien for egenskaben **godkendelse service URI** svarer til det OMS arbejdsområde-ID.

    ![OMS-OpsMgr-mg-authsvcuri-Property-MS](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)


## <a name="remove-integration-with-oms"></a>Fjerne Integration med OMS
Når du ikke længere kræver integration mellem Operations Manager management gruppe og OMS arbejdsområde, der er flere trin, der kræves korrekt fjerne tilslutning og konfiguration i gruppen administration. Følgende procedure skal du opdatere din OMS arbejdsområde ved at slette referencen for management group, slette OMS forbindelser, og slet derefter management packs understøttende OMS.   

1.  Åbn Operations Manager kommandoshell med en konto, der er medlem af rollen Operations Manager administratorer.

    >[AZURE.WARNING] Kontrollere, at du ikke har nogen brugerdefinerede management packs med word Advisor eller IntelligencePack i navnet, før du fortsætter, ellers følgende trin kan slette dem fra gruppen administration.

2.  Fra shell kommandoprompten, Skriv`Get-SCOMManagementPack -name "*advisor*" | Remove-SCOMManagementPack`

3.  Type, næste gang`Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack`

4.  Åbn konsollen Operations Manager handlinger med en konto, der er medlem af rollen Operations Manager administratorer.
5.  Vælg noden **Management Packs** under **Administration**og i den **øje:** skal du skrive **Advisor** og bekræfte de følgende management packs stadig er importeret i din gruppe i Administration:

    - Microsoft System Center Advisor
    - Microsoft System Center Advisor interne

6. Klik på feltet **Indstillinger** i portalen OMS.
7.  Vælg **forbundet kilder**.
8.  I tabellen i afsnittet System Center Operations Manager, skal du se navnet på den management-gruppe, du vil fjerne fra arbejdsområdet.  Klik på **Fjern**under kolonnen **Sidste Data**.  

    >[AZURE.NOTE] **Fjern** link vil ikke være tilgængelige indtil efter 14 dage, hvis der ikke er nogen aktivitet, der er fundet fra gruppen forbundne administration.  
   
9.  Der vises et vindue, der beder dig om at bekræfte, at du vil fortsætte med fjernelsen.  Klik på **Ja** for at fortsætte. 

Hvis du vil slette de to forbindelser - Microsoft.SystemCenter.Advisor.DataConnector og Advisor Connector, Gem PowerShell-script under til din computer og udføre ved hjælp af eksemplerne nedenfor.

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

>[AZURE.NOTE] Den computer, du kører dette script fra, hvis det ikke en indholdsstyringsserver bør have Operations Manager 2012 SP1 eller R2 kommandoshell installeret afhængigt af versionen af din gruppe i administration.

```
    `param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

I fremtiden Hvis du planlægger på genoprette forbindelse til din administration af gruppe til en OMS arbejdsområde, du skal importere igen på `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` management pack fil fra den seneste opdateringspakke anvendt til din gruppe i administration.  Du kan finde denne fil i den `%ProgramFiles%\Microsoft System Center 2012` eller `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` mappe.

## <a name="next-steps"></a>Næste trin

- [Tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md) til at tilføje funktionalitet og indsamle data.
- [Konfigurer proxy og firewall-indstillinger i Log analyser](log-analytics-proxy-firewall.md) , hvis din organisation bruger en proxyserver eller firewall, så supportmedarbejdere kan kommunikere med Log Analytics-tjenesten.

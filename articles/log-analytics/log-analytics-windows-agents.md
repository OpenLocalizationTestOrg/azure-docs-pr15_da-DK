<properties
    pageTitle="Oprette forbindelse Windows-computere til Log Analytics | Microsoft Azure"
    description="I denne artikel beskrives trinnene til at oprette forbindelse til Windows-computere i din lokale infrastruktur direkte til OMS ved hjælp af en tilpasset version af Microsoft overvågning Agent (MMA)."
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


# <a name="connect-windows-computers-to-log-analytics"></a>Oprette forbindelse Windows-computere til Log Analytics

I denne artikel beskrives trinnene til at oprette forbindelse til Windows-computere i din lokale infrastruktur direkte til OMS arbejdsområder ved hjælp af en tilpasset version af Microsoft overvågning Agent (MMA). Du skal installere og oprette forbindelse supportmedarbejdere for alle de computere, du vil til indbyggede til OMS i rækkefølge efter dem til at sende data til OMS og for at få vist og reagere på disse data i portalen OMS. Hver agent kan rapportere til flere arbejdsområder.

Du kan installere supportmedarbejdere ved hjælp af konfiguration, kommandolinjen, eller med beskedteksten tilstand konfiguration (DTK) i Azure Automation.  

>[AZURE.NOTE] For virtuelle maskiner kan kører i Azure, du forenkle installation ved hjælp af [virtuelt filtypenavn](log-analytics-azure-vm-extension.md).

På computere med forbindelse til internettet anvender agenten forbindelse til internettet til at sende data til OMS. For computere, der ikke har forbindelse til internettet, kan du bruge en proxy eller OMS Log Analytics domænes.

Det er nemt ved hjælp af 3 enkle trin at tilslutte din Windows-computere OMS:

1. Hente installationsfilen agent
2. Installere agent ved hjælp af den metode, du vælger
3. Konfigurere tjenesten agent eller tilføje yderligere arbejdsområder, hvis det er nødvendigt

I det følgende diagram viser forholdet mellem din Windows-computere og OMS, når du har installeret og konfigureret supportmedarbejdere.

![OMS-direkte-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## <a name="system-requirements-and-required-configuration"></a>Systemkrav og påkrævede konfiguration
Før du installerer eller implementerer supportmedarbejdere, kan du se følgende oplysninger til at sikre, at du opfylder kravene.

- Du kan kun installere OMS MMA på computere, der kører Windows Server 2008 SP 1 eller nyere eller Windows 7 SP1 eller nyere.
- Du skal have et OMS-abonnement.  Yderligere oplysninger finder du i [komme i gang med Log Analytics](log-analytics-get-started.md).
- Alle Windows-computere skal kunne oprette forbindelse til internettet ved hjælp af HTTPS. Denne forbindelse kan være direkte via en proxy, eller OMS Log Analytics domænes.
- Du kan installere OMS MMA på enkeltstående computere, servere og virtuelle computere. Hvis du vil oprette forbindelse Azure-hostet virtuelle maskiner til OMS, skal du se [virtuelle forbinde Azure-computere til Log Analytics](log-analytics-azure-vm-extension.md).
- Agenten skal bruge TCP-port 443 til forskellige ressourcer. Se [konfigurere proxy og firewall-indstillinger i Log Analytics](log-analytics-proxy-firewall.md)kan finde flere oplysninger.

## <a name="download-the-agent-setup-file-from-oms"></a>Hente installationsfilen agent fra OMS
1. Klik på feltet **Indstillinger** i portalen OMS, på siden **Oversigt** .  Klik på fanen **Forbindelse kilder** øverst.  
    ![Forbundne kilder under fanen](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Klik på **Hent Windows Agent** gælder for din computer processortype for at hente installationsfilen under **Vedhæfte computere direkte**.
3. Klik på ikonet kopi til højre for **Arbejdsområde-ID**, og Indsæt-ID i Notesblok.
4. Klik på ikonet Kopiér og Indsæt tasten i Notesblok i højre side af **Primærnøgle**.     
    ![kopiere arbejdsområde-ID og primærnøgle](./media/log-analytics-windows-agents/oms-direct-agent-primary-key.png)

## <a name="install-the-agent-using-setup"></a>Installere agent ved hjælp af konfiguration
1. Kør installationsprogrammet for at installere agenten på en computer, du vil administrere.
2. Klik på **Næste**på siden Velkommen.
3. Læs licensvilkårene siden Licensbetingelser, og klik derefter på **jeg accepterer**.
4. Ændre eller beholde standardmappen installation, og klik derefter på **Næste**på siden destinationsmappe.
5. På siden Indstillinger for Sideopsætning Agent, kan du vælge at oprette forbindelse på agent til Azure Log Analytics (OMS), Operations Manager, eller du kan lade valgmulighederne være tomt, hvis du vil konfigurere en agent for den senere. Klik på **Næste**.   
    - Hvis du vælger at oprette forbindelse til Azure Log Analytics (OMS), indsætte **Arbejdsområde-ID** og **Arbejdsområde nøgle (primærnøgle)** , du kopierede til Notesblok i den forrige procedure, og klik derefter på **Næste**.  
        ![indsætte arbejdsområde-ID og primærnøgle](./media/log-analytics-windows-agents/connect-workspace.png)
    - Hvis du vælger at oprette forbindelse til Operations Manager, Angiv **Management gruppenavn**, **Indholdsstyringsserver** navn og **Management serverport**, og klik derefter på **Næste**. Vælg den lokale systemkonto eller en lokal domænekonto, og klik derefter på **Næste**på siden Agent handling konto.  
        ![konfiguration af administration](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![agent handling konto](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. Gennemse valgmulighederne på siden klar til at installere, og klik derefter på **Installer**.
7. Siden på konfigurationen er gennemført, klik på **Udfør**.
8. Når du er færdig, vises **Microsoft overvågning Agent** i **Kontrolpanel**. Du kan gennemse din konfiguration der og bekræfte, at agenten er forbundet til funktionsdygtige indsigt (OMS). Når du har forbindelse til OMS, agenten viser en meddelelse om: **feltet Microsoft overvågning Agent er oprettet forbindelse til tjenesten Microsoft Operations Management Suite.**

## <a name="install-the-agent-using-the-command-line"></a>Installere agent ved hjælp af kommandolinjen
- Rediger, og brug derefter eksemplet nedenfor til at installere agent ved hjælp af kommandolinjen.

    >[AZURE.NOTE] Hvis du vil opgradere en agent, skal du bruge Log analyser scripting API. Se næste afsnit for at opgradere en agent.

    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## <a name="upgrade-the-agent-and-add-a-workspace-using-a-script"></a>Opgradere agenten og tilføje et arbejdsområde, der bruger et script
Du kan opgradere en agent og tilføje et arbejdsområde, ved hjælp af Log analyser scripting API med eksemplet nedenfor PowerShell.

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

>[AZURE.NOTE] Hvis du har det script eller kommandolinjen tidligere har brugt til at installere eller konfigurere agent, `EnableAzureOperationalInsights` er blevet udskiftet med `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Installere agent ved hjælp af DTK i Azure Automation

>[AZURE.NOTE] Denne procedure og script eksempel opgraderer ikke en eksisterende agent.

1. Importere xPSDesiredStateConfiguration DTK modul fra [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) til Azure automatisering.  
2.  Oprette Azure automatisering variable Aktiver til *OPSINSIGHTS_WS_ID* og *OPSINSIGHTS_WS_KEY*. Indstil *OPSINSIGHTS_WS_ID* til dit OMS Log Analytics arbejdsområde-ID, og Indstil *OPSINSIGHTS_WS_KEY* til den primære nøgle i arbejdsområdet.
3.  Brug nedenstående script og gemmer den som MMAgent.ps1
4.  Ændre, og brug derefter i følgende eksempel for at installere agent ved hjælp af DTK i Azure Automation. Importere MMAgent.ps1 til Azure automatisering ved hjælp af Azure Automation-grænseflade eller cmdlet.
5.  Tildele en node af konfigurationen. Inden for 15 minutter noden kontrollerer dens konfiguration, og MMA installeres noden.

```
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "http://download.microsoft.com/download/0/C/0/0C072D6E-F418-4AD4-BCB2-A362624F400A/MMASetup-AMD64.exe"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}  


```


## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Konfigurere en agent manuelt eller tilføje flere arbejdsområder
Hvis du har installeret supportmedarbejdere, men ikke har konfigureret dem, eller hvis du vil agent skal referere til flere arbejdsområder, kan du bruge følgende oplysninger til at aktivere en agent eller omkonfigurere den. Når du har konfigureret agenten, bliver du registreret hos agenttjenesten og får nødvendige konfigurationsoplysninger og management packs, der indeholder oplysninger om løsning.

1. Når du har installeret Microsoft overvågning Agent, Åbn **Kontrolpanel**.
2. Åbn **Microsoft overvågning Agent** , og klik derefter på fanen **Azure Log Analytics (OMS)** .   
3. Klik på **Tilføj** for at åbne dialogboksen **Tilføj et Log Analytics arbejdsområde** .
4. Indsætte **Arbejdsområde-ID** og **Arbejdsområde nøgle (primærnøgle)** , du kopierede til Notesblok i en foregående fremgangsmåde for det arbejdsområde, som du vil tilføje, og klik derefter på **OK**.  
    ![konfigurere funktionsdygtige indsigt](./media/log-analytics-windows-agents/add-workspace.png)

Når dataene er indsamlet fra computere overvåges af agenten, vises antallet af computere overvåges af OMS i portalen OMS under fanen **Forbindelse kilder** i **Indstillinger** som **Servere forbindelse**.


## <a name="to-disable-an-agent"></a>Deaktivere en agent
1. Åbn **Kontrolpanel**, når du har installeret agenten.
2. Åbn Microsoft overvågning Agent, og klik derefter på fanen **Azure Log Analytics (OMS)** .
3. Vælg et arbejdsområde, og klik derefter på **Fjern**. Gentag dette trin for alle andre arbejdsområder.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>Du kan også konfigurere supportmedarbejdere at rapportere til en Operations Manager Administration af gruppe

Hvis du bruger Operations Manager i din IT-infrastruktur, kan du også bruge MMA agent som Operations Manager agent.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Sådan konfigureres MMA supportmedarbejdere at rapportere til en Operations Manager Administration af gruppe
1.  Åbn **Kontrolpanel**på den computer, hvor agenten er installeret.
2.  Åbn **Microsoft overvågning Agent** , og klik derefter på fanen **Operations Manager** .
    ![Under fanen Microsoft overvågning Agent Operations Manager](./media/log-analytics-windows-agents/om-mg01.png)
3.  Hvis serverne Operations Manager har integration med Active Directory, skal du klikke på **Opdater automatisk management gruppetildelinger fra Active Directory-Domænetjenester**.
4.  Klik på **Tilføj** for at åbne dialogboksen **Tilføj en gruppe i administration** .  
    ![Microsoft overvågning Agent tilføje en administration af gruppe](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  I **Administration gruppenavn** skal du skrive navnet på din administration af gruppe.
6.  Skriv navnet på den primære indholdsstyringsserver i feltet **primær management server** .
7.  Skriv TCP-portnummeret i feltet **Administration serverport** .
8.  Vælg den lokale systemkonto eller en lokal domænekonto under **Agent handling konto**.
9.  Klik på **OK** for at lukke dialogboksen **Tilføj en gruppe i administration** , og klik derefter på **OK** for at lukke dialogboksen **Microsoft overvågning agentegenskaber** .

## <a name="optionally-configure-agents-to-use-the-oms-log-analytics-forwarder"></a>Du kan også konfigurere supportmedarbejdere for at bruge OMS Log Analytics domænes

Hvis du har servere eller klienter, der ikke har forbindelse til internettet, kan du stadig få dem sende data til OMS ved hjælp af OMS Log Analytics domænes.  Når du bruger domænes, sendes alle data fra supportmedarbejdere via en enkelt server, der har adgang til internettet. Domænes overfører data fra supportmedarbejdere til OMS direkte uden at analysere nogle af de data, der overføres.

Se [OMS Log Analytics domænes](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder) mere at vide om domænes, herunder installation og konfiguration.

Oplysninger om, hvordan du konfigurerer din supportmedarbejdere for at bruge en proxyserver, som i dette tilfælde er OMS domænes, finder du i [konfigurere proxy og firewall-indstillinger i Log Analytics](log-analytics-proxy-firewall.md).

## <a name="optionally-configure-proxy-and-firewall-settings"></a>Du kan også konfigurere proxy og firewall-indstillinger
Hvis du har proxyservere eller firewalls i dit miljø, der begrænser adgangen til internettet, kan du se [konfigurere proxy og firewall-indstillinger i Log Analytics](log-analytics-proxy-firewall.md) til at aktivere din supportmedarbejdere til at kommunikere til OMS-tjenesten.

## <a name="next-steps"></a>Næste trin

- [Tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md) til at tilføje funktionalitet og indsamle data.
- [Konfigurer proxy og firewall-indstillinger i Log analyser](log-analytics-proxy-firewall.md) , hvis din organisation bruger en proxyserver eller firewall, så supportmedarbejdere kan kommunikere med Log Analytics-tjenesten.

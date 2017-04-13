<properties
    pageTitle="Gentage VMware virtuelle maskiner til Azure ved hjælp af gendannelse af websteder med Azure automatisering DTK | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du bruger Azure automatisering DTK skal installeres automatisk tjenesten Azure websted gendannelse mobilitet og Azure agent for virtuelle/fysiske maskiner til Azure."
    services="site-recovery"
    documentationCenter=""
    authors="krnese"
    manager="lorenr"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="krnese"/>

# <a name="replicate-vmware-virtual-machines-to-azure-by-using-site-recovery-with-azure-automation-dsc"></a>Gentage VMware virtuelle maskiner til Azure ved hjælp af gendannelse af websteder med Azure automatiske DTK

I handlinger Management-pakken giver vi dig et omfattende sikkerhedskopiering og genoprettelse efter genoprettelsesløsning, som du kan bruge som en del af din løbende forretningsplan.

Vi har startet denne rejse sammen med Hyper-V ved hjælp af Hyper-V replika. Men vi har udvidet for at understøtte en forskellige opsætning, fordi kunder har flere hypervisors og platforme i deres skyer.

Hvis du kører VMware arbejdsmængder og/eller fysiske servere i dag, kører en indholdsstyringsserver alle komponenter Azure gendannelse af websteder i dit miljø til at håndtere kommunikation og replikering med Azure, når Azure er din destination.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Installere websted gendannelse mobilitet tjenesten ved hjælp af automatisering DTK
Lad os starte med at gøre en hurtig oversigt over, hvad denne indholdsstyringsserver betyder.

Management-serveren kører flere serverroller. En af disse roller er *konfiguration*, som koordinater kommunikation og administrerer data replikering og gendannelse processer.

Desuden fungerer rollen *proces* som en gentagelse gateway. Denne rolle modtager gentagelse data fra beskyttet kilde maskiner, optimerer med cachelagring, komprimering og kryptering og sender den til en Azure-lager-konto. En af funktionerne for rollen proces er også skubbe installation af tjenesten mobilitet til beskyttede computere og udføre automatisk registrering af VMware FOS.

Hvis der er en failback fra Azure skal håndtere rollen *master target* gentagelse dataene som en del af denne handling.

For de beskyttede maskiner, vi er afhængige af *mobilitet tjeneste*. Denne komponent er installeret på hver enkelt computer (VMware VM eller fysisk server), du vil gentage til Azure. Det registrerer data skriver på computeren og sender dem til indholdsstyringsserver (proces rolle).

Det er vigtigt at forstå din arbejdsbelastninger, infrastrukturen og de involverede komponenter, når du håndtere Forretningskontinuitet. Du kan derefter opfylder kravene til dine gendannelse tid formålet (RTO) og gendannelse punkt formålet (frigivne Produktionsordre). I denne kontekst er tjenesten mobilitet nøgle til at sikre, at din arbejdsbelastninger, som er beskyttet, som du ville forvente.

Så hvordan kan du, i en optimeret måde sikre dig, at du har en stabil beskyttet konfiguration ved hjælp af nogle handlinger Management Suite-komponenter?

I denne artikel indeholder et eksempel på, hvordan du kan bruge Azure automatisering beskedteksten tilstand konfiguration (DTK), sammen med gendannelse af websteder, for at sikre, at:

- Tjenesten mobilitet og Azure VM agent er installeret på Windows-computere, som du vil beskytte.
- Tjenesten mobilitet og Azure VM agent kører altid, når Azure, er gentagelse mål.

## <a name="prerequisites"></a>Forudsætninger

- Et lager til at gemme den nødvendige konfiguration
- Et lager til at gemme den nødvendige adgangskoden til at registrere med management server

 > [AZURE.NOTE] Der genereres en entydig adgangskoden til hver management server. Hvis du vil installere flere management-servere, har du sikre dig, at den korrekte adgangskoden er gemt i filen passphrase.txt.

- Windows Management Framework (WMF) 5.0 er installeret på de computere, du vil aktivere til beskyttelse (et krav om automatiske DTK)

 > [AZURE.NOTE] Hvis du vil bruge DTK til Windows-computere, der har WMF 4.0 installeret, skal du se afsnittet [Brug DTK i afbrudt miljøer](#Use DSC in disconnected environments).

Tjenesten mobilitet kan installeres via kommandolinjen og accepterer flere argumenter. Det er grunden skal du har de binære filer (efter at hente dem fra din installation) og gemme dem i et sted, hvor du kan hente dem ved hjælp af en DTK konfiguration.

## <a name="step-1-extract-binaries"></a>Trin 1: Udtrække binære filer

1. Hvis du vil udtrække de filer, du skal bruge til denne konfiguration, skal du gå til i følgende mappe på din management server:

    **\Microsoft azure websted Recovery\home\svsystems\pushinstallsvc\repository**

    I denne mappe, skal du se en MSI-fil med navnet:

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    Brug følgende kommando til at uddrage installationsprogrammet:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe/q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**

2. Markér alle filer og sende dem til en komprimeret ZIP-komprimeret mappe.

Du har nu de binære filer, du skal bruge til at automatisere installationen af tjenesten mobilitet ved hjælp af automatisering DTK.

### <a name="passphrase"></a>Adgangskoden

Derefter skal du finde ud af, hvor du vil placere denne ZIP-mappe. Du kan bruge en Azure-lager-konto, som vist nyere for at gemme den adgangskode, som du har brug for konfigurationen. Agenten Registrer derefter med management server som en del af processen.

Den adgangskode, du har fået, da du installerede management-serveren kan gemmes i en tekstfil som passphrase.txt.

Anbring både zip-mappen og adgangskoden i en dedikeret objektbeholder i kontoen Azure-lager.

![Mappeplacering](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Hvis du foretrækker at opbevare disse filer på en del på dit netværk, kan du gøre det. Du skal blot at sikre, at DTK ressourcen, du vil bruge senere har adgang og kan hente de konfiguration og adgangskoden.

## <a name="step-2-create-the-dsc-configuration"></a>Trin 2: Oprette DTK konfiguration

Konfigurationen afhænger af WMF 5.0. For at computeren kan blive anvendt konfigurationen gennem Automatiske DTK skal WMF 5.0 være til stede.

Miljøet bruger følgende eksempel DTK konfiguration:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
Konfigurationen skal du gøre følgende:

- Variablerne, der fortæller konfigurationen, hvor du kan få de binære filer til tjenesten mobilitet og Azure VM agent, hvor du kan få adgangskoden, og hvor du vil gemme output.
- Konfigurationen vil importere ressourcen xPSDesiredStateConfiguration DTK, så du kan bruge `xRemoteFile` at hente filerne fra lageret.
- Konfigurationen opretter en mappe, hvor du vil gemme de binære filer.
- Arkiv ressourcen udpakke filerne fra den ZIP-mappe.
- Pakken Installer ressource installeres tjenesten mobilitet fra UNIFIEDAGENT. EXE installer med de bestemte argumenter. (Variablerne, oprette betingelser, der skal ændres afspejler dit miljø.)
- Pakken AzureAgent ressource installeres Azure VM agent, som er anbefalet til hver VM, der kører i Azure. Azure VM agent gør det også muligt at føje filtypenavne til VM efter failover.
- Den tjeneste eller de ressourcer sikrer, at de relaterede Mobility tjenester og Azure services altid kører.

Gemme konfigurationen som **ASRMobilityService**.

>[AZURE.NOTE] Husk at erstatte CSIP i din konfiguration for at afspejle den faktiske indholdsstyringsserver, så agenten der skal knyttes korrekt og anvender den rigtige adgangskode.

## <a name="step-3-upload-to-automation-dsc"></a>Trin 3: Overføre til automatisering DTK

Da den DTK konfiguration, som du har foretaget vil importere et påkrævet DTK ressource-modul (xPSDesiredStateConfiguration), skal du importere pågældende modul i automatisering, før du overfører DTK konfigurationen.

Log på din konto med Automation, gå til **Aktiver** > **moduler**, og klik på **Gennemse galleri**.

Her kan du søge efter modulet og importere den til din konto.

![Importér modul](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Når du er færdig med dette, gå til din computer, hvor du har installeret modulet Azure ressourcestyring og fortsætte med at importere den nyoprettede DTK konfiguration.

### <a name="import-cmdlets"></a>Importere cmdletter

Log på abonnementet Azure i PowerShell. Ændre cmdletter for at afspejle dit miljø og registrere kontooplysningerne automatisering i en variabel:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Overføre konfigurationen til automatisering DTK ved hjælp af følgende cmdlet:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Samle konfigurationen i automatisering DTK

Derefter skal du samle konfigurationen i automatisering DTK, så du kan begynde at registrere noder til den. Du kan opnå, ved at køre følgende cmdlet:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Det kan tage et par minutter, fordi du grundlæggende anvender konfigurationen for tjenesten hostet DTK hente.

Når du samle konfigurationen, kan du hente oplysningerne om jobbet ved hjælp af PowerShell (Get-AzureRmAutomationDscCompilationJob) eller ved at bruge [Azure-portalen](https://portal.azure.com/).

![Hente job](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Du har nu udgivet og overføres konfigurationen af DTK til automatisering DTK.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Trin 4: Indbyggede maskiner til automatisering DTK
>[AZURE.NOTE] En af forudsætningerne for at fuldføre dette scenario er, at din Windows-computere er opdateret med den seneste version af WMF. Du kan hente og installere den korrekte version til din platform fra [Overførselscenter](https://www.microsoft.com/download/details.aspx?id=50395).

Nu kan du oprette en metaconfig for DTK, du vil anvende på din noder. Hvis du vil lykkes med dette, skal du hente slutpunkt URL-adressen og den primære nøgle til kontoen markerede automatisering i Azure. Du kan finde disse værdier under **taster** på bladet **alle indstillinger** for kontoen automatiske.

![Nøgleværdier](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

I dette eksempel har du en fysisk Windows Server 2012 R2-server, du vil beskytte ved hjælp af gendannelse af websteder.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Kontrollere, om ventende Omdøb filhandlinger i registreringsdatabasen

Før du begynder at knytte serveren med Automation DTK slutpunktet, anbefaler vi, at du kontrollerer for ventende Omdøb filhandlinger i registreringsdatabasen. De kan forhindre konfigurationen i afslutte på grund af en ventende genstart.

Kør følgende cmdlet for at bekræfte, at der er ingen ventende genstart på serveren:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Hvis dette viser tomme, er det OK for at fortsætte. Hvis ikke, du skal tage dette ved at genstarte serveren under et vedligeholdelsesvindue.

For at anvende konfigurationen på serveren, skal du starte PowerShell ISE Integrated Scripting miljø () og køre følgende script. Dette er grundlæggende en DTK lokale konfiguration, som fortæller lokale Configuration Manager-program til at registrere med Automation DTK-tjenesten og hente den specifikke variant (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Denne konfiguration medfører lokale Configuration Manager-program til at registrere sig selv med Automation DTK. Det kan også angive, hvordan programmet skal fungere, hvad det skal gøre, hvis der er en konfiguration drift (ApplyAndAutoCorrect), og hvordan den skal fortsætte med konfigurationen, hvis en genstart er påkrævet.

Når du kører dette script, skal noden begynde at registrere med Automation DTK.

![Node registrering i gang](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Hvis du går tilbage til portalen Azure, kan du se, at den nyligt registrerede node har nu blev vist på portalen.

![Registrerede node i portalen](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

På serveren, kan du køre følgende PowerShell-cmdlet for at bekræfte, at noden er blevet registreret korrekt:

```powershell
Get-DscLocalConfigurationManager
```

Når konfigurationen har trukket og anvendt på serveren, kan du kontrollere dette ved at køre følgende cmdlet:

```powershell
Get-DscConfigurationStatus
```

Output viser, at serveren er korrekt, der er trukket dens konfiguration:

![Output](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Desuden har installationsprogrammet til mobilitet service sin egen logfil, der kan findes på *systemdrev*\ProgramData\ASRSetupLogs.

Det var det. Du har nu installeret og registreret tjenesten mobilitet på den computer, du vil beskytte ved hjælp af gendannelse af websteder. DTK sikrer, at de påkrævede tjenester altid kører.

![Vellykket installation](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Når indholdsstyringsserver registrerer installation, kan du konfigurere beskyttelse og aktivere replikering på computeren ved hjælp af gendannelse af websteder.

## <a name="use-dsc-in-disconnected-environments"></a>Brug DTK i afbrudt miljøer

Hvis dine maskiner ikke har forbindelse til internettet, kan du stadig stole på DTK til at installere og konfigurere tjenesten mobilitet på arbejdsbelastninger, som du vil beskytte.

Du kan oprette din egen DTK hente server i dit miljø til grundlæggende, har de samme funktioner, som du angiver med Automation DTK. Det vil sige, vil klienterne trække konfigurationen (efter at det er registreret) til DTK slutpunkt. En anden mulighed er dog til at overføre manuelt DTK konfigurationen til dine maskiner, enten lokalt eller fra en fjernplacering.

Bemærk, at i dette eksempel er en ekstra parameter for navnet på computer. Eksterne filer er nu placeret på et eksternt share, der skal være tilgængeligt ved computere, som du vil beskytte. Slutningen af scriptet særlige tilsynsforskrifter konfigurationen og derefter starter anvende DTK konfigurationen på computeren.

### <a name="prerequisites"></a>Forudsætninger

Sørg for, at xPSDesiredStateConfiguration PowerShell-modulet er installeret. Til Windows-computere, hvor WMF 5.0 er installeret, kan du installere modulet xPSDesiredStateConfiguration ved at køre følgende cmdlet på mål-computere:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Du kan også hente og Gem modulet, i tilfælde af, at du har brug at distribuere til Windows-computere, der har WMF 4.0. Kør denne cmdlet på en computer, hvor PowerShellGet (WMF 5.0) er til stede:

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Også sikre, at [Windows 8.1 opdatere KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) er installeret på maskiner til WMF 4.0.

Følgende konfiguration kan overføres til Windows-computere, som har WMF 5.0 og WMF 4.0:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Hvis du vil oprette en forekomst af din egen DTK hente server på virksomhedens netværk til at efterligne de funktioner, kan du få automatisering DTK, skal du se [konfiguration af en DTK webserver hente](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Valgfrit: Installere en DTK konfiguration ved hjælp af en skabelon til Azure ressourcestyring

I denne artikel har fokuseret på, hvordan du kan oprette din egen DTK konfiguration for at installere tjenesten mobilitet og Azure VM Agent – og sikre, at automatisk, som de kører på de computere, som du vil beskytte. Vi har også en skabelon til Azure Resource Manager, vil installere denne DTK konfiguration til en ny eller eksisterende Azure automatisering konto. Skabelonen anvender Inputparametre til at oprette automatiske aktiver, der indeholder variablerne for dit miljø.

Når du installerer skabelonen, kan du bare referere til trin 4 i denne vejledning til indbyggede dine maskiner.

Skabelonen skal du gøre følgende:

1. Brug en eksisterende automatiske konto eller oprette en ny
2. Tage Inputparametre til:
    - ASRRemoteFile – den placering, hvor du har gemt installationsprogrammet til mobilitet service
    - ASRPassphrase – den placering, hvor du har gemt filen passphrase.txt
    - ASRCSEndpoint – IP-adressen på dit indholdsstyringsserver
3. Importere xPSDesiredStateConfiguration PowerShell-modulet
4. Oprette og samle DTK konfiguration

Alle de foregående trin sker i den rigtige rækkefølge, så du kan starte onboarding dine maskiner til beskyttelse.

Skabelonen med instruktioner til installation, er placeret på [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Installere skabelonen ved hjælp af PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Næste trin

Når du installerer mobilitet service supportmedarbejdere, kan du [aktivere gentagelse](site-recovery-vmware-to-azure.md#step-6-replicate-applications) til virtuelle computere.

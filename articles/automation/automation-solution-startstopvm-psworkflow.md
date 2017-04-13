<properties 
    pageTitle="Start og Stop virtuelle maskiner med Automation Azure - PowerShell arbejdsproces | Microsoft Azure"
    description="Grafiske version af Azure automatisering tilfælde, herunder runbooks for at starte og stoppe klassisk virtuelle computere."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="07/06/2016"
    ms.author="bwren" />

# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Azure scenarie med Automation - start og Stop virtuelle maskiner

Dette scenarie Azure automatisering indeholder runbooks for at starte og stoppe klassisk virtuelle computere.  Du kan bruge dette scenario på grund af følgende:  

- Brug runbooks uden ændringer i dit eget miljø. 
- Ændre runbooks for at udføre tilpasset funktionalitet.  
- Ringe på runbooks fra en anden runbook som en del af en overordnede løsning. 
- Brug runbooks som selvstudier til at lære runbook redigering begreber. 

> [AZURE.SELECTOR]
- [Grafiske](automation-solution-startstopvm-graphical.md)
- [PowerShell arbejdsproces](automation-solution-startstopvm-psworkflow.md)

Dette er PowerShell arbejdsproces runbook version af dette scenario. Det er også tilgængelige ved hjælp af [grafiske runbooks](automation-solution-startstopvm-graphical.md).

## <a name="getting-the-scenario"></a>Få dette scenario

Dette scenarie består af to PowerShell arbejdsproces runbooks, som kan hentes fra følgende links.  Se den [grafiske version](automation-solution-startstopvm-graphical.md) af dette scenarie for links til den grafiske runbooks.

| Runbook | Link | Type | Beskrivelse |
|:---|:---|:---|:---|
| Start-AzureVMs | [Starte Azure klassisk FOS](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | PowerShell arbejdsproces | Starter alle klassisk virtuelle maskiner i en Azure subscriptionor alle virtuelle maskiner med et bestemt tjenestenavn. |
| Stop AzureVMs | [Stoppe Azure klassisk FOS](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | PowerShell arbejdsproces | Stopper alle virtuelle maskiner på en konto med automation eller alle virtuelle maskiner med et bestemt tjenestenavn.  |


## <a name="installing-and-configuring-the-scenario"></a>Installation og konfiguration af dette scenario

### <a name="1-install-the-runbooks"></a>1. installere runbooks

Når du henter runbooks, kan du importere dem med brug af fremgangsmåden i [importere en Runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### <a name="2-review-the-description-and-requirements"></a>2. Gennemse beskrivelsen og krav
Runbooks omfatter kommenterede Hjælp-tekst, der indeholder en beskrivelse og påkrævet aktiver.  Du kan også finde de samme oplysninger fra denne artikel. 

### <a name="3-configure-assets"></a>3. Konfigurer Aktiver
Runbooks kræver, at de følgende aktiver, skal du oprette og udfylde med relevante værdier.

| Aktivtype | Aktiver navn | Beskrivelse |
|:---|:---|:---|:---|
| Legitimationsoplysninger | AzureCredential | Indeholder legitimationsoplysninger for en konto, der har tilladelse til at starte og stoppe virtuelle maskiner i Azure-abonnement.  Du kan også angive et andet legitimationsoplysninger aktiv i parameteren **legitimationsoplysninger** aktivitetens **Tilføj AzureAccount** . |
| Variabel | AzureSubscriptionId | Indeholder abonnementet Azure abonnement-ID. |

## <a name="using-the-scenario"></a>Ved hjælp af dette scenario

### <a name="parameters"></a>Parametre

Runbooks har følgende parametre.  Du skal angive værdier for de obligatoriske parametre og kan eventuelt angive værdier for andre parametre, afhængigt af dine behov.

| Parameter | Type | Obligatorisk | Beskrivelse |
|:---|:---|:---|:---|
| ServiceName | streng | Nej | Hvis du har angivet en værdi, er derefter alle virtuelle maskiner med dette tjenestenavn startet eller ikke længere.  Hvis ingen værdi ikke er angivet, er derefter alle klassisk virtuelle maskiner i Azure-abonnement i gang eller ikke længere. |
| AzureSubscriptionIdAssetName | streng | Nej | Indeholder navnet på det [variable aktiv](#installing-and-configuring-the-scenario) , der indeholder abonnement-ID for abonnementet Azure.  Hvis du ikke angiver en værdi, anvendes *AzureSubscriptionId* .  |
| AzureCredentialAssetName | streng | Nej | Indeholder navnet på [legitimationsoplysninger aktiv](#installing-and-configuring-the-scenario) , der indeholder legitimationsoplysninger for runbook til brug.  Hvis du ikke angiver en værdi, anvendes *AzureCredential* .  |

### <a name="starting-the-runbooks"></a>Starte runbooks

Du kan bruge en af metoder i [starter en runbook i Azure automatisering](automation-starting-a-runbook.md) til at starte en af runbooks i dette scenarie.

Følgende eksempel kommandoer bruger Windows PowerShell til at køre **StartAzureVMs** for at starte alle virtuelle maskiner med navnet på tjenesten *MyVMService*.

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### <a name="output"></a>Output

Runbooks vil [sende en meddelelse](automation-runbook-output-and-messages.md) til hver enkelt virtuelt med angivelse eller ej instruktionerne til at starte eller stoppe blev sendt.  Du kan søge efter en bestemt tekststreng i svaret for at bestemme resultatet for hver runbook.  De mulige output strenge findes i den følgende tabel.

| Runbook | Betingelse | Meddelelse |
|:---|:---|:---|
| Start-AzureVMs | Virtuelt kører allerede  | MyVM kører allerede |
| Start-AzureVMs | Starte anmodning om virtuelt sendt | MyVM er blevet startet |
| Start-AzureVMs | Startanmodning om virtuelt mislykkedes  | MyVM kunne ikke startes |
| Stop AzureVMs | Virtuelt er allerede stoppet  | MyVM er allerede stoppet |
| Stop AzureVMs | Stoppe anmodning om virtuelt sendt | MyVM er stoppet |
| Stop AzureVMs | Stopanmodning om virtuelt mislykkedes  | MyVM kunne ikke stoppes |

For eksempel forsøger følgende kodestykke fra en runbook at starte alle virtuelle maskiner med navnet på tjenesten *MyServiceName*.  Hvis nogen af start anmodninger om fejl, kan derefter fejl handlinger hentes. 

    $results = Start-AzureVMs -ServiceName "MyServiceName"
    foreach ($result in $results) {
        if ($result -like "* has been started" ) {
            # Action to take in case of success.
        }
        else {
            # Action to take in case of error.
        }
    }


## <a name="detailed-breakdown"></a>Detaljeret opdeling

Følgende er en detaljeret opdeling af runbooks i dette scenarie.  Du kan bruge disse oplysninger til enten tilpasse runbooks eller bare for at få mere for at vide fra dem til oprettelse af din egen scenarier med automation.

### <a name="parameters"></a>Parametre

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

Arbejdsprocessen starter ved at få værdierne for [Inputparametre](#using-the-scenario).  Hvis aktiv navnene der ikke gives er standardnavne bruges.

### <a name="output"></a>Output

    # Returns strings with status messages
    [OutputType([String])]

Denne linje erklærer, output af runbook stadig er en streng.  Dette er ikke nødvendigt, men er en bedste praksis for, når runbook bruges som en [underordnet runbook](automation-child-runbooks.md) , så en overordnet runbook ved outputtypen kan forvente.

### <a name="authentication"></a>Godkendelse

    # Connect to Azure and select the subscription to work against
    $Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
    $null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
    $SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

De næste linjer Angiv [legitimationsoplysninger](automation-configuring.md#configuring-authentication-to-azure-resources) og Azure-abonnement, der skal bruges for resten af runbook.
Først skal vi bruge **Get-AutomationPSCredential** for at få det aktiv, der indeholder legitimationsoplysningerne med adgang til start og stop virtuelle maskiner i Azure-abonnement. **Tilføj AzureAccount** bruger derefter dette aktiv til at angive legitimationsoplysninger.  Output er tildelt til en dummy variabel, så det ikke er medtaget i outputtet runbook.  

Variabelt aktivet med abonnement-ID hentes derefter med **Get-AutomationVariable** og det abonnement, der er konfigureret med **Vælg AzureSubscription**.

### <a name="get-vms"></a>Få FOS

    # If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
    { 
        $VMs = Get-AzureVM -ServiceName $ServiceName
    }
    else 
    { 
        $VMs = Get-AzureVM
    }

**Get-AzureVM** bruges til at hente de virtuelle maskiner runbook fungerer sammen med.  Hvis en værdi er angivet i variablen **ServiceName** input, hentes kun virtuelle maskiner med dette tjenestenavn.  Hvis **ServiceName** er tom, så hentet alle virtuelle maskiner.

### <a name="startstop-virtual-machines-and-send-output"></a>Start/Stop virtuelle maskiner og sende output

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
        if ($VM.PowerState -eq "Started")
        {
            # The VM is already started, so send notice
            Write-Output ($VM.InstanceName + " is already running")
        }
        else
        {
            # The VM needs to be started
            $StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

            if ($StartRtn.OperationStatus -ne 'Succeeded')
            {
                # The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
            }
            else
            {
                # The VM started, so send notice
                Write-Output ($VM.InstanceName + " has been started")
            }
        }
    }

De næste linjer gennemgå hver virtuelt.  Først er **PowerState** på den virtuelle maskine markeret til at få vist, hvis det allerede kører eller stoppet, afhængigt af runbook.  Hvis det er allerede i tilstanden destination, sendes en meddelelse til output og runbook enderne.  Hvis ikke, så **Start-AzureVM** eller **Stop AzureVM** bruges til at forsøge at starte eller stoppe den virtuelle maskine med resultatet af anmodningen gemt til en variabel.  En meddelelse sendes til at angive, om anmodningen om at starte eller stoppe blev sendt.


## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om at arbejde med underordnede runbooks, skal du se [underordnede runbooks i Azure Automation](automation-child-runbooks.md) 
- Hvis du vil vide mere om output meddelelser under udførelse af runbook og logføring til hjælp til fejlfinding af skal du se [Runbook output og meddelelser i Azure automatisering](automation-runbook-output-and-messages.md)

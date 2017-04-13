<properties
   pageTitle="Føje Azure automatiske runbooks til gendannelse planer | Microsoft Azure"
   description="I denne artikel beskrives, hvordan Azure gendannelse af websteder nu gør det muligt at udvide gendannelse plan ved hjælp af Azure automatisering til at udføre kompleks opgaver under genoprettelsen til Azure"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="gauravd"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="10/23/2016"
   ms.author="ruturajd@microsoft.com"/>


# <a name="add-azure-automation-runbooks-to-recovery-plans---classic"></a>Føje Azure automatisering runbooks til gendannelse planer - klassisk


I dette selvstudium beskrives, hvordan Azure gendannelse af websteder integreres med Azure automatisering til at levere udvidelsesmuligheder til gendannelse planer. Gendannelse planer kan dirigerer gendannelse af din virtuelle maskiner, der er beskyttet via Azure gendannelse af websteder til både replikering sekundær skyen og gentagelse til Azure scenarier. De også hjælpe med at foretage den gendannelse **ensartede og nøjagtige** **gentaget**og **automatiseret**. Hvis du mislykkes over din virtuelle maskiner til Azure, integration med Automation Azure udvider gendannelse planer og giver dig mulighed for at udføre runbooks, således at effektiv automatiseringsopgaver.

Hvis du ikke har hørt om Azure automatisering endnu, Tilmeld dig [her](https://azure.microsoft.com/services/automation/) og downloade eksempelscripts deres [her](https://azure.microsoft.com/documentation/scripts/). Du kan finde flere oplysninger om [Gendannelse af Azure websteder](https://azure.microsoft.com/services/site-recovery/) og Sådan dirigerer gendannelse til Azure ved hjælp af gendannelse planer [her](https://azure.microsoft.com/blog/?p=166264).

I denne korte selvstudium vil vi se på hvordan du kan integrere Azure automatiske runbooks i gendannelse planer. Vi vil automatisere enkle opgaver, der tidligere påkrævet manuel handling og se, hvordan du konverterer en flere trin genoprettelse til et enkelt klik gendannelse handling. Vi ser også på, hvordan du kan foretage fejlfinding af et simpelt script, hvis det går galt.

## <a name="protect-the-application-to-azure"></a>Beskytte programmet til Azure

Lad os starte med et enkelt program bestående af to virtuelle computere. Her, har vi en HRweb anvendelse af Fabrikam. Fabrikam-HRweb-front end og Fabrikam-Hrweb-back end-er de to virtuelle maskiner beskyttet til Azure ved hjælp af Azure gendannelse af websteder. Følg nedenstående trin for at beskytte de virtuelle maskiner ved hjælp af Azure gendannelse af websteder.

1.  Aktivér beskyttelse af dine virtuelle computere.

2.  Sikre, at de virtuelle maskiner har fuldført indledende gentagelse og er replikering.

3.  Vent, indtil det første replikering er fuldført, og status gentagelse siger beskyttet.

![](media/site-recovery-runbook-automation/01.png)
---------------------

I dette selvstudium skal du oprette en gendannelse plan for programmet Fabrikam HRweb Sådan flytter du programmet til Azure. Derefter vi vil integrere den med en runbook, som opretter et slutpunkt på den mislykkedes over Azure virtuelt at kunne levere websider på port 80.

Først skal Lad os oprette en gendannelse plan for vores program.

## <a name="create-the-recovery-plan"></a>Oprette gendannelse planen

Hvis du vil gendanne programmet til Azure, skal du oprette en plan for gendannelse.
Brug af en gendannelse plan, kan du angive rækkefølgen af gendannelse af virtuelle maskiner. Den virtuelle maskine, der er placeret i gruppen 1, gendanne og starte første, og derefter følge den virtuelle maskine i gruppe 2.

Oprette en gendannelse planlægge, der ligner nedenfor.

![](media/site-recovery-runbook-automation/12.png)

Læse mere om gendannelse planer skal du læse dokumentation [her](https://msdn.microsoft.com/library/azure/dn788799.aspx "her").

Dernæst skal oprette de nødvendige elementer i Azure Automation.

## <a name="create-the-automation-account-and-its-assets"></a>Oprette kontoen automatisering og dens Aktiver

Du har brug for en Azure automatiske konto til at oprette runbooks. Hvis du ikke allerede har en konto, kan du gå til Azure automatisering fane, der er markeret med ![](media/site-recovery-runbook-automation/02.png)og oprette en ny konto.

1.  Giv kontoen et navn til at identificere med.

2.  Angiv en geografisk område, hvor du vil placere kontoen.

Det anbefales at placere kontoen i det samme område som ASR samling.

![](media/site-recovery-runbook-automation/03.png)

Dernæst skal du oprette de følgende aktiver i kontoen.

### <a name="add-a-subscription-name-as-asset"></a>Tilføje et abonnementsnavn som aktiv

1.  Tilføje en ny indstilling ![](media/site-recovery-runbook-automation/04.png) i Azure automatiske aktiver og vælge til![](media/site-recovery-runbook-automation/05.png)

2.  Vælg typen variable som **streng**

3.  Angive variabelnavn som **AzureSubscriptionName**

    ![](media/site-recovery-runbook-automation/06.png)

4.  Angive faktiske Azure abonnementets navn som den variable værdi.

    ![](media/site-recovery-runbook-automation/07_1.png)

Du kan identificere navnet på dit abonnement på indstillingssiden for din konto på Azure-portalen.

### <a name="add-an-azure-login-credential-as-asset"></a>Tilføje en Azure logon legitimationsoplysninger som aktiv

Azure automatiske bruger Azure PowerShell til at oprette forbindelse til abonnementet og fungerer på elementer der. I dette skal du godkende med din Microsoft-konto eller en arbejds- eller skolekonto.
Du kan gemme kontooplysningerne i et aktiv skal bruges sikkert af runbook.

1.  Tilføje en ny indstilling ![](media/site-recovery-runbook-automation/04.png) i Azure automatisering aktiver og vælge![](media/site-recovery-runbook-automation/09.png)

2.  Vælg typen legitimationsoplysninger som **Windows PowerShell legitimationsoplysninger**

3.  Angiv navnet som **AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  Angiv brugernavnet og adgangskoden til at logge på med.

Begge disse indstillinger er nu tilgængelig på dine aktiver.

![](media/site-recovery-runbook-automation/11.png)

Du kan finde flere oplysninger om, hvordan du opretter forbindelse til dit abonnement via PowerShell er angivet [her](../powershell-install-configure.md).

Derefter skal opretter du en runbook i Azure automatisering, kan du tilføje et slutpunkt til front end virtuelt efter failover.

## <a name="azure-automation-context"></a>Azure automatisering kontekst

ASR sender en kontekst variabel til runbook kan hjælpe dig med at skrive deterministisk scripts. En kan hævde, navnene på Skytjenesten og den virtuelle maskine er forudsigelige, men der sker, at det ikke er altid sag på grund af visse scenarier som den, hvor navnet på navnet på den virtuelle computer muligvis har ændret på grund af ikke-understøttede tegn i Azure. Derfor sendes disse oplysninger til den ASR gendannelse plan som en del af *konteksten*.

Nedenfor vises et eksempel på udseendet af variablen kontekst.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


Tabellen nedenfor indeholder navn og beskrivelse til hver variabel i konteksten.

**Variabelnavn** | **Beskrivelse**
---|---
RecoveryPlanName | Navnet på plan køres. Hjælper med at handle ud fra navn ved hjælp af det samme script
FailoverType | Angiver, om sekundære test, planlagt eller uventet.
FailoverDirection | Angiv, om gendannelse er primær eller sekundær
Gruppe-id | Identificere gruppe tal i den gendannelse plan, når planen kører
VmMap | Matrix af alle virtuelle maskiner i gruppen
VMMap nøgle | Entydig nøgle (GUID) for hver VM. Det er den samme som den virtuelle maskine VMM ID, hvis det er relevant.
RoleName | Navnet på den Azure VM, gendannes
CloudServiceName | Azure skybaseret tjeneste navn som den virtuelle maskine er oprettet under.


Til at identificere VmMap Key i den kontekst, du kan også gå til siden VM egenskaber i ASR og kig på egenskaben VM GUID.

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>Redigere en automatisering runbook

Nu oprette runbook for at åbne port 80 på en front end virtuel maskine.

1.  Oprette en ny runbook i kontoen Azure automatisering med navnet **OpenPort80**

    ![](media/site-recovery-runbook-automation/14.png)

2.  Gå til visningen forfatter af runbook, og Angiv kladdetilstand.

3.  Først angive variablen for at bruge som konteksten gendannelse plan

    ```
        param (
            [Object]$RecoveryPlanContext
        )

    ```

4.  Opretter forbindelse til det abonnement, ved hjælp af legitimationsoplysninger og abonnement navnet næste

    ```
        $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

        # Connect to Azure
        $AzureAccount = Add-AzureAccount -Credential $Cred
        $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
        Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    ```

    Bemærk, at du bruger de Azure Aktiver – **AzureCredential** og **AzureSubscriptionName** her.

5.  Nu angive slutpunktdetaljer og GUID på den virtuelle maskine, som du vil vise slutpunktet. I dette tilfælde den front end virtuelle maskine.

    ```
        # Specify the parameters to be used by the script
        $AEProtocol = "TCP"
        $AELocalPort = 80
        $AEPublicPort = 80
        $AEName = "Port 80 for HTTP"
        $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
    ```

    Dette angiver Azure slutpunkt protokollen, lokale port på VM og dens tilknyttede offentlige port. Disse variabler er parametre, der kræves af Azure kommandoer, som føjer slutpunkter til FOS. VMGUID indeholder GUID på den virtuelle maskine, du har brug for til at betjene på.

6.  Scriptet udtrækker konteksten for den angivne VM GUID og oprette et slutpunkt på en virtuel maskine, der refereres til af det nu.

    ```
        #Read the VM GUID from the context
        $VM = $RecoveryPlanContext.VmMap.$VMGUID

        if ($VM -ne $null)
        {
            # Invoke pipeline commands within an InlineScript

            $EndpointStatus = InlineScript {
                # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
                # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

                $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                    Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                    Update-AzureVM
                Write-Output $Status
            }
        }
    ```

7. Når dette er gennemført, der påløber Publicer ![](media/site-recovery-runbook-automation/20.png) at give dit script skal være tilgængelige for udførelse af.

Fuldført scriptet er angivet under for reference

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>Tilføj scriptet til planen, gendannelse

Når scriptet er klar, kan du tilføje den til den gendannelse plan, du oprettede tidligere.

1.  I den gendannelse-plan, du har oprettet, skal du vælge at tilføje et script efter gruppe 2. ![](media/site-recovery-runbook-automation/15.png)

2.  Angiv et scriptnavn. Dette er blot et fuldt navn til denne script til viser inden for den gendannelse plan.

3.  Vælg Azure automatisering kontonavnet i sekundære til Azure script –.

4.  Vælg runbook du har oprettet i Runbooks Azure.

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>Primære side scripts

Når du udfører en failover til Azure, kan du også vælge at udføre primære side scripts. Disse scripts kører på serveren VMM under failover.
Primære scripts er kun tilgængelige kun for foreløbig lukning og skrive lukning faser. Dette skyldes, at vi forventer primære webstedet skal være typisk ikke tilgængelig, når nedbrud rammer.
Under failover en ikke-planlagt kun, hvis du vælger den til primære websted handlinger, forsøger at køre de primære scripts. Hvis de er ikke tilgængelig eller timeout, fortsætter med sekundære at gendanne de virtuelle computere.
Primære side scripts er ikke tilgængelig for VMware/fysiske/Hyper-v-websteder uden VMM beskyttet til Azure - mens failover til Azure.
Men, når du failback fra Azure til en lokal installation, scripts på primære (Runbooks) kan bruges til alle destinationer undtagen VMware.

## <a name="test-the-recovery-plan"></a>Teste gendannelse planen

Når du har tilføjet runbook til den plan, du kan starte en test failover og se det i aktion. Det anbefales altid at køre en test failover for at teste dit program og den gendannelse plan til at sikre, at der ikke er nogen fejl.

1.  Vælg planen, gendannelse og starte en test failover.

2.  Du kan se om runbook er udført eller ikke via dens status under kørsel af plan.

    ![](media/site-recovery-runbook-automation/17.png)

3.  Du kan også få vist detaljerede runbook udførelse af status på siden Azure automatiske job for runbook.

    ![](media/site-recovery-runbook-automation/18.png)

4.  Når sekundære er fuldført, bortset fra runbook udførelse af resultatet, kan du se om udførelsen er gået igennem eller ikke ved at besøge siden Azure virtuelt og kigger på slutpunkterne.

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>Eksempel på scripts

Mens vi gik automatisere en ofte anvendte opgaven med at tilføje et slutpunkt til en Azure virtuelt i dette selvstudium, kan du gøre et antal andre effektive automatiseringsopgaver ved hjælp af Azure automatisering. Microsoft og Azure automatisering gruppen giver eksempel runbooks som kan hjælpe dig med at få en introduktion til dine egne løsninger og utility runbooks, som du kan bruge som dokumentkomponenter for større automatiseringsopgaver. Begynde at bruge dem fra galleriet og oprette effektive et enkelt klik gendannelse-planer til dine programmer ved hjælp af Azure gendannelse af websteder.

## <a name="additional-resources"></a>Yderligere ressourcer

[Azure automatiske oversigt] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure automatisering oversigt")

[Eksempel Azure automatiseringsscripts] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Eksempel Azure automatiseringsscripts")

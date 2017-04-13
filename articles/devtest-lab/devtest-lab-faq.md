<properties
    pageTitle="Azure DevTest øvelser ofte stillede spørgsmål om | Microsoft Azure"
    description="Find svar på ofte stillede spørgsmål Azure DevTest øvelser"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="tarcher"/>

# <a name="azure-devtest-labs-faq"></a>Azure DevTest øvelser ofte stillede spørgsmål

I denne artikel besvares nogle af de mest almindelige spørgsmål om Azure DevTest øvelser.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>Generelt
- [Hvad nu, hvis mit spørgsmål ikke besvaret her?](#what-if-my-question-isnt-answered-here)
- [Hvorfor skal jeg bruge Azure DevTest øvelser?](#why-should-i-use-azure-devtest-labs) 
- [Hvad betyder "bekymre, selvbetjening"?](#what-does-quotworry-free-self-servicequot-mean)
- [Hvordan kan jeg bruge Azure DevTest øvelser?](#how-can-i-use-azure-devtest-labs) 
- [Hvordan får jeg faktureret til Azure DevTest øvelser?](#how-am-i-billed-for-azure-devtest-labs) 
 
## <a name="security"></a>Sikkerhed 
- [Hvad er de forskellige sikkerhedsniveauer i Azure DevTest øvelser?](#what-are-the-different-security-levels-in-azure-devtest-labs) 
- [Hvordan opretter jeg en rolle til at give brugerne til at udføre en specifik opgave?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task) 
 
## <a name="cicd-integration--automation"></a>CI/CD Integration og automatisering 
 
- [Kan Azure DevTest øvelser integreres med min CI/CD toolchain?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain) 
 
## <a name="virtual-machines"></a>Virtuelle maskiner 
 
- [Hvorfor kan jeg ikke se visse FOS i bladet virtuelle Azure-computere, der står i Azure DevTest øvelser?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs) 
- [Hvad er forskellen mellem brugerdefinerede billeder og formler?](#what-is-the-difference-between-custom-images-and-formulas) 
- [Hvordan opretter jeg flere FOS fra den samme skabelon på én gang?](#how-do-i-create-multiple-vms-from-the-same-template-at-once) 
- [Hvordan flytter jeg så mine eksisterende Azure FOS i min Azure DevTest øvelser øvelse?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab) 
- [Kan jeg vedhæfte flere diske til min FOS?](#can-i-attach-multiple-disks-to-my-vms) 
- [Hvordan jeg automatisere processen med at overførsel af Virtuelle filer for at oprette brugerdefinerede billeder?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images) 
- [Hvordan kan jeg automatisere processen med at slette alle FOS i min øvelse?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)
 
## <a name="artifacts"></a>Elementer 
 
- [Hvad er elementer?](#what-are-artifacts) 
 
## <a name="lab-configuration"></a>Øvelse konfiguration 
 
- [Hvordan opretter jeg en øvelse fra en skabelon til Azure ressourcestyring?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template) 
- [Hvorfor er min FOS er oprettet i forskellige grupper med vilkårlig navne? Kan jeg omdøbe eller ændre disse grupper?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups) 
- [Hvor mange øvelser kan jeg oprette under det samme abonnement?](#how-many-labs-can-i-create-under-the-same-subscription)
- [Hvor mange FOS kan jeg oprette per øvelse?](#how-many-vms-can-i-create-per-lab)
- [Hvordan deler jeg et direkte link til min øvelse?](#how-do-i-share-a-direct-link-to-my-lab)
- [Hvad er en Microsoft-konto?](#what-is-a-microsoft-account)
 
## <a name="troubleshooting"></a>Fejlfinding i forbindelse med 
 
- [Min genstand mislykkedes under oprettelse af VM. Hvordan udfører jeg fejlfinding det?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it) 
- [Hvorfor ikke min eksisterende virtuelle netværk gemme korrekt?](#why-isnt-my-existing-virtual-network-saving-properly)  

### <a name="what-if-my-question-isnt-answered-here"></a>Hvad nu, hvis mit spørgsmål ikke besvaret her?
Hvis dit spørgsmål ikke vises her, skal du fortælle os, og vil vi hjælpe dig med at finde et svar.

- Stil et spørgsmål i [Disqus tråd](#comments) i slutningen af disse ofte stillede spørgsmål og kommunikere med Azure Cache team og andre communitymedlemmer om i denne artikel.
- Stil et spørgsmål på [Azure DevTest øvelser MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)for at nå et bredere publikum, og at kommunikere med Azure DevTest øvelser teamet og andre medlemmer af gruppen.
- Hvis anmode om en funktion, skal du sende dit anmodninger og ideer til [Azure DevTest øvelser bruger tale](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs"></a>Hvorfor skal jeg bruge Azure DevTest øvelser? 
Azure DevTest øvelser kan gemme din team tid og penge. Udviklere kan oprette deres egne miljøer ved hjælp af flere forskellige grundtal, og brug elementer til hurtigt at installere og konfigurere programmer. Brug af brugerdefinerede billeder og formler, kan virtuelle maskiner gemmes som skabeloner og nemt gengives. Desuden tilbyder øvelser flere konfigurerbar politikker, der tillader øvelse administratorer til at reducere spild og administrere et team miljøer. Disse politikker omfatter automatisk-lukning, omkostninger grænseværdi maksimale FOS per bruger og maksimale VM størrelser. Læs [Oversigt over](devtest-lab-overview.md) en mere detaljeret beskrivelse af Azure DevTest øvelser, eller se [introduktionsvideo](/documentation/videos/videos/what-is-azure-devtest-labs). 

### <a name="what-does-worry-free-self-service-mean"></a>Hvad betyder "bekymre, selvbetjening"?
"Bekymre, selvbetjening" betyder, at udviklere og testere oprette deres egne miljøer efter behov, og administratorer har sikkerheden for at vide, at Azure DevTest øvelser til at minimere spilder og styre omkostninger. Administratorer kan angive, hvilke VM størrelser er tilladt, det maksimale antal FOS, og når FOS er i gang, og Luk. Azure DevTest øvelser gør det også nemt at overvåge omkostninger og konfigurere beskeder til at holde øje med hvordan ressourcer i øvelse bruges. 

### <a name="how-can-i-use-azure-devtest-labs"></a>Hvordan kan jeg bruge Azure DevTest øvelser? 
Azure DevTest øvelser er nyttig, når du kræver Udviklingscenter eller teste miljøer, og du vil genskabe dem hurtigt og/eller administrere dem med omkostninger gemme politikker. 

Her er nogle scenarier, som vores kunder anvender Azure DevTest øvelser til: 

- Administrere Udviklingscenter og test miljøer på ét sted, bygger ved hjælp af politikker for at reducere omkostninger og brugerdefinerede billeder til at dele på tværs af teamet.
- Udvikle et program ved hjælp af brugerdefinerede billeder til at gemme tilstanden disken i alle udvikling faser.
- Sporing af omkostninger i forhold til opgavefremdrift. 
- Oprettelse af masse testmiljøer til kvalitet assurance test.
- Brug af elementer og formler til at konfigurere nemt og Genskab et program på forskellige miljøer. 
- Distribuere FOS for hackathons (samarbejde Udviklingscenter eller test arbejde), og derefter nemt deaktivere klargøring dem Når begivenheden slutter. 

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Hvordan får jeg faktureret til Azure DevTest øvelser? 
Azure DevTest øvelser er en gratis tjeneste, hvilket betyder, at oprette øvelser og konfigurere politikker, skabeloner og elementer er gratis. Du betaler kun for de Azure ressourcer, der bruges i din øvelser, som virtuelle maskiner, lager konti og virtuelle netværk. Læs mere om [Azure DevTest øvelser priser](https://azure.microsoft.com/pricing/details/devtest-lab/)for flere oplysninger om omkostninger af øvelse ressourcer. 

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Hvad er de forskellige sikkerhedsniveauer i Azure DevTest øvelser?  
Sikkerhed access bestemmes af [Azure Role-Based Access kontrolelement (RBAC)](../active-directory/role-based-access-built-in-roles.md). For at forstå, hvordan access virker hjælper med at forstå forskellene mellem et tilladelsesniveau, en rolle og et omfang, som defineret af RBAC.

- **Tilladelse** - et tilladelsesniveau er en defineret adgang til en bestemt handling. Et tilladelsesniveau kan for eksempel være læseadgang til alle virtuelle computere. 
- **Rolle** - en rolle er et sæt af tilladelser, der kan grupperes og tildelt til en bruger. For eksempel har en "abonnement ejeren" adgang til alle ressourcer inden for et abonnement. 
- **Område** - et omfang er et niveau i hierarkiet for Azure ressource. Et omfang kan for eksempel være en ressourcegruppe eller en enkelt øvelse eller hele abonnementet. 
 
I området af Azure DevTest øvelser, der er to typer af roller for at definere brugertilladelser: øvelse ejer og øvelse brugeroplysninger.

- **Øvelse ejer** - en øvelse ejer har adgang til alle ressourcer inden for øvelse. Derfor, de kan ændre politikker, læse og skrive en hvilken som helst FOS, ændre det virtuelle netværk og så videre. 
- **Øvelse bruger** – en øvelse bruger kan få vist alle øvelse de ressourcer, som FOS, politikker og virtuelle netværk, men de kan ikke redigere politikker eller en hvilken som helst VM'er, der er oprettet af andre brugere. Det er også muligt at oprette brugerdefinerede roller i Azure DevTest øvelser, og du kan lære at gøre i artiklen, [give brugertilladelser til bestemte øvelse politikker](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). 
 
Da områder hierarkiske, når en bruger har tilladelser på et bestemt område, er de automatisk tildelt disse tilladelser på hver underniveau omfang encompassed. Eksempelvis hvis en bruger er tildelt rollen som abonnement ejer, har derefter de adgang til alle ressourcer i et abonnement. Disse ressourcer omfatter alle virtuelle maskiner, alle virtuelle netværk og alle øvelser. Dermed, arver ejer af et abonnement automatisk øvelse ejer rolle. Modsat er dog ikke sand. En øvelse ejer har adgang til en øvelse, som er et mindre område end niveauet abonnement. En øvelse ejer er derfor ikke kunne se virtuelle maskiner eller virtuelle netværk eller en hvilken som helst ressourcer, der er uden for øvelse. 

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Hvordan opretter jeg en rolle til at give brugerne til at udføre en specifik opgave?
En omfattende artikel om at oprette brugerdefinerede roller og tildele tilladelser til denne rolle kan findes her. Her er et eksempel på et script, som opretter rollen "DevTest øvelser avancerede bruger", som har tilladelse til at starte og stoppe alle FOS i øvelse:
 
    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User" 
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
    $policyRoleDef.Id = $null 
    $policyRoleDef.Name = "DevTest Labs Advance User" 
    $policyRoleDef.IsCustom = $true 
    $policyRoleDef.AssignableScopes.Clear() 
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  
 
### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Kan Azure DevTest øvelser integreres med min CI/CD toolchain? 
Hvis du bruger VSTS, er der en [Azure DevTest øvelser opgaver lokalnummer](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) , hvor du kan automatisere din release pipeline i Azure DevTest øvelser. Nogle af anvendelser af dette lokalnummer, omfatter:

- Oprette og implementere en VM automatisk og konfigurere det med det seneste build ved hjælp af Azure fil kopi eller PowerShell VSTS opgaver. 
- Automatisk hentning af tilstanden for en VM efter test for at genskabe en fejl på den samme VM til yderligere undersøgelse. 
- Slette VM i slutningen af version rørledningen, når det er ikke længere er nødvendigt. 

De følgende blogindlæg giver vejledning og oplysninger om brug af filtypenavnet VSTS:
 
- [Azure DevTest øvelser – VSTS udvidelse](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) 
- [Installere en ny VM i en eksisterende AzureDevTestLab fra VSTS](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) 
- [Ved hjælp af VSTS versionsstyring til fortløbende installationer til AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) 
 
For andre CI/CD toolchains kan de tidligere nævnte scenarier, som kan opnås ved filtypenavnet VSTS opgaver på samme måde opnås ved installation af [Azure ressourcestyring skabeloner](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) ved hjælp af [Azure PowerShell-cmdletter](../resource-group-template-deploy.md) og [.NET SDK'er](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Du kan også bruge [REST API'er til DevTest øvelser](http://aka.ms/dtlrestapis) du integrere systemet med din toolchain.  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>Hvorfor kan jeg ikke se visse FOS i bladet virtuelle Azure-computere, der står i Azure DevTest øvelser?
Når der oprettes en VM i Azure DevTest øvelser, får tilladelse til at få adgang til denne VM. Du kan få vist den både i bladet øvelser og bladet **virtuelle computere** . Brugere i rollen DevTest øvelser kan se alle virtuelle maskiner, der er oprettet i øvelse via den øvelse **alle virtuelle maskiner** blade. Men brugere i rollen DevTest øvelser automatisk tildeles ikke læseadgang til VM ressourcer, som andre har oprettet. Derfor vises disse FOS ikke i bladet **virtuelle computere** . 

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>Hvad er forskellen mellem brugerdefinerede billeder og formler? 
Et brugerdefineret billede er en virtuel harddisk (virtuel harddisk), mens en formel er et billede, som du kan konfigurere yderligere indstillinger, du kan gemme og Genskab. Et brugerdefineret billede kan være hensigtsmæssigt, hvis du vil hurtigt oprette flere miljøer med det samme grundlæggende, fast billede. En formel kan være bedre, hvis du vil genbruge konfigurationen af din VM med de seneste bit, et virtuelt netværk/undernet eller en bestemt størrelse. Du kan finde en mere detaljeret beskrivelse, i artiklen, [sammenligne brugerdefinerede billeder og formler i DevTest øvelser](devtest-lab-comparing-vm-base-image-types.md). 
 
### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hvordan opretter jeg flere FOS fra den samme skabelon på én gang? 
Du kan bruge [VSTS opgaver lokalnummer](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) eller [oprette en skabelon til Azure ressourcestyring](devtest-lab-add-vm-with-artifacts.md#save-arm-template) mens du opretter en VM og [installere Azure ressourcestyring skabelonen ud fra Windows PowerShell](../resource-group-template-deploy.md). 
 
### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>Hvordan flytter jeg så mine eksisterende Azure FOS i min Azure DevTest øvelser øvelse? 
Vi er ved at designe en løsning til at flytte direkte FOS til Azure DevTest øvelser, men i øjeblikket kan du kopiere din eksisterende FOS til Azure DevTest øvelser på følgende måde: 

1. Kopiere den Virtuelle fil af din eksisterende VM ved hjælp af denne [Windows PowerShell-script](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1) 
1. [Oprette det brugerdefinerede billede](devtest-lab-create-template.md) i din Azure DevTest øvelser øvelse. 
1. Oprette en VM i øvelse fra dit eget billede 
 
### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Kan jeg vedhæfte flere diske til min FOS? 
Vedhæfte flere diske til FOS understøttes.  
 
### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hvordan jeg automatisere processen med at overførsel af Virtuelle filer for at oprette brugerdefinerede billeder? 
Der er to muligheder:

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) kan bruges til at kopiere eller overføre Virtuelle filer til kontoen lagerplads, der er knyttet til øvelse.
- [Microsoft Azure lagerplads Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) er en enkeltstående app, der kører på Windows, OSX og Linux.   
 
For at finde kontoen destination lagerplads, der er knyttet til din øvelse skal du følge disse trin:

1. Log på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
1. Vælg **Grupper** i panelet til venstre. 
1. Find og vælg den ressourcegruppe, der er knyttet til din øvelse. 
1. Vælg en af lagerplads konti bladet **Oversigt** . 
1. Vælg **BLOB**.
1. Se efter overførsler på listen. Hvis der ikke findes nogen, gå tilbage til trin 4 og prøve en anden lagerplads konto.
1. Bruge **URL-adresse** som din destination i din AzCopy kommando.


### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hvordan kan jeg automatisere processen med at slette alle FOS i min øvelse?

Ud over at slette FOS fra din øvelse i portalen Azure, kan du slette alle FOS i din øvelse ved hjælp af en PowerShell-script. I eksemplet nedenfor blot ændre parameterværdier under **værdier for at ændre** kommentaren. Du kan hente den `subscriptionId`, `labResourceGroup`, og `labName` værdier fra bladet øvelse i portalen Azure. 


    # Delete all the VMs in a lab
    
    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    
    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object { 
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}
    
    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>Hvad er elementer? 
Elementer er brugerdefinerbare elementer, der kan bruges til at udrulle dine seneste bit eller din Udviklingscenter værktøjer til en VM. De er knyttet til din VM under oprettelse af med nogle få enkle Klik, og når VM er klargjort, elementer installation og konfiguration af din VM. Der er forskellige allerede eksisterende elementer i vores [offentlige Github lager](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), men du kan også nemt [oprette dine egne elementer](devtest-lab-artifact-author.md). 

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>Hvordan opretter jeg en øvelse fra en skabelon til Azure ressourcestyring? 
Vi har fået et [Github lager af øvelse Azure ressourcestyring skabeloner](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) , som du kan installere som-er, eller redigere for at oprette brugerdefinerede skabeloner for din øvelser. Hver af disse skabeloner har et link, som du kan klikke på for at installere øvelse som-findes under dit eget Azure-abonnement, eller du kan tilpasse skabelon og [installere ved hjælp af PowerShell eller Azure CLI](../resource-group-template-deploy.md).
 
### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Hvorfor er min FOS er oprettet i forskellige grupper med vilkårlig navne? Kan jeg omdøbe eller ændre disse grupper? 
Grupper er oprettet på denne måde i rækkefølge for Azure DevTest øvelser til at administrere brugertilladelser og adgang til virtuelle computere. Mens du kan flytte VM til en anden ressourcegruppe med navnet på dit ønskede, anbefales gøre så ikke. Vi arbejder på forbedring af denne oplevelse for at tillade større fleksibilitet.   
 
### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hvor mange øvelser kan jeg oprette under det samme abonnement? 
Der er ingen bestemt grænse på antallet øvelser, der kan oprettes per abonnement. De ressourcer, er dog begrænset per abonnement. Du kan læse om [begrænsninger og kvoter for pålagt Azure abonnementer](../azure-subscription-service-limits.md) og [hvordan du kan øge disse begrænsninger](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests). 
 
### <a name="how-many-vms-can-i-create-per-lab"></a>Hvor mange FOS kan jeg oprette per øvelse? 
Der er ingen bestemt grænse på antallet VM'er, der kan oprettes per øvelse. Dog understøtter i øjeblikket øvelse kun om 40 VM'er, der kører på samme tid i standard-lager og 25 VM'er, der kører samtidig i premium lagerplads. Vi arbejder på i øjeblikket øge disse begrænsninger. 

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hvordan deler jeg et direkte link til min øvelse?

Du kan udføre følgende procedure for at dele et direkte link til brugerne øvelse.

1. Gå til øvelse i portalen Azure.
2. Kopiér øvelse URL-adressen fra din browser, og del den med dine øvelse brugere. 

>[AZURE.NOTE] Hvis brugerne øvelse er eksterne brugere med en [MSA konto](#what-is-a-microsoft-account) , og de ikke hører til virksomhedens Active directory, kan de modtage en fejlmeddelelse, når du navigerer til linket medfølgende. Hvis de får en fejlmeddelelse, kan du bede dem om at klikke på personens navn i øverste højre hjørne af portalen Azure, og vælg den mappe, hvor øvelse der findes i afsnittet **mappe** i menuen.

### <a name="what-is-a-microsoft-account"></a>Hvad er en Microsoft-konto?

En Microsoft-konto er, hvad du bruger til stort set alt, hvad jeg gøre med Microsoft enheder og tjenester. Det er en mailadresse og adgangskode, du bruger til at logge på Skype, Outlook.com, OneDrive, Windows Phone og Xbox LIVE – og det betyder, at dine filer, fotos, kontakter og indstillinger kan følge du til en hvilken som helst enhed. 

>[AZURE.NOTE] Microsoft-konto, der bruges til at blive kaldt "Windows Live ID".
 
### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Min genstand mislykkedes under oprettelse af VM. Hvordan udfører jeg fejlfinding det? 
Referere til blogindlægget [Sådan foretages fejlfinding af problemer med elementer i AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) - skrevet af en af vores MVP'er - for at lære at få logge om din mislykkedes genstand. 
 
### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Hvorfor ikke min eksisterende virtuelle netværk gemme korrekt?  
En mulighed er, at din virtuelle netværksnavn indeholder perioder. Hvis det er tilfældet, skal du prøve at fjerne perioderne eller erstatte dem med bindestreger, og prøv derefter at gemme det virtuelle netværk igen.

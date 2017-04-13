<properties
 pageTitle="Autoskalering HPC Pack klyngenoder | Microsoft Azure"
 description="Automatisk forøge og formindske antallet af HPC Pack klynge Beregn knuder i Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Forøge og formindske HPC Pack klyngeressourcer i Azure ifølge klynge arbejdsbelastningen automatisk




Hvis du installerer Azure "burst" noder i din HPC Pack klynge, eller du har oprettet en HPC Pack klynge i Azure FOS, kan du en metode til at automatisk Forøg eller Formindsk antallet af Azure Beregn ressourcer som noder eller kerner ifølge aktuelle arbejdsbelastningen på klyngen. Dette kan du bruge dine Azure ressourcer mere effektivt og administrere deres omkostninger.
Konfigurere egenskaben HPC Pack klynge **AutoGrowShrink**for at gøre dette. Du kan også køre **AzureAutoGrowShrink.ps1** HPC PowerShell-script, der er installeret med HPC Pack.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Desuden kan aktuelt du kun automatisk vokse og formindske HPC Pack Beregn noder, der kører et Windows Server-operativsystem.

## <a name="set-the-autogrowshrink-cluster-property"></a>Angive egenskaben AutoGrowShrink klynge

### <a name="prerequisites"></a>Forudsætninger

* **HPC Pack 2012 R2 opdatering 2 eller nyere klynge** - Klyngenoden hoved kan være installeret enten lokalt eller i en Azure VM. Se [konfigurere en hybrid klynge med HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) at komme i gang med en lokal hoved node og Azure "burst" noder. Se [scriptet til implementering af HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) hurtigt skal installere en HPC Pack klynge i Azure FOS.


* **For en klynge med en hoved node i Azure** - Hvis du bruger HPC Pack IaaS installation scriptet til at oprette klyngen, skal du aktivere egenskaben **AutoGrowShrink** klynge ved at angive indstillingen AutoGrowShrink i klynge konfigurationsfil. Få mere at vide i dokumentationen tilhørende [script hente](https://www.microsoft.com/download/details.aspx?id=44949). 

    Du kan også aktivere egenskaben **AutoGrowShrink** klynge, når du installerer klyngen ved hjælp af HPC PowerShell-kommandoer, der er beskrevet i følgende afsnit. Forberedelse til dette skal du først benytte følgende fremgangsmåde:
    1. Konfigurere et Azure management certifikat for noden hoved og i Azure-abonnement. For en test installation du bruger et standard Microsoft HPC Azure selvsigneret certifikat, der HPC Pack installeres på noden hoved, og Overfør blot dette certifikat til abonnementet Azure. Se [TechNet-biblioteket vejledning](https://technet.microsoft.com/library/gg481759.aspx)til indstillinger og trin.
    2. Køre **regedit** på noden hoved, gå til HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo og tilføje en ny strengværdi. Angiv værdinavnet til "Miniature", og Værdidata til miniature af certifikat i trin 1.


### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>HPC PowerShell-kommandoer til at angive egenskaben AutoGrowShrink

Følgende eksempel er HPC PowerShell-kommandoer til at angive **AutoGrowShrink** og finjustere funktionsmåden med yderligere parametre. Se [AutoGrowShrink parametre](#AutoGrowShrink-parameters) senere i denne artikel til den komplette liste over indstillinger. 

Hvis du vil køre disse kommandoer, du start HPC PowerShell på Klyngenoden hoved som administrator.

**Aktivere egenskaben AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 1

**Sådan deaktiverer du egenskaben AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 0

**Ændre Forøg intervallet i minutter**

    Set-HpcClusterProperty –GrowInterval <interval>

**Ændre Formindsk intervallet i minutter**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**Til at få vist den aktuelle konfiguration af AutoGrowShrink**

    Get-HpcClusterProperty –AutoGrowShrink

### <a name="autogrowshrink-parameters"></a>AutoGrowShrink parametre

Følgende er AutoGrowShrink parametre, du kan ændre ved hjælp af kommandoen **Sæt HpcClusterProperty** .

* **EnableGrowShrink** - parameter til at aktivere eller deaktivere egenskaben **AutoGrowShrink** .
* **ParamSweepTasksPerCore** - antal parametriske oprydning opgaver kan vokse én core. Standard er særligt én core hver opgave. 
 
    >[AZURE.NOTE] HPC Pack QFE KB3134307 ændres **ParamSweepTasksPerCore** til **TasksPerResourceUnit**. Det er baseret på ressourcetypen og kan være node, socket eller core.
    
* **GrowThreshold** - grænseværdi i kø opgaver til at udløse automatisk forøgelse. Standardværdien er 1, hvilket betyder, hvis der ikke er 1 eller flere opgaver i tilstanden i kø automatisk vokse noder.
* **GrowInterval** - Interval i minutter til at udløse automatisk forøgelse. Standardintervallet er 5 minutter.
* **ShrinkInterval** - Interval i minutter til at udløse automatisk formindske. Standardinterval er 5 minutter. |
* **ShrinkIdleTimes** - antal fortløbende kontrol for at formindske for at angive noderne er ledige. Standard er 3 gange. Eksempelvis hvis **ShrinkInterval** er 5 minutter, kontrollerer HPC Pack, om noden er inaktiv hver 5 minutter. Hvis noderne er i inaktiv tilstand, når 3 fortløbende kontrollerer (15 minutter), formindskes HPC Pack pågældende node.
* **ExtraNodesGrowRatio** - yderligere procentdel af noder til at opnå vækst for jobs, der passerer MPI (Message Interface). Standardværdien er 1, hvilket betyder, at HPC Pack vokser noder 1% for MPI sager. 
* **GrowByMin** - parameter til at angive, om politikken dobbelte størrelse er baseret på de mindste ressourcer, der kræves til jobbet. Standard er falsk, hvilket betyder, at HPC Pack vokser noder til job, baseret på de maksimale ressourcer, der kræves for job.
* **SoaJobGrowThreshold** - grænseværdi for indgående SOA anmodninger om at udløse automatisk vokse proces. Standardværdien er 50000.  
    
    >[AZURE.NOTE] Denne parameter understøttes Start i HPC Pack 2012 R2 Update 3.
    
* **SoaRequestsPerCore** -antal indgående SOA anmodninger kan vokse én core. Standardværdien er 20000.  

    >[AZURE.NOTE] Denne parameter understøttes Start i HPC Pack 2012 R2 Update 3.

### <a name="mpi-example"></a>MPI eksempel

Som standard vokser HPC Pack 1% ekstra noder til MPI jobs (**ExtraNodesGrowRatio** er angivet til 1). Det skyldes, at MPI kræver muligvis flere noder, og jobbet kan kun køre, når alle noder er klar. Når Azure starter noder, nogle gange en node muligvis mere tid til at starte end andre medfører, at andre noder er inaktiv, mens du venter, knude til klar. Ved voksende ekstra noder, HPC Pack reducerer ventetiden for denne ressource og gemmer potentielt omkostninger. For at øge procentdelen af ekstra noder til MPI jobs (for eksempel til 10%), skal du køre en kommando, der ligner

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>SOA eksempel

Som standard **SoaJobGrowThreshold** er indstillet til 50000 og **SoaRequestsPerCore** er indstillet til 200000. Hvis du har indsendt ét SOA job 70000 anmodninger, der skal knyttes en opgave, og indgående anmodninger er 70000. I dette tilfælde HPC Pack vokser vokser 1 core for den opgave, og indgående anmodninger om (70000 50000) / kerne 20000 = 1, så vokser i alt 2 borekerner til dette SOA job.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Kør scriptet AzureAutoGrowShrink.ps1

### <a name="prerequisites"></a>Forudsætninger

* **HPC Pack 2012 R2 Update 1 eller nyere klynge** - scriptet **AzureAutoGrowShrink.ps1** er installeret i mappen % CCP_HOME % placering. Klyngenoden hoved kan være installeret enten lokalt eller i en Azure VM. Se [konfigurere en hybrid klynge med HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) til komme i gang med en lokal hoved node og Azure "burst" noder. Se [scriptet til implementering af HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) til hurtigt for at implementere en HPC Pack klynge i Azure FOS, eller brug en [skabelon til Azure Hurtig start](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).

* **Azure PowerShell 0.8.12** - scriptet i øjeblikket, afhænger af denne bestemte version af Azure PowerShell. Hvis du kører en nyere version på noden hoved, kan det være nødvendigt at nedgradere Azure PowerShell til at køre scriptet [version 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) . 

* **For en klynge med Azure burst noder** - køre scriptet på en klientcomputer, hvor HPC Pack er installeret eller på hovedet node. Hvis kører på en klientcomputer, kan du sikre dig, at du angiver variablen $env: CCP_SCHEDULER korrekt til at pege på noden hoved. Azure "burst" knuderne skal allerede være føjet til klyngen, men de kan være i tilstanden ikke installeret.


* **For en klynge implementeret i Azure FOS** - køre scriptet på noden hoved VM, fordi det afhænger af **Start-HpcIaaSNode.ps1** og **Stop HpcIaaSNode.ps1** scripts, som er installeret der. Disse scripts desuden kræver en Azure management certifikat eller publicere indstillingsfil (se [Administrer beregne noder i en HPC Pack klynge i Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)). Kontrollér, at alle noden Beregn FOS, du har brug for der allerede er føjet til klyngen. De kan være i tilstanden showet afbrydes.

### <a name="syntax"></a>Syntaksen for

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
### <a name="parameters"></a>Parametre

 * **NodeTemplates** - navnene på node skabelonerne til at definere området for knuderne til forøge og formindske. Hvis ikke er angivet (Standardværdien er @()), alle noder i gruppen **AzureNodes** node er i omfang, når **NodeType** har en værdi af AzureNodes, og alle noder i gruppen **ComputeNodes** node er i omfang, når **NodeType** har en værdi af ComputeNodes.

 * **JobTemplates** - navnene på job skabelonerne til at definere området for knuderne kan vokse.

 * **NodeType** - typen af node for at forstørre og formindske. Understøttede værdier er:

     * **AzureNodes** – til Azure PaaS (burst) knuderne i et lokalt eller Azure IaaS klynge.

     * **ComputeNodes** - for beregningsnode FOS kun i en Azure IaaS klynge.

* **NumOfQueuedJobsPerNodeToGrow** - antal køjob, der kræves for at udvide en node.

* **NumOfQueuedJobsToGrowThreshold** - grænsen for antallet af køjob til at starte processen med at vokse.

* **NumOfActiveQueuedTasksPerNodeToGrow** - antallet af aktive i kø opgaver, der kræves for at udvide en node. Hvis **NumOfQueuedJobsPerNodeToGrow** er angivet med en værdi, der er større end 0, ignoreres denne parameter.

* **NumOfActiveQueuedTasksToGrowThreshold** - grænsen for antallet af aktive i kø opgaver for at starte processen med at vokse.

* **NumOfInitialNodesToGrow** - indledende mindste antallet af knuder kan vokse, hvis alle knuderne i omfang er **Ikke installeret** eller **stoppet (Deallocated)**.

* **GrowCheckIntervalMins** - intervallet i minutter mellem Kontroller kan vokse.

* **ShrinkCheckIntervalMins** - intervallet i minutter mellem kontrol for at formindske.

* **ShrinkCheckIdleTimes** - antallet af fortløbende Formindsk Kontroller (adskilt af **ShrinkCheckIntervalMins**) for at angive noderne er ledige.

* **UseLastConfigurations** - de forrige konfigurationer, der er gemt i filen argument.

* **ArgFile**- navnet på filen argument, der bruges til at gemme og opdatere konfigurationerne for at køre scriptet.

* **LogFilePrefix** - præfiksnavnet på logfilen. Du kan angive en sti. Som standard skrives loggen til den aktuelle arbejdsmappe.

### <a name="example-1"></a>Eksempel 1

I følgende eksempel konfigurerer noderne Azure burst installeret med skabelonen standard AzureNode til at forstørre og formindske automatisk. Hvis alle noder er først i tilstanden **Ikke installeres** , er i gang mindst 3 noder. Hvis antallet af køjob overskrider 8, starter scriptet noder, indtil deres antallet overskrider forholdet mellem køjob til **NumOfQueuedJobsPerNodeToGrow**. Hvis der findes en node er inaktiv i 3 gange i træk inaktiv, er det ikke længere.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Eksempel 2

I følgende eksempel konfigurerer noden Azure Beregn FOS installeret med skabelonen standard ComputeNode til at forstørre og formindske automatisk.
De job af jobskabelon standard definere omfanget af arbejdsbelastningen på klyngen. Hvis alle noder først er stoppet, er i gang mindst 5 noder. Hvis antallet af aktive i kø opgaver overskrider 15, starter scriptet noder, indtil deres antallet overskrider forholdet mellem aktive i kø opgaver til **NumOfActiveQueuedTasksPerNodeToGrow**. Hvis der findes en node er inaktiv i 10 gange i træk inaktiv, er det ikke længere.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

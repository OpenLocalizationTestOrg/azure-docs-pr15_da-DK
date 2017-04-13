<properties
   pageTitle="Introduktion til Azure batchen PowerShell | Microsoft Azure"
   description="Få en hurtig introduktion til de Azure PowerShell-cmdletter, du kan bruge til at administrere tjenesten Azure batchen"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/20/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-powershell-cmdlets"></a>Introduktion til Azure batchen PowerShell-cmdlet'er
Du kan udføre og scripts mange af de samme opgaver, du kan udføre med batchen API'er, portalen Azure og Azure kommandolinjen (CLI) med Azure batchen PowerShell-cmdletter. Dette er en hurtig introduktion til de cmdletter, du kan bruge til at administrere dine batchen konti og arbejde med dine batchen ressourcer som grupper, job og opgaver.

Du kan finde en komplet liste over batchen cmdletter og detaljerede cmdlet syntaksen [Azure batchen cmdlet reference](https://msdn.microsoft.com/library/azure/mt125957.aspx).

I denne artikel er baseret på-cmdletter i Azure PowerShell version 3.0.0. Vi anbefaler, at du opdaterer din Azure PowerShell ofte for at kunne udnytte tjenesten opdateringer og forbedringer.

## <a name="prerequisites"></a>Forudsætninger

Udføre følgende handlinger for at bruge Azure PowerShell til at administrere dine batchen ressourcer.

* [Installere og konfigurere Azure PowerShell](../powershell-install-configure.md)

* Køre **Logon-AzureRmAccount** cmdlet til at oprette forbindelse til dit abonnement (Azure batchen cmdletter Forsendelsesdato i modulet Azure ressourcestyring):

    `Login-AzureRmAccount`

* **Registrere med navneområdet batchen udbyder**. Denne handling kun skal kunne udføres **én gang hver abonnement**.

    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Administrere batchen konti og nøgler

### <a name="create-a-batch-account"></a>Oprette en Batch-konto

**Ny AzureRmBatchAccount** opretter en batchen konto i en bestemt ressourcegruppe. Hvis du ikke allerede har en ressourcegruppe, oprette en ved at køre Cmdletten [Ny AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) . Angive en af de Azure områder i parameteren **placering** som "Centrale USA". Eksempel:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Opret derefter en batchen konto i ressourcegruppen, der angiver et navn til kontoen i <*account_name*> og placeringen af og navnet på din ressourcegruppe. Oprette kontoen batchen kan gå et stykke tid at fuldføre. Eksempel:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [AZURE.NOTE] Kontonavnet batchen skal være entydige for det Azure område for ressourcegruppen, indeholde mellem 3 og 24 tegn og bruge små bogstaver og tal.

### <a name="get-account-access-keys"></a>Få konto access-taster
**Get-AzureRmBatchAccountKeys** viser de access-taster, der er knyttet til en Azure batchen konto. For eksempel køre følgende for at få de primære og sekundære taster på den konto, du har oprettet.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Opret en ny nøgle i access
**Ny AzureRmBatchAccountKey** genererer en ny primær eller sekundær kontonøgle for en Azure batchen-konto. Skriv for at oprette en ny primær nøgle til kontoen batchen, f.eks.:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [AZURE.NOTE] Hvis du vil oprette en ny sekundær nøgle, Angiv "Sekundære" til parameteren **KeyType** . Du skal genoprette tasterne primære og sekundære separat.

### <a name="delete-a-batch-account"></a>Slette en batchen konto
**Fjern AzureRmBatchAccount** sletter en batchen konto. Eksempel:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Når du bliver bedt om det, kan du bekræfte, at du vil fjerne kontoen. Konto fjernelse kan gå et stykke tid at fuldføre.

## <a name="create-a-batchaccountcontext-object"></a>Oprette et BatchAccountContext objekt

Hvis du vil godkende ved hjælp af batchen PowerShell-cmdletterne, når du opretter og administrere batchen grupper, job, opgaver og andre ressourcer, du først oprette en BatchAccountContext objektet for at gemme dit kontonavn og nøgler:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Du kan videregive objektet BatchAccountContext til cmdletter, bruger parameteren **BatchContext** .

> [AZURE.NOTE] Som standard bruges firmaets primære nøgle til godkendelse, men du kan eksplicit vælge tasten for at bruge ved at ændre din BatchAccountContext objektets **KeyInUse** egenskab: `$context.KeyInUse = "Secondary"`.

## <a name="create-and-modify-batch-resources"></a>Oprette og redigere batchen ressourcer
Bruge cmdletter som **Ny AzureBatchPool**, **Ny AzureBatchJob**og **Ny AzureBatchTask** til at oprette ressourcer under en batchen konto. Der er svarer **Get -** og **angive -** cmdlet'er til at opdatere egenskaberne for eksisterende ressourcer, og **Fjern -** cmdletter for at fjerne ressourcer under en batchen konto.

Når du bruger mange af disse cmdlet'er samt overføre et BatchContext objekt, skal du oprette eller overførsel af objekter, der indeholder detaljerede ressourceindstillinger, som vist i følgende eksempel. Få vist detaljeret hjælp til hver cmdlet Yderligere eksempler.

### <a name="create-a-batch-pool"></a>Oprette en batchen gruppe

Når du opretter eller opdatere en batchen gruppe, vælger du en skybaseret tjenestekonfiguration eller et virtuelt konfiguration for operativsystemet på noderne Beregn (se [Oversigt over batchen funktion](batch-api-basics.md#pool)). Valget afgør, om din Beregn noder afbildning, med en af de [Azure gæst OS versioner](../cloud-services/cloud-services-guestos-update-matrix.md#releases) eller med en af de understøttede Linux eller Windows VM billeder i Azure Marketplace.

Når du kører **Ny AzureBatchPool**, kan du sende operativsystemindstillingerne i et PSCloudServiceConfiguration eller PSVirtualMachineConfiguration objekt. For eksempel opretter følgende cmdlet en ny gruppe i batchen med størrelse lille Beregn noder i skyen tjenestekonfiguration afbildning med den seneste version af operativsystem af familie 3 (Windows Server 2012). Her angiver parameteren **CloudServiceConfiguration** variablen *$configuration* som PSCloudServiceConfiguration objekt. Parameteren **BatchContext** angiver en tidligere defineret variable *$context* som BatchAccountContext objekt.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Target antallet af knuder, Beregn i den nye gruppe bestemmes af en Autoskalering formel. I dette tilfælde formlen er blot **$TargetDedicated = 4**, der angiver antallet af knuder Beregn i puljen er 4 højst.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Forespørgsel til grupper, job, opgaver og andre oplysninger

Bruge-cmdletter som **Get-AzureBatchPool**, **Get-AzureBatchJob**og **Få AzureBatchTask** for forespørgsel for objekter, der er oprettet under en batchen konto.

### <a name="query-for-data"></a>Forespørgsel efter data

Brug **Get-AzureBatchPools** som et eksempel, for at finde dine grupper. Som standard gemmes denne forespørgsler for alle grupper under din konto, hvis du allerede objektet BatchAccountContext i *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Bruge et OData-filter

Du kan angive et OData-filter ved hjælp af parameteren **Filter** til at finde kun de objekter, du er interesseret i. For eksempel kan du finde alle grupper med id'er, der starter med "myPool":

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Denne metode er ikke så fleksible som ved hjælp af "Where-objekt" i en lokal pipeline. Men forespørgslen sendes til tjenesten batchen direkte så al filtrering sker på serversiden, gemme internetbåndbredde.

### <a name="use-the-id-parameter"></a>Brug parameteren id.

Et alternativ til et OData-filter er at bruge parameteren **Id** . Til at forespørge om en bestemt samling med id "myPool":

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

**Id-** parameteren understøtter kun fuld-id-søgning, ikke jokertegn eller OData-typografi filtre.

### <a name="use-the-maxcount-parameter"></a>Bruge parameteren MaxCount

Hver cmdlet returnerer som standard maksimalt 1000 objekter. Hvis du kommer til denne grænse, enten indskrænke filteret for at få tilbage færre objekter eller eksplicit er angivet op ved hjælp af parameteren **MaxCount** . Eksempel:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

For at fjerne den øvre grænse, skal du angive **MaxCount** til 0 eller mindre.

### <a name="use-the-powershell-pipeline"></a>Bruge PowerShell pipeline

Batchen cmdletter kan udnytte PowerShell pipeline for at sende data mellem cmdletter. Dette er den samme effekt som en parameter, der angiver, men gør det nemmere at arbejde med flere enheder.

Eksempelvis finder og få vist alle opgaver under din konto:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Genstart (genstart) noderne Beregn i en gruppe:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Pakke programadministration

Programpakker er en forenklet måde at implementere programmer til Beregn knuderne i dine grupper. Med batchen PowerShell-cmdletter, kan du overføre og administrere programpakker i kontoen batchen og implementere pakke versioner for at beregne noder.

**Opret** et program:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Tilføj** en programpakke:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Angiv den **standardversionen** for programmet:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Liste over** en programpakker

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Slette** en programpakke

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Slette** et program

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

>[AZURE.NOTE] Du skal slette alle versioner af et program programmet pakke, før du sletter programmet. Du får fejlen 'Konflikt', hvis du forsøger at slette et program, der aktuelt har programpakker.

### <a name="deploy-an-application-package"></a>Installere en programpakke

Når du opretter en gruppe, kan du angive en eller flere programpakker til installation. Når du angiver en pakke på klokkeslættet for oprettelsen af puljen, og installeres den i hver node som node joinforbindelser puljen. Pakker installeres også, når en node genstartet eller reimaged.

Angiv den `-ApplicationPackageReference` indstilling, når du opretter en gruppe for at installere en programpakke på den gruppe noder, som de deltager puljen. Først skal oprette et **PSApplicationPackageReference** objekt, og Konfigurer den med den program-Id og pakke version, du vil installere til den gruppe Beregn noder:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Nu oprette puljen, og Angiv objektet pakke reference som argument til den `ApplicationPackageReferences` indstilling:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Du kan finde flere oplysninger om programpakker i [programmet installation med Azure batchen programpakker](batch-application-packages.md).

>[AZURE.IMPORTANT] [Kæde en Azure-lager konto](#linked-storage-account-autostorage) skal du til kontoen batchen bruge programpakker.

### <a name="update-a-pools-application-packages"></a>Opdatere en samling programpakker

Hvis du vil opdatere de programmer, der er tildelt til en eksisterende gruppe, skal du først oprette en PSApplicationPackageReference objekt med de ønskede egenskaber (program-Id og pakke version):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Dernæst skal få puljen fra batchen, rydde alle eksisterende pakker, føje vores nye pakke og opdatere tjenesten batchen med de nye pulje indstillinger:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Du har nu opdateret i puljen egenskaber i tjenesten batchen. Hvis du vil installere faktisk programpakken for at beregne noder i puljen, men skal du genstarte eller reimage disse noder. Du kan genstarte hver node i en gruppe med denne kommando:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

>[AZURE.TIP] Du kan installere flere programpakker til noderne Beregn i en gruppe. Hvis du vil *tilføje* en programpakke i stedet for at erstatte de aktuelt installerede pakker, udelader de `$pool.ApplicationPackageReferences.Clear()` foregående linje.

## <a name="next-steps"></a>Næste trin

* Se [reference til Azure batchen-cmdlet](https://msdn.microsoft.com/library/azure/mt125957.aspx)til detaljerede cmdlet syntaks og eksempler.

* Se [program-installation med Azure batchen programpakker](batch-application-packages.md)kan finde flere oplysninger om programmer og -programpakker i Batch.

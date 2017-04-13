<properties
    pageTitle="Oversigt over Azure ressourcegruppe project installation scriptet | Microsoft Azure"
    description="I denne artikel beskrives, hvordan PowerShell-script i Azure ressourcegruppe installation projektet fungerer."
    services="visual-studio-online"
    documentationCenter="na"
    authors="tfitzmac"
    manager="timlt"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/26/2016"
    ms.author="tomfitz" />

# <a name="overview-of-the-azure-resource-group-project-deployment-script"></a>Oversigt over Azure ressourcegruppe project installation script

Azure ressourcegruppe installation projicerer hjælpe dig med fase og installere filer og andre elementer på Azure. Når du opretter et Azure ressourcestyring installationsprojekt i Visual Studio, føjes en PowerShell-script, kaldet **Implementer AzureResourceGroup.ps1** til projektet. Dette emne indeholder oplysninger om dette script gør og hvordan du kan udføre den både i og uden for Visual Studio.

## <a name="what-does-the-script-do"></a>Hvad gør scriptet?

Implementer AzureResourceGroup.ps1 scriptet gør to ting, der er vigtige for arbejdsprocessen for installation.

- Overføre filer eller elementer, der kræves for installation af skabelon
- Installere skabelonen

Den første del af scriptet overfører filer og elementer til installation, og det sidste cmdlet i scriptet faktisk installere skabelonen. Eksempelvis hvis en virtuel maskine skal være konfigureret med et script, uploader scriptet installation først sikkert scriptet til konfiguration til en Azure-lager-konto. Dette gør det tilgængeligt tilgængeligt til Azure ressourcestyring til konfiguration af den virtuelle maskine under klargøringen.

Da ikke alle skabelon installationer skal have ekstra elementer, der skal overføres, evalueres parameteren Skift kaldet *uploadArtifacts* . Hvis alle elementer skal overføres, angive parameteren *uploadArtifacts* , når du ringer til scriptet. Bemærk, at den primære skabelonfil og parametre fil ikke overføres. Kun andre filer, som konfigurationsscripts, indlejrede installation skabeloner, og programfiler skal overføres.

## <a name="detailed-script-description"></a>Detaljeret scriptbeskrivelse

Følgende er en beskrivelse af, hvilke Vælg sektioner af Implementer AzureResourceGroup.ps1 Azure PowerShell-script gør.

>[AZURE.NOTE] Dette beskriver version 1.0 af scriptet Implementer AzureResourceGroup.ps1.

1.  Definere parametre, der bruges til Azure ressourcestyring installation project. Nogle parametre har standardværdier, der blev angivet, da projektet blev oprettet. Du kan ændre disse standardværdier i scriptet eller tilføje forskellige parameterværdier, før du udfører scriptet.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

  	|Parameter|Beskrivelse|
  	|---|---|
  	|$ResourceGroupLocation|Område eller data center placeringen for ressourcegruppen, som **Vest USA** eller **Østasien**.|
  	|$ResourceGroupName|Navnet på Azure ressourcegruppen.|
  	|$UploadArtifacts|En binær værdi, der angiver, om elementer skal overføres til Azure fra systemet.|
  	|$StorageAccountName|Navnet på kontoen Azure-lager, hvor din elementer er blevet overført.|
  	|$StorageAccountResourceGroupName|Navnet på gruppen Azure ressource, der indeholder kontoen lagerplads.|
  	|$StorageContainerName|Navnet på objektbeholderen til lagring af bruges til overførsel af elementer.|
  	|$TemplateFile|Stien til installationsfilen (`<app name>.json`) i projektet Azure ressourcegruppe.|
  	|$TemplateParametersFile|Stien til parameterfilen (`<app name>.parameters.json`) i projektet Azure ressourcegruppe.|
  	|$ArtifactStagingDirectory|Stien på din computer, hvor elementer lokalt overføres, herunder PowerShell-script rodmappen. Denne sti kan være absolutte eller i forhold til scriptplacering.|
  	|$AzCopyPath|Den sti, hvor værktøjet AzCopy.exe kopierer .zip-filer, herunder PowerShell-script rodmappen. Denne sti kan være absolutte eller i forhold til scriptplacering.|
  	|$DSCSourceFolder|Stien til mappen DTK (beskedteksten tilstand konfiguration) kilde, herunder PowerShell-script rodmappen. Denne sti kan være absolutte eller i forhold til scriptplacering. Se [Introduktion til filtypenavnet Azure PowerShell DTK (beskedteksten tilstand konfiguration)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx), hvis det er relevant, kan finde flere oplysninger.|

1.  Kontrollér, om elementer skal overføres til Azure. Hvis ikke, skal du gå videre til trin 11. Ellers skal du udføre følgende trin.

1.  Konvertere en hvilken som helst variabler med relative stier til absolutte stier. Eksempelvis ændre en sti såsom `..\Tools\AzCopy.exe` til `C:\YourFolder\Tools\AzCopy.exe`. Desuden initialiseret variabler *ArtifactsLocationName* og *ArtifactsLocationSasTokenName* til null. *ArtifactsLocation* og *SaSToken* kan være parametre til skabelonen. Hvis deres værdier er null efter at have læst i parameterfilen, genererer scriptet værdier for dem.

    Værktøjerne Azure bruge parameteren værdier *_artifactsLocation* og *_artifactsLocationSasToken* i skabelonen til at administrere elementer. Hvis PowerShell-script finder parametre med navne, men parameterværdierne der ikke findes, scriptet overfører elementer, og returnerer relevante værdier for disse parametre. Den overfører dem derefter til cmdlet via `@OptionsParameters`.

  	|Variabel|Beskrivelse|
  	|---|---|
  	|ArtifactsLocationName|Stien til hvor Azure elementer er placeret.|
  	|ArtifactsLocationSasTokenName|SAS (delt Access underskrift) token navnet, der bruges af scriptet til at godkende Service Bus. Du kan få flere oplysninger i [Delt adgang signatur godkendelse med Service Bus](service-bus-shared-access-signature-authentication.md) .|

    ```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.  Dette afsnit kontrollerer om den <app name>. parameters.json fil (kaldet "parametre fil") har en overordnet node navngivne **parametre** (i den `else` Bloker). Ellers har det ingen overordnede node. Formaterne kan accepteres.
    
    ```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.  Navigere gennem samlingen af JSON parametre. Hvis en parameterværdi har fået tildelt til *_artifactsLocation* eller *_artifactsLocationSasToken*, skal du angive den variable *$OptionalParameters* med disse værdier. Dette forhindrer, at scriptet ved et uheld overskrive eventuelle parameterværdier, du angiver.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.  Få lagerplads kontonøgle og kontekst for lagerplads konto ressourcen bruges til at holde elementer til installation.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.  Hvis du bruger DTK PowerShell til at konfigurere en virtuel maskine, kræver filtypenavnet DTK elementer i en enkelt zip-fil. Så, oprette en .zip-arkivfilen for DTK konfiguration. For at gøre dette, Kontrollér, om $DSCSourceFolder findes. Hvis der findes en DTK konfiguration, kan du fjerne den og derefter oprette en ny komprimeret fil kaldet dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.  Hvis ingen sti til Azure elementer ikke er angivet i filen parametre, kan du angive en sti til PowerShell-script skal bruges, når overførsel af elementer. Oprette en sti, ved hjælp af en kombination af lager firmaets slutpunkt sti plus lagerplads objektbeholdernavnet for at gøre dette. Opdater derefter filen parametre med denne nye sti.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.  Brug værktøjet **AzCopy** (inkluderet i mappen **Tools** i projektet Azure ressourcegruppe installation) til at kopiere alle filer fra din lokale lager slip sti til kontoen online Azure-lager. Hvis dette trin mislykkes, skal du afslutte scriptet, da installationen ikke sandsynligvis kan udføres uden de nødvendige elementer.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.  Hvis et SAS token for elementer placering ikke er angivet i filen parametre, kan du oprette én for at give midlertidig skrivebeskyttet adgang til objektbeholderen online lagerplads. Derefter overføre pågældende SAS token på cmdline som en "optionalParameter". Bemærk, at alle parametre på cmdline højere prioritet over værdier, der er angivet i parameterfilen.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Oprette ressourcegruppen, hvis den ikke allerede findes, og markér filen skabelon og parametre for en hvilken som helst valideringsfejl, der forhindrer installation fra efterfølgende.

    ```
    # Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

    Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Til sidst skal installere skabelonen. Denne kode opretter et entydigt navn til den installation, ved hjælp af et tidsstempel.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## <a name="deploy-the-resource-group"></a>Installere ressourcegruppen

### <a name="to-deploy-the-resource-group-in-visual-studio"></a>Installere ressourcegruppe i Visual Studio

1. Vælg **Installer**på genvejsmenuen Azure ressourcegruppe projektets > **Ny installation**.

    ![][0]

1. I dialogboksen **Implementer til ressourcegruppe** skal du enten vælge en eksisterende ressourcegruppe i rullemenuen til at installere til, eller Vælg ** &lt;Opret ny... &gt;** for at oprette en ny ressourcegruppe.

    ![][1]

1. Hvis du bliver bedt om det, indtast ressource gruppenavn og placering i dialogboksen **Opret ressourcegruppe** og derefter vælge knappen **Opret** .

    ![][2]

1. Vælg knappen **Rediger parametre** for at få vist dialogboksen **Rediger parametre** , og angiv derefter en hvilken som helst manglende parameterværdier.

    ![][3]

    >[AZURE.NOTE] Hvis nogen obligatoriske parametre har brug for værdier, vises denne dialogboks automatisk, når du installerer.

    ![][4]

1. Når du er færdig skrive parameterværdier, Vælg knappen **Gem** , og vælg derefter knappen **Installer** .

    Scriptet installation (Implementer-AzureResourceGroup.ps1) kører og installerer skabelonen, sammen med alle elementer på Azure.

### <a name="to-deploy-the-resource-group-by-using-powershell"></a>Installere ressourcegruppen ved hjælp af PowerShell

Hvis du vil køre scriptet uden brug af Visual Studio installere kommandoen og brugergrænseflade, i genvejsmenuen for scriptet, skal du vælge **Åbn med PowerShell ISE**.

![][5]


## <a name="command-deployment-examples"></a>Kommandoen installationseksempler

### <a name="deploy-using-default-values"></a>Installere ved hjælp af standardværdier

I dette eksempel viser, hvordan til at køre scriptet ved hjælp af parameter standardværdierne. (Da Placeringsparameteren ikke har en standardværdi, du skal angive et.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### <a name="deploy-overriding-the-default-values"></a>Installere tilsidesætte standardværdierne

I dette eksempel viser, hvordan til at køre scriptet for at installere skabelon og parametre filer, der er forskellige fra standardværdierne.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### <a name="deploy-using-uploadartifacts-for-staging"></a>Installere ved hjælp af UploadArtifacts til midlertidige

I dette eksempel viser, hvordan til at køre scriptet for at overføre elementer fra mappen version og installere ikke-standard skabeloner.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

I dette eksempel viser, hvordan til at køre scriptet i en Azure PowerShell opgave i Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## <a name="next-steps"></a>Næste trin
Lær mere om Azure ressourcestyring ved at læse [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md).

Se flere eksempler på arbejde med Azure ressourcegruppe projekter under [Implementer og administrere Azure ressourcer](https://github.com/Microsoft/HealthClinic.biz/wiki/Deploy-and-manage-Azure-resources) fra [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 forbinde [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Du kan finde flere Hurtig start-guider fra HealthClinic.biz videoen, [Azure udvikler værktøjer Hurtig start-guider](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png

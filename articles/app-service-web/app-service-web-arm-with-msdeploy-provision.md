<properties
    pageTitle="Installere en WebApp, ved hjælp af MSDeploy med hostname og ssl-certifikat"
    description="Bruge en skabelon til Azure ressourcestyring til at installere en online ved hjælp af MSDeploy og konfiguration af brugerdefineret hostname og et SSL-certifikat"
    services="app-service\web"
    manager="erikre"
    documentationCenter=""
    authors="jodehavi"
    />

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="john.dehavilland"/>

# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>Installere en WebApp med MSDeploy, brugerdefinerede hostname og SSL-certifikat

Denne vejledning vejleder gennem oprettelse af en-til-slut-installation til en Azure-WebApp, udnytte MSDeploy samt tilføjelse af en brugerdefineret hostname og et SSL-certifikat til skabelonen ARM.

Se [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md), du kan finde flere oplysninger om oprettelse af skabeloner.

###<a name="create-sample-application"></a>Oprette Northwind

Du kan implementere en ASP.NET web application. Det første trin er at oprette et simpelt webprogram (eller du kan vælge at bruge en eksisterende,-i hvilket tilfælde kan du springe dette trin).

Åbn Visual Studio-2015 og vælg Filer > nyt projekt. Vælg Web på den dialogboks, der vises > ASP.NET-webprogrammet. Vælg Web under skabeloner og vælge skabelonen MVC. Vælg _Skift godkendelsestype_ til _Ingen godkendelse_. Dette er blot til at gøre eksempelprogrammet så enkelt som muligt.

På dette tidspunkt har du en grundlæggende ASP.Net WebApp klar til brug som en del af installationsprocessen.

###<a name="create-msdeploy-package"></a>Oprette MSDeploy pakke

Næste trin er at oprette pakken for at installere WebApp til Azure. Gemme projektet for at gøre dette, og derefter køre følgende fra kommandolinjen:

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Dette vil oprette en ZIP-pakke under mappen PackageLocation. Programmet er nu klar til at være installeret, som du kan oprette ud af en Azure ressourcestyring skabelon ved at gøre det nu.

###<a name="create-arm-template"></a>Oprette ARM skabelon
Først skal Lad os starte med en grundlæggende ARM skabelon, som opretter et webprogram og en hosting plan (Bemærk, at parametre og variabler ikke vises af pladshensyn).

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

Derefter skal du ændre web app ressourcen for at tage en indlejret MSDeploy ressource. Dette kan du referere til pakken oprettede tidligere, og fortæl Azure ressourcestyring bruge MSDeploy til at udrulle pakken til Azure-WebApp. Følgende viser Microsoft.Web/sites ressourcen med den indlejrede MSDeploy ressource:

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Nu vil du bemærke, at MSDeploy ressourcen tager en **packageUri** egenskab, der defineres således:

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Denne **packageUri** tager lagerplads konto-uri som peger på kontoen lagerplads, hvor du vil overføre din pakke zip til. Ressourcestyring Azure udnytter [Delt Access signaturer](../storage/storage-dotnet-shared-access-signature-part-1.md) for at adskille pakken lokalt fra kontoen lagerplads når du installerer skabelonen. Denne proces automatiseret via en PowerShell-script, der kan overføre pakken og kalde Azure Management API for at oprette de pågældende taster obligatorisk, og overføre dem til skabelonen som parametre (*_artifactsLocation* og *_artifactsLocationSasToken*). Du skal definere parametre for mappen, og filnavn pakken er overført til under objektbeholderen til lagring af.

Derefter skal du tilføje i en anden indlejrede ressource du konfigurerer hostname bindinger for at udnytte et brugerdefineret domæne. Du skal først at sikre, at du ejer værtsnavnet og konfigurere det til at være godkendt af Azure, at du ejer det - se [konfigurere et brugerdefineret domænenavn i Azure App-tjeneste](web-sites-custom-domain-name.md). Når det er gjort kan du tilføje følgende til din skabelon under afsnittet Microsoft.Web/sites ressource:

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

Til sidst skal du tilføje en anden øverste niveau ressource, Microsoft.Web/certificates. Denne ressource skal indeholde dit SSL-certifikat og findes på det samme niveau som online og hosting plan.

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

Du skal være et gyldigt SSL-certifikat for at konfigurere denne ressource. Når du har den gyldigt certifikat skal du udtrækker pfx byte som en base64-streng. En af mulighederne til at uddrage dette er at bruge følgende PowerShell-kommando:

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

Du kan derefter sende det som en parameter til skabelonen ARM installation.

På dette tidspunkt er skabelonen ARM klar.

###<a name="deploy-template"></a>Installere skabelon

De sidste trin er at stykke det alle sammen i en fuldstændig til en komplet installation. At gøre installation nemmere kan du udnytte det **Implementer AzureResourceGroup.ps1** PowerShell-script, der er tilføjet, når du opretter en Azure ressourcegruppe projekt i Visual Studio til at hjælpe med overførsel af alle elementer, der er påkrævet i skabelonen. Det kræver, at du har oprettet en lagerplads-konto, du vil bruge forvejen. I dette eksempel skal jeg har oprettet en delt storage konto for package.zip at blive overført. Scriptet udnytter AzCopy for at overføre pakken til kontoen lagerplads. Du overfører i din genstand mappeplacering, og Overfør scriptet automatisk alle filer i den pågældende mappe til objektbeholderen navngivet lagerplads. Når du ringer Implementer AzureResourceGroup.ps1, du har derefter opdatere SSL bindingerne for at tilknytte den brugerdefinerede hostname med dit SSL-certifikat.

Den følgende PowerShell viser en fuld implementering kalder installation-AzureResourceGroup.ps1:

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

På dette tidspunkt dit program skal er blevet installeret, og du bør kunne gå til det via https://www.yourcustomdomain.com

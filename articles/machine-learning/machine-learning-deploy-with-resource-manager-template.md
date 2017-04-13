<properties
    pageTitle="Installere Machine Learning arbejdsområde ved hjælp af Azure ressourcestyring skabelon | Microsoft Azure"
    description="Sådan installeres et arbejdsområde til Azure Machine Learning ved hjælp af Azure ressourcestyring skabelon"
    services="machine-learning"
    documentationCenter=""
    authors="ahgyger"
    manager="haining"
    editor="garye"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="ahgyger"/>
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Installere Machine Learning arbejdsområde ved hjælp af Azure ressourcestyring

## <a name="introduction"></a>Introduktion
Ved hjælp af en Azure ressourcestyring skabelon til distribution af sparer du tid ved at give du en SVG metode til at installere forbundne komponenter med en validering, og prøv igen ordning. Hvis du vil konfigurere Azure Machine Learning arbejdsområder, for eksempel skal du først konfigurere en konto til Azure-lager og derefter installere dit arbejdsområde. Forestil dig at udføre dette manuelt for hundredvis af arbejdsområder. Nemmere alternativ er at bruge en skabelon til Azure Ressourcestyring for at implementere et Azure Machine Learning arbejdsområde og alle dens afhængigheder. I denne artikel fører dig gennem processen trinvise. Du kan finde en god oversigt over Azure Ressourcestyring, [Azure ressourcestyring oversigt](../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Trinvis vejledning: oprette et Machine Learning arbejdsområde
Vi vil oprette en Azure ressourcegruppe og derefter installere en ny konto Azure-lager og et nyt Azure Machine Learning arbejdsområde ved hjælp af en ressourcestyring skabelon. Når installationen er fuldført, kan vi udskrive vigtige oplysninger om de arbejdsområder, der er oprettet (den primære nøgle, workspaceID og URL-adressen til arbejdsområdet).

### <a name="create-an-azure-resource-manager-template"></a>Oprette en skabelon til Azure ressourcestyring
Et Machine Learning arbejdsområde kræver en Azure lagerplads konto til at gemme de datasæt, der er knyttet til den.
Følgende skabelon bruger navnet på ressourcegruppen til at generere kontonavn lager og navnet på arbejdsområdet.  Det også bruger kontonavn lager som en egenskab ved oprettelse af arbejdsområdet.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Gemme denne skabelon som mlworkspace.json fil under c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Installere ressourcegruppen, baseret på skabelonen
* Åbn PowerShell
* Installere moduler til Azure ressourcestyring og Azure Service Management  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Disse trin hente og installere de moduler, der er nødvendige for at fuldføre de resterende trin. Dette skal kun udføres én gang i det miljø, hvor du udfører PowerShell-kommandoer.   

* Godkende til Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Dette trin skal gentages for hver enkelt session. Når godkendt, skal dine abonnementsoplysninger vises.

![Azure-konto][1]

Nu hvor vi har adgang til Azure, kan vi oprette ressourcegruppen.

* Oprette en ressourcegruppe

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Kontrollér, at ressourcegruppen er klargjort korrekt. **ProvisioningState** skal være "lykkedes."
Gruppen ressourcenavnet bruges af skabelonen til at generere kontonavn lager. Kontonavn lager skal være mellem 3 og 24 tegn og bruge tal og små bogstaver.

![Ressourcegruppe][2]

* Ved hjælp af den ressource gruppe installation, installere et nyt Machine Learning arbejdsområde.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Når installationen er fuldført, er det nemt at få adgang til egenskaber i arbejdsområdet, du har installeret. For eksempel kan du åbne den primære nøgletoken.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

En anden måde at hente tokens af eksisterende arbejdsområde er at bruge kommandoen Aktiver AzureRmResourceAction. For eksempel kan du angive de primære og sekundære tokens af alle arbejdsområder.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Når arbejdsområdet er klargjort, kan du også automatisere mange Azure Machine Learning Studio opgaver ved hjælp af [PowerShell-modul til Azure Machine Learning](http://aka.ms/amlps).

## <a name="next-steps"></a>Næste trin 
* Lær mere om [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md). 
* Har et kig på [Azure Hurtig start skabeloner lager](https://github.com/Azure/azure-quickstart-templates). 
* Se denne video om [Azure ressourcestyring](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 
 
<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->

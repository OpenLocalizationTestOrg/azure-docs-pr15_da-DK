<properties
    pageTitle="Bruge mærker til at organisere dine Azure ressourcer | Microsoft Azure"
    description="Viser, hvordan du anvender mærker for at organisere ressourcer til fakturering og administration af."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="AzurePortal"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="tomfitz"/>


# <a name="using-tags-to-organize-your-azure-resources"></a>Bruge mærker til at organisere dine Azure ressourcer

Ressourceleder, der gør det muligt at organisere logisk ressourcer ved at anvende mærker. Koderne består af nøgle/værdi-par, der identificerer ressourcer med egenskaber, som du definerer. For at markere ressourcer som tilhører samme kategori, skal du anvende samme mærke til disse ressourcer.

Når du får vist ressourcer til et bestemt mærke, kan du se ressourcer fra alle ressourcegrupper. Du er ikke begrænset til kun ressourcer i ressourcegruppen samme, som gør det muligt at organisere dine ressourcer på en måde, som er uafhængig af relationerne installation. Mærker kan være nyttig, når du har brug for til at organisere ressourcer for fakturerings- eller administration.

Hver kode, du føjer til en ressource eller ressourcegruppe føjes automatisk til abonnement hele taksonomien. Du kan også på forhånd udfylde taksonomien for dit abonnement med mærkenavne og værdier, du vil bruge som ressourcer, der er mærket i fremtiden.

Hver ressource eller ressourcegruppe kan have op til 15 mærker. Kodenavnet er begrænset til 512 tegn, og tag værdi er begrænset til 256 tegn.

> [AZURE.NOTE] Du kan kun anvende mærker til ressourcer, der understøtter ressourcestyring handlinger. Hvis du har oprettet et virtuelt, virtuelt netværk eller lagerplads gennem klassisk installation modellen (såsom via portalen klassisk), du kan ikke anvende et mærke på ressourcen. Geninstaller disse ressourcer gennem Ressourcestyring for at understøtte mærkning. Alle andre ressourcer understøtter mærkning.

## <a name="templates"></a>Skabeloner

For at mærke en ressource under installationen, skal du blot tilføje **mærker** elementet til den ressource, du installerer, og giv kodenavn og værdi. Kodenavn og værdi skal ikke allerede findes i dit abonnement. Du kan angive op til 15 mærker for hver ressource.

I følgende eksempel viser en lagerplads konto med et tag.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Ressourceleder, der understøtter i øjeblikket ikke, behandling af et objekt til mærkenavne og værdier. I stedet overfører et objekt til mærke værdierne, men du skal stadig angive mærkenavne, som vist i følgende eksempel.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## <a name="portal"></a>Portal

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>Azure CLI

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>REST-API

Portalen og PowerShell Brug begge [Ressourcestyring REST-API](https://msdn.microsoft.com/library/azure/dn848368.aspx) i baggrunden. Hvis du vil integrere mærkning i et andet miljø, kan du få mærker med få på ressource-id og opdatere sæt af mærker med et programrettelse opkald.


## <a name="tags-and-billing"></a>Mærker og fakturering

Du kan bruge mærker til at gruppere dataene fakturering for understøttede tjenester. For eksempel gør [virtuelle maskiner integreret med Azure ressourcestyring](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) det muligt at definere og anvende mærker for at organisere det fakturering forbrug for virtuelle maskiner. Hvis du kører flere FOS for andre virksomheder, kan du bruge mærker til gruppen brugen efter omkostningssted.  
Du kan også bruge mærker til at kategorisere omkostninger ved runtime-miljø som det fakturering forbrug for VM'er, der kører i produktionsmiljø.

Du kan hente oplysninger om mærker via [Azure Ressourceforbrug og RateCard API'er](billing-usage-rate-card-overview.md) eller filen brugen kommaseparerede værdier (CSV). Du kan hente filen brugen fra [Azure konti portal](https://account.windowsazure.com/) eller [EA portal](https://ea.azure.com). Se [få indsigt i dine Microsoft Azure Ressourceforbrug](billing-usage-rate-card-overview.md)kan finde flere oplysninger om programmeringsmæssig adgang til faktureringsoplysninger. Du kan finde REST-API handlinger, [Azure fakturering RESTEN API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Når du henter brugen CSV for tjenester, der understøtter mærker til fakturering, vises mærkerne i kolonnen **mærker** . Se [forstå din faktura til Microsoft Azure](billing/billing-understand-your-bill.md)kan finde flere oplysninger.

![Se mærker i fakturering](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Næste trin

- Du kan anvende begrænsninger og konventioner på tværs af dit abonnement med tilpassede politikker. Politikken du definerer kan kræve, at alle ressourcer, der har en værdi for et bestemt mærke. Se [Politik om brug af til at administrere ressourcer og kontrollere adgangen](resource-manager-policy.md)kan finde flere oplysninger.
- Se [Bruge Azure PowerShell med Azure ressourcestyring](./powershell-azure-resource-manager.md)for at få en introduktion til brug af Azure PowerShell, når du installerer ressourcer.
- Under [Brug af Azure CLI til Mac, Linux, og Windows med Azure ressourcestyring](./xplat-cli-azure-resource-manager.md)for at få en introduktion til brug af Azure CLI, når du installerer ressourcer.
- Se en introduktion til ved hjælp af portalen [ved hjælp af portalen Azure for at administrere dine Azure ressourcer](./azure-portal/resource-group-portal.md)  

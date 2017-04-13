<properties
   pageTitle="Implementere ressourcer med REST-API og skabelon | Microsoft Azure"
   description="Brug Azure ressourcestyring og ressourcestyring REST-API til at implementere en ressourcer til Azure. Ressourcerne, der er defineret i en ressourcestyring skabelon."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implementere ressourcer til Ressourcestyring skabeloner og ressourcestyring REST-API

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [REST-API](resource-group-template-deploy-rest.md)

I denne artikel beskrives, hvordan du bruger ressourcestyring REST-API med ressourcestyring skabeloner skal installeres dine ressourcer til Azure.  

> [AZURE.TIP] Hjælp til fejlfinding en fejl under installation, skal du se:
>
> - [Få vist installationshandlinger med REST-API](resource-manager-troubleshoot-deployments-rest.md) til at få mere at vide om at få oplysninger, der hjælper dig med at foretage fejlfinding af fejlen
> - [Fejlfinding i forbindelse med almindelige fejl, når du installerer ressourcer til Azure med Azure ressourcestyring](resource-manager-common-deployment-errors.md) for at se, hvordan du kan løse almindelige installationsfejl

Skabelonen kan være enten en lokal fil eller en ekstern fil, der er tilgængelige via en URI. Når din skabelon er placeret i en lagerplads-konto, kan du begrænse adgangen til skabelonen og angiver en delt signatur (SAS) adgangstoken under installationen.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Installere med REST API'ER
1. Angive [fælles parametre og sidehoveder](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), herunder godkendelsestokens.
2. Hvis du ikke har en eksisterende ressourcegruppe, kan du oprette en ressourcegruppe. Angiv dit abonnement-id, navnet på den nye ressourcegruppe og placering, du skal bruge til din løsning. Få mere at vide under [oprette en ressourcegruppe](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Validere din installation, inden du udfører den ved at køre handlingen [Valider installation af en skabelon](https://msdn.microsoft.com/library/azure/dn790547.aspx) . Når du tester installationen, du give parametre, præcis, som du ville gøre, når du udfører installationen (vist i næste trin).

3. Oprette en installation. Angiv dit abonnement-id, navnet på ressourcegruppen skal installeres, navnet på installationen, og et link til din skabelon. Du kan finde oplysninger om skabelonfilen [parameterfil](#parameter-file). Du kan finde flere oplysninger om REST-API til at oprette en ressourcegruppe, [Opret en skabelon installation](https://msdn.microsoft.com/library/azure/dn790564.aspx). Bemærk **tilstand** er indstillet til **tilvækst**. For at køre en komplet implementering skal du angive **tilstand** til **afsluttet**. Vær forsigtig, når du bruger tilstanden fuldført, som du kan slette ressourcer, der ikke er i skabelonen ved et uheld.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",
              }
            }
          }
   
      Hvis du vil logge svar indhold, skal indeholde anmodning om indhold eller begge **debugSetting** i indkaldelsen.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      Du kan konfigurere kontoen lagerplads til at bruge en delt adgangstoken signatur (SAS). Du kan finde yderligere oplysninger finder [Delegering af Access med en delt Access-signatur](https://msdn.microsoft.com/library/ee395415.aspx).

4. Hent status for installation af skabelon. Du kan finde flere oplysninger, kan du se [få oplysninger om installation af en skabelon](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Næste trin
- Se et eksempel med at udrulle ressourcer via biblioteket .NET klient [Implementer ressourcer ved hjælp af .NET biblioteker og en skabelon](virtual-machines/virtual-machines-windows-csharp-template.md).
- Se [redigering skabeloner](resource-group-authoring-templates.md#parameters)for at definere parametre i skabelon.
- Vejledning i installation af din løsning til forskellige miljøer, under [udvikling og testmiljøer i Microsoft Azure](solution-dev-test-environments.md).
- Få mere at vide om at bruge en KeyVault til at overføre sikker værdier, skal du se [overføre sikker værdier under installationen](resource-manager-keyvault-parameter.md).

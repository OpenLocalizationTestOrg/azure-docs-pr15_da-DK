<properties
   pageTitle="Få vist installation operationer med REST-API | Microsoft Azure"
   description="Beskriver, hvordan du bruger Azure ressourcestyring REST-API til at finde problemer fra ressourcestyring installation."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-resource-manager-rest-api"></a>Få vist installationshandlinger med Azure ressourcestyring REST API'ER

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST-API](resource-manager-troubleshoot-deployments-rest.md)

Hvis du har modtaget en fejl, når du installerer ressourcer på Azure, kan du vil se flere oplysninger om de installationshandlinger, der blev udført. REST-API indeholder handlinger, som gør det muligt at finde fejlene, og bestemme mulige løsninger.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Du kan undgå nogle fejl ved at validere skabelonen og infrastruktur før installation. Du kan også logge yderligere anmodningen og svaret oplysninger under installationen, der kan være nyttige senere til fejlfinding. For at få mere for at vide om at validere og oplysninger om logføring og svar, skal du se [Implementer en ressourcegruppe med Azure ressourcestyring skabelon](resource-group-template-deploy-rest.md).

## <a name="troubleshoot-with-rest-api"></a>Foretage fejlfinding af med REST API'ER

1. Installere dine ressourcer med handlingen [Opret en skabelon installation](https://msdn.microsoft.com/library/azure/dn790564.aspx) . Hvis du vil bevare oplysninger, der kan være nyttige til fejlfinding, skal du angive egenskaben **debugSetting** i JSON Anmod om at **requestContent** og/eller **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
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
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    Værdien **debugSetting** er som standard angivet til **ingen**. Når du angiver værdien **debugSetting** , nøje overveje typer oplysninger, du sender under installationen. Ved logføringsoplysninger om anmodningen eller svar, kan du eventuelt udsætte følsomme data, som er hentet via handlingerne installation. 

2. Få oplysninger om en installation med handlingen [få oplysninger om installation af en skabelon](https://msdn.microsoft.com/library/azure/dn790565.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    I svaret, Bemærk især **provisioningState** , **correlationId** og **fejl** elementer. **CorrelationId** bruges til at registrere relaterede begivenheder og kan være praktisk, når du arbejder med teknisk support for at foretage fejlfinding af et problem.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
            }  
          }
        }

3. Få oplysninger om installationshandlinger med handlingen [liste over alle skabelon installationshandlinger](https://msdn.microsoft.com/library/azure/dn790518.aspx) . 

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    Svaret omfatter anmodningen og/eller svar oplysninger, der er baseret på hvad du har angivet i egenskaben **debugSetting** under installationen.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Få begivenheder fra overvågningslogge til installation med handlingen [liste over management begivenheder i et abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## <a name="next-steps"></a>Næste trin

- Hjælp til løsning af fejl bestemt-installation, skal du se [løse almindelige fejl, når du installerer ressourcer til Azure med Azure ressourcestyring](resource-manager-common-deployment-errors.md).
- Hvis du vil lære at bruge overvågningslogge til at overvåge andre typer handlinger, se [overvåge forskellige handlinger med ressourcestyring](resource-group-audit.md).
- For at validere installationen, før du udfører den, skal du se [Implementer en ressourcegruppe med Azure ressourcestyring skabelon](resource-group-template-deploy.md).

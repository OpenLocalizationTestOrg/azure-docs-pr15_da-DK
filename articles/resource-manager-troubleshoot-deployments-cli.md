<properties
   pageTitle="Få vist installation operationer med Azure CLI | Microsoft Azure"
   description="Beskriver, hvordan du bruger Azure CLI til at finde problemer fra ressourcestyring installation."
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-cli"></a>Få vist installationshandlinger med Azure CLI

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST-API](resource-manager-troubleshoot-deployments-rest.md)

Hvis du har modtaget en fejl, når du installerer ressourcer på Azure, kan du vil se flere oplysninger om de installationshandlinger, der blev udført. Azure CLI indeholder kommandoer, som gør det muligt at finde fejlene, og bestemme mulige løsninger.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Du kan undgå nogle fejl ved at validere skabelonen og infrastruktur før installation. Du kan også logge yderligere anmodningen og svaret oplysninger under installationen, der kan være nyttige senere til fejlfinding. For at få mere for at vide om at validere og oplysninger om logføring og svar, skal du se [Implementer en ressourcegruppe med Azure ressourcestyring skabelon](resource-group-template-deploy-cli.md).

## <a name="use-audit-logs-to-troubleshoot"></a>Brug overvågningslogge at foretage fejlfinding af

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Hvis du vil se fejl for en installation, skal du følge nedenstående trin:

1. Kør kommandoen **azure logfil Vis** for at få vist overvågningsloggene. Du kan medtage den **– sidste installation** mulighed for at hente kun logfilen til den seneste installation.

        azure group log show ExampleGroup --last-deployment

2. Kommandoen **Vis azure gruppe log** returnerer mange oplysninger. Til fejlfinding vil du som regel til at fokusere på mislykkede handlinger. Følgende script bruger indstillingen **– json** og værktøjet [jq](https://stedolan.github.io/jq/) JSON til at søge i logfilen for mislykkede forsøg på installation.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"Target\":null,\"Details\":[{\"Message\":\"Website with given name
            mysite already exists.\"},{\"Code\":\"Conflict\"},{\"ErrorEntity\":{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"ExtendedCode\":
            \"54001\",\"MessageTemplate\":\"Website with given name {0} already exists.\",\"Parameters\":[\"mysite\"],\"InnerErrors\":null}}],\"Innererror\":null}"
        },
        ...

    Du kan se **Egenskaber** indeholder oplysninger i json om handlingen mislykkedes.

    Du kan bruge den **– detaljeret** og **- vv** muligheder for at se flere oplysninger fra loggene.  Brug den **– detaljeret** mulighed for at få vist trinnene handlingerne gennemfører på `stdout`. Brug indstillingen **- vv** for en komplet anmodning historik. Meddelelserne, der indeholder ofte vigtige oplysninger om årsagen om fejl.

3. For at fokusere på statusmeddelelsen for mislykkedes poster, skal du bruge følgende kommando:

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == \"Failed\") | .properties.statusMessage"


## <a name="use-deployment-operations-to-troubleshoot"></a>Brug installationshandlinger til at foretage fejlfinding af

1. Få den overordnede status for en installation med kommandoen **Vis azure gruppe installation** . I eksemplet herunder mislykkedes installationen.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Hvis du vil se meddelelsen til mislykkedes handlinger for en installation, skal du bruge:

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == \"Failed\") | .properties.statusMessage.Message"


## <a name="next-steps"></a>Næste trin

- Hjælp til løsning af fejl bestemt-installation, skal du se [løse almindelige fejl, når du installerer ressourcer til Azure med Azure ressourcestyring](resource-manager-common-deployment-errors.md).
- Hvis du vil lære at bruge overvågningslogge til at overvåge andre typer handlinger, se [overvåge forskellige handlinger med ressourcestyring](resource-group-audit.md).
- For at validere din installation, inden du udfører den, skal du se [Implementer en ressourcegruppe med Azure ressourcestyring skabelon](resource-group-template-deploy.md).

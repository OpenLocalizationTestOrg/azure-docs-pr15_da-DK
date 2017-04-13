<properties
   pageTitle="Få vist installation operationer med PowerShell | Microsoft Azure"
   description="Beskriver, hvordan du bruger Azure PowerShell til at finde problemer fra ressourcestyring installation."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-powershell"></a>Få vist installationshandlinger med Azure PowerShell

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST-API](resource-manager-troubleshoot-deployments-rest.md)

Du kan få vist handlinger for en installation via Azure PowerShell. Du kan være mest interesseret i at få vist handlingerne, når du har modtaget en fejl under installation, så i denne artikel fokuserer på visning af handlinger, som har mislykkedes. PowerShell indeholder cmdletter, så du let kan finde fejlene og bestemme mulige løsninger.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Du kan undgå nogle fejl ved at validere skabelonen og infrastruktur før installation. Du kan også logge yderligere anmodningen og svaret oplysninger under installationen, der kan være nyttige senere til fejlfinding. For at få mere for at vide om at validere og oplysninger om logføring og svar, skal du se [Implementer en ressourcegruppe med Azure ressourcestyring skabelon](resource-group-template-deploy.md).

## <a name="use-deployment-operations-to-troubleshoot"></a>Brug installationshandlinger til at foretage fejlfinding af

1. For at få den overordnede status for en installation skal du bruge kommandoen **Get-AzureRmResourceGroupDeployment** . Du kan filtrere resultaterne for kun disse installationer, der har mislykkedes.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Som returnerer de mislykkede installationer i følgende format:
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. De enkelte installationer er som regel består af flere handlinger med hver handling, der repræsenterer et trin i installationsprocessen. For at se, hvad der gik galt med en installation, skal du som regel se oplysninger om installationshandlinger. Du kan se status for handlinger med **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Som returnerer flere handlinger med hver af dem i følgende format:
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. For at få flere oplysninger om mislykkedes handlinger skal du hente egenskaber for handlinger med **fejltilstand** .

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Som returnerer alle mislykkedes handlinger med hver af dem i følgende format:
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    Bemærk registrering-ID for handlingen. Du skal bruge, i næste trin til at fokusere på en bestemt handling.

4. For at få statusmeddelelsen af en bestemt mislykket handling skal du bruge følgende kommando:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Der returneres:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## <a name="use-audit-logs-to-troubleshoot"></a>Brug overvågningslogge at foretage fejlfinding af

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Hvis du vil se fejl for en installation, skal du følge nedenstående trin:

1. For at hente logposter skal du køre kommandoen **Get-AzureRmLog** . Du kan bruge parametrene **ResourceGroup** og **Status for** at returnere kun hændelser, der mislykkedes for en enkelt ressourcegruppe. Hvis du ikke angiver et start- og tidspunkt, returneres elementer for den seneste time.
For at hente køre fejlbehæftede handlinger for den seneste time f.eks.:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Du kan angive et bestemt timespan. I det næste eksempel vil vi se for mislykkede handlinger for den sidste dag. 

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    Eller du kan angive en bestemt start- og sluttidspunkt for mislykkede handlinger:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Hvis denne kommando returnerer for mange poster og egenskaber, kan du fokusere din overvågning indsats ved at hente egenskaben **Properties** . Vi kan også indeholde parameteren **DetailedOutput** for at få vist fejlmeddelelserne.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    Som returnerer egenskaber for logposter i følgende format:
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. Baseret på disse resultater, Lad os fokusere på det andet element. Du kan indskrænke resultaterne yderligere ved at kigge på statusmeddelelsen for den pågældende post.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Der returneres:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## <a name="next-steps"></a>Næste trin

- Hjælp til løsning af fejl bestemt-installation, skal du se [løse almindelige fejl, når du installerer ressourcer til Azure med Azure ressourcestyring](resource-manager-common-deployment-errors.md).
- Hvis du vil lære at bruge overvågningslogge til at overvåge andre typer handlinger, se [overvåge forskellige handlinger med ressourcestyring](resource-group-audit.md).
- For at validere installationen, før du udfører den, skal du se [Implementer en ressourcegruppe med Azure ressourcestyring skabelon](resource-group-template-deploy.md).


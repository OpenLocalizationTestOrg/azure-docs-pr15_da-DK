<properties
   pageTitle="Oprette en intern justering af belastning ved hjælp af en skabelon i ressourcestyring | Microsoft Azure"
   description="Lær, hvordan du opretter en intern justering af belastning ved hjælp af en skabelon i ressourcestyring."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-using-a-template"></a>Oprette en intern justering af belastning ved hjælp af en skabelon

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][Klassisk implementeringsmodel](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Installere skabelonen ved hjælp af klik til at udrulle

Eksempel skabelonen tilgængelig i den offentlige lager bruger en parameterfil, der indeholder standard værdier, der bruges til at generere dette scenario, der er beskrevet ovenfor. Klik på **Implementer til Azure**for at installere denne skabelon ved hjælp af Klik på Følg [dette link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer)for at installere, erstatte Standardparameterværdier, hvis det er nødvendigt, og følg vejledningen i portalen.

## <a name="deploy-the-template-by-using-powershell"></a>Installere skabelonen ved hjælp af PowerShell

Følg nedenstående trin for at installere den skabelon, du har hentet ved hjælp af PowerShell.

1. Hvis du aldrig har brugt Azure PowerShell, se, [hvordan du installere og konfigurere Azure PowerShell](../../articles/powershell-install-configure.md) , og følg instruktionerne helt til end for at logge på Azure, og vælg dit abonnement.
2. Hent parameterfilen til din lokale disk.
3. Redigere filen, og Gem den.
4. Køre **Ny AzureRmResourceGroupDeployment** cmdlet for at oprette en ressourcegruppe ved hjælp af skabelonen.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Installere skabelonen ved hjælp af Azure CLI

Følg nedenstående trin for at installere skabelonen ved hjælp af Azure CLI.

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../../articles/xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.
2. Kør kommandoen **azure config tilstand** for at skifte til Ressourcestyring tilstand, som vist nedenfor.

        azure config mode arm

    Her er den forventede afgang for kommandoen ovenfor:

        info:    New mode is arm

3. Åbn parameterfilen, vælg dens indhold og gemme den til en fil på computeren. I dette eksempel skal vi har gemt parameterfilen til *parameters.json*.

4. Kør kommandoen **azure gruppe installation oprette** til at installere den nye interne justering af belastning ved hjælp af skabelonen og parameter filerne, du har downloadet og ændret ovenfor. Listen vises, når output forklares de parametre, bruges.

        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>Næste trin

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstanden for ved hjælp af kilde IP-forbindelse](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)




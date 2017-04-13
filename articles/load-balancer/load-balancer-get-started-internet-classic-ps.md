<properties
   pageTitle="Få en introduktion til en via justering af belastning i tilstanden Klassisk ved hjælp af PowerShell internettet | Microsoft Azure"
   description="Lær, hvordan du opretter en via justering af belastning i tilstanden Klassisk ved hjælp af PowerShell internettet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Få en introduktion til en via justering af belastning (klassisk) i PowerShell internettet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel. Du kan også [se, hvordan du opretter en via justering af belastning ved hjælp af Azure ressourcestyring internettet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## <a name="set-up-load-balancer-using-powershell"></a>Konfigurere justering af belastning ved hjælp af PowerShell

Følg nedenstående trin for at konfigurere en justering af belastning ved hjælp af powershell skal:

1. Hvis du aldrig har brugt Azure PowerShell, se, [hvordan du installere og konfigurere Azure PowerShell](../../articles/powershell-install-configure.md) , og følg instruktionerne helt til end for at logge på Azure, og vælg dit abonnement.


2. Når du har oprettet en virtuel maskine, kan du bruge PowerShell-cmdlet'er til at føje en belastningsjustering til en virtuel maskine inden for samme skytjenesten.

I følgende eksempel føjer du en belastningsjustering angive kaldet "webfarm" til skyen tjenesten "mytestcloud" (eller myctestcloud.cloudapp.net), tilføje slutpunkterne for justering af belastning virtuelle maskiner med navnet "web1" og "web2". Justering af belastning modtager netværkstrafik på port 80 og indlæse saldi mellem de virtuelle maskiner, der er defineret af det lokale slutpunkt (i dette tilfælde port 80) ved hjælp af TCP.


### <a name="step-1"></a>Trin 1
Oprette et Indlæs afstemmes slutpunkt for den første VM "web1"

    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### <a name="step-2"></a>Trin 2

Oprette et andet slutpunkt for den anden VM "web2" med det samme Indlæs belastningsjusteringstjenesten sætnavn

    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Fjerne en virtuel maskine fra en justering af belastning

Du kan bruge Fjern AzureEndpoint til at fjerne et virtuelt slutpunkt fra justering af belastning

    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## <a name="next-steps"></a>Næste trin

Du kan også [få en introduktion til en intern justering af belastning](load-balancer-get-started-ilb-classic-ps.md) og konfigurere, hvilken type [fordeling tilstand](load-balancer-distribution-mode.md) til en especific Indlæs belastningsjusteringstjenesten netværk trafik funktionsmåde.

Hvis dit program skal beholde forbindelser Live for servere bag en belastningsjustering, kan du forstå mere om [inaktive TCP timeoutindstillinger for belastningsjustering](load-balancer-tcp-idle-timeout.md). Det hjælper med at få mere for at vide om inaktiv forbindelse funktionsmåde, når du bruger Azure justering af belastning.


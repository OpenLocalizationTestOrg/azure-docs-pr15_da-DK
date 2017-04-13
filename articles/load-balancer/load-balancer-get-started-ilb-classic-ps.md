<properties
   pageTitle="Oprette en intern justering af belastning ved hjælp af PowerShell i den klassiske implementeringsmodel | Microsoft Azure"
   description="Lær at oprette en intern justering af belastning ved hjælp af PowerShell i modellen Klassisk installation"
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
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Få en introduktion til en intern belastningsjustering (klassisk) ved hjælp af PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](load-balancer-get-started-ilb-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Oprette en intern justering af belastning angive for virtuelle maskiner

Hvis du vil oprette et internt Indlæs belastningsjusteringstjenesten sæt og servere, der sender deres trafik til den, skal du gøre følgende:

1. Oprette en forekomst af interne indlæse justering, der skal være slutpunkt for indgående trafik skal være på tværs af servere i et netværksbelastningen sæt.

1. Tilføj slutpunkter, der svarer til de virtuelle maskiner, modtager den indgående trafik.

1. Konfigurere servere, der sender trafik skal være Indlæs afstemmes for at sende deres trafik til den virtuelle IP-adresse (VIP) adresse på den interne indlæse justering forekomst.


### <a name="step-1-create-an-internal-load-balancing-instance"></a>Trin 1: Oprette en intern belastning forekomst

Du kan oprette en intern belastning forekomst med følgende Windows PowerShell-kommandoer til en eksisterende skytjeneste eller en skybaseret tjeneste, der er installeret under et internationale virtuelt netværk:

    $svc="<Cloud Service Name>"
    $ilb="<Name of your ILB instance>"
    $subnet="<Name of the subnet within your virtual network>"
    $IP="<The IPv4 address to use on the subnet-optional>"

    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


Bemærk, at denne brug af [Tilføj AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell-cmdlet bruger sættet DefaultProbe parameter. Du kan finde flere oplysninger om yderligere parametersæt, [Tilføj AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Trin 2: Føje slutpunkter til den interne belastning forekomst

Her er et eksempel:

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $ilb="ilbset"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Trin 3: Konfigurere serverne for at sende deres trafik til den nye interne belastning slutpunkt

Du skal konfigurere de servere, hvis trafik der skal fungere Indlæs afstemmes for at bruge den nye IP-adresse (VIP) på den interne indlæse justering forekomst. Dette er den adresse, som den interne belastning forekomst lytter. I de fleste tilfælde skal du blot tilføje eller ændre en DNS-post til VIP på den interne belastning forekomst.

Hvis du har angivet IP-adressen under oprettelse af den interne belastning forekomst, har du allerede VIP. Ellers kan du se VIP fra følgende kommandoer:

    $svc="<Cloud Service Name>"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



For at bruge disse kommandoer skal du udfylde værdierne og fjerne den < og >. Her er et eksempel:

    $svc="mytestcloud"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


Bemærk IP-adressen fra visningen af kommandoen Get-AzureInternalLoadBalancer, og foretag de nødvendige ændringer til servere eller DNS-poster for at sikre, at trafik bliver sendt til VIP.

>[AZURE.NOTE]Microsoft Azure-platform bruger en statisk, offentligt distribueret IPv4-adresse til en række forskellige administrative scenarier. IP-adressen er 168.63.129.16. Denne IP-adresse skal ikke blokeres af enhver firewalls, da det kan medføre uventede funktionsmåde.
>Denne IP-adresse bruges i forhold Azure interne indlæse justering, ved at overvåge sonder fra justering af belastning til at bestemme tilstanden for virtuelle maskiner i et sæt Indlæs afstemmes. Hvis en sikkerhedsgruppe netværk bruges til at begrænse trafik til Azure virtuelle maskiner i et internt netværksbelastningen sæt eller anvendes på et virtuelt netværks-undernet, kan du sikre dig, at en netværk sikkerhedsregel føjes til tillade trafik fra 168.63.129.16.


## <a name="example-of-internal-load-balancing"></a>Eksempel på interne belastning

Se følgende afsnit for at gennemgå du slutningen end at oprettelsen af et Indlæs afstemmes sæt til to eksempler på konfigurationer.

### <a name="an-internet-facing-multi-tier-application"></a>En via programmet på computeren med flere lag internettet

Du vil angive en Indlæs afstemmes database tjeneste for en række internettet Web-servere. Begge sæt servere, der er hostet i en enkelt Azure skybaseret tjeneste. Web server trafik til TCP-port 1433 skal fordeles mellem to virtuelle maskiner i databasen lag. Figur 1 viser konfigurationen.

![Interne netværksbelastningen sæt for databaseniveau](./media/load-balancer-internal-getstarted/IC736321.png)


Konfigurationen består af følgende:

- Eksisterende skytjenesten vært virtuelle maskiner hedder mytestcloud.

- De to eksisterende databaseservere navngives Saldo 1, DB2.

- Web-servere i web-lag oprette forbindelse til databaseservere i databasen lag ved hjælp af privat IP-adresse. En anden mulighed er at bruge dine egne DNS for det virtuelle netværk og Registrer en A-post for interne Indlæs belastningsjusteringstjenesten sæt manuelt.

Følgende kommandoer konfigurere en ny interne belastning forekomst med navnet **ILBset** og føje slutpunkter til de virtuelle maskiner, der svarer til to databaseserverne:

    $svc="mytestcloud"
    $ilb="ilbset"
    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $vmname="DB1"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

    $epname="TCP-1433-1433-2"
    $vmname="DB2"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


## <a name="remove-an-internal-load-balancing-configuration"></a>Fjerne en intern belastning konfiguration

Hvis du vil fjerne en virtuel maskine som et slutpunkt fra en intern Indlæs belastningsjusteringstjenesten forekomst, skal du bruge følgende kommandoer:

    $svc="<Cloud service name>"
    $vmname="<Name of the VM>"
    $epname="<Name of the endpoint>"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Hvis du vil bruge disse kommandoer, angive de værdier, fjerne den < og >.

Her er et eksempel:

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Hvis du vil fjerne en intern Indlæs belastningsjusteringstjenesten forekomst fra en skybaseret tjeneste, skal du bruge følgende kommandoer:

    $svc="<Cloud service name>"
    Remove-AzureInternalLoadBalancer -ServiceName $svc

Hvis du vil bruge disse kommandoer, Udfyld værdien, og fjern den < og >.

Her er et eksempel:

    $svc="mytestcloud"
    Remove-AzureInternalLoadBalancer -ServiceName $svc



## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Yderligere oplysninger om interne Indlæs belastningsjusteringstjenesten cmdletter


Hvis du vil have yderligere oplysninger om interne belastning cmdletter, skal du køre følgende kommandoer på en Windows PowerShell-prompt:

- Get-help nye AzureInternalLoadBalancerConfig-fuld

- Get-help tilføje AzureInternalLoadBalancer-fuld

- Få hjælp Get-AzureInternalLoadbalancer-fuld

- Get-help Fjern AzureInternalLoadBalancer-fuld

## <a name="next-steps"></a>Næste trin

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstanden for ved hjælp af kilde IP-forbindelse](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)
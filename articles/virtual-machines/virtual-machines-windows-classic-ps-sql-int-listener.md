<properties
    pageTitle="Konfigurere en ILB lytteren til altid på tilgængelighed grupper | Microsoft Azure"
    description="Dette selvstudium bruger ressourcer, der er oprettet med den klassiske implementeringsmodel og opretter en altid på tilgængelighed gruppe lytteren i Azure ved hjælp af en intern Indlæs belastningsjusteringstjenesten (ILB)."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Konfigurere en ILB lytter for altid på tilgængelighed-grupper i Azure

> [AZURE.SELECTOR]
- [Interne lytteren](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Eksterne lytteren](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="overview"></a>Oversigt

Dette emne beskrives, hvordan du konfigurerer en lytter for en altid på tilgængelighed gruppe ved hjælp af en **Intern Indlæs belastningsjusteringstjenesten (ILB)**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]For at konfigurere en ILB lytter for en altid på tilgængelighed gruppe i ressourcestyring model skal du se [konfigurere en intern justering af belastning for en altid på tilgængelighed gruppe i Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).


Din tilgængelighed gruppe kan indeholde replikaer, der er lokale kun Azure kun eller dække både lokalt og Azure hybridkonfigurationer. Azure replikaer kan være placeret i det samme område eller på tværs af flere områder ved hjælp af flere virtuelle netværk (VNets). Følgende fremgangsmåde forudsætter, at du allerede har [konfigureret en tilgængelighed gruppe](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) , men ikke har konfigureret en lytter.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Retningslinjer og begrænsninger for interne lyttere
Bemærk følgende retningslinjer på tilgængelighed gruppe lytteren i Azure ved hjælp af ILB:

- Tilgængelighed gruppe lytteren understøttes på Windows Server 2008 R2, Windows Server 2012 og Windows Server 2012 R2.

- Kun én interne tilgængelighed gruppe lytteren understøttes per skybaseret tjeneste, fordi lytteren er konfigureret til at ILB, og der er kun én ILB per skybaseret tjeneste. Men det er muligt at oprette flere eksterne lyttere. Se [konfigurere en ekstern lytteren for altid på tilgængelighed grupper i Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md)kan finde flere oplysninger.

- Det er ikke understøttet for at oprette en intern lytteren i den samme skybaseret tjeneste, hvor du også har en ekstern lytteren ved hjælp af tjenesten skyen offentlige VIP.

## <a name="determine-the-accessibility-of-the-listener"></a>Finde ud af hjælp til handicappede i lytteren

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

I denne artikel fokuserer på at oprette en lytter, der bruger en **Intern Indlæs belastningsjusteringstjenesten (ILB)**. Hvis du har brug for en offentlig/ekstern lytteren, kunne se versionen af denne artikel, som indeholder trin til konfiguration af en [ekstern lytteren](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Oprette netværksbelastningen VM slutpunkter med direkte server returnerede

For ILB, skal du først oprette den interne justering af belastning. Dette gøres i scriptet nedenfor.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. For **ILB**, skal du tildele en statisk IP-adresse. Først skal du undersøge den aktuelle VNet konfiguration ved at køre følgende kommando:

        (Get-AzureVNetConfig).XMLConfiguration

1. Notér navnet for det undernet, der indeholder de VM'er, der hoster replikaerne **undernet** . Dette vil blive brugt i parameteren **$SubnetName** i scriptet.

1. Bemærk derefter **VirtualNetworkSite** navn og den første **AddressPrefix** for det undernet, der indeholder de VM'er, der hoster replikaerne. Se efter en tilgængelig IP-adresse ved at overføre begge værdier til kommandoen **Test-AzureStaticVNetIP** og at undersøge **AvailableAddresses**. Hvis VNet blev navnet *MyVNet* og havde en undernetadresse interval, der startede *172.16.0.128*f.eks., følgende kommando skal vise tilgængelige adresser:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Vælg en af de tilgængelige adresser og bruge det i parameteren **$ILBStaticIP** i følgende script.

3. Kopiér PowerShell-script under i et tekstredigeringsprogram, og Angiv de variable værdier, der passer til dit miljø (Bemærk, at standardindstillingerne har fået for nogle parametre). Bemærk, at eksisterende installationer, der bruger forbindelse grupper ikke kan tilføje ILB. Du kan finde flere oplysninger om ILB krav, [Interne justering af belastning](../load-balancer/load-balancer-internal-overview.md). Hvis din tilgængelighed gruppe strækker sig over Azure områder, skal du også køre scriptet én gang i hver datacenter for skytjeneste og noder, der er placeret i pågældende datacenter.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

1. Når du har angivet variablerne, kan du kopiere scriptet fra tekstredigeringsprogrammet til sessionen Azure PowerShell til at køre den. Hvis du bliver bedt om det stadig viser >>, Skriv ENTER igen for at sikre, at scriptet begynder at køre. Bemærk!

>[AZURE.NOTE] Azure klassisk portalen understøtter ikke den interne justering af belastning på nuværende tidspunkt, så du ikke se ILB eller slutpunkterne på portalen Azure klassisk. **Get-AzureEndpoint** returnerer dog en intern IP-adresse, hvis belastning kører på den. Ellers returneres null.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Kontrollere, at KB2854082 er installeret, hvis det er nødvendigt

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Åbn firewallporte i tilgængelighed gruppenoder

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Oprette tilgængelighed gruppe lytteren

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. ILB, skal du bruge IP-adressen på den interne Indlæs belastningsjusteringstjenesten (ILB) oprettede tidligere. Brug følgende script til at hente denne IP-adresse i PowerShell.

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. Kopiér PowerShell-script for dit operativsystem i et tekstredigeringsprogram på en af FOS, og Angiv variablerne til de værdier, du nævnt tidligere.

    For Windows Server 2012 eller nyere kan du bruge følgende script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
        
    Brug følgende script til Windows Server 2008 R2:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
    

1. Én gang du har angivet variablerne, Åbn en øgede Windows PowerShell-vinduet, og derefter kopiere scriptet fra tekstredigeringsprogrammet og indsætte i sessionen Azure PowerShell til at køre den. Hvis du bliver bedt om det stadig viser >>, Skriv ENTER igen for at sikre, at scriptet begynder at køre.

2. Gentag dette på hver VM. Dette script konfigurerer ressourcen IP-adresse med skytjenesten IP-adresse og angiver andre parametre som efterprøvning af af port. Når ressourcen IP-adresse er online, kan det derefter svare på afstemning på efterprøvning af af porten fra netværksbelastningen slutpunktet har oprettet tidligere i dette selvstudium.

## <a name="bring-the-listener-online"></a>Få lytteren online

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Opfølgning elementer

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Teste tilgængelighed gruppe lytteren (inden for den samme VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<properties
    pageTitle="Konfigurere en ekstern lytteren til altid på tilgængelighed-grupper | Microsoft Azure"
    description="Dette selvstudium vejleder dig gennem trinnene for at oprette en altid på tilgængelighed gruppe lytteren i Azure, der er eksternt tilgængelige ved hjælp af den offentlige tilknyttede skytjeneste virtuelle IP-adresse."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Konfigurere en ekstern lytter for altid på tilgængelighed-grupper i Azure

> [AZURE.SELECTOR]
- [Interne lytteren](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Eksterne lytteren](virtual-machines-windows-classic-ps-sql-ext-listener.md)

Dette emne beskrives, hvordan du konfigurerer en lytter for en altid på tilgængelighed gruppe, der er eksternt tilgængelige på internettet. Dette er gjort muligt ved at knytte tjenesten skyen **Offentlige virtuelle IP-adresse (VIP)** adresse til lytteren.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Din tilgængelighed gruppe kan indeholde replikaer, der er lokale kun Azure kun eller dække både lokalt og Azure hybridkonfigurationer. Azure replikaer kan være placeret i det samme område eller på tværs af flere områder ved hjælp af flere virtuelle netværk (VNets). Følgende fremgangsmåde forudsætter, at du allerede har [konfigureret en tilgængelighed gruppe](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) , men ikke har konfigureret en lytter.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Retningslinjer og begrænsninger for eksterne lyttere

Bemærk følgende retningslinjer om tilgængeligheden af gruppen lytteren i Azure, når du installerer ved hjælp af skybaseret tjeneste fise VIP-adresse:

- Tilgængelighed gruppe lytteren understøttes på Windows Server 2008 R2, Windows Server 2012 og Windows Server 2012 R2.

- Klientprogrammet skal placeres på en anden skybaseret tjeneste end den, der indeholder din tilgængelighed gruppe FOS. Azure understøtter ikke direkte server retur med klient- og i den samme skybaseret tjeneste.

- Trinnene i denne artikel viser som standard, hvordan du konfigurerer et lytteren at bruge skyen service virtuelle IP-adresse (VIP) adresse. Men det er muligt at reservere og oprette flere VIP-adresser for skybaseret tjeneste. Dette gør det muligt at følge trinnene i denne artikel til at oprette flere lyttere, der hver især knyttes til en anden VIP. Du kan finde oplysninger om, hvordan du opretter flere VIP-adresser, [Flere VIPs per skybaseret tjeneste](../load-balancer/load-balancer-multivip.md).

- Hvis du opretter en lytter til et hybridmiljø, skal den lokale netværk har forbindelse til det offentlige Internet ud over at websted VPN med Azure virtuelt netværk. Når du er i det Azure undernet nås tilgængelighed gruppe lytteren kun af den offentlige IP-adresse respektive skytjenesten.

- Det er ikke understøttet for at oprette en ekstern lytteren i den samme skybaseret tjeneste, hvor du også har en intern lytteren ved hjælp af den interne Indlæs belastningsjusteringstjenesten (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Finde ud af hjælp til handicappede i lytteren

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

I denne artikel fokuserer på at oprette en lytter, der bruger **eksterne justering af belastning**. Hvis du vil en lytter, der er private til netværket virtuel skal kunne se versionen af denne artikel, som indeholder trin til konfiguration af en [lytteren med ILB](virtual-machines-windows-classic-ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Oprette netværksbelastningen VM slutpunkter med direkte server returnerede

Eksterne belastning bruger den virtuelle skytjenesten, der er vært din FOS offentlige virtuelle IP-adresse. Så behøver du ikke at oprette eller konfigurere belastning i dette tilfælde.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Kopiér PowerShell-script under i et tekstredigeringsprogram, og Angiv de variable værdier, der passer til dit miljø (standardindstillinger for har fået for nogle parametre). Bemærk, at hvis din tilgængelighed gruppe strækker sig over Azure områder, skal du køre scriptet én gang i hver datacenter for skytjeneste og noder, der er placeret i pågældende datacenter.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

1. Når du har angivet variablerne, kan du kopiere scriptet fra tekstredigeringsprogrammet til sessionen Azure PowerShell til at køre den. Hvis du bliver bedt om det stadig viser >>, Skriv ENTER igen for at sikre, at scriptet begynder at køre.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Kontrollere, at KB2854082 er installeret, hvis det er nødvendigt

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Åbn firewallporte i tilgængelighed gruppenoder

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Oprette tilgængelighed gruppe lytteren

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Til eksterne belastningsjustering, skal du hente den offentlige virtuelle IP-adresse skytjenesten, der indeholder dine replikaer. Log på portalen Azure klassisk. Gå til den skybaseret tjeneste, der indeholder din tilgængelighed gruppe VM. Åbne visningen **Dashboard** .

3. Bemærk, at den adresse, der vises under **offentlige virtuelle IP-adresse (VIP) adresse**. Gentag dette trin for hver skybaseret tjeneste, der indeholder en VM, der er vært en replika, hvis din løsning strækker sig over VNets.

4. Kopiér PowerShell-script under i et tekstredigeringsprogram på en af FOS, og Angiv variablerne til de værdier, du nævnt tidligere.

        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

        Import-Module FailoverClusters

        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Én gang du har angivet variablerne, Åbn en øgede Windows PowerShell-vinduet, og derefter kopiere scriptet fra tekstredigeringsprogrammet og indsætte i sessionen Azure PowerShell til at køre den. Hvis du bliver bedt om det stadig viser >>, Skriv ENTER igen for at sikre, at scriptet begynder at køre.

1. Gentag dette på hver VM. Dette script konfigurerer ressourcen IP-adresse med skytjenesten IP-adresse og angiver andre parametre som efterprøvning af af port. Når ressourcen IP-adresse er online, kan det derefter svare på afstemning på efterprøvning af af porten fra netværksbelastningen slutpunktet har oprettet tidligere i dette selvstudium.

## <a name="bring-the-listener-online"></a>Få lytteren online

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Opfølgning elementer

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Teste tilgængelighed gruppe lytteren (inden for den samme VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Teste tilgængelighed gruppe lytteren (via internettet)

For at få adgang til lytteren fra uden for det virtuelle netværk, skal du bruge eksterne/offentlige belastning (beskrevet i dette emne) i stedet for ILB, som er kun tilgængelig i den samme VNet. I forbindelsesstrengen, kan du angive navnet på tjenesten skyen. Eksempelvis hvis du havde en skybaseret tjeneste med navnet *mycloudservice*, ville sætningen sqlcmd være på følgende måde:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

I modsætning til det forrige eksempel, skal SQL-godkendelse bruges, fordi kalderen ikke kan bruge windows-godkendelse via internettet. Du kan finde flere oplysninger, se [altid på tilgængelighed gruppe i Azure VM: klient Connectivity scenarier](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Når du bruger SQL-godkendelse, Sørg for, at du opretter den samme logon på begge replikaer. Du kan finde flere oplysninger om fejlfinding af logon med tilgængelighed grupper, se [Sådan tilknytte logon, eller brug indeholdt SQL-databasebruger for at oprette forbindelse til andre replikaer og knytte til tilgængelighed databaser](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Hvis er altid på replikaer i forskellige undernet, klienter skal angive **MultisubnetFailover = True** i forbindelsesstrengen. Dette resulterer i parallelle forbindelse forsøg replikaer i de forskellige undernet. Bemærk, at dette scenario omfatter en installation i tværs område altid på tilgængelighed-gruppe.

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<properties
   pageTitle="Konfigurere altid på tilgængelighed gruppe lyttere – Microsoft Azure"
   description="Konfigurere tilgængeligheden af gruppen lyttere på Azure ressourcestyring modellen, ved hjælp af en intern justering af belastning med en eller flere IP-adresser."
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="10/20/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Konfigurere en eller flere altid på tilgængelighed gruppe lyttefunktioner - ressourcestyring 

Dette emne viser, hvordan du kan gøre to ting:

- Oprette en intern justering af belastning for SQL Server tilgængelighed grupper ved hjælp af PowerShell-cmdlet'er.

- Føje yderligere IP-adresser til belastningsjustering til at understøtte mere end én SQL Server tilgængelighed gruppe. 

I SQL Server er en tilgængelighed gruppe lytteren et virtuelt netværksnavn som klienter opretter forbindelse til for at få adgang til en database i den primære eller sekundære replika. På Azure virtuelle maskiner indeholder en belastningsjustering IP-adressen for lytteren. Justering af belastning dirigerer trafik til forekomsten af SQL Server, som lytter på efterprøvning af af port. I de fleste tilfælde bruges en tilgængelighed gruppe en intern justering af belastning. En Azure interne justering af belastning kan være vært for en eller flere IP-adresser. Hver IP-adresse bruger en bestemt efterprøvning af af port. Dette dokument viser, hvordan du kan bruge PowerShell til at oprette en ny belastningsjustering, eller Føj IP-adresser til en eksisterende justering af belastning for SQL Server tilgængelighed grupper. 

Muligheden for at tildele flere IP-adresser til en intern justering af belastning er ny bruger af Azure og er kun tilgængelig i ressourcestyring model. For at udføre denne opgave, skal du have en SQL Server tilgængelighed gruppe, der er installeret på Azure virtuelle maskiner i ressourcestyring model. Begge SQL Server virtuelle maskiner skal være medlem af det samme sæt tilgængelighed. Du kan bruge den [Microsoft-skabelon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) til automatisk for at oprette gruppen tilgængelighed i Azure ressourcestyring. Denne skabelon opretter automatisk gruppen tilgængelighed, herunder den interne justering af belastning for dig. Hvis du foretrækker, kan du [konfigurere en AlwaysOn tilgængelighed gruppe](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Dette emne kræver, at dine tilgængelighed grupper er allerede konfigureret.  

Relaterede emner omfatter:

- [Konfigurere AlwaysOn tilgængelighed grupper i Azure VM (grafiske)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   

- [Konfigurere en VNet-VNet forbindelse ved hjælp af Azure ressourcestyring og PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Konfigurere Windows Firewall

Konfigurere Windows Firewall for at give adgang til SQL Server. Du skal konfigurere firewallen til at tillade TCP-forbindelser til porte bruges af SQL Server-forekomsten, samt den port, som lytteren efterprøvning af af. Se [konfigurere en Windows Firewall til Database Engine Access](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1)kan finde detaljerede oplysninger. Oprette en indgående regel for SQL Server-port og for efterprøvning af af port.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Eksempel på et Script: Oprette en intern justering af belastning med PowerShell

> [AZURE.NOTE] Hvis du oprettede din tilgængelighed gruppe med [Microsoft skabelon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) afkrydsningsfeltet Indlæs behøver du ikke har fuldført dette trin. 

Følgende PowerShell-script opretter en intern justering af belastning, konfigurerer belastningsjustering regler, og angiver en IP-adresse for justering af belastning. Åbn Windows PowerShell ISE for at køre scriptet, og Indsæt scriptet i ruden Script. Brug `Login-AzureRMAccount` til at logge på PowerShell. Hvis du har flere Azure abonnementer, kan du bruge `Select-AzureRmSubscription ` til at angive abonnementet. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>Eksempel på et script: føje en IP-adresse til en eksisterende justering af belastning, PowerShell

For at bruge mere end én tilgængelighed gruppe skal du bruge PowerShell til at tilføje en ekstra IP-adresse til en eksisterende justering af belastning. Hver IP-adresse kræver sin egen belastningsjustering regel, efterprøvning af af port og front port.

Front-end-port er den port, programmer bruger til at oprette forbindelse til SQL Server-forekomsten. IP-adresser for forskellige tilgængelighed grupper kan bruge den samme front-end-port.

>[AZURE.NOTE] For SQL Server tilgængelighed grupper kræver hver IP-adresse en bestemt efterprøvning af af port. Hvis en IP-adresse på belastningsjustering bruger efterprøvning af af port 59999, kan ingen andre IP-adresser på justering af belastning for eksempel bruge efterprøvning af af port 59999. 

- Finde oplysninger om justering af belastning begrænsninger **privat forsiden afslutte IP per justering af belastning** under [Netværk begrænsninger - Azure ressourcestyring](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

- Finde oplysninger om tilgængelighed i gruppen begrænsninger [begrænsninger (tilgængelighed grupper)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Følgende script tilføjer en ny IP-adresse til en eksisterende justering af belastning. Opdater variabler for dit miljø. ILB bruger lytteren port for indlæsning justering af belastning front-end port. Denne port kan være den port, SQL Server lytter på. For standard forekomster af SQL Server er dette port 1433. Regel for en gruppe i tilgængelighed af belastning kræver en flydende IP-adresse (returnerede direkte server), så back-end-porten er den samme som front-end-porten.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurere klynge for at bruge Indlæs belastningsjusteringstjenesten IP-adresse 

Næste trin er at konfigurere lytteren på klyngen, og få lytteren online. For at opnå dette, skal du gøre følgende: 

1. Oprette tilgængelighed gruppe lytteren på sekundær klynge  

2. Få lytteren online

## <a name="1-create-the-availability-group-listener-on-the-failover-cluster"></a>1. oprette tilgængelighed gruppe lytteren på sekundær klynge

I dette trin skal du føje en klient adgangspunkt til sekundær klynge med failoverklyngestyring og derefter bruge PowerShell til at konfigurere klyngeressourcen for at lytte på efterprøvning af af port. 

1. Bruge RDP til at oprette forbindelse til den Azure virtuelle maskine, der er vært den primære replika. 

2. Åbn failoverklyngestyring.

3. Vælg noden **netværk** , og noter netværksnavn klynge. Brug dette navn i den `$ClusterNetworkName` variabelt i PowerShell-script.

4. Udvid klyngenavnet, og klik derefter på **roller**.

5. I ruden **roller** , skal du højreklikke på tilgængelighed gruppenavn og derefter vælge **Tilføj ressource** > **Klient adgangspunkt**.

6. Oprette et navn til denne nye lytteren, i feltet **navn** og derefter to gange på **Næste** , og klik derefter på **Udfør**. Du skal ikke bringe ressourcen til online eller lytter på dette tidspunkt.
 
    Navnet på den nye lytteren er navnet på det netværk, der anvender programmer til at oprette forbindelse til databaser i gruppen SQL Server tilgængelighed.

7. Klik på fanen **ressourcer** , og derefter udvide adgangspunkt klient, du lige har oprettet. Højreklik på IP-ressourcen, og klik på Egenskaber. Notér navnet på IP-adressen. Du vil bruge dette navn i den `$IPResourceName` variable i PowerShell-script.

8. Klik på **Statiske IP-adresse** , og Angiv den statiske IP-adresse til den samme adresse, du brugte, da du angiver Indlæs belastningsjusteringstjenesten IP-adressen på portalen Azure under **IP-adresse** . 

9. Deaktivere NetBIOS for denne adresse, og klik på **OK**. Gentag dette trin for hver IP-ressource, hvis din løsning strækker sig over flere Azure VNets. 

10. Gøre SQL Server tilgængelighed Group ressourcen afhænger af IP-adressen. Højreklikke på ressourcen i klynge manager, dette er under **Andre ressourcer**under fanen **ressourcer** . 

11. Åbn en øgede PowerShell ISE på Klyngenoden, der er i øjeblikket vært den primære replika, og Indsæt følgende kommandoer i et nyt script. Klik på navnet på lytteren under fanen **afhængigheder** .
 
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
 
6. Opdater variablerne og køre PowerShell-scriptet for at konfigurere den IP-adresse og port til den nye lytter.

 >[AZURE.NOTE] Hvis dine SQL-servere er i separate områder, skal du køre PowerShell-scriptet to gange. Første gang bruger klynge netværksnavn klynge IP-ressourcenavnet, og Indlæs belastningsjusteringstjenesten IP-adresse fra den første ressourcegruppe. For anden gang bruge klynge netværksnavn klynge IP-ressourcenavnet, og Indlæs belastningsjusteringstjenesten IP-adresse fra den anden ressourcegruppe.

Klyngen har nu en tilgængelighed gruppe lytteren ressource.

## <a name="2-bring-the-listener-online"></a>2. sætter lytteren online

Med tilgængelighed gruppe lytteren ressourcen konfigureret, kan du tage lytteren online så programmer kan oprette forbindelse til databaser i gruppen tilgængelighed med lytteren.

1. Gå tilbage til failoverklyngestyring. Udvid **roller** og derefter fremhæve din tilgængelighed gruppe. Højreklik på navnet for lytteren, og klik på **Egenskaber**under fanen **ressourcer** .

1. Klik på fanen **afhængigheder** . Hvis der er flere ressourcer, der vises, skal du bekræfte, IP-adresser har eller ej og, afhængigheder. Klik på **OK**.

1. Højreklik på navnet for lytteren, og klik på **Få Online**.

1. Når lytteren er online, under fanen **ressourcer** , skal du højreklikke på gruppen tilgængelighed, og klik på **Egenskaber**.

1. Oprette en afhængighed af lytteren navn ressource (ikke IP-adresse ressourcenavnet). Klik på **OK**.

1. Start SQL Server Management Studio og oprette forbindelse til den primære replika.

1. Gå til **AlwaysOn høj tilgængelighed** | **tilgængelighed grupper** | **tilgængelighed gruppe lyttere**. 

1. Du bør nu se lytteren navnet, du har oprettet i failoverklyngestyring. Højreklik på navnet for lytteren, og klik på **Egenskaber**.

1. Angiv portnummeret for tilgængelighed gruppe lytteren ved hjælp af $EndpointPort du tidligere har brugt i boksen **Port** (1433 var standard), klik derefter på **OK**.

Du har nu en SQL Server tilgængelighed gruppe i Azure virtuelle maskiner, der kører i ressourcestyring tilstand. 

## <a name="test-the-connection-to-the-listener"></a>Teste forbindelsen til lytteren

At teste forbindelsen:

1. RDP til en SQL Server, der er i det samme virtuelle netværk, men ikke ejer replikaen. Det kan være andre SQL Server i klyngen.

2. Bruge **sqlcmd** til at teste forbindelsen. For eksempel opretter følgende script en **sqlcmd** forbindelse til den primære replika gennem lytteren med Windows-godkendelse:

    ```
    sqlmd -S <listenerName> -E
    ```

    Hvis lytteren bruger en port end standard port (1433), angive porten i forbindelsesstrengen. For eksempel opretter følgende kommando for sqlcmd forbindelse til en lytter på port 1435: 
    
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD forbindelse opretter automatisk forbindelse til den primære replika vært for forekomsten af SQL Server. 

>[AZURE.NOTE] Sørg for, at du angiver porten er åben på firewallen på begge SQL-servere. Begge servere kræver en indgående regel for TCP-port, du bruger. Se [tilføje eller redigere firewallregel](http://technet.microsoft.com/library/cc753558.aspx) kan finde flere oplysninger. 

## <a name="guidelines-and-limitations"></a>Retningslinjer og begrænsninger

Bemærk følgende retningslinjer på tilgængelighed gruppe lytteren i Azure ved hjælp af interne justering af belastning:

- Justering af belastning, du kun åbne lytteren fra i det samme virtuelle netværk med en intern.

## <a name="for-more-information"></a>Du kan finde flere oplysninger

Finde flere oplysninger i [konfigurere altid på tilgængelighed gruppere i Azure VM manuelt](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### <a name="powershell-cmdlets"></a>PowerShell-cmdlet'er

Brug følgende PowerShell-cmdlet'er til at oprette en intern justering af belastning til Azure virtuelle computere.

- `New-AzureRmLoadBalancer`opretter en justering af belastning. Du kan få flere oplysninger i [Ny AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) . 

- `New-AzureRMLoadBalancerFrontendIpConfig`opretter en front end IP-konfiguration for belastningsjustering. Du kan få flere oplysninger i [Ny AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) .

- `New-AzureRmLoadBalancerRuleConfig`opretter en regel konfiguration for belastningsjustering. Du kan få flere oplysninger i [Ny AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) . 

- `New-AzureRMLoadBalancerBackendAddressPoolConfig`opretter en back end-adresse puljen konfiguration for belastningsjustering. Du kan få flere oplysninger i [Ny AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) . 

- `New-AzureRmLoadBalancerProbeConfig`opretter en efterprøvning af af konfiguration for belastningsjustering. Du kan få flere oplysninger i [Ny AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) .

Hvis du vil fjerne en belastningsjustering fra en Azure ressourcegruppe skal du bruge `Remove-AzureRmLoadBalancer`. Du kan finde flere oplysninger [Fjern AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).

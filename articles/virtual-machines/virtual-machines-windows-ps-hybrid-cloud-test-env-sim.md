<properties 
    pageTitle="Simuleret hybrid skyen testmiljø | Microsoft Azure" 
    description="Oprette et simuleret skyen hybridmiljø for IT pro eller udvikling afprøvningen, ved hjælp af to Azure virtuelle netværk og en VNet-VNet forbindelse." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>Konfigurere et simuleret skyen hybridmiljø til test

I denne artikel vejleder dig gennem oprettelsen af et simuleret cloud-hybridmiljø med Microsoft Azure ved hjælp af to Azure virtuelle netværk. Her er den resulterende konfiguration.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Dette simulerer et hybrid skyen produktionsmiljø og består af:

- En simuleret og forenklet lokale netværk hostet i en Azure virtuelt netværk (det virtuelle TestLab-netværk).
- Et simuleret tværs lokale virtuelt netværk Azure (TestVNET) som vært.
- En VNet-VNet forbindelse mellem de to virtuelle netværk.
- En sekundær domænenavn fra domænecontrolleren i det virtuelle TestVNET netværk.

Dette giver en datotype og almindelige Start peg fra, kan du:

- Udvikle og teste programmer i et simuleret hybridmiljø til skyen.
- Oprette konfigurationer af test af computere, nogle inden for det virtuelle TestLab-netværk og nogle inden for det TestVNET virtuelle netværk og til at simulere hybrid skybaseret IT arbejdsmængder.

Der er fire store faser til konfiguration af hybrid skyen testmiljø:

1.  Konfigurere TestLab virtuelle netværket.
2.  Oprette det virtuelle netværk i tværs det lokale miljø.
3.  Oprette VNet-VNet VPN-forbindelse.
4.  Konfigurere DC2. 

Denne konfiguration kræver et Azure-abonnement. Hvis du har et abonnement til MSDN eller Visual Studio, skal du se [månedlige Azure kredit for Visual Studio abonnenter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Virtuelle maskiner og virtuelt netværk gateways i Azure betale en løbende omkostninger, når de kører. Denne omkostning er faktureret mod din MSDN eller som er betalt abonnement. En VPN-Azure gateway er implementeret som et sæt af to Azure virtuelle maskiner. Oprette testmiljøet for at minimere omkostningerne, og Udfør dine nødvendige test og demonstration så hurtigt som muligt.

## <a name="phase-1-configure-the-testlab-virtual-network"></a>Fase 1: Konfigurere TestLab virtuelle netværket

Bruge instruktionerne i emnet [Base konfiguration testmiljø](https://technet.microsoft.com/library/mt771177.aspx) til at konfigurere DC1, APP1 og KLIENT1 computere i det Azure virtuelle netværk med navnet TestLab. 

Dernæst skal starte en Azure PowerShell-prompt.

> [AZURE.NOTE] Følgende kommando angiver Brug Azure PowerShell 1.0 eller nyere.

Log på din konto.

    Login-AzureRMAccount

Få abonnementets navn ved hjælp af følgende kommando.

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Angiv dit Azure-abonnement. Du kan bruge det samme abonnement, du brugte til at opbygge din grundlæggende konfiguration i fase 1. Erstatte alt i anførselstegn, herunder den < og > tegn med det rigtige navn.

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Føj en gateway-undernet TestLab virtuelle netværk af din grundlæggende konfiguration, der skal bruges til at hoste Azure gatewayen.

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Derefter skal du anmode om en offentlig IP-adresse til at tildele til gatewayen for det virtuelle netværk TestLab.

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

Dernæst skal du oprette din gateway.

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Husk på, ny gateways kan tage 20 minutter eller mere til at oprette.

Oprette forbindelse til DC1 med CORP\User1 legitimationsoplysninger fra portalen Azure på din lokale computer. For at konfigurere CORP domæne, så computere og brugere kan bruge deres lokale domænenavn fra domænecontrolleren til godkendelse, skal du køre disse kommandoer fra en administrator niveau Windows PowerShell kommandoprompt på DC1.

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Dette er den aktuelle konfiguration.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)
 
## <a name="phase-2-create-the-testvnet-virtual-network"></a>Fase 2: Oprette det virtuelle TestVNET-netværk

Først skal du oprette det virtuelle TestVNET-netværk og beskytte den med en netværk sikkerhedsgruppe.

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

Derefter skal du anmode om en offentlig IP-adresse til allokeres til gatewayen for det virtuelle netværk, TestVNET og oprette din gateway.

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Dette er den aktuelle konfiguration.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)
 
##<a name="phase-3-create-the-vnet-to-vnet-connection"></a>Fase 3: Oprette VNet-VNet-forbindelse

Først skal hente en tilfældigt, kryptografisk stærk, 32-tegns delt nøgle fra administratoren af netværk eller sikkerhed. Alternativt kan du bruge oplysningerne på [Opret en tilfældig streng i en IPsec forhåndsdelt nøgle](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) til at hente en delt nøgle.

Brug derefter kommandoerne til at oprette VNet-VNet VPN-forbindelse, hvilket kan tage noget tid at fuldføre.

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Efter et par minutter, der skal oprettes forbindelse.

Dette er den aktuelle konfiguration.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)
 
## <a name="phase-4-configure-dc2"></a>Fase 4: Konfigurere DC2

Opret først et virtuelt for DC2. Køre disse kommandoer i kommandoprompten Azure PowerShell på din lokale computer.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
    $vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Opret derefter forbindelse til den nye DC2 virtuelle maskine fra Azure-portalen.

Konfigurer en Windows Firewall regel for at tillade trafik til grundlæggende forbindelser test. Kør følgende kommandoer fra en administrator niveau Windows PowerShell kommandoprompten på DC2.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

Kommandoen ping bør medføre fire vellykket svar fra IP-adresse 10.0.0.4. Dette er en test af trafik på tværs af VNet-VNet forbindelsen.

Dernæst skal du tilføje ekstra data disken på DC2 som en ny enhed med bogstavet F:.

1.  Klik på **filer og lagerplads tjenester**i venstre rude af Server Manager, og klik derefter på **diske**.
2.  Klik på **disk 2** (med **Partition** indstillet til **Ukendt**) i indholdsruden i gruppen **diske** .
3.  Klik på **opgaver**, og klik derefter på **Ny lydstyrken**.
4.  I afsnittet før du begynder side i guiden Ny lydstyrken, klik på **Næste**.
5.  Klik på **Disk 2**, på siden Vælg server og disk, og klik derefter på **Næste**. Når du bliver bedt om det, skal du klikke på **OK**.
6.  Klik på **Næste**på siden Angiv størrelsen på siden lydstyrken.
7.  Klik på **Næste**på siden Tildel til en side, drev bogstav eller en mappe.
8.  Klik på **Næste**på siden Vælg arkiv system indstillinger.
9.  Klik på **Opret**på siden Bekræft valg.
10. Klik på **Luk**, når du er færdig.

Dernæst skal konfigurere DC2 som en replikeringsdomænecontroller for domænet, corp.contoso.com. Køre disse kommandoer fra Windows PowerShell-kommandoprompten på DC2.

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Bemærk, at du bliver bedt om at levere både CORP\User1 adgangskoden og en adgangskode, Directory Services gendanne tilstand (DSRM), og at genstarte DC2.

Nu, hvor det virtuelle TestVNET netværk har sin egen DNS-server (DC2), skal du konfigurere det TestVNET virtuelle netværk for at bruge denne DNS-server.

1.  Klik på ikonet virtuelle netværk i den venstre rude i portalen Azure, og klik derefter på **TestVNET**.
2.  Klik på **DNS-servere**på fanen **Indstillinger** .
3.  Skriv **192.168.0.4** for at erstatte 10.0.0.4 i **primære DNS-server**.
4.  Klik på **Gem**.

Dette er den aktuelle konfiguration. 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)
 
Din simuleret cloud-hybridmiljø er nu klar til at afprøve.

## <a name="next-step"></a>Næste trin

- Konfigurere en [webbaseret, linje med forretningsprogram](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) i dette miljø.

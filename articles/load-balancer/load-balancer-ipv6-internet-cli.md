<properties
    pageTitle="Oprette en via justering af belastning, IPv6 i Azure ressourcestyring ved hjælp af Azure CLI internettet | Microsoft Azure"
    description="Lær, hvordan du opretter en via justering af belastning, IPv6 i Azure ressourcestyring ved hjælp af Azure CLI internettet"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="IPv6-azure justering af belastning dobbelt stak, offentlige IP-adresse, oprindelige ipv6, mobile, og iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="create-an-internet-facing-load-balancer-with-ipv6-in-azure-resource-manager-using-the-azure-cli"></a>Oprette en via justering af belastning, IPv6 i Azure ressourcestyring ved hjælp af Azure CLI internettet

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [Azure CLI](./load-balancer-ipv6-internet-cli.md)
- [Skabelon](./load-balancer-ipv6-internet-template.md)

En Azure justering af belastning er belastningsjustering Layer-4 (TCP, UDP). Justering af belastning giver høj tilgængelighed ved at distribuere indgående trafik mellem forekomster af sund tjenesten i skytjenester eller virtuelle maskiner i et Indlæs belastningsjusteringstjenesten sæt. Azure justering af belastning kan også give disse tjenester på flere porte, flere IP-adresser eller begge dele.

## <a name="example-deployment-scenario"></a>Eksempel på installation scenarie

I følgende diagram vises belastningsjustering løsning der installeres ved hjælp af skabelonen eksempel, der er beskrevet i denne artikel.

![Indlæse belastningsjusteringstjenesten scenarie](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

I dette scenarie skal du oprette følgende Azure ressourcer:

- to virtuelle maskiner (VM'er)
- et virtuelt netværksgrænsefladen til hver VM med både IPv4 og IPv6-adresser, der er tildelt
- en forbindelse til internettet justering af belastning med en IPv4 og IPv6 offentlige IP-adressen
- en tilgængelighed, der er angivet til, der indeholder to FOS
- to indlæse justering af regler for at tilknytte de offentlige VIPs til private slutpunkterne

## <a name="deploying-the-solution-using-the-azure-cli"></a>Implementering af løsningen ved hjælp af Azure CLI

Følgende trin viser, hvordan du opretter en via justering af belastning ved hjælp af Azure ressourcestyring med CLI internettet. Med Azure Resource Manager hver ressource er oprettet og konfigureret enkeltvis, og angiv derefter sammen for at oprette en ressource.

Hvis du vil installere belastningsjustering, skal du oprette og konfigurere følgende objekter:

- IP-konfiguration af front end - indeholder offentlige IP-adresser for indgående netværkstrafik.
- Adresse back end-puljen - indeholder netværksgrænseflader (NIC) for de virtuelle maskiner til at modtage netværkstrafik fra justering af belastning.
- Justering af belastning regler - indeholder regler tilknytte en offentlig port på justering af belastning til port i adressegruppen back end.
- Indgående regler for NAT - indeholder regler tilknytte en offentlig port på justering af belastning til en port til en bestemt virtuel maskine i puljen back end-adresse.
- Sonder - indeholder sundhed sonder bruges til at kontrollere tilgængeligheden af forekomster af virtuelle maskiner i puljen back end-adresse.

Du kan finde yderligere oplysninger finder [Azure ressourcestyring support til justering af belastning](load-balancer-arm.md).

## <a name="set-up-your-cli-environment-to-use-azure-resource-manager"></a>Konfigurere dit CLI miljø til at bruge Azure ressourcestyring

I dette eksempel skal kører vi CLI værktøjerne i et PowerShell-kommandovindue. Vi bruger ikke Azure PowerShell-cmdletter, men vi bruge Powershell's scripting-funktioner til at forbedre læsbarheden og genbrug.

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../../articles/xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.

2. Kør kommandoen **azure config tilstand** for at skifte til Ressourcestyring tilstand.

        azure config mode arm

    Forventet afgang:

        info:    New mode is arm

3. Log på Azure og få en liste over abonnementer.

        azure login

    Angiv dine Azure legitimationsoplysninger, når du bliver bedt om.

        azure account list

    Vælg det abonnement, du vil bruge. Noter abonnementets Id til det næste trin.

4. Konfigurere PowerShell variabler til brug sammen med CLI-kommandoer.

        ```
        $subscriptionid = "########-####-####-####-############"  # enter subscription id
        $location = "southcentralus"
        $rgName = "pscontosorg1southctrlus09152016"
        $vnetName = "contosoIPv4Vnet"
        $vnetPrefix = "10.0.0.0/16"
        $subnet1Name = "clicontosoIPv4Subnet1"
        $subnet1Prefix = "10.0.0.0/24"
        $subnet2Name = "clicontosoIPv4Subnet2"
        $subnet2Prefix = "10.0.1.0/24"
        $dnsLabel = "contoso09152016"
        $lbName = "myIPv4IPv6Lb"
        ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Oprette en ressourcegruppe, belastningsjustering, et virtuelt netværk og undernet

1. Oprette en ressourcegruppe

        azure group create $rgName $location

2. Oprette en justering af belastning

        $lb = azure network lb create --resource-group $rgname --location $location --name $lbName

3. Oprette et virtuelt netværk (VNet).

        $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix

    Opret to undernet i denne VNet.

        $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
        $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Oprette offentlige IP-adresser til front end-puljen

1. Konfigurere PowerShell variabler

        $publicIpv4Name = "myIPv4Vip"
        $publicIpv6Name = "myIPv6Vip"

2. Oprette en offentlig IP-adresse IP-front end-puljen.

        $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
        $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel

    >[AZURE.IMPORTANT]Justering af belastning bruger domæne navnet på den offentlige IP-som det fulde Domænenavn. Dette en ændring fra klassisk installation, som bruger skytjenesten navn som justering af belastning fulde Domænenavn.
    >I dette eksempel er fulde *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Oprette front end- og back end-grupper

I dette eksempel opretter front end-puljen, der modtager indgående netværkstrafikken på justering af belastning IP- og back end-IP-puljen hvor front end-puljen sender netværkstrafik Indlæs afstemmes.

1. Konfigurere PowerShell variabler

        $frontendV4Name = "FrontendVipIPv4"
        $frontendV6Name = "FrontendVipIPv6"
        $backendAddressPoolV4Name = "BackendPoolIPv4"
        $backendAddressPoolV6Name = "BackendPoolIPv6"

2. Oprette en front-end-IP-gruppe at tilknytte den offentlige IP-adresse, der er oprettet i ovenstående trin og belastning.

        $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
        $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
        $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
        $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName

## <a name="create-the-probe-nat-rules-and-lb-rules"></a>Oprette efterprøvning af af, NAT-regler og regler for kg

I dette eksempel oprettes følgende elementer:

- efterprøvning af af en regel for at kontrollere, om forbindelse til TCP-port 80
- en NAT regel til at oversætte al indgående trafik på port 3389 til port 3389 for RDP<sup>1</sup>
- en NAT regel til at oversætte al indgående trafik på port 3391 til port 3389 for RDP<sup>1</sup>
- en Indlæs belastningsjusteringstjenesten regel saldo al indgående trafik på port 80 til port 80 på adresserne i back end-puljen.

<sup>1</sup> NAT-regler er knyttet til en bestemt virtuelt forekomst bag justering af belastning. Netværkstrafikken ankommer på port 3389 sendes til bestemte virtuelt og port, der er knyttet til NAT reglen. Du skal angive et protocol (UDP eller TCP) for en NAT-regel. Begge protokoller kan ikke tildeles til den samme port.

1. Konfigurere PowerShell variabler

        $probeV4V6Name = "ProbeForIPv4AndIPv6"
        $natRule1V4Name = "NatRule-For-Rdp-VM1"
        $natRule2V4Name = "NatRule-For-Rdp-VM2"
        $lbRule1V4Name = "LBRuleForIPv4-Port80"
        $lbRule1V6Name = "LBRuleForIPv6-Port80"

2. Oprette efterprøvning af af

    I følgende eksempel oprettes en TCP-test, der kontrollerer for forbindelse til back end-TCP-port 80 hver 15 sekunder. Det bliver markeret back end-ressourcen tilgængelig efter to på hinanden følgende mislykkede forsøg.

        $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName

3. Oprette indgående NAT-regler, der tillader RDP forbindelser til back end-ressourcer

        $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
        $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName

4. Oprette en belastningsjustering regler, der sender trafik til forskellige back end-porte, alt efter hvilken front-end modtaget anmodningen

        $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
        $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName

5. Kontrollere dine indstillinger

        azure network lb show --resource-group $rgName --name $lbName

    Forventet afgang:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show


## <a name="create-nics"></a>Oprette netværkskort

Oprette netværkskort og knytte dem til NAT regler, regler for indlæsning af belastningsjusteringstjenesten og sonder.

1. Konfigurere PowerShell variabler

        $nic1Name = "myIPv4IPv6Nic1"
        $nic2Name = "myIPv4IPv6Nic2"
        $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
        $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
        $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
        $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
        $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
        $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"

2. Oprette en NIC for hver back end- og tilføje en IPv6-konfiguration.

        $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

        $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Opret back end-VM ressourcer, og Vedhæft hver NIC

For at oprette FOS, skal du have en lagerplads konto. Belastning skal FOS være medlem af et sæt tilgængelighed. Du kan finde flere oplysninger om oprettelse af FOS se [oprette en Azure VM ved hjælp af PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

1. Konfigurere PowerShell variabler

        $storageAccountName = "ps08092016v6sa0"
        $availabilitySetName = "myIPv4IPv6AvailabilitySet"
        $vm1Name = "myIPv4IPv6VM1"
        $vm2Name = "myIPv4IPv6VM2"
        $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
        $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
        $disk1Name = "WindowsVMosDisk1"
        $disk2Name = "WindowsVMosDisk2"
        $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
        $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
        $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
        $vmUserName = "vmUser"
        $mySecurePassword = "PlainTextPassword*1"

    >[AZURE.WARNING] I dette eksempel bruges brugernavnet og adgangskoden for FOS i klar tekst. Relevante omtanke tages ved hjælp af legitimationsoplysninger i Ryd. Du kan finde en mere sikker metode til håndtering af legitimationsoplysninger i PowerShell cmdlet [Get-legitimationsoplysninger](https://technet.microsoft.com/library/hh849815.aspx) .

2. Oprette sættet lagerplads konto og tilgængelighed

    Du kan bruge en eksisterende konto lagerplads, når du opretter FOS. Følgende kommando opretter en ny konto lagerplads.

        $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"

    Opret derefter sættet tilgængelighed.

        $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location

3. Oprette virtuelle maskiner med de tilknyttede netværkskort

        $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

        $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn  --storage-account-name $storageAccountName --disable-bginfo-extension

## <a name="next-steps"></a>Næste trin

[Introduktion til konfiguration af en intern justering af belastning](load-balancer-get-started-ilb-arm-cli.md)

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)

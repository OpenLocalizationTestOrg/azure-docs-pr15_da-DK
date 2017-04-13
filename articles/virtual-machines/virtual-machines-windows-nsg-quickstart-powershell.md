<properties
   pageTitle="Åbn porte til en VM ved hjælp af PowerShell | Microsoft Azure"
   description="Lær, hvordan du åbner en port / oprette et slutpunkt til din Windows-VM ved hjælp af Azure ressource manager installation tilstand og Azure PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Åbne porte og slutpunkter til en VM i Azure ved hjælp af PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Hurtig kommandoer
Hvis du vil oprette en sikkerhedsgruppe netværk og ACL regler, du har brug for [den seneste version af Azure PowerShell installeret](../powershell-install-configure.md). Du kan også [udføre disse trin, ved hjælp af portalen Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Log på din Azure-konto:

```powershell
Login-AzureRmAccount
```

Erstat eksempel parameternavne med dine egne værdier i eksemplerne nedenfor. Eksempel parameternavne inkluderet `myResourceGroup`, `myNetworkSecurityGroup`, og `myVnet`.

Oprette en regel. I følgende eksempel oprettes en regel med navnet `myNetworkSecurityGroupRule` tillade TCP-trafik på port 80:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Dernæst skal oprette dit netværk sikkerhedsgruppe og tildele den HTTP-regel, du lige har oprettet på følgende måde. I følgende eksempel oprettes en netværk sikkerhedsgruppe med navnet `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

Nu Lad os tildele sikkerhedsgruppe dit netværk til et undernet. I følgende eksempel tildeles en eksisterende virtuelt netværk med navnet `myVnet` til variablen `$vnet`:

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Knytte sikkerhedsgruppe dit netværk til dit undernet. I følgende eksempel knyttes undernet med navnet `mySubnet` med dit netværk sikkerhedsgruppe:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Til sidst skal opdatere din virtuelt netværk for at ændringerne kan træde i kraft:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Flere oplysninger om netværk sikkerhedsgrupper
Hurtig kommandoerne her kan du kommer i gang med trafik der flyder til din VM. Netværk sikkerhedsgrupper giver mange fantastiske funktioner og granularitet for at styre adgang til dine ressourcer. Du kan læse mere om [oprettelse af en sikkerhedsgruppe netværk og ACL regler her](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Du kan definere netværk sikkerhedsgrupper og ACL regler som en del af Azure ressourcestyring skabeloner. Læs mere om [oprettelse af netværk sikkerhedsgrupper med skabeloner](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Hvis du skal bruge port-videresendelse for at tilknytte en entydig eksterne port til en intern port på din VM, bruge regler for oversættelse (NAT) og belastningsjustering. Du vil muligvis få vist TCP-port 8080 eksternt og har trafik til TCP port 80 på en VM. Du kan få mere at vide om at [oprette en forbindelse til internettet justering af belastning](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Næste trin
Du har oprettet en simpel regel for at tillade HTTP-trafik i dette eksempel. Du kan finde oplysninger om at oprette mere detaljerede miljøer i følgende artikler:

- [Oversigt over Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md)
- [Hvad er et netværk sikkerhed gruppe (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Azure ressourcestyring oversigt til Indlæs balancere](../load-balancer/load-balancer-arm.md)
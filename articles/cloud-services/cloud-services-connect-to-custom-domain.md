<properties
  pageTitle="Forbinde en skybaseret tjeneste til et brugerdefineret domænenavn fra domænecontrolleren | Microsoft Azure"
  description="Lær, hvordan du forbinde dine roller for web/arbejder med et brugerdefineret AD-domæne ved hjælp af PowerShell og AD-domæne lokalnummer"
  services="cloud-services"
  documentationCenter=""
  authors="Thraka"
  manager="timlt"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="adegeo"/>

# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Oprette forbindelse Azure Cloud Services-roller til en brugerdefineret AD domænenavn fra domænecontrolleren Azure som vært

Vi vil først konfigurere et virtuelt netværk (VNet) i Azure. Vi der tilføjes en Active Directory-domænecontrolleren (hostet på en Azure Virtual Machine) i VNet. Vi vil derefter føje eksisterende roller i skyen-tjeneste til de allerede er oprettet VNet og efterfølgende forbinde dem til domænecontrolleren.

Før vi går i gang, par ting at huske på:

1.  Dette selvstudium bruger PowerShell, så sørg for, at du har Azure PowerShell installeret og klar til brug. For at få hjælp til konfiguration af Azure PowerShell, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

2.  Din AD domænecontrolleren og Web/arbejder rolle forekomster skal være i VNet.

Følg denne trinvise vejledning, og hvis du støder på problemer, Skriv en kommentar nedenfor. En person vil vende tilbage til dig (Ja, vi læser kommentarer).

3. Det netværk, der refereres til af skyen service <mark>skal være</mark> et **Klassisk virtuelt netværk**.

## <a name="create-a-virtual-network"></a>Oprette et virtuelt netværk

Du kan oprette et virtuelt netværk i Azure ved hjælp af Azure klassisk portal eller PowerShell. Dette selvstudium skal bruge vi PowerShell. For at oprette et virtuelt netværk ved hjælp af portalen Azure klassisk skal du se [Oprette virtuelle netværk](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Oprette en virtuel maskine

Når du har afsluttet konfigurationen af det virtuelle netværk, skal du oprette en AD domænenavn fra domænecontrolleren. Dette selvstudium skal vil vi konfigurere en AD domænenavn fra domænecontrolleren på en Azure Virtual Machine.

For at gøre dette, du Opret en virtuel maskine via PowerShell ved hjælp af kommandoerne nedenfor:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Hæve din virtuelle maskine til et domænenavn fra domænecontrolleren
For at konfigurere den virtuelle maskine som en AD domænenavn fra domænecontrolleren, skal du logge på til VM og Konfigurer den til.

For at logge på til VM, kan du få RDP-fil via PowerShell ved at bruge kommandoerne nedenfor.

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Når du er logget på VM, kan du konfigurere din virtuelle maskine som en AD domænenavn fra domænecontrolleren ved at følge den trinvise vejledning om, [hvordan du konfigurerer din kunde AD domænenavn fra domænecontrolleren](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Føje din skytjeneste til det virtuelle netværk

Derefter skal du føje implementeringen af denne skyen service til den VNet, du lige har oprettet. Ændre din skyen service cscfg for at gøre dette, ved at tilføje de relevante afsnit i dit cscfg ved hjælp af Visual Studio eller editor efter eget valg.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Næste opbygge din cloud services-projekt og installerer det til Azure. Hvis du vil have hjælp til implementeringen cloud services pakken i Azure, se, [hvordan du oprette og Implementer en skybaseret tjeneste](cloud-services-how-to-create-deploy.md#deploy)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Forbinde dine web/arbejder roller til domænet

Når projektet skyen tjenesten er installeret på Azure, kan du oprette forbindelse din rolle forekomster til dit eget AD-domæne, der bruger filtypenavnet AD-domæne. For at tilføje filtypenavnet AD-domæne til din eksisterende cloud services installation og deltage i dit eget domæne, skal du udføre følgende kommandoer i PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

Og det var det.

Du cloud services nu være medlem af dit brugerdefinerede domænenavn fra domænecontrolleren. Hvis du vil have mere at vide om de forskellige indstillinger til at konfigurere AD domæne filtypenavn, kan du bruge PowerShell hjælp, som vist nedenfor.

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```

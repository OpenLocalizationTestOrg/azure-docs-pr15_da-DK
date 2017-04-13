<properties 
   pageTitle="Sådan angives en statisk interne private IP"
   description="Forstå statisk interne IP'er (fald), og hvordan du administrerer dem"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Sådan angives en statisk interne privat IP-adresse ved hjælp af PowerShell (klassisk)
I de fleste tilfælde behøver du ikke angiver en statisk interne IP-adresse til din virtuelle maskine. FOS i et virtuelt netværk modtager automatisk en intern IP-adresse fra et område, du angiver. Men i visse tilfælde, der angiver en statisk IP-adresse til en bestemt VM giver mening. For eksempel, hvis din VM er skal køre DNS eller bliver et domænenavn fra domænecontrolleren. En statisk interne IP-adresse forbliver med VM endda gennem en stop/deprovision tilstand. 

> [AZURE.IMPORTANT] Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: [ressourcestyring og klassisk](../resource-manager-deployment-model.md). Denne artikel omhandler ved hjælp af den klassiske implementeringsmodel. Microsoft anbefaler, at de fleste nye installationer bruger [ressourcestyring implementeringsmodel](virtual-networks-static-private-ip-arm-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Sådan kontrolleres, hvis en bestemt IP-adresse er tilgængelig
Køre følgende PowerShell-kommando for at bekræfte, hvis IP-adresse *10.0.0.7* findes i en vnet med navnet *TestVnet*, og Bekræft værdien for *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

>[AZURE.NOTE] Hvis du vil teste kommandoen over i et sikkert miljø Følg vejledningen i [oprette et virtuelt netværk](virtual-networks-create-vnet-classic-portal.md) for at oprette en vnet med navnet *TestVnet* og sikre, at det bruger *10.0.0.0/8* -adresseområde.

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Sådan angives en statiske interne IP-Adresser, når du opretter en VM
Nedenstående PowerShell-script opretter en ny skybaseret tjeneste med navnet *TestService*, derefter henter et billede fra Azure, og derefter opretter en VM med navnet *TestVM* i nye skytjenesten ved hjælp af det hentede billede, angiver VM skal være i et undernet med navnet *undernet 1*og indstiller *10.0.0.7* som en statiske interne IP-Adresser for VM:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzureSubnet –SubnetNames Subnet-1 `
  	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
  	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Hvordan du kan hente statisk interne IP-oplysninger til en VM
Kør følgende PowerShell-kommando for at få vist statisk interne IP-oplysningerne for den VM, der er oprettet med scriptet ovenfor, og overholde værdierne for *IP-adresse*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Sådan fjernes en statiske interne IP-Adresser fra en VM
For at fjerne den statiske interne IP-Adresser føjet til VM i scriptet ovenfor, skal du køre følgende PowerShell-kommando:
    
    Get-AzureVM -ServiceName TestService -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Sådan føjes en statisk interne IP-adresse til en eksisterende VM
Hvis du vil tilføje en intern statisk oprettet IP-adresse til VM ved hjælp af script over runt følgende kommando:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
  	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
  	| Update-AzureVM

## <a name="next-steps"></a>Næste trin

[Reserverede IP](virtual-networks-reserved-public-ip.md)

[Forekomst niveau offentlige IP-adresse (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Reserverede IP-REST API'er](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<properties 
   pageTitle="Sådan angives en statiske privat IP-Adresser i tilstanden Klassisk ved hjælp af PowerShell | Microsoft Azure"
   description="Forstå statisk privat IP'er (fald), og hvordan du administrerer dem i tilstanden Klassisk og PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-powershell"></a>Sådan angives en statisk privat IP-adresse (klassisk) i PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel. Du kan også [administrere en statisk privat IP-adresse i implementeringsmodel ressourcestyring](virtual-networks-static-private-ip-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Nedenstående eksempel PowerShell kommandoerne forventer et enkelt miljø, som allerede har oprettet. Hvis du vil køre kommandoer, som de vises i dette dokument, skal først opbygge testmiljø, der er beskrevet i [oprette en VNet](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Sådan kontrolleres, hvis en bestemt IP-adresse er tilgængelig
Køre følgende PowerShell-kommando for at bekræfte, hvis IP-adresse *192.168.1.101* findes i en VNet med navnet *TestVNet*, og Bekræft værdien for *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Forventet afgang:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Sådan angives en statisk privat IP-adresse, når du opretter en VM
PowerShell-script nedenfor opretter en ny skybaseret tjeneste med navnet *TestService*, og klik derefter henter et billede fra Azure, opretter en VM med navnet *DNS01* i nye skytjenesten ved hjælp af det hentede billede, angiver VM skal være i et navngivet *front end*-undernet og indstiller *192.168.1.7* som en statisk privat IP-adresse for VM:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

Forventet afgang:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hvordan du kan hente statiske privat IP-adresseoplysninger for en VM
Kør følgende PowerShell-kommando for at få vist statisk privat IP-adresseoplysningerne for det VM, der er oprettet med scriptet ovenfor, og overholde værdierne for *IP-adresse*:

    Get-AzureVM -Name DNS01 -ServiceName TestService

Forventet afgang:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
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

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Sådan fjernes en statisk privat IP-adresse fra en VM
Fjerne den statiske private IP-adresse tilføjet til VM i scriptet ovenfor, køre følgende PowerShell-kommando:
    
    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Forventet afgang:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Sådan føjes en statisk privat IP-adresse til en eksisterende VM
Tilføje en statisk privat IP-adresse til VM oprettet ved hjælp af script over runt følgende kommando:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Forventet afgang:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [reserveret offentlige IP-](virtual-networks-reserved-public-ip.md) adresser.
- Få mere at vide om [forekomst niveau offentlige IP-adresse (ILPIP)](virtual-networks-instance-level-public-ip.md) adresser.
- Se [reserverede IP-REST API'er](https://msdn.microsoft.com/library/azure/dn722420.aspx).

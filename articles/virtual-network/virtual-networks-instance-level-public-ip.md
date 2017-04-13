<properties 
   pageTitle="Forekomst niveau offentlige IP-adresse (ILPIP) | Microsoft Azure"
   description="Forstå ILPIP (PIP), og hvordan du administrerer dem"
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
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="instance-level-public-ip-overview"></a>Forekomst niveau offentlige IP-oversigt
En forekomst niveau offentlige IP-adresse (ILPIP) er en offentlig IP-adresse, som du kan tildele direkte til din VM eller rolle forekomst i stedet for i skyen Service, som din VM eller rolle forekomst er placeret. Dette træder ikke i stedet for VIP (virtuelle IP), der er tildelt til din skybaseret tjeneste. Hellere, det er en ekstra IP-adresse, du kan bruge til at oprette forbindelse direkte til din VM eller rolle forekomst.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](virtual-network-ip-addresses-overview-arm.md). 

Kontrollér, at du forstå, hvordan [IP-adresser](virtual-network-ip-addresses-overview-classic.md) fungerer i Azure.

>[AZURE.NOTE] Tidligere, blev en ILPIP kaldes en PIP, som står for offentlige IP-adresse. 

![Forskellen mellem ILPIP og VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Som vist i figur 1, skytjenesten er adgang til dem via en VIP, mens de enkelte FOS er normalt adgang til dem via VIP:&lt;portnummer&gt;. Ved at tildele en ILPIP til en bestemt VM, VM kan åbnes direkte ved hjælp af IP-adressen.

Når du opretter en skybaseret tjeneste i Azure, der tilsvarende A DNS-poster oprettes automatisk for at give adgang til tjenesten via et fuldt domænenavn (fulde Domænenavn) i stedet for ved hjælp af den faktiske VIP. Den samme fremgangsmåde sker for ILPIP, tillade adgang til den VM eller rolle forekomst af fulde Domænenavn i stedet for ILPIP. Eksempelvis hvis du opretter en skybaseret tjeneste med navnet *contosoadservice*, og du konfigurerer en web rolle med navnet *contosoweb* med to forekomster, Azure vil registrere følgende A-poster for forekomster:

- contosoweb\_IN_0.contosoadservice.cloudapp.net
- contosoweb\_IN_1.contosoadservice.cloudapp.net 

>[AZURE.NOTE] Du kan tildele kun én ILPIP for hver VM eller rolle forekomst. Du kan bruge op til 5 ILPIP'S per abonnement. ILPIP understøttes ikke på nuværende tidspunkt for multi-NIC FOS.

## <a name="why-should-i-request-an-ilpip"></a>Hvorfor bør jeg anmode om en ILPIP?
Hvis du vil kunne oprette forbindelse til din VM eller rolle forekomst af en IP-adresse, der er tildelt direkte til den, i stedet for at bruge skyen servicere VIP:&lt;portnummer&gt;, anmode om en ILPIP for din VM eller din rolle forekomst.
- **Passiv FTP** - ved at få en ILPIP på din VM, du kan modtage trafik på næsten enhver port, behøver du ikke at åbne et slutpunkt til at modtage trafik. Dette gør det muligt for scenarier som passiv FTP, hvor portene, der er valgt dynamisk.
- **Udgående IP** - udgående trafik kommer fra VM går med ILPIP som kilde og identificerer entydigt VM til eksterne enheder.

## <a name="how-to-request-an-ilpip-during-vm-creation"></a>Hvordan du anmoder om en ILPIP under oprettelse af VM
PowerShell-script nedenfor opretter en ny skybaseret tjeneste med navnet *FTPService*, og klik derefter henter et billede fra Azure, og opretter en VM med navnet *FTPInstance* ved hjælp af det hentede billede, angiver VM at bruge en ILPIP og føjer VM til den nye tjeneste:

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Sådan Hent oplysninger om ILPIP for en VM
Kør følgende PowerShell-kommando for at få vist ILPIP oplysningerne for den VM, der er oprettet med scriptet ovenfor, og overholde værdierne for *PublicIPAddress* og *PublicIPName*:

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>Sådan fjernes en ILPIP fra en VM
For at fjerne den ILPIP, der er føjet til VM i scriptet ovenfor, skal du køre følgende PowerShell-kommando:
    
    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Remove-AzurePublicIP `
  	| Update-AzureVM

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Sådan føjes en ILPIP til en eksisterende VM
Hvis du vil tilføje en ILPIP til VM oprettet ved hjælp af scriptet ovenfor, skal du køre følgende kommando:

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Set-AzurePublicIP -PublicIPName ftpip2 `
  	| Update-AzureVM

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>Hvordan du kan knytte en ILPIP til en VM ved hjælp af en tjeneste konfigurationsfil
Du kan også knytte en ILPIP til en VM ved hjælp af en tjeneste-konfigurationsfil (CSCFG). Eksempel xml nedenfor viser, hvordan du konfigurerer en skybaseret tjeneste for at bruge en ILPIP navnet *MyPublicIP* for en forekomst af rolle: 
    
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Næste trin

- Forstå, hvordan [IP håndtere](virtual-network-ip-addresses-overview-classic.md) fungerer i modellen, klassisk installation.

- Få mere at vide om [reserveret IP'er](virtual-networks-reserved-public-ip.md).
 
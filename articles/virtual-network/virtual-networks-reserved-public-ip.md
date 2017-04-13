<properties
   pageTitle="Reserveret IP | Microsoft Azure"
   description="Forstå reserveret IP-adresser og hvordan du administrerer dem"
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

# <a name="reserved-ip-overview"></a>Reserverede IP-oversigt
IP-adresser i Azure falder inden for to kategorier: dynamisk og reserverede. Offentlige IP-adresser, der administreres af Azure er dynamiske som standard. Det betyder, at IP-adresse bruges til en given skybaseret tjeneste (VIP) eller til at få adgang til en VM eller rolle forekomst direkte (ILPIP) fra tid til anden kan ændre, hvor ressourcerne lukning eller deallokeres.

For at forhindre IP-adresser, kan du reservere en IP-adresse. Reserverede IP'er kan kun bruges som en VIP, at sikre, at skytjenesten IP-adresse skal være den samme selv som ressourcer lukkes eller deallokeres. Desuden kan du konvertere eksisterende dynamiske IP'er bruges som en VIP til en reserveret IP-adresse.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Lær at reservere en statisk offentlige IP-adresse ved hjælp af [ressourcestyring implementeringsmodel](virtual-network-ip-addresses-overview-arm.md).

Kontrollér, at du forstå, hvordan [IP-adresser](virtual-network-ip-addresses-overview-classic.md) fungerer i Azure.

## <a name="when-do-i-need-a-reserved-ip"></a>Hvornår skal jeg bruge en reserveret IP-adresse?
- **Du vil sikre dig, at IP-adresse er reserveret i dit abonnement**. Hvis du vil reservere en IP-adresse, der ikke udgives fra dit abonnement under alle omstændigheder, skal du bruge en reserveret offentlige IP-adresse.  
- **Du vil din IP-adresse til at holde med din skybaseret tjeneste endda på tværs af holdt op med at eller deallokeres tilstand (FOS)**. Hvis du vil din tjeneste, der skal åbnes ved hjælp af en IP-adresse, der ikke ændres, selv når FOS i skytjenesten er stop eller deallokeres.
- **Du vil sikre dig, at udgående trafik fra Azure bruger en mere forudsigelige IP-adresse**. Du skal muligvis din lokale firewall, der er konfigureret til at tillade kun trafik fra bestemte IP-adresser. Ved at reservere en IP-adresse, du vil vide kildens IP-adresse og behøver ikke at opdatere din firewallregler på grund af en ændring af IP.

## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
1. Kan jeg bruge en reserveret IP-adresse for alle Azure-tjenester?  
  - Reserverede IP'er kan kun bruges til FOS og skyen service forekomst roller fremvises en VIP.
1. Hvor mange reserveret IP'er kan jeg have?  
  - Alle Azure abonnementer har tilladelse til at bruge 20 reserveret IP'er på nuværende tidspunkt. Du kan dog anmode om yderligere reserveret IP'er. På siden [abonnement og begrænsninger for tjenesten](../azure-subscription-service-limits.md) kan finde flere oplysninger.
1. Er der et gebyr for reserveret IP'er?
  - Se [Reserverede IP-adresse priser oplysninger](http://go.microsoft.com/fwlink/?LinkID=398482) til prisoplysninger.
1. Hvordan jeg reservere en IP-adresse?
  - Du kan bruge PowerShell eller [Azure Management REST-API](https://msdn.microsoft.com/library/azure/dn722420.aspx) til at reservere en IP-adresse i et bestemt område. Denne reserverede IP-adresse er knyttet til dit abonnement. Du kan reservere en IP-adresse ved hjælp af portalen administration.
1. Kan jeg bruge det med forbindelse gruppe baseret VNets?
  - Reserverede IP'er understøttes kun i internationale VNets. Det understøttes ikke for VNets, der er knyttet til forbindelsen mellem processorer grupper. Se [om internationale VNets og forbindelse grupper](virtual-networks-migrate-to-regional-vnet.md)kan finde flere oplysninger om at knytte en VNet til et område eller en forbindelse gruppe.

## <a name="how-to-manage-reserved-vips"></a>Sådan administreres reserveret VIPs

Før du kan bruge reserveret IP-adresser, skal du føje den til dit abonnement. For at oprette en reserveret IP-adresse fra gruppen af offentlige IP-adresser tilgængelige i den *Centrale USA* placering skal du køre følgende PowerShell-kommando:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

Bemærk, men du ikke kan angive hvad IP-adresse der reserveret. For at se, hvilke IP-adresser er reserveret i dit abonnement, skal du køre følgende PowerShell-kommando, og Bemærk værdierne for *ReservedIPName* og *adresse*:

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

Når en IP-adresse er reserveret, forbliver den knyttet til dit abonnement, indtil du slette den. For at slette den reserverede IP ovenstående, skal du køre følgende PowerShell-kommando:

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## <a name="how-to-reserve-the-ip-address-of-an-existing-cloud-service"></a>Sådan reserver IP-adressen på en eksisterende skytjeneste

Du kan reservere IP-adressen på en eksisterende skytjeneste ved at tilføje parameteren *- ServiceName* . For at reservere en skybaseret tjeneste *TestService* på *Centrale USA* placering IP-adresse, du køre følgende PowerShell-kommando:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## <a name="how-to-associate-a-reserved-ip-to-a-new-cloud-service"></a>Hvordan du kan knytte en reserveret IP-adresse til en ny skybaseret tjeneste
Nedenstående script opretter en ny reserveret IP-adresse, og derefter knytter den til en ny skybaseret tjeneste med navnet *TestService*.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] Når du opretter en reserveret IP-adresse til brug med en skybaseret tjeneste, skal du stadig refererer til VM ved hjælp af *VIP:&lt;portnummer >* til indgående kommunikation. Reservere en IP-adresse betyder ikke du kan oprette forbindelse til VM direkte. Den reserverede IP er tildelt til skybaseret tjeneste, hvor VM er blevet installeret på. Hvis du vil oprette forbindelse til en VM af IP direkte, har du konfigurerer en forekomst niveau offentlige IP. En forekomst niveau offentlige IP er en slags offentlige IP-adresse (kaldet en ILPIP), der er tildelt direkte til din VM. Det kan være reserveret. Du kan få flere oplysninger i [Forekomst niveau offentlige IP-adresse (ILPIP)](virtual-networks-instance-level-public-ip.md) .

## <a name="how-to-remove-a-reserved-ip-from-a-running-deployment"></a>Hvordan du fjerner en reserveret IP-adresse i en igangværende installation
For at fjerne den reserverede IP, der er føjet til den nye tjeneste, der er oprettet i ovenstående scriptet, skal du køre følgende PowerShell-kommando:

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] Fjerne en reserveret IP-adresse fra en igangværende installation fjerner ikke reservationen fra dit abonnement. Det frigør blot IP-adresse der skal bruges i en anden ressource i dit abonnement.

## <a name="how-to-associate-a-reserved-ip-to-a-running-deployment"></a>Hvordan du kan knytte en reserveret IP-adresse til en igangværende installation
Nedenstående script opretter en ny skybaseret tjeneste med navnet *TestService2* med en ny VM med navnet *TestVM2*, og derefter knytter den eksisterende reserverede IP med navnet *MyReservedIP* til skybaseret tjeneste.

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## <a name="how-to-associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Hvordan du kan knytte en reserveret IP-adresse til en skybaseret tjeneste ved hjælp af en tjeneste konfigurationsfil
Du kan også knytte en reserveret IP-adresse til en skybaseret tjeneste ved hjælp af en tjeneste-konfigurationsfil (CSCFG). Eksempel xml nedenfor viser, hvordan du konfigurerer en skybaseret tjeneste for at bruge en reserveret VIP med navnet *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Næste trin

- Forstå, hvordan [IP håndtere](virtual-network-ip-addresses-overview-classic.md) fungerer i modellen, klassisk installation.

- Få mere at vide om [reserveret private IP-adresser](virtual-networks-reserved-private-ip.md).

- Få mere at vide om [forekomst niveau offentlige IP-adresse (ILPIP) adresser](virtual-networks-instance-level-public-ip.md).

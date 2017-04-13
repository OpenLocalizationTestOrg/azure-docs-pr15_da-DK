<properties 
   pageTitle="Angive DNS-indstillinger i en tjeneste konfigurationsfil | Microsoft Azure"
   description="angive brugerdefinerede DNS-indstillinger ved hjælp af tjenesten konfigurationsfil for virtuelt netværk"
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
   ms.date="02/24/2016"
   ms.author="jdial" />

# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Angive DNS-indstillinger i en tjeneste konfigurationsfil

## <a name="dns-elements"></a>DNS-elementer

En tjeneste konfigurationsfil kan indeholde et DNS-serverne element med en liste over IPv4-adresser for DNS Domain Name System ()-servere, der anvender tjenesten. Indstillinger i tjenesten konfigurationsfil tilsidesætter indstillinger i konfigurationsfil netværk. Du kan finde yderligere oplysninger finder [Azure Service konfiguration skema (.cscfg filer)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration element**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING] Attributten **name** i elementet **DnsServer** bruges kun som en referencenavn. Den repræsenterer ikke værtsnavnet for DNS-serveren. Hver **DnsServer** attributværdien skal være entydigt i hele Microsoft Azure-abonnementet.

## <a name="see-also"></a>Se også

[Azure Service konfiguration skema (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Azure virtuelt netværk konfiguration skema](http://go.microsoft.com/fwlink/?LinkId=248093)

[Konfigurere et virtuelt netværk ved hjælp af netværk konfigurationsfiler](http://go.microsoft.com/fwlink/?LinkId=248094)

[Om virtuelle netværksindstillinger i portalen administration](http://go.microsoft.com/fwlink/?LinkId=248092)


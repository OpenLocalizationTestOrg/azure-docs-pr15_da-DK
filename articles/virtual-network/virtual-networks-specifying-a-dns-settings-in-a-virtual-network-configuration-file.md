<properties 
   pageTitle="Angive DNS-indstillinger i et virtuelt netværk konfigurationsfil | Microsoft Azure"
   description="Sådan ændres indstillinger for DNS-server i et virtuelt netværk ved hjælp af et virtuelt netværk konfigurationsfil i modellen Klassisk installation"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" /> 


# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Angive DNS-indstillinger i et virtuelt netværk konfigurationsfil

En netværk konfigurationsfil indeholder to elementer, som du kan bruge til at angive indstillinger for Domain Name System (DNS): **DNS-serverne** og **DnsServerRef**. Du kan tilføje en liste over DNS-servere ved at angive deres IP-adresser og reference navne til **DNS-serverne** elementet. Du kan bruge et **DnsServerRef** element til at angive, hvilke DNS-server poster fra elementet DNS-serverne bruges til andet netværk websteder i netværket virtuel.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel.

Konfigurationsfil netværk kan indeholde følgende elementer. Titlen på hvert element er knyttet til en side, der giver yderligere oplysninger om indstillingerne element værdi.

>[AZURE.IMPORTANT] Finde oplysninger om hvordan du konfigurerer konfigurationsfil netværk, kan du se [konfigurere et virtuelt netværk ved hjælp af et netværk konfigurationsfil](virtual-networks-using-network-configuration-file.md). Du kan finde oplysninger om hvert element, der er indeholdt i konfigurationsfil netværk [Azure virtuelle netværk konfiguration skema](https://msdn.microsoft.com/library/azure/jj157100.aspx).

[DNS-Element](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING] Attributten **name** i elementet **DnsServer** bruges kun som en reference til elementet **DnsServerRef** . Den repræsenterer ikke værtsnavnet for DNS-serveren. Hver **DnsServer** attributværdien skal være entydige i hele Microsoft Azure-abonnementet

[Virtuelt netværk websteder Element](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

>[AZURE.NOTE] For at angive denne indstilling for elementet virtuelle netværk websteder, skal den være tidligere defineret i elementet DNS. DnsServerRef *navn* i elementet virtuelle netværk websteder skal referere til en navneværdi, der er angivet i elementet DNS for DnsServer *navn*.

## <a name="next-steps"></a>Næste trin

- Forstå [Azure virtuelt netværk konfiguration skemaet](http://go.microsoft.com/fwlink/?LinkId=248093).
- Forstå [Azure Service konfiguration skemaet](https://msdn.microsoft.com/library/windowsazure/ee758710).
- [Konfigurere et virtuelt netværk ved hjælp af netværk konfigurationsfiler](virtual-networks-using-network-configuration-file.md).

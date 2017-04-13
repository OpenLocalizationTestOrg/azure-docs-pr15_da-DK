<properties 
   pageTitle="Sådan bruges offentlige IP-adresser i et virtuelt netværk"
   description="Lær, hvordan du konfigurerer et virtuelt netværk for at bruge offentlige IP-adresser"
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
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="public-ip-address-space-in-a-virtual-network-vnet"></a>Offentlige IP-adresseområde i et virtuelt netværk (VNet)

Virtuelt netværk (VNets) kan indeholde både offentlige og private (RFC 1918 adresseblokke) IP-adresseområder. Når du tilføjer en offentlige IP-adresseområder, behandles det som en del af den private VNet IP-adresseområder, der kun er tilgængelig i VNet, forbundne VNets og fra din lokale placering.

Billedet herunder viser en VNet, der indeholder offentlige og private IP-adresse mellemrum.

![Offentlige IP grundlæggende](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## <a name="how-do-i-add-a-public-ip-address-range"></a>Hvordan tilføjer jeg en offentlige IP-adresseområder?

Du kan tilføje en offentlige IP-adresseområder på samme måde, skal du tilføje en privat IP-adresseområder; ved enten ved hjælp af en *netcfg* fil eller ved at tilføje konfigurationen i [Azure-portalen](http://portal.azure.com). Du kan tilføje en offentlige IP-adresseområder, når du opretter din VNet, eller du kan gå tilbage og tilføje den senere. Eksemplet herunder viser både offentlige og private IP-adresse mellemrum konfigureret i den samme VNet.

![Offentlige IP-adresse i portalen](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## <a name="are-there-any-limitations"></a>Er der nogen begrænsninger?

Der er nogle få IP-adresseområder, der ikke er tilladt:

- 224.0.0.0/4 (Multicast)

- 255.255.255.255/32 (udsendelse)

- 127.0.0.0/8 (tilbagekobling)

- 169.254.0.0/16 (link-local)

- 168.63.129.16/32 (interne DNS)

## <a name="next-steps"></a>Næste trin

[Sådan håndteres DNS-servere, der bruges af en VNet](virtual-networks-manage-dns-in-vnet.md)
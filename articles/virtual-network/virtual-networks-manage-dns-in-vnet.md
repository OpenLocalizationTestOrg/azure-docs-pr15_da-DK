<properties 
   pageTitle="Administrere DNS-servere, der bruges af et virtuelt netværk (VNet)"
   description="Lær at tilføje og fjerne DNS-servere i et virtuelt netværk (vnet)"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="manage-dns-servers-used-by-a-virtual-network-vnet"></a>Administrere DNS-servere, der bruges af et virtuelt netværk (VNet)

Du kan administrere listen over DNS-servere, der bruges i en VNet i portalen Management eller i konfigurationsfil netværk. Du kan føje op til 12 DNS-servere for hver VNet. Når du angiver DNS-servere, er det vigtigt at bekræfte, at du viser dine DNS-servere i den rigtige rækkefølge for dit miljø. DNS-serverlister virker ikke round robin. De bruges i den rækkefølge, de er angivet. Hvis den første DNS-server på listen kan nås, bruger klienten DNS-server, uanset om DNS-serveren fungerer korrekt eller ej. Hvis du vil ændre DNS-server rækkefølgen for netværket virtuel skal fjerne DNS-servere på listen og føje dem tilbage i den rækkefølge, du vil.

>[AZURE.WARNING] Når listen DNS er blevet opdateret, skal du genstarte de virtuelle maskiner, der er placeret i netværket virtuel, så de løfte nye DNS server settings. Virtuelle maskiner fortsætter med at bruge deres aktuelle konfiguration, før de genstartes.

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-management-portal"></a>Redigere en liste over DNS-servere for et virtuelt netværk ved hjælp af portalen administration

1. Log på **administrationsportalen til**.

1. Klik på **netværk**i navigationsruden, og klik derefter på navnet på netværket virtuel i kolonnen **navn** .

1. Klik på **Konfigurer**.

1. I **DNS-servere**, kan du konfigurere følgende:

    - **Til at registrere (Tilføj) en ny DNS server –** Nøjes med at skrive navn og IP-adresse i felterne. Dette tilføjer en DNS-server til netværket virtuel DNS-servere liste og også registrerer DNS-server med Azure.

    - **Tilføje en DNS-server, der tidligere blev registreret –** Hvis du allerede har registreret en DNS-server med Azure, kan du vælge den på listen udfyldt på forhånd.

    - **Fjerne en DNS-server fra netværket virtuel –** Klik på X ud for den server, du vil fjerne. Bemærk, at dette kun fjerner serveren fra listen virtuelt netværk. DNS-serveren forbliver registrerede i Azure til dine andre virtuelle netværk til brug. Hvis du vil slette en DNS-server fra dit abonnement, skal du gå til siden **netværk -> DNS-servere** .

    - **Til at ændre rækkefølgen af DNS-servere –** Fjern alle DNS-servere, der vises, og føj dem igen i den rækkefølge, du vil. Husk, at dette ikke er en round robin-DNS-liste.

    - **Omdøbe en DNS-server –** Markere DNS-serveren på listen og derefter skrive det nye navn. Dette vil registrere en ny DNS-server i Azure, samt føje det til listen DNS-servere for netværket virtuel. Den gamle DNS-server og dens IP-adresse, forbliver registrerede med Azure. Du kan slette den på siden **DNS-servere** , hvis du ikke bruger den til andre virtuelle netværk.

1. Klik på **Gem** nederst på siden for at gemme din nye konfiguration af DNS-servere.

1. Genstart de virtuelle maskiner, der er placeret i det virtuelle netværk tilladelse til at hente de nye DNS-indstillinger.

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>Redigere en liste med DNS-server ved hjælp af et netværk konfigurationsfil

Hvis du vil redigere en liste over DNS-servere ved hjælp af et netværk konfigurationsfil, skal du først eksportere dine indstillinger for søgekonfiguration fra portalen administration. Derefter skal du redigere konfigurationsfil netværk og importere dem tilbage gennem portalen administration. Nedenfor finder du en overordnet oversigt over trin til at fuldføre denne proces.

1. Eksportere dine virtuelle netværksindstillinger til en netværk konfigurationsfil. Se [Eksportere virtuelle netværksindstillinger til en netværk konfigurationsfil](virtual-networks-using-network-configuration-file.md)kan finde flere oplysninger og trin til at eksportere dine netværksindstillinger konfiguration.

1. Angiv oplysningerne for netværket virtuel DNS-server. Du kan finde flere oplysninger om angivelse af en DNS-server, kan du se [angive en DNS-Server i et virtuelt netværk konfigurationsfil](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). For flere oplysninger om netværk konfigurationsfiler skal du se [Azure virtuelle netværk konfiguration skemaet](https://msdn.microsoft.com/library/azure/jj157100.aspx) og [konfigurere et virtuelt netværk ved hjælp af et netværk konfigurationsfil](virtual-networks-using-network-configuration-file.md).

1. Importere konfigurationsfil netværk. Flere oplysninger og trin til at importere din konfigurationsfil netværk, kan du se [importere en netværk konfigurationsfil](virtual-networks-using-network-configuration-file.md).

1. Genstart de virtuelle maskiner, der er placeret i det virtuelle netværk tilladelse til at hente de nye DNS-indstillinger.

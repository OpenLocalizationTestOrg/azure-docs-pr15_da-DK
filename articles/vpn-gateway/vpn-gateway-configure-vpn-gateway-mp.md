<properties 
   pageTitle="Konfigurere en VPN-Gateway i portalen Azure klassisk | Microsoft Azure"
   description="I denne artikel fører dig gennem konfiguration af netværket virtuel VPN-gateway og ændre en gateway VPN routing type."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vpn-gateway-for-the-classic-deployment-model"></a>Konfigurere en VPN-gateway for den klassiske implementeringsmodel


Hvis du vil oprette en sikker tværs lokale forbindelse mellem Azure og din lokale placering, skal du konfigurere en VPN-gateway-forbindelse. En gateway i modellen Klassisk installation, kan du være en af to VPN routing typer: statisk eller dynamisk. Afhænger af den type, du vælger både på dit netværk design plan, og den lokale VPN-enhed, du vil bruge. 

Nogle indstillinger for forbindelse, som en forbindelse til punkt-til-websted, f.eks en dynamisk routing gateway. Hvis du vil konfigurere din gateway for at understøtte både punkt-til-websted (P2S) forbindelser og en-til-websted (S2S) forbindelse, har du konfigurere en dynamisk routing gateway, selvom-til-websted kan konfigureres med enten gateway VPN routing type. 

Desuden skal sikre dig, at den enhed, du vil bruge til din forbindelse understøtter den VPN routing type, du vil oprette. Se [om VPN-enheder](vpn-gateway-about-vpn-devices.md).


**Om denne artikel** 

I denne artikel er skrevet til den klassiske implementeringsmodel ved hjælp af [Klassisk portal](https://manage.windowsazure.com) (ikke Azure portal). 

**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-overview"></a>Oversigt over konfiguration

Følgende trin fører dig gennem konfigurationen af din VPN-gateway i portalen Azure klassisk. Følgende fremgangsmåde gælder for gateways for virtuelle netværk, der er oprettet ved hjælp af den klassiske implementeringsmodel. Ikke alle konfigurationsindstillinger for gateways er på nuværende tidspunkt tilgængelig på portalen Azure. Når det er tilfældet, skal vi oprette en ny række instruktioner, der gælder for portalen Azure.


1. [Oprette en VPN-gateway til din VNet](#create-a-vpn-gateway)

1. [Indsamle oplysninger om konfigurationen af VPN-enhed](#gather-information-for-your-vpn-device-configuration)

1. [Konfigurere din VPN-enhed](#configure-your-vpn-device)

1. [Bekræfte dit lokale netværk områder og VPN-gateway IP-adresse](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### <a name="before-you-begin"></a>Inden du går i gang

Før du konfigurerer din gateway, skal du først oprette netværket virtuel. For trin til at oprette et virtuelt netværk til forbindelse i tværs det lokale miljø, skal du se [konfigurere et virtuelt netværk med en - til-websted VPN-forbindelse](vpn-gateway-site-to-site-create.md), eller [Konfigurer et virtuelt netværk med en punkt-til-site VPN-forbindelse](vpn-gateway-point-to-site-create.md). Derefter skal du følge nedenstående trin til at konfigurere gatewayen VPN og indsamle de oplysninger, skal du konfigurere din VPN-enhed. 

Hvis du allerede har en VPN-gateway, og du vil ændre typen VPN routing, se, [hvordan du ændre typen VPN routing til din gateway](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Oprette en VPN-gateway

1. Kontrollér, at statuskolonnen for netværket virtuel er **oprettet**i [Azure klassisk portal](https://manage.windowsazure.com), på siden **netværk** .

1. Klik på navnet på netværket virtuel i kolonnen **navn** .

1. Bemærk, at denne VNet ikke har en gateway, der er konfigureret endnu på siden **Dashboard** . Du får vist denne status, når du går gennem trinnene til at konfigurere din gateway.

![Gateway, der ikke oprettet](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


Klik derefter på **Opret Gateway**nederst på siden. Du kan vælge enten *Statisk Routing* - eller *Dynamisk Routing*. Typen VPN routing du vælger, afhænger af par faktorer. For eksempel hvad enheden VPN understøtter, og om du har brug at understøtte forbindelser til punkt-til-websted. Kontrollere, [Om VPN-enheder til virtuelle netværksforbindelsen](vpn-gateway-about-vpn-devices.md) for at bekræfte den VPN routing type, du har brug for. Når gatewayen er blevet oprettet, kan du skifte mellem gateway VPN routing typer uden at slette og genoprette gatewayen. Klik på **Ja**, når systemet beder dig om at bekræfte, at du vil den gateway, der er oprettet.

![Gatewayen VPN routing type](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Når din gateway opretter, meddelelse om gateway grafik på siden ændres til gul og siger, at *Oprette gatewayen*. Det kan tage op til 45 minutter til at oprette gatewayen. Vent, indtil gatewayen er fuldført, før du kan gå videre med andre indstillinger for søgekonfiguration.

![Oprettelse af gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Når gatewayen ændres til *tilslutning*, kan du indsamle de oplysninger, du har brug for din VPN-enhed.

![Gateway-forbindelse](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="gather-information-for-your-vpn-device-configuration"></a>Indsamle oplysninger om konfigurationen af VPN-enhed

Når gatewayen er blevet oprettet, kan du indsamle oplysninger om konfigurationen af VPN-enhed. Disse oplysninger er placeret på siden **Dashboard** for netværket virtuel:

1. **Gatewayen IP-adresse-** IP-adressen kan findes på siden **Dashboard** . Du kan ikke se den indtil, når din gateway er færdig med at oprette.

1. **Delt nøgle –** Klik på **Administrer nøgle** i bunden af skærmen. Klik på ikonet ud for tasten for at kopiere den til Udklipsholder, og derefter indsætte og gemme nøglen. Denne knap fungerer kun, når der er en enkelt S2S VPN-tunnel. Hvis du har flere S2S VPN-tunneler, skal du bruge *Få virtuelle netværk Gateway delt nøgle* API eller PowerShell-cmdlet.

![Administrere nøgle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## <a name="configure-your-vpn-device"></a>Konfigurere din VPN-enhed

Når du har fuldført de forrige trin, skal du eller din netværksadministrator at konfigurere VPN enheden for at oprette forbindelsen. Se [Om VPN-enheder til virtuelle netværksforbindelsen](vpn-gateway-about-vpn-devices.md) kan finde flere oplysninger om VPN-enheder.

Når VPN-enheden er konfigureret, kan du se forbindelsesoplysningerne opdaterede på siden Dashboard til din VNet.

Du kan også køre et af følgende kommandoer for at teste forbindelsen:

|                      | Cisco ASA             | Cisco ISR/ASR         | Juniper SSG/ISG | Juniper SRX/J                            |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **Kontrollere hovedtilstand**  | vise kryptografi isakmp systemadministratorens | vise kryptografi isakmp systemadministratorens | få ike cookie  | vise sikkerhed ike sikkerhed-tilknytning   |
| **Kontrollere sikkerhedstilknytninger i hurtigtilstand** | vise kryptografi ipsec systemadministratorens  | vise kryptografi ipsec systemadministratorens  | få systemadministratorens          | Vis IPSec-sikkerhed-tilknytning. |


## <a name="verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Bekræfte dit lokale netværk områder og VPN-gateway IP-adresse

### <a name="verify-your-vpn-gateway-ip-address"></a>Bekræfte din VPN-gateway IP-adresse

For gateway du tilslutter korrekt skal enheden VPN IP-adresse være korrekt konfigureret til det lokalnetværk, du har angivet for din konfiguration i tværs det lokale miljø. Dette er typisk konfigureret under konfigurationsprocessen til websted. Men hvis du tidligere har brugt denne lokale netværk med en anden enhed, eller IP-adressen er blevet ændret for denne lokale netværk, redigere indstillinger for at angive den korrekte Gateway IP-adresse.

1. Klik på **netværk** i ruden til venstre portalen for at bekræfte din gateway IP-adresse, og vælg derefter **Lokalnetværk** øverst på siden. Du får vist VPN-Gateway-adressen for hvert lokale netværk, du har oprettet. Vælg VNet for at redigere IP-adressen, og klik på **Rediger** nederst på siden.

1. Redigere IP-adressen på siden **Angiv dit lokale netværk oplysninger** , og klik derefter på næste pilen nederst på siden.

1. Klik på markeringen nederst til højre til at gemme dine indstillinger på siden **Angiv-adresseområde** .

### <a name="verify-the-address-ranges-for-your-local-networks"></a>Bekræfte adresseområder for dit lokale netværk

Du skal bekræfte, at hver IP-adresseområder er angivet for den korrekte trafik passerer gennem gatewayen til din lokale placering. Hvert område skal være angivet i konfigurationen af Azure **Lokalnetværk** . Afhængigt af netværkskonfigurationen af din lokale placering, kan det være en smule store opgave. Trafik, der er bundet til en IP-adresse, der er indeholdt i de viste områder sendes via virtuelt netværk VPN-gateway. De områder, du liste ikke behøver at være private områder, selvom du skal bekræfte, at din lokale konfiguration kan modtage indgående trafik.

Tilføje eller redigere områderne til et lokalnetværk, skal du følge nedenstående trin.

1. For at redigere IP-adresseområder for et lokalt netværk skal du klikke på **netværk** i ruden til venstre portalen og vælg derefter **Lokalnetværk** øverst på siden. I portalen er den nemmeste måde at få vist de områder, du har angivet på siden **Rediger** . Vælg VNet for at få vist dine områder, og klik på **Rediger** nederst på siden.

1. På siden **Angiv dit lokale netværk oplysninger** Foretag eventuelle ændringer. Klik på næste pilen nederst på siden.

1. Foretag ændringerne netværk adresse plads på siden **Angiv-adresseområde** . Klik derefter på markering for at gemme din konfiguration.

## <a name="how-to-view-gateway-traffic"></a>Sådan får du vist gateway trafik

Du kan se din gateway og gateway trafik fra din side med virtuelt netværk **Dashboard** .

På siden **Dashboard** kan du se følgende:

- Mængden af data, der flyder gennem din gateway, både data i og data ud.

- Navnene på DNS-servere, der er angivet for netværket virtuel.

- Forbindelse mellem din gateway og enheden VPN.

- Den delte nøgle, der bruges til at konfigurere din gateway-forbindelse til din VPN-enhed.


## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Sådan ændrer du typen VPN routing til din gateway

Da nogle connectivity konfigurationer er kun tilgængelige for bestemte gateway routing, kan du finde, du vil ændre gatewayen VPN routing type for en eksisterende VPN-gateway. Du vil muligvis føje punkt-til-site forbindelse til en eksisterende websted til websted forbindelse, der indeholder en statisk gateway. Punkt-til-site forbindelser kræver en dynamisk gateway. Det betyder, at konfigurere en P2S forbindelse, skal du ændre din gateway VPN routing type fra statisk til dynamisk.

Hvis du vil ændre en gateway VPN routing type, skal du slette eksisterende gatewayen og derefter oprette en ny gateway med den nye routing type. Du behøver ikke at slette det hele virtuelle netværk for at ændre typen gateway routing.

Før du ændrer din gateway VPN routing type, skal du kontrollere, at enheden VPN understøtter den routing type, du vil bruge. Se [Om VPN-enheder til virtuelle netværksforbindelsen](vpn-gateway-about-vpn-devices.md)for at hente nye routing konfiguration prøver og kontrollere VPN-enhed systemkrav.

>[AZURE.IMPORTANT] Når du sletter et virtuelt netværk VPN-gateway, udgivet VIP tildelt gatewayen. Når du genoprette gatewayen, tildeles en ny VIP til den.

1. **Slette eksisterende VPN gatewayen.**

    Gå til bunden af siden, og klik på **Slet Gateway**på siden **Dashboard** for netværket virtuel. Vent på, at gatewayen er blevet slettet meddelelsen. Når du modtager besked på skærmen, at din gateway er blevet slettet, kan du oprette en ny gateway.

1. **Oprette en ny VPN-gateway.**

    Brug proceduren øverst på siden til at oprette en ny gateway: [Opret en VPN-gateway](#create-a-vpn-gateway).


## <a name="next-steps"></a>Næste trin

Du kan føje virtuelle maskiner til netværket virtuel. Se, [hvordan du opretter en brugerdefineret virtuel maskine](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Hvis du vil konfigurere en VPN-forbindelse til punkt-til-websted, skal du se [konfigurere en VPN-forbindelse til punkt-til-websted](vpn-gateway-point-to-site-create.md).

 

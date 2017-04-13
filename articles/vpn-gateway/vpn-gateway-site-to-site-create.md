<properties
   pageTitle="Oprette et virtuelt netværk med en-til-websted VPN-Gateway-forbindelse ved hjælp af portalen Azure klassisk | Microsoft Azure"
   description="Oprette en VNet med en VPN-Gateway, S2S forbindelse til tværs det lokale miljø og hybridkonfigurationer ved hjælp af den klassiske implementeringsmodel."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-classic-portal"></a>Oprette en VNet med forbindelse til websted ved hjælp af portalen Azure klassisk

> [AZURE.SELECTOR]
- [Ressourcestyring - Azure-portalen](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Ressourcestyring - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Klassisk - klassisk Portal](vpn-gateway-site-to-site-create.md)

I denne artikel vejledes du gennem oprettelse af et virtuelt netværk og en-til-websted VPN-gateway-forbindelse til dit lokale netværk ved hjælp af den klassiske implementeringsmodel og klassisk portalen. Websted til websted forbindelser kan bruges til tværs lokal og hybrid konfigurationer.

![Websted til diagram] (./media/vpn-gateway-site-to-site-create/site2site.png "websted til websted")


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Installation-modeller og metoder til at websted forbindelser

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

I følgende tabel vises de tilgængelige installation modeller og metoder til at websted konfigurationer. Når en artikel med konfigurationstrinnene er tilgængelig, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Yderligere konfigurationer 

Hvis du vil oprette forbindelse VNets sammen, skal du se [konfigurere en VNet-VNet forbindelse til den klassiske implementeringsmodel](virtual-networks-configure-vnet-to-vnet-connection.md). Hvis du vil føje en forbindelse til websted til et VNet, der allerede har en forbindelse, kan du se [tilføje en S2S forbindelse til en VNet med en eksisterende forbindelse for VPN-gateway](vpn-gateway-multi-site.md).
 
## <a name="before-you-begin"></a>Inden du går i gang

Kontrollér, at du har følgende elementer, før du starter konfigurationen.

- En VPN-kompatibel enhed og en person, der er i stand til at konfigurere den. Se [om VPN-enheder](vpn-gateway-about-vpn-devices.md). Hvis du ikke kender konfiguration af enheden VPN eller ikke har kendskab til den IP-adresse, områder, der er placeret i dine lokale netværkskonfiguration, skal du være koordineret med en person, der kan give disse oplysninger for dig.

- En eksternt modstående offentlige IP-adresse til din VPN-enhed. Denne IP-adresse må ikke være placeret bag en NAT.

- Et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="CreateVNet"></a>Oprette din virtuelt netværk

1. Log på [Azure klassisk portal](https://manage.windowsazure.com/).

2. Klik på **Ny**i nederste venstre hjørne af skærmen. Klik på **Netværkstjenester**i navigationsruden, og klik derefter på **Virtuelt netværk**. Klik på **Brugerdefineret oprette** for at starte konfigurationsguiden.

3. For at oprette din VNet skal du angive dine indstillinger for søgekonfiguration på følgende sider:

## <a name="Details"></a>Virtuelt netværk oplysningssiden

Angiv følgende oplysninger:

- **Navn**: Navngiv netværket virtuel. For eksempel, *EastUSVNet*. Du skal bruge denne virtuelle netværksnavn, når du installerer din FOS og PaaS forekomster, så du ikke vil foretage navnet for kompliceret.
- **Placering**: placeringen er direkte relateret til den fysiske placering (område), hvor du vil dine ressourcer (FOS) skal placeres. Hvis du vil FOS, du anvender denne virtuelle netværk, der skal være fysisk er placeret i *Af USA*, Vælg den nye placering. Du kan ikke ændre det område, der er knyttet til dit virtuelle netværk, du har oprettet.

## <a name="DNS"></a>DNS-servere og VPN-forbindelse side

Angiv følgende oplysninger, og klik derefter på næste pilen nederst til højre.

- **DNS-servere**: Angiv navnet på DNS-server og IP-adresse, eller Vælg en tidligere registrerede DNS-server i genvejsmenuen. Denne indstilling oprette ikke en DNS-server. Det kan du angive de DNS-servere, du vil bruge til navneoversættelse for denne virtuelle netværk.
- **Konfigurere VPN til-websted**: Markér afkrydsningsfeltet for **Konfigurer en VPN til-websted**.
- **Lokalnetværk**: et lokalt netværk repræsenterer din fysiske lokalt placering. Du kan vælge et lokalt netværk, som du tidligere har oprettet, eller du kan oprette et nyt lokale netværk. Hvis du vælger for at bruge et lokalt netværk, du tidligere har oprettet, gå til konfigurationssiden af **Lokalnetværk** , og Bekræft, at VPN-enhed IP-adresse (offentlig modstående IPv4-adresse) VPN-enhed er nøjagtigt.

## <a name="Connectivity"></a>Websted til websted connectivity side

Hvis du vil oprette et nyt lokale netværk, får du vist siden **Til websted Connectivity** . Hvis du vil bruge et lokalt netværk, du tidligere har oprettet, vises ikke på denne side i guiden, og du kan gå videre til næste afsnit.

Angiv følgende oplysninger, og klik derefter på pilen Næste.

-   **Navn**: det navn, du vil ringe til din lokale (lokalt) netværk websted.
-   **VPN-enhed IP-adresse**: offentlige modstående IPv4-adressen på din lokale VPN-enhed, som du kan bruge til at oprette forbindelse til Azure. VPN-enheden kan ikke være placeret bag en NAT.
-   **Adresseområde**: omfatte Start IP- og CIDR (antal adresse). Du angiver den adresse, range(s), som du vil blive sendt gennem den virtuelle netværksgateway til din lokale lokalt placering. Hvis en IP-destinationsadresse falder inden for de områder, du angiver her, det er distribueret via gatewayen virtuelt netværk.
-   **Tilføj adresseområde**: Hvis du har flere adresseområder, du vil blive sendt gennem den virtuelle netværksgateway, angive hver ekstra adresseområde. Du kan tilføje eller fjerne områder senere på siden **Lokalnetværk** .

## <a name="Address"></a>Virtuelt netværk adresse mellemrum side

Angiv det adresseområde, som du vil bruge til dit virtuelle netværk. Dette er de dynamiske IP-adresser (FALD), der skal tildeles til FOS og andre rolle forekomster, du anvender denne virtuelle netværk.

Det er især vigtigt at markere et område, der ikke overlapper med en af de områder, der bruges til dit lokale netværk. Du skal være koordineret med din netværksadministrator. Din netværksadministrator kan være nødvendigt at skærer ud af et område af IP-adresser fra dit lokale netværk adresseområde så du kan bruge til dit virtuelle netværk.

Angiv følgende oplysninger, og klik derefter på markeringen nederst til højre til at konfigurere dit netværk.

- **Adresseområde**: omfatte Start IP-adresse og adresse antal. Kontrollér, at felterne adresse, du angiver ikke overlapper et af felterne adresse, du har på dit lokale netværk.
- **Tilføj undernet**: medtage første IP- og adresse antal. Yderligere undernet er ikke påkrævet, men du overveje at oprette et separat undernet for VM'er, der har statisk FALD. Eller du måske har din FOS i et undernet, der er adskilt fra dine andre rolle forekomster.
- **Tilføj gateway undernet**: Klik for at tilføje gateway-undernettet. Gateway-undernet bruges kun til gatewayen virtuelt netværk og er påkrævet til denne konfiguration.

Klik på markeringen nederst på siden, og netværket virtuel begynder at oprette. Når den er fuldført, vises **oprettet** vises under **Status** på siden **netværk** i portalen Azure klassisk. Når VNet er blevet oprettet, kan du derefter konfigurere din gateway, virtuelt netværk.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="VNetGateway"></a>Konfigurere din gateway, virtuelt netværk

Konfigurere gatewayen virtuelt netværk for at oprette en sikker forbindelse til websted. Se [konfigurere et virtuelt netværksgateway på portalen Azure klassisk](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Næste trin

Når forbindelsen er fuldført, kan du føje virtuelle maskiner til dine virtuelle netværk. Dokumentationen [virtuelle maskiner](https://azure.microsoft.com/documentation/services/virtual-machines/) kan finde flere oplysninger.

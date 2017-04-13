<properties
    pageTitle="Sådan konfigurerer du VPN-forbindelser i Azure API Management"
    description="Lær, hvordan du konfigurerer en VPN-forbindelse i Azure API Management og access web services igennem."
    services="api-management"
    documentationCenter=""
    authors="antonba"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="antonba"/>

# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>Sådan konfigurerer du VPN-forbindelser i Azure API Management

API Management VPN-support kan du forbinde dine API Management gateway til et virtuelt Azure-netværk (klassisk). Dette giver mulighed for administration af API kunder til at oprette sikker forbindelse til deres back end-web-tjenester, der er lokalt eller på anden måde er utilgængelige for det offentlige internet.

>[AZURE.NOTE] Azure API administration fungerer med klassisk VNETs. Du kan finde oplysninger om oprettelse af en klassisk VNET, [Opret et virtuelt netværk (klassisk) ved hjælp af portalen Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Du kan finde oplysninger om tilslutning klassisk VNETs til ARM VNETS [tilslutning klassisk VNets til nye VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="enable-vpn"> </a>Aktivere VPN-forbindelser

>Forbindelse for VPN er kun tilgængelig i **Premium** og **udvikler** niveauer. Åbn din API Management-tjenesten i [Azure klassisk Portal][] for at skifte til den, og Åbn derefter fanen **skala** . Vælg Premium niveauet under sektionen **Generelt** , og klik på Gem.

Åbn din API Management-tjenesten i [Azure klassisk Portal][] for at aktivere VPN-forbindelse, og Skift til fanen **Konfigurer** . 

Skifte **VPN-forbindelse** til **i**sektionen VPN.

![Konfigurere fanen for administration af API forekomst][api-management-setup-vpn-configure]

Du får nu vist en liste over alle områder, hvor din API Management-tjenesten er klargjort.

Vælg en VPN- og undernet for hvert område. På listen over VPN er udfyldt baseret på de virtuelle tilgængelige i abonnementet Azure netværk, der oprettes i det område, du vil konfigurere.

![Vælg VPN][api-management-setup-vpn-select]

Klik på **Gem** nederst på skærmen. Du kan ikke udføre andre handlinger med API Management-tjenesten fra portalen Azure klassisk, mens det er ved at opdatere. Gatewayen service kan stadig og runtime opkald bør ikke berøres.

Bemærk, at VIP-adressen for gateway vil ændre hver gang VPN er aktiveret eller deaktiveret.

## <a name="connect-vpn"> </a>Opret forbindelse til en webtjeneste bag VPN

Når din API Management-tjenesten er forbundet med VPN-Forbindelsen, er få adgang til webtjenester i det virtuelle netværk ikke anderledes end at få adgang til offentlige tjenester. Skrive i den lokale adresse eller værtsnavn (Hvis en DNS-server er konfigureret til det virtuelle Azure-netværk) for din webtjeneste i feltet **URL-adresse service** når du opretter en ny API eller redigere en eksisterende liste.

![Tilføje API fra VPN][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>Nødvendige porte for API Management VPN-support

Når en API Management service forekomst er placeret i en VNET, anvendes portene i den følgende tabel. Hvis disse porte blokeres, fungerer tjenesten muligvis ikke korrekt. Har du en eller flere af disse porte, der er blokeret er den mest almindelige forkert konfiguration problem, når API administration af med en VNET.

| Porte                      | Retning        | Transport Protocol | Formål                                                          | Kilde / Destination              |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80, 443                      | Indgående          | TCP                | Klientkommunikation til administration af API                           | INTERNET / VIRTUAL_NETWORK        |
| 80,443                       | Udgående         | TCP                | API Management afhængighed af Azure lager og Azure Service Bus | VIRTUAL_NETWORK / INTERNET        |
| 1433                         | Udgående         | TCP                | Administration af API afhængigheder af SQL                               | VIRTUAL_NETWORK / INTERNET        |
| 9350, 9351, 9352, 9353, 9354 | Udgående         | TCP                | Administration af API afhængigheder af Service Bus                       | VIRTUAL_NETWORK / INTERNET        |
| 5671                         | Udgående         | AMQP               | API Management afhængighed til at logge på hændelse Hub politik            | VIRTUAL_NETWORK / INTERNET        |
| 6381, 6382, 6383             | Indgående/udgående | UDP                | Administration af API afhængigheder på Redis Cache                       | VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445                          | Udgående         | TCP                | API Management afhængighed af Azure filshare for CIFFER            | VIRTUAL_NETWORK / INTERNET        |

## <a name="custom-dns"> </a>Konfiguration af brugerdefinerede DNS-server

Administration af API afhænger af et antal Azure tjenester. Når en API Management service forekomst er placeret i en VNET, hvor der bruges en brugerdefineret DNS-server, skal have lov til at løse værtsnavne af disse Azure tjenester. Følg [vejledningen](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) på brugerdefineret DNS-installation.  

## <a name="related-content"> </a>Relateret indhold


* [Oprette et virtuelt netværk med en-til-websted VPN-forbindelse ved hjælp af portalen Azure klassisk][]
* [Sådan bruger du API inspektion spore kalder i Azure API Management][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure klassisk Portal]: https://manage.windowsazure.com/

[Oprette et virtuelt netværk med en-til-websted VPN-forbindelse ved hjælp af portalen Azure klassisk]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[Sådan bruger du API inspektion spore kalder i Azure API Management]: api-management-howto-api-inspector.md

<properties
   pageTitle="Konfigurere en VNet-VNet forbindelse til den klassiske implementeringsmodel | Microsoft Azure"
   description="Sådan forbinder Azure virtuelle netværk sammen ved hjælp af PowerShell og Azure klassisk portalen."
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
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>Konfigurere en VNet-VNet forbindelse til den klassiske implementeringsmodel

> [AZURE.SELECTOR]
- [Ressourcestyring - Azure-portalen](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Ressourcestyring - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Klassisk - klassisk Portal](virtual-networks-configure-vnet-to-vnet-connection.md)



I denne artikel fører dig gennem trinnene til at oprette og forbinde virtuelle netværk sammen ved hjælp af den klassiske implementeringsmodel (også kaldet Service Management). Følgende trin bruge Azure klassisk portal til at oprette VNets og gateways og PowerShell for at konfigurere forbindelsen VNet-VNet. Du kan ikke konfigurere forbindelsen i portalen.

![VNet til VNet Connectivity Diagram](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)


### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Installation-modeller og metoder til VNet-VNet forbindelser

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Følgende tabel viser de tilgængelige installation modeller og metoder til VNet-VNet konfigurationer. Når en artikel med konfigurationstrinnene er tilgængelig, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Om VNet-VNet forbindelser

Oprette forbindelse et virtuelt netværk til en anden virtuelt netværk minder (VNet til VNet) om forbindelse et virtuelt netværk til en lokal placering. Begge typer connectivity bruges en VPN-gateway for at give en sikker tunnel ved hjælp af IPsec/IKE. 

Den VNets, du opretter forbindelse kan være i forskellige abonnementer og forskellige områder. Du kan kombinere VNet til VNet kommunikation med flere websted konfigurationer. Her kan du oprette netværkstopologier, der kombinerer tværs lokale forbindelse med mellem virtuelle netværksforbindelse.


### <a name="why-connect-virtual-networks"></a>Hvorfor oprette forbindelse virtuelle netværk?

Vil du måske at oprette forbindelse virtuelle netværk af følgende årsager:

- **Cross geografisk område-redundans og geografisk tilstedeværelse**
    - Du kan konfigurere din egen geografisk gentagelse eller synkronisering med sikre forbindelser uden at gå over internettet slutpunkter.
    - Med Azure justering af belastning og Microsoft eller tredjeparts klyngedannelse teknologi, kan du konfigurere meget tilgængelige arbejdsprocess med geografisk redundans på tværs af flere Azure områder. Et vigtigt eksempel er til konfiguration af SQL altid på med tilgængelighed grupper, der strækker sig over flere Azure områder.

- **Internationale med flere lag programmer med stærke isolationsniveauet rammen**
    - I det samme region, kan du konfigurere med flere lag programmer med flere VNets forbindelse sammen med stærke isolationsniveauet og sikker kommunikation mellem niveau.

- **Cross mellem organisation kommunikation i Azure-abonnement**
    - Hvis du har flere Azure abonnementer, du kan oprette forbindelse arbejdsbelastninger fra forskellige abonnementer sammen sikkert mellem virtuelle netværk.
    - Du kan aktivere tværs af organisationen kommunikation med sikker VPN-teknologi i Azure til virksomheder eller tjenesteudbydere.

### <a name="vnet-to-vnet-faq-for-classic-vnets"></a>VNet-VNet ofte stillede spørgsmål om klassisk VNets

- De virtuelle netværk kan være i de samme eller forskellige abonnementer.

- De virtuelle netværk kan være i de samme eller forskellige Azure områder (placeringer).

- En skybaseret tjeneste eller et slutpunkt til justering af belastning kan ikke dække på tværs af virtuelle netværk, selvom de er forbundet med hinanden.

- Opretter forbindelse til flere virtuelle netværk sammen kræver ikke en VPN-enheder.

- VNet-VNet understøtter forbindende Azure virtuelle netværk. Den ikke understøtter forbindende virtuelle computere eller tjenester, der ikke er installeret på et virtuelt netværk i skyen.

- VNet-VNet kræver dynamiske routing gateways. Azure statisk routing gateways understøttes ikke.

- Virtuel netværksforbindelsen kan bruges samtidigt med flere websted VPN. Der er op til 10-VPN-tunneler for et virtuelt netværk VPN-gateway, oprette forbindelse til enten andre virtuelle netværk eller lokale websteder.

- Adresse mellemrum af de virtuelle netværk og on-premises lokale netværk websteder skal ikke overlapper. Overlappende adresse mellemrum medfører, at oprettelsen af virtuelle netværk eller blive overført netcfg konfigurationsfiler mislykkes.

- Overflødige tunneler mellem et par af virtuelle netværk understøttes ikke.

- Alle VPN-tunneler for VNet, herunder P2S VPN'er dele den tilgængelige båndbredde til gatewayen VPN og den samme VPN gateway oppetid SLA i Azure.

- VNet-VNet trafik varer på tværs af det grundlæggende element Azure.


## <a name="step1"></a>Trin 1 – planlægge din IP-adresseområder

Det er vigtigt at overveje de områder, du skal bruge til at konfigurere dine virtuelle netværk. For denne konfiguration, skal du sikre dig, at ingen af dine VNet områder overlapper med hinanden eller med nogen af de lokale netværk, som de opretter forbindelse til.

I følgende tabel vises et eksempel på, hvordan du definerer din VNets. Brug feltet områder, der som en vejledning kun. Notér områder for dine virtuelle netværk. Du skal bruge disse oplysninger til efterfølgende trin.

**Eksempelindstillinger for**

|Virtuelt netværk  |Adresseområde               |Område      |Opretter forbindelse til lokale netværk websted|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1            |VNet1 (10.1.0.0/16)         |USA vest     |VNet2Local (10.2.0.0/16)      |
|VNet2            |VNet2 (10.2.0.0/16)         |Japan øst  |VNet1Local (10.1.0.0/16)      |
  
## <a name="step-2---create-vnet1"></a>Trin 2 – oprette VNet1

I dette trin skal oprette vi VNet1. Når du bruger en af eksemplerne, skal du erstatte din egen værdier. Hvis din VNet allerede findes, behøver du ikke at udføre dette trin. Men du har brug at bekræfte, at IP-adresseområder ikke overlapper med områderne for din anden VNet eller med en af de andre VNets, du vil oprette forbindelse.

1. Log på [Azure klassisk portal](https://manage.windowsazure.com). I denne artikel bruge vi portalen klassisk, fordi nogle af de nødvendige konfigurationsindstillinger ikke er endnu er tilgængelige i portalen Azure.

2. Klik på **Ny**i nederste venstre hjørne af skærmen, > **Netværk Services** > **Virtuelt netværk** > **Brugerdefineret oprette** til at starte konfigurationsguiden. Når du navigerer i guiden, kan du tilføje de angivne værdier på hver side.

### <a name="virtual-network-details"></a>Virtuelt netværk detaljer

Angiv følgende oplysninger på siden detaljer om virtuelle netværk:

  ![Virtuelt netværk detaljer](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Navn** - navn netværket virtuel. For eksempel VNet1.
  - **Placering** – når du opretter et virtuelt netværk skal du knytte den til en Azure placering (område). Hvis du vil din VM'er, der er installeret på netværket virtuel skal være fysisk er placeret i Vest USA, Vælg den nye placering. Du kan ikke ændre den placering, der er knyttet til dit virtuelle netværk, du har oprettet.

### <a name="dns-servers-and-vpn-connectivity"></a>DNS-servere og VPN-forbindelse

Angiv følgende oplysninger på siden DNS-servere og VPN-forbindelse, og klik derefter på næste pilen nederst til højre.

  ![DNS-servere og VPN-forbindelse](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

- **DNS-servere** – Angiv navnet på DNS-server og IP-adresse, eller Vælg en tidligere registrerede DNS-server på rullelisten. Denne indstilling oprette ikke en DNS-server. Det kan du angive de DNS-servere, du vil bruge til navneoversættelse for denne virtuelle netværk. Hvis du vil have navneoversættelse mellem dine virtuelle netværk, har du konfigurerer dine egne DNS-server i stedet for at bruge den navneoversættelse, som leveres af Azure.
- Undlad at vælge et af felterne for de P2S eller S2S forbindelse. Klik på pilen nederst til højre til at flytte til det næste skærmbillede.

### <a name="virtual-network-address-spaces"></a>Virtuelt netværk adresse mellemrum

Angiv det adresseområde, du vil bruge til netværket virtuel på siden virtuelle netværk adresse mellemrum. Dette er de dynamiske IP-adresser (FALD), der skal tildeles til FOS og andre rolle forekomster, du anvender denne virtuelle netværk. 

Hvis du opretter en VNet, der skal også have en forbindelse til dit lokale netværk, er det vigtigt at markere et område, der ikke overlapper med en af de områder, der bruges til dit lokale netværk. Det er tilfældet, skal du være koordineret med din netværksadministrator. Din netværksadministrator kan være nødvendigt at skærer ud af et område af IP-adresser fra dit lokale netværk adresseområde så du kan bruge til din VNet.

  ![Virtuelt netværk adresse mellemrum side](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **Adresseområde** -, herunder første IP- og adresse antal. Kontrollér, at felterne adresse, du angiver ikke overlapper med en af felterne adresse, du har på dit lokale netværk. I dette eksempel skal bruge vi 10.1.0.0/16 til VNet1.
  - **Tilføj undernet** - som herunder første IP- og adresse antal. Yderligere undernet er ikke påkrævet, men du overveje at oprette et separat undernet for VM'er, der har statisk FALD. Eller du måske har din FOS i et undernet, der er adskilt fra dine andre rolle forekomster.
 
**Klik på markeringen** på nederst til højre på siden og netværket virtuel begynder at oprette. Når den er fuldført, vises "Oprettet" vises under Status på siden netværk.

## <a name="step-3---create-vnet2"></a>Trin 3 – oprette VNet2

Derefter skal du gentage de foregående trin for at oprette en anden VNet. I senere trin, du vil oprette forbindelse to VNets. Du kan referere til [eksempler på indstillinger](#step1) i trin 1. Hvis din VNet allerede findes, behøver du ikke at udføre dette trin. Men, skal du bekræfte, at IP-adresseområder ikke overlapper med en af de andre VNets eller lokale netværk, som du vil oprette forbindelse til.

## <a name="step-4---add-the-local-network-sites"></a>Trin 4 – Føj lokale netværk websteder

Når du opretter en VNet-VNet konfiguration, skal du konfigurere lokale netværk websteder, som vises på siden **Lokalnetværk** i portalen. Azure bruger de indstillinger, der er angivet i hvert lokale netværk websted til at bestemme, hvordan til at dirigere trafik mellem VNets. Du kan se det navn, du vil bruge til at referere til hvert websted lokale netværk. Det er bedst at bruge mere beskrivende tekst, som du vælge værdien fra en rulleliste i efterfølgende trin.

For eksempel VNet1 opretter forbindelse til et lokalt netværk websted, du opretter med navnet "VNet2Local". Indstillingerne for VNet2Local indeholder adressepræfikser for VNet2 og en offentlig IP-adresse til gatewayen VNet2. VNet2 opretter forbindelse til et lokalt netværk-websted, du opretter med navnet "VNet1Local", der indeholder de adressepræfikser for VNet1 og den offentlige IP-adresse til gatewayen VNet1.

### <a name="localnet"></a>Tilføje webstedet lokale netværk VNet1Local

1. Klik på **Ny**i nederste venstre hjørne af skærmen, > **Netværk Services** > **Virtuelt netværk** > **Tilføje lokalnetværk**.

2. Skriv et navn, du vil bruge til at repræsentere det netværk, du vil oprette forbindelse til **navn**, på siden **Angiv detaljerne for dit lokale netværk** . I dette eksempel kan du bruge "VNet1Local" til at referere til IP-adresseområder og gateway for VNet1.

3. Angive en gyldig offentlige IP-adresse for **VPN-enhed IP-adresse (valgfrit)**. Typisk, du vil bruge den faktiske eksterne IP-adresse til en VPN-enhed. VNet-VNet konfigurationer, kan du bruge den offentlige IP-adresse, der er tildelt til gatewayen til din VNet. Men da, at du endnu ikke har oprettet gatewayen, kan du angive en gyldig offentlige IP-adresse som en pladsholder. Undlade ikke – det er ikke valgfrit for denne konfiguration. På et senere tidspunkt, skal du gå tilbage til disse indstillinger og konfigurere dem med de tilsvarende gateway IP-adresser, når Azure genererer den. Klik på pilen for at gå videre til næste skærmbillede.

4. Angiv det IP-adresse-område og den adresse, antal for VNet1 på siden **Angiv siden adresse**. Dette skal svare nøjagtigt til det område, der er konfigureret til VNet1. Azure bruger IP-adresseområder, du angiver for at omdirigere trafikken beregnet til VNet1. Klik på markering for at oprette det lokale netværk.

### <a name="add-the-local-network-site-vnet2local"></a>Tilføje webstedet lokale netværk VNet2Local

Bruge ovenstående trin for at oprette webstedet lokale netværk "VNet2Local". Du kan referere til værdierne i de [eksempelindstillinger for](#step1) i trin 1, hvis det er nødvendigt.

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>Konfigurere hver VNet til at pege på et lokalt netværk

Hver VNet skal pege på det pågældende lokale netværk, du vil omdirigere trafikken til. 

#### <a name="for-vnet1"></a>For VNet1

1. Gå til siden **Konfigurer** for virtuelt netværk **VNet1**. 
2. Vælg "Opret forbindelse til det lokale netværk" under til websted connectivity, og vælg derefter **VNet2Local** som det lokale netværk på rullelisten. 
3. Gemme dine indstillinger.

#### <a name="for-vnet2"></a>For VNet2

1. Gå til siden **Konfigurer** for virtuelt netværk **VNet2**. 
2. Vælg "Opret forbindelse til det lokale netværk" under connectivity til-websted, og derefter vælge **VNet1Local** på rullelisten som det lokale netværk. 
3. Gemme dine indstillinger.

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>Trin 5 – konfigurere en gateway til hver VNet

Konfigurere en dynamisk Routing gateway for hvert virtuelle netværk. Denne konfiguration understøtter ikke statisk Routing gateways. Hvis du bruger VNets, der tidligere er blevet konfigureret, og som allerede har dynamisk Routing gateways, behøver du ikke at udføre dette trin. Hvis din gateways er statisk Routing, skal du slette dem og gendanne dem, som dynamisk Routing gateways. Hvis du sletter en gateway, bliver udgivet tildelt til den offentlige IP-adressen, og du har brug at gå tilbage og omkonfigurere nogen af dit lokale netværk og VPN-enheder med den nye offentlige IP-adresse til ny gateway.

1. Kontrollér, at statuskolonnen for netværket virtuel er **oprettet**på siden **netværk** .

2. Klik på navnet på netværket virtuel i kolonnen **navn** . I dette eksempel skal bruge vi "VNet1".

3. Bemærk, at denne VNet ikke har en gateway, der er konfigureret endnu på siden **Dashboard** . Du får vist denne status, ændre, som du gennemgå trinnene til at konfigurere din gateway.

4. Klik på **Opret Gateway** og **Dynamisk Routing**nederst på siden. Når systemet beder dig om at bekræfte, at du vil den gateway, der er oprettet, skal du klikke på Ja.

    ![Gateway-type](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  

5. Når din gateway opretter, meddelelse om gateway grafik på siden ændres til gul og siger "Oprettelse af gatewayen". Det tager typisk om 30 minutter til at oprette gatewayen.

6. Gentag de samme trin for VNet2. Du behøver ikke at fuldføre, før du begynder at oprette gatewayen til dine andre VNet første VNet gatewayen.

7. Når gatewaystatus ændres til "Tilslutning", vises den offentlige IP-adresse for hver gateway i dashboardet. Notér IP-adressen, der svarer til hver VNet, tager sig ikke for at blande dem. Dette er de IP-adresser, der bruges, når du redigerer dine pladsholder IP-adresser for VPN-enhed for hver lokale netværk.

## <a name="step-6---edit-the-local-network"></a>Trin 6 - Rediger det lokale netværk

1. På siden **Lokalnetværk** skal du klikke på navnet på den lokalnetværk, du vil redigere, og klik derefter på **Rediger** nederst på siden. Skriv IP-adressen for gateway, der svarer til VNet for **VPN-enhed IP-adresse**. For eksempel til VNet1Local, Placer i den gateway IP-adresse, der er tildelt til VNet1. Klik på pilen nederst på siden.

2. Klik på markeringen nederst til højre uden at foretage ændringer på siden **Angiv-adresseområde** .

## <a name="step-7---create-the-vpn-connection"></a>Trin 7 – Opret en VPN-forbindelse

Når alle de forrige trin, der er fuldført, angive tasterne IPsec/IKE allerede delt og oprette forbindelsen. Denne række trin bruger PowerShell og kan ikke konfigureres i portalen. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af Azure PowerShell-cmdlet'er. Sørg for at hente den nyeste version af cmdlet'erne Service Management (SM). 

1. Åbn Windows PowerShell, og log på.

        Add-AzureAccount

2. Vælg det abonnement, som din VNets er placeret.

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"

3. Oprette forbindelserne. Bemærk, at den delte nøgle er præcis det samme i eksempler. Den delte nøgle altid skal svare til.


    VNet1 til VNet2 forbindelse

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    VNet2 til VNet1 forbindelse

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Vent på, forbindelser initialiseret. Når gatewayen er initialiseret, gatewayen ser ud som følgende illustration.

    ![Gatewaystatus for - forbindelse](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  

    [AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>Næste trin

Du kan føje virtuelle maskiner til dine virtuelle netværk. Se den [virtuelle maskiner dokumentation](https://azure.microsoft.com/documentation/services/virtual-machines/) kan finde flere oplysninger.



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 

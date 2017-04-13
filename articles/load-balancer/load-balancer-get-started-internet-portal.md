<properties
   pageTitle="Oprette en forbindelse til internettet justering af belastning i ressourcestyring ved hjælp af portalen Azure | Microsoft Azure"
   description="Lær at oprette en forbindelse til internettet justering af belastning i ressourcestyring ved hjælp af portalen Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="anavinahar"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="annahar" />

# <a name="creating-an-internet-facing-load-balancer-using-the-azure-portal"></a>Oprette en forbindelse til internettet justering af belastning ved hjælp af portalen Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler implementeringsmodel ressourcestyring. Du kan også [se, hvordan du opretter en forbindelse til internettet justering af belastning ved hjælp af klassisk installation](load-balancer-get-started-internet-classic-portal.md)

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Dette viser rækkefølgen af individuelle opgaver skal udføres for at oprette en justering af belastning og beskriver detaljerede oplysninger om, hvad der udføres for at opnå målet.

## <a name="what-is-required-to-create-an-internet-facing-load-balancer"></a>Hvad er påkrævet for at oprette en forbindelse til internettet justering af belastning?

Du har brug at oprette og konfigurere følgende objekter for at installere en justering af belastning.

- IP-konfiguration af front end - indeholder offentlige IP-adresser for indgående netværkstrafik.

- Adresse back end-puljen - indeholder netværksgrænseflader (NIC) for de virtuelle maskiner til at modtage netværkstrafik fra justering af belastning.

- Justering af belastning regler - indeholder regler tilknytte en offentlig port på justering af belastning til port i adressegruppen back end.

- Indgående regler for NAT - indeholder regler tilknytte en offentlig port på justering af belastning til en port til en bestemt virtuel maskine i puljen back end-adresse.

- Sonder - indeholder sundhed sonder bruges til at kontrollere tilgængeligheden af forekomster af virtuelle maskiner i puljen back end-adresse.

Du kan få mere at vide om Indlæs belastningsjusteringstjenesten komponenter med Azure ressourcestyring i [Azure ressourcestyring understøttelse af belastning](load-balancer-arm.md).


## <a name="set-up-a-load-balancer-in-azure-portal"></a>Konfigurere en belastningsjustering Azure-portalen

> [AZURE.IMPORTANT] I dette eksemplet antages, du har et virtuelt netværk kaldet **myVNet**. Referere til at [oprette virtuelle netværk](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) til at gøre dette. Det antages det der er et undernet inden for **myVNet** kaldet **Kg undernet være** og to FOS titlen **web1** og **web2** henholdsvis i det samme sæt tilgængelighed kaldet **myAvailSet** i **myVNet**. Se [dette link](../virtual-machines/virtual-machines-windows-hero-tutorial.md) for at oprette FOS.


1. Gå til portalen Azure fra en browser: [http://portal.azure.com](http://portal.azure.com) og login med din Azure-konto.

2. Vælg **Ny**på den øverste venstre side af skærmen > **netværk** > **justering af belastning.**

3. Skriv et navn til din justering af belastning i bladet **Opret justering af belastning** . Her er den hedder **myLoadBalancer**.

4. Vælg **offentlige**under **Type**.

5. Oprette en ny offentlige IP kaldet **myPublicIP**under **offentlige IP-adresse**.

6. Vælg **myRG**under ressourcegruppe. Vælg derefter en passende **placering**, og klik derefter på **OK**. Justering af belastning begynder derefter til at udrulle og tager et par minutter at fuldføre installationen.

![Opdatere ressourcegruppe af justering af belastning](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-a-back-end-address-pool"></a>Oprette en adresse til back end-puljen

1. Når din justering af belastning har installeret, kan du vælge den fra i dine ressourcer. Under indstillinger for at markere back end-grupper. Skriv et navn til din back end-puljen. Klik derefter på knappen **Tilføj** mod øverst del af bladet, der vises.

2. Klik på **Tilføj en virtuel maskine** i bladet **Tilføj back end-puljen** .  Vælg **Angiv en tilgængelighed** under **tilgængelighed sæt** , og vælg **myAvailSet**. Derefter vælger **vælge virtuelle maskiner** under afsnittet virtuelle maskiner i bladet, og klik på **web1** og **web2**, de to VM'er, der er oprettet for justering af belastning. Sørg for, at begge er blå markeret til venstre, som vist i billedet nedenfor. Klik derefter på **Markér** i pågældende blade, efterfulgt af OK i bladet **Vælg virtuelle computere** og derefter på **OK** i bladet **tilføje back end-puljen** .

    ![Føje til back end-adresse puljen- ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Kontrollér at sikre, at dine meddelelser rulleliste med liste har en opdatering angående gemme Indlæs belastningsjusteringstjenesten back end-puljen ud over at opdatere netværksgrænsefladen til både feltet FOS **web1** og **web2**.


## <a name="create-a-probe-lb-rule-and-nat-rules"></a>Oprette en efterprøvning af af kg regel og NAT regler

1. Oprette en tilstand efterprøvning af af.

    Vælg sonder under indstillinger for din justering af belastning. Klik derefter på **Tilføj** findes i øverst del af bladet.

    Der er to måder at konfigurere en efterprøvning af af: HTTP eller TCP. I dette eksempel vises HTTP, men TCP kan konfigureres på samme måde.
    Opdater de nødvendige oplysninger. Som nævnt, indlæse **myLoadBalancer** balance trafik på Port 80. Du har markeret kurven er HealthProbe.aspx, Interval er 15 sekunder eller beskadiget grænseværdi er 2. Når færdig, skal du klikke på **OK** for at oprette efterprøvning af af.

    Lade markøren hvile på i ikon for at få flere oplysninger om disse individuelle konfigurationer, og hvordan de kan ændres for at imødekomme til dine behov.

    ![Tilføje en efterprøvning af af](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

2. Oprette en regel for indlæsning af belastning.

    Klik på regler i sektionen Indstillinger af din justering af belastning af belastning. Klik på **Tilføj**i bladet nye. Navngiv reglen. Det er her, HTTP. Vælg front end-port og back end-port. Her er 80 valgt i begge. Vælg **back-end kg** som din back end-puljen og den tidligere oprettet **HealthProbe** som efterprøvning af af. Andre konfigurationer kan angives efter dine behov. Klik derefter på OK for at gemme belastningsjustering regel.

    ![Tilføje en regel til justering af belastning](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

3. Oprette indgående NAT regler

    Klik på indgående NAT regler under afsnittet Indstillinger i din justering af belastning. I den nye blade, klik på **Tilføj**. Navngiv indgående NAT reglen. Her er den hedder **inboundNATrule1**. Destinationen skal være den offentlige IP-adresse, der er tidligere har oprettet. Vælg Brugerdefineret under tjeneste, og vælg den protokol, du vil bruge. Her er TCP markeret. Angiv port, 3441, og den destinationsadresse-port, i dette tilfælde 3389. Klik derefter på OK for at gemme denne regel.

    Gentag dette trin for den anden indgående NAT regel kaldet inboundNATrule2 fra port 3442 til destinationen port 3389, når den første regel er oprettet.

    ![Tilføje en indgående NAT-regel](./media/load-balancer-get-started-internet-portal/6-load-balancer-inbound-nat-rules.png)

## <a name="remove-a-load-balancer"></a>Fjerne en justering af belastning

Hvis du vil slette en belastningsjustering, Vælg den justering af belastning du vil fjerne. Klik på **Slet** findes i øverst del af bladet i bladet *Justering af belastning* . Vælg derefter **Ja** når du bliver bedt om.

## <a name="next-steps"></a>Næste trin

[Introduktion til konfiguration af en intern justering af belastning](load-balancer-get-started-ilb-arm-cli.md)

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)

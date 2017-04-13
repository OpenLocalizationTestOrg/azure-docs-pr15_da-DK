<properties 
   pageTitle="StorSimple virtuelle matrix web administration af Brugergrænsefladen | Microsoft Azure"
   description="Beskriver, hvordan til at udføre grundlæggende enhed administrationsopgaver via internettet StorSimple virtuelle matrix brugergrænseflade."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Brug Webbrugergrænsefladen til at administrere din StorSimple virtuelle matrix

![konfiguration af arbejdsgang](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Oversigt

Selvstudier i denne artikel gælder for Microsoft Azure StorSimple virtuelle matrixen (også kaldet StorSimple lokale virtuelle enheden) kører marts 2016 generelt tilgængelig (GA) version. I denne artikel beskrives nogle af de komplekse arbejdsprocesser og administrationsopgaver, der kan udføres på StorSimple virtuelle matrixen. Du kan administrere StorSimple virtuelle matrixen ved hjælp af StorSimple Manager Brugergrænsefladen (kaldet portalen brugergrænseflade) og lokale internettet brugergrænseflade til enheden. I denne artikel fokuserer på de opgaver, du kan udføre ved hjælp af internettet brugergrænseflade.

I denne artikel indeholder følgende selvstudier:

- Få krypteringsnøglen service data
- Foretage fejlfinding web Brugergrænsefladen konfiguration
- Oprette en logfil-pakke
- Lukke eller genstarte enheden

## <a name="get-the-service-data-encryption-key"></a>Få krypteringsnøglen service data

En tjeneste data krypteringsnøgle genereres, når du har registreret dit første enhed med tjenesten StorSimple Manager. Denne tast derefter er påkrævet med tjenesten registrering-tasten til at registrere flere enheder med tjenesten StorSimple Manager.

Hvis du har forlagt din tjeneste data krypteringsnøgle og har du brug for at hente det, kan du udføre følgende trin på det lokale websted Brugergrænsefladen på enheden, der er registreret med din tjeneste.

#### <a name="to-get-the-service-data-encryption-key"></a>At få krypteringsnøglen service data

1. Oprette forbindelse til den lokale web brugergrænseflade. Gå til **konfiguration af** > **skyen indstillinger**.
  

2. Klik på **Hent service data krypteringsnøgle**nederst på siden. En nøgle vises. Kopier og Gem denne tast.
    
    ![få service data krypteringsnøgle 1](./media/storsimple-ova-web-ui-admin/image27.png)
   


## <a name="troubleshoot-web-ui-setup-errors"></a>Foretage fejlfinding web Brugergrænsefladen konfiguration

I visse tilfælde når du konfigurerer enhed via lokale internettet brugergrænseflade, kan du støde på fejl. Hvis du vil diagnosticere og løse disse fejl, kan du køre diagnosticering-test.

#### <a name="to-run-the-diagnostic-tests"></a>Sådan fungerer diagnostiske test

1. Gå til **fejlfinding**i den lokale internettet brugergrænseflade, > **diagnostiske tests**.

    ![Kør diagnostik 1](./media/storsimple-ova-web-ui-admin/image29.png)

2. Nederst på siden skal du klikke på **Kør diagnosticeringstest**. Dette starter test for at diagnosticere mulige problemer med dit netværk, enhed, webtjenesteproxy, klokkeslæt eller skyen indstillinger. Du får besked, at enheden kører test.

3. Når testene, der er udført, vises resultaterne. I følgende eksempel viser resultaterne af diagnostiske tests. Bemærk, at proxyindstillinger web ikke er konfigureret på denne enhed, og derfor kan ikke køres web proxy test. Alle de andre test for netværksindstillinger, DNS-server og tidsindstillinger blev gennemført.

    ![Kør diagnostik 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Oprette en logfil pakke

En log pakke består af de relevante logfiler, som Microsoft Support kan hjælpe med fejlfinding af problemer med en hvilken som helst enhed. I denne udgave, kan en log pakke oprettes via lokale internettet brugergrænseflade.

#### <a name="to-generate-the-log-package"></a>Til at generere pakken log

1. Gå til **fejlfinding**i den lokale internettet brugergrænseflade, > **systemets logfiler**.

    ![generere log pakke 1](./media/storsimple-ova-web-ui-admin/image31.png)

2. Klik på **Opret log installationspakke**nederst på siden. Der oprettes en pakke med systemets logfiler. Det kan tage et par minutter.

    ![generere log pakken 2](./media/storsimple-ova-web-ui-admin/image32.png)

    Du får besked, når pakken er blevet oprettet, og siden opdateres for at angive dato og klokkeslæt, hvor pakken blev oprettet.

    ![generere log pakke 3](./media/storsimple-ova-web-ui-admin/image33.png)

3. Klik på **Hent log pakke**. En ZIP-pakke der, hentes på systemet.

    ![generere log pakke 4](./media/storsimple-ova-web-ui-admin/image34.png)

4. Du kan udpakke pakken hentede log og få vist logfilerne system.

## <a name="shut-down-and-restart-your-device"></a>Luk og genstart enheden

Du kan lukke eller genstarte enheden virtuelle ved hjælp af webdelen lokale brugergrænseflade. Vi anbefaler, at inden du genstarter, bør du enheder eller aktier offline på værten, og klik derefter på enheden. Dette vil minimere enhver mulighed for beskadigelse af data. 

#### <a name="to-shut-down-your-virtual-device"></a>At lukke enheden virtuelle

1. Gå til **vedligeholdelse**i den lokale internettet brugergrænseflade, > **Power indstillinger**.

2. Klik på **Luk computeren**nederst på siden.

    ![enhed lukning 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. Der vises en advarsel om, at en lukning af enheden kan afbryde en hvilken som helst EY, der var i gang, hvilket resulterer i en nedetid. Klik på ikonet Kontrollér ![Markér ikonet](./media/storsimple-ova-web-ui-admin/image3.png).

    ![advarsel om lukning af enhed](./media/storsimple-ova-web-ui-admin/image37.png)

    Du får besked, at lukningen er blevet startet.

    ![enhed lukning i gang](./media/storsimple-ova-web-ui-admin/image38.png)

    Enheden lukkes nu. Hvis du vil starte din enhed, skal du gøre det via Hyper-V Manager.

#### <a name="to-restart-your-virtual-device"></a>Genstarte enheden virtuelle

1. Gå til **vedligeholdelse**i den lokale internettet brugergrænseflade, > **Power indstillinger**.

2. Klik på **Genstart**nederst på siden.

    ![Genstart enheden](./media/storsimple-ova-web-ui-admin/image36.png)

3. Der vises en advarsel om, at genstarte enheden afbryde en hvilken som helst IOs, der var i gang, hvilket resulterer i en nedetid. Klik på ikonet Kontrollér ![Markér ikonet](./media/storsimple-ova-web-ui-admin/image3.png).

    ![advarsel om genstart](./media/storsimple-ova-web-ui-admin/image37.png)

    Du får besked, genstart er blevet startet.

    ![Genstart startet](./media/storsimple-ova-web-ui-admin/image39.png)

    Mens genstart er i gang, mister du forbindelsen til Brugergrænsefladen. Du kan overvåge genstart ved at opdatere Brugergrænsefladen med jævne mellemrum. Alternativt kan du overvåge genstart Enhedsstatus via Hyper-V Manager.

## <a name="next-steps"></a>Næste trin

Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din enhed](storsimple-manager-service-administration.md).

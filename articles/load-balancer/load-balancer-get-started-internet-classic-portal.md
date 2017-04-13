
<properties
   pageTitle="Få en introduktion til en via justering af belastning i klassisk implementeringsmodel ved hjælp af portalen Azure klassisk internettet | Microsoft Azure"
   description="Lær, hvordan du opretter en via justering af belastning i klassisk implementeringsmodel ved hjælp af portalen Azure klassisk internettet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Få en introduktion til en via justering af belastning (klassisk) i portalen Azure klassisk internettet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel. Du kan også [se, hvordan du opretter en via justering af belastning ved hjælp af Azure ressourcestyring internettet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Konfigurere en forbindelse til internettet justering af belastning for virtuelle maskiner

For at indlæse balance netværkstrafik fra internettet på tværs af virtuelle maskiner af en skybaseret tjeneste, skal du oprette et netværksbelastningen sæt. Denne procedure antages det, at du allerede har oprettet de virtuelle maskiner, og at de er alle inden for samme skytjenesten.

**Konfigurere et netværksbelastningen sæt til virtuelle maskiner**

1. Klik på **virtuelle maskiner**i Azure klassisk portalen, og klik derefter på navnet på en virtuel maskine i sættet netværksbelastningen.

2. Klik på **slutpunkter**, og klik derefter på **Tilføj**.

3. På siden **Tilføj et slutpunkt til en virtuel maskine** , skal du klikke på den højre pil.

4. På siden **Angiv detaljerne i slutpunktet** :

    * Skriv et navn til slutpunktet i **navn**, eller Vælg navnet på listen over foruddefinerede slutpunkter for almindelige protokoller.
    * Vælg den protokol, der kræves, før typen af slutpunkt, enten TCP eller UDP, i **protokol**, efter behov.
    * I **Port offentlige og Private Port**skal du skrive de portnumre, du vil virtuelt bruge, efter behov. Du kan bruge private port og firewallregler på den virtuelle maskine til at omdirigere trafik på en måde, der er relevante for dit program. Private port kan være den samme som den offentlige port. For eksempel for et slutpunkt for (HTTP) webtrafik, kan du tildele port 80 til både den offentlige og private port.

5. Vælg **Opret et Indlæs afstemmes sæt**, og klik derefter på den højre pil.

6. Skriv et navn til sættet Indlæs afstemmes på siden **Konfigurer sættet Indlæs afstemmes** , og derefter tildele værdierne for efterprøvning af af funktionsmåden for Azure belastning. Den belastning anvender sonder til at bestemme, om virtuelle maskiner i sættet netværksbelastningen er tilgængelige til at modtage indgående trafik.

7. Klik på markeringen i afkrydsningsfeltet for at oprette det netværksbelastningen slutpunkt. Du får vist **Ja** i kolonnen **netværksbelastningen sætnavn** på siden **slutpunkter** for den virtuelle maskine.

8. I portalen, klik på **virtuelle maskiner**, klik på navnet på en ekstra virtuel maskine i sættet netværksbelastningen, skal du klikke på **slutpunkter**og klik derefter på **Tilføj**.

9. Klik på **Tilføj slutpunkt til et eksisterende Indlæs afstemmes**på siden **Tilføj et slutpunkt til en virtuel maskine** , Vælg navnet på det Indlæs afstemmes sæt, og klik på højre pil.

10. Skriv et navn til slutpunktet på siden **Angiv detaljerne for slutpunktet** , og klik derefter på markeringen.

Gentag trin 8-10 for de ekstra virtuelle maskiner i dialogboksen Indlæs afstemmes Angiv.



## <a name="next-steps"></a>Næste trin

[Introduktion til konfiguration af en intern justering af belastning](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)


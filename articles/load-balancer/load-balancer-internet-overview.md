
<properties
   pageTitle="Oversigt over belastningsjusteringstjenesten via internettet | Microsoft Azure "
   description="Oversigt til via justering af belastning og dens funktioner internettet. Hvordan fungerer det belastningsjustering til Azure ved hjælp af virtuelle maskiner og skytjenester."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="internet-facing-load-balancer-overview"></a>Oversigt over internettet modstående Indlæs belastningsjusteringstjenesten

Azure justering af belastning knytter offentlige IP-adresse og port antallet af indgående trafik til private IP-adresse og port antallet af den virtuelle maskine og omvendt til svar trafik fra den virtuelle maskine. Regler for indlæsning af justering af belastning gør det muligt at distribuere bestemte typer trafik mellem flere forskellige virtuelle computere eller tjenester. Du kan for eksempel spredes indlæsning af web anmodning trafik på tværs af flere-servere eller web roller.

For en skybaseret tjeneste, der indeholder forekomster af web roller eller arbejder roller, kan du angive et offentligt slutpunkt i service-definitionsfil (.csdef).

Når du har flere forekomster af rolle til en web eller arbejder rolle installation, bliver justering af belastning konfiguration for den og filen _servicedefinition.csdef_ indeholder konfigurationen af slutpunktet. Den måde at føje forekomster til skyen installationen ændres forekomst antallet på konfigurationsfil service (.csfg).

I følgende figur vises et Indlæs afstemmes slutpunkt for krypterede webtrafik, der deles af tre virtuelle maskiner til den offentlige og private TCP-port af 443. Disse tre virtuelle maskiner er i et Indlæs afstemmes sæt.

![offentlige Indlæs belastningsjusteringstjenesten eksempel](./media/load-balancer-internet-overview/IC727496.png))

Figur 1 - indlæsning afstemmes slutpunkt for krypterede webtrafik

Når internetklienter sende webside anmodninger til den offentlige IP-adresse skytjenesten på TCP-port 443, distribuerer Azure belastning anmodninger mellem de tre virtuelle maskiner i dialogboksen Indlæs afstemmes Angiv. Du kan finde flere oplysninger om indlæsning belastningsjusteringstjenesten algoritmer, kan du se [indlæse belastningsjusteringstjenesten oversigtssiden](load-balancer-overview.md#load-balancer-features).

Som standard distribuerer Azure justering af belastning netværkstrafik ligeligt mellem flere virtuelt forekomster. Du kan også konfigurere session forbindelse, kan finde flere oplysninger, se [indlæse belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Næste trin

Få mere at vide om [interne justering af belastning](load-balancer-internal-overview.md) for bedre at forstå, hvilke justering af belastning er en bedre egnet til skyen installationen.

Du kan også [få en introduktion til en via justering af belastning internettet](load-balancer-get-started-internet-arm-ps.md) og konfigurere, hvilken type [fordeling tilstand](load-balancer-distribution-mode.md) til en bestemt Indlæs belastningsjusteringstjenesten netværk trafik funktionsmåde.

Hvis dit program skal beholde forbindelser Live for servere bag en belastningsjustering, kan du forstå mere om [inaktive TCP timeoutindstillinger for belastningsjustering](load-balancer-tcp-idle-timeout.md). Det hjælper med at få mere for at vide om inaktiv forbindelse funktionsmåde, når du bruger Azure justering af belastning.

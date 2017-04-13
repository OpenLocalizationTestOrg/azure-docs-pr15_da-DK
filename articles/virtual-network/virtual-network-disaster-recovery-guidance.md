<properties
    pageTitle="Hvad du skal gøre i tilfælde af en Azure tjenesteforstyrrelse Azure virtuelle netværk, som påvirker beskyttelsen | Microsoft Azure"
    description="Få mere at vide, hvad du skal gøre i tilfælde af en Azure tjenesteforstyrrelse Azure virtuelle netværk, som påvirker beskyttelsen."
    services="virtual-network"
    documentationCenter=""
    authors="NarayanAnnamalai"
    manager="jefco"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="virtual-network"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="narayan;aglick"/>

#<a name="virtual-network--business-continuity"></a>Virtuelt netværk – Forretningskontinuitet

##<a name="overview"></a>Oversigt

Et virtuelt netværk (VNet) er en logisk repræsentation af dit netværk i skyen. Det gør det muligt at definere dine egne private IP-adresseområder og segment skal tildeles netværket i undernet. VNets fungerer som en sikkerhed og rettighedsadministration rammen til at hoste din Beregn ressourcer som virtuelle Azure-computere og Cloud Services (web/arbejder roller). En VNet kan direkte privat IP-kommunikation mellem de ressourcer, der er hostet i den. Et virtuelt netværk kan også sammenkædes med et lokalt netværk via en af indstillingerne hybrid som en VPN-Gateway eller ExpressRoute.
 
Der oprettes en VNet er omfattet af et område. Du kan oprette VNets med samme adresseområde i to forskellige områder (det vil sige os Øst og OS vest, men kan ikke oprette forbindelse dem til hinanden direkte). 

##<a name="business-continuity"></a>Forretningskontinuitet

Der kan være flere forskellige måder, dit program kunne blive afbrudt. Et bestemt område kan være helt beskåret på grund af neutralt nedbrud eller delvise nedbrud på grund af en fejl i forbindelse med flere enheder og tjenester. Tjenesten VNet påvirkningen er anderledes i hver af disse situationer.

**Sp: Hvad skal du gøre i tilfælde af en afbrydelse til en hel område? Det vil sige, hvis et område er helt skæringsdatoen på grund af neutralt nedbrud? Hvad sker der med det virtuelle netværk, der er hostet i området?**

SV: det virtuelle netværk og ressourcer i det pågældende område forbliver ikke tilgængelig under tidspunktet for tjenesteforstyrrelse.

![Enkel virtuelle netværksdiagram](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Sp: Hvad kan jeg oprette igen det samme virtuelle netværk i et andet område?**

SV: virtuelt netværk (VNet) er relativt lette ressource. Du kan aktivere Azure API'er for at oprette en VNet med det samme adresseområde i et andet område. For at genoprette det samme miljø, som findes i det pågældende område, er det nødvendigt at ringe API til at udrulle dine Cloud Services (web/arbejder roller) og virtuelle maskiner, du havde igen. Du får også rotation af en VPN-Gateway og oprette forbindelse til dit lokale netværk, hvis du havde lokal forbindelse (f.eks som i en hybridinstallation).

Instruktioner til oprettelse af en VNet findes [her](./virtual-networks-create-vnet-arm-pportal.md). 

**Sp: kan en kopi af en VNet i et bestemt område oprettes igen i et andet område forvejen?**

SV: Ja, kan du oprette to VNets ved hjælp af de samme privat IP-adresseområder og ressourcer i to forskellige områder forvejen. Hvis en kunde vært for tjenester i VNet via internettet, kan de har konfigurere trafik Manager geografisk dirigere trafik til det område, der er aktiv. Men en kunde ikke oprette forbindelse to VNets med det samme adresseområde til deres lokale netværk som den medfører routing problemer. En kunde kan oprette forbindelse på andre VNet i det område, der er tilgængelige med identiske adresseområde til deres lokale netværk på tidspunktet for nedbrud og tab af en VNet i ét område.

Instruktioner til oprettelse af en VNet findes [her](./virtual-networks-create-vnet-arm-pportal.md).

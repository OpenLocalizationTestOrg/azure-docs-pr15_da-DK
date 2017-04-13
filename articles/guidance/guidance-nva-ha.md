<properties
   pageTitle="Implementere virtuelle anvendelser i høj tilgængelighed | Microsoft Azure"
   description="Sådan installerer netværk virtuelle anvendelser i høj tilgængelighed."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="telmos"/>

# <a name="deploying-virtual-appliances-in-high-availability"></a>Implementere virtuelle anvendelser i høj tilgængelighed

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskrives en række metoder til implementering af netværk virtuelle anvendelser (NVAs) på en meget tilgængelig måde. Før du fortsætter, Sørg for, at du forstår hvordan [brugerdefinerede omdirigerer (UDR)] [ udr-overview] og [justering af belastning] [ lb-overview] arbejde i Azure.

Du kan bruge forskellige NVAs, der er tilgængelige i Azure marketplace du udvider funktionaliteten i Azure på samme måde, du bruger anvendelser i dit lokale datacenter. I følgende figur vises et eksempel på installation af en [enkelt for] [ nva-scenario] som en firewall maskinen. 

![[0]][0]

Selvom den foregående installation fungerer, introducerer en ét afsnit. Hvis virtuelle maskinen mislykkes, flyder nogen trafik. Hvis du vil løse dette problem, skal du bruge flere NVAs. Der kræver dog også andre indstillinger og ressourcer, der skal bruges afhængigt af dine behov.

Du kan bruge en af følgende løsninger til at udrulle miljø med en høj tilgængelighed for.

|Løsning|Fordele|Overvejelser i forbindelse med|
|---|---|---|
|Vandindtrængen med lag 7 virtuelle udstyr|Alle noder er aktive|Kræver en for, kan afbryde forbindelser og bruge SNAT<br/>Kræver en separat række NVAs til trafik kommer fra internettet og fra Azure<br/>Kan kun bruges til trafik kommer uden for Azure|
|Vandindtrængen-udgangspunkt med lag 7 virtuelle anvendelser|Alle noder er aktive<br/>Stand til at håndtere trafik, der stammer i Azure |Kræver en for, kan afbryde forbindelser og bruge SNAT<br/>Kræver en separat række NVAs til trafik kommer fra internettet og fra Azure|
|PIP UDR Skift|Enkelt sæt af NVAs for al trafik<br/>Kan håndtere al trafik (ingen begrænsning på portregler)|Aktiv / passiv<br/>Kræver en failover-proces|

## <a name="ingress-with-layer-7-virtual-appliances"></a>Vandindtrængen med lag 7 virtuelle udstyr
Du kan bruge et sæt af NVAs bag en Azure belastningsjustering til at levere forbindelse til Azure arbejdsbelastninger bag et lille sæt serversiden porte (såsom HTTP og HTTPS). I følgende figur vises fremhæver Sådan giver høj tilgængelighed i dette scenarie på niveauet for.

![[1]][1]

I dette scenarie skal skal netværk maskinen virtuel bruges afbryde alle forbindelser og overføre dem til arbejdsbelastningen undernettet. Arbejdsbelastningen virtuelle maskiner (VM'er) reagere på for de modtaget en anmodning om fra, og trafik flyder uden problemer. 

## <a name="ingress-egress-with-layer-7-virtual-appliances"></a>Vandindtrængen-udgangspunkt med lag 7 virtuelle udstyr
Du kan udvide den foregående arkitektur og tilføje et ekstra sæt NVAs til at håndtere trafik kommer fra Azure skal håndteres af NVAs, som vist i følgende figur:

![[2]][2]

I dette scenarie skal dirigeres al trafik kommer i Azure til en intern justering af belastning, der distribuerer Indlæs mellem et andet sæt NVAs. Disse NVAs dirigere trafik til internettet via deres individuelle offentlige IP-adresser. 

## <a name="pip-udr-switch"></a>PIP UDR Skift
Du kan undgå at oprette flere for stakke ved hjælp af to NVAs i aktiv / passiv tilstand. I dette scenarie skal kan du skifte den offentlige IP-adresse (PIP) og brugerdefinerede omdirigerer (UDRs), når du holder op med den aktive node.  

![[3]][3]

Dette scenarie ligner scenariet enkelt for. Den eneste forskel er, at PIP og UDRs skal ændres for at skifte trafik mellem NVAs. Disse ændringer kan udføres manuelt, eller du kan også automatisere dem. Hvis du vil automatisere, kan du installere et program til begge NVAs, der kontrollerer, om tilstanden for den aktive node. Når den aktive node er nede, kan dit program ændre PIP og UDRs kædes sammen med den passive node.

En mulige implementering af denne løsning er at bruge en [ZooKeeper] [ zookeeper] daemon på NVAs til at håndtere fyldtegn valg (beslutte, hvilke node er den aktive node). Når en leder er valgt, ringer op til Azure REST-API til at fjerne PIP fra noden mislykkedes, og vedhæft den til lederen. Det skal også ændre UDRs til at pege på den nye fyldtegn privat IP-adresse.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du kan [implementere en DMZ mellem Azure og dit lokale datacenter] [ dmz-on-prem] ved hjælp af lag-7 NVAs.
- Lær, hvordan du kan [implementere en DMZ mellem Azure og internettet] [ dmz-internet] ved hjælp af lag-7 NVAs.

<!-- links -->
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[lb-overview]: ../load-balancer/load-balancer-overview.md
[zookeeper]: https://zookeeper.apache.org/
[nva-scenario]: ../virtual-network/virtual-network-scenario-udr-gw-nva.md
[dmz-on-prem]: guidance-iaas-ra-secure-vnet-hybrid.md
[dmz-internet]: guidance-iaas-ra-secure-vnet-dmz.md

<!-- images -->
[0]: ./media/guidance-nva-ha/single-nva.png "Enkelt for arkitektur"
[1]: ./media/guidance-nva-ha/l7-ingress.png "Lag 7 vandindtrængen"
[2]: ./media/guidance-nva-ha/l7-ingress-egress.png "Lag 7 indgangs- og udgangspunkt"
[3]: ./media/guidance-nva-ha/active-passive.png "Aktiv / passiv klynge"
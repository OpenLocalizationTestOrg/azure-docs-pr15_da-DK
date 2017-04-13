<properties
   pageTitle="Azure sundhed Ressourceoversigt | Microsoft Azure"
   description="Oversigt over Azure ressource tilstand"
   services="Resource health"
   documentationCenter="dev-center-name"
   authors="BernardoAMunoz"
   manager=""
   editor=""/>

<tags
   ms.service="resource-health"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Supportability"
   ms.date="06/01/2016"
   ms.author="BernardoAMunoz"/>

# <a name="azure-resource-health-overview"></a>Azure Ressourceoversigt tilstand

Azure ressource tilstand er en tjeneste, der viser tilstanden for individuelle Azure ressourcer og giver handlings vejledning for at foretage fejlfinding af problemer. I et skybaseret miljø, hvor det er ikke muligt at få direkte adgang til servere eller infrastrukturelementer, er målet for ressource sundhed at reducere den tid, kunder bruger om fejlfinding, især at reducere den tid, der afgøre Hvis i roden af problemet fastlægger i programmet, eller hvis det skyldes en begivenhed i Azure-platformen.

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>Det betragtes som en ressource, og hvordan ved ressource sundhed bestemmer, om ressourcen er sund eller ej? 
En ressource er en brugerforekomst af, der er oprettet af en ressourcetype, der leveres af en tjeneste, for eksempel: en virtuel maskine, en WebApp eller en SQL-database. 

Ressource-tilstand er afhængig af signaler udsendes af ressourcen og/eller tjenesten for at afgøre, om en ressource er sund eller ej. Det er vigtigt at lægge mærke, der aktuelt ressource sundhed kun konti til tilstanden for en bestemt ressource indtaste og ikke overveje andre elementer, der kan bidrage til den overordnede sundhed. For eksempel ved rapportering af status for en virtuel maskine, kun Beregn del af infrastrukturen betragtes, det vil sige problemer i netværket ikke vises i ressource sundhed, medmindre der er et angivet service afbrydelse vil i så fald det være fået gennem banner øverst i bladet. Du kan finde flere oplysninger om tjenesten afbrydelse tilbydes senere i denne artikel. 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>Hvordan adskiller sig fra Dashboard over tjenestetilstand ressource tilstand?

De oplysninger, som ressource tilstand er mere detaljeret end hvad leveres af Tilstandsdashboardet i tjenesten. Mens SHD kommunikerer hændelser, der påvirker tilgængeligheden af en tjeneste i et område, ressource sundhed viser oplysninger, der er relevante for en bestemt ressource, f.eks. det vil vise hændelser, der påvirker tilgængeligheden af en virtuel maskine, en web-app eller en SQL-database. Eksempelvis hvis en node genstarter uventet, vil kunder, hvis virtuelle maskiner kørte på noden kunne få årsagen hvorfor deres VM er ikke tilgængelig for et bestemt tidsrum.   

## <a name="how-to-access-resource-health"></a>Sådan får du adgang ressource tilstand
For de tjenester, der er tilgængelige via ressource tilstand er der 2 måder at få adgang til ressourcen tilstand.

### <a name="azure-portal"></a>Azure-portalen
Bladet ressource tilstand i Azure-portalen indeholder detaljerede oplysninger om tilstanden på ressourcen, samt anbefalede handlinger, der varierer afhængigt af den aktuelle tilstand for ressourcen. Denne blade yder den bedste oplevelse ved forespørgsler ressource sundhed, som det letter adgang til andre ressourcer i portalen. Som nævnt før, er sæt af anbefalede handlinger i bladet ressource tilstand varierer afhængigt af den aktuelle tilstand er:

* Sund ressourcer: da der blev fundet ikke-problem, som påvirke tilstanden for ressourcen handlingerne, der fokuserer på din hjælp til fejlfinding i forbindelse med processen. For eksempel indeholder den direkte adgang til bladet fejlfinding, som forklarer, hvordan du kan løse de mest almindelige problemer med kunder ansigt.
* Beskadiget ressource: For problemer, der er forårsaget af Azure, vises bladet handlinger Microsoft tager (eller har truffet) til at gendanne ressourcen. For problemer, der er forårsaget af bruger startet handlinger, blade opretter en liste over handlinger kunder kan tage så løser problemet, og gendanne ressourcen.  

Når du har logget på portalen Azure, er der to metoder til at få adgang til bladet ressource tilstand: 

###<a name="open-the-resource-blade"></a>Åbn bladet ressource
Åbn bladet ressource til en given ressource. Klik på ressourcen tilstand til at åbne bladet ressource tilstand på bladet indstillinger, der åbnes ved siden af bladet ressource. 

![Ressource sundhed blade](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>Hjælp og Support blade
Åbn bladet Hjælp og Support ved at klikke på spørgsmålstegnet i øverste højre hjørne og derefter vælge Hjælp + Support. 

**Fra den øverste navigationslinje**

![Hjælp + support](./media/resource-health-overview/HelpAndSupport.png)

Klik på feltet åbnet bladet ressource sundhed abonnement, vises alle ressourcerne i dit abonnement. Ud for hver enkelt ressource er der et ikon, der angiver sin tilstand. At klikke på hver ressource åbnes bladet ressource tilstand.

**Ressource sundhed felt**

![Ressource sundhed felt](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>Hvad betyder status ressource tilstand?
Der er 4 forskellige sundhed statusser, som du kan se for ressourcen.

### <a name="available"></a>Tilgængelige
Tjenesten er ikke registreret problemer i den platform, som kan, som påvirker beskyttelsen tilgængeligheden af ressourcen. Dette er angivet med et ikon for grønt flueben. 

![Ressource er tilgængelig](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Ikke tilgængelig

I dette tilfælde har tjenesten fundet en igangværende problem i den platform, som, som påvirker beskyttelsen tilgængeligheden af denne ressource, for eksempel noden hvor VM kørte uventet genstartes. Dette er angivet med en rød advarselsikon. Yderligere oplysninger om problemet er angivet i den midterste del af bladet, herunder: 

1.  Hvilke handlinger Microsoft tager gendanne ressourcen 
2.  En detaljeret tidslinje af problemet, herunder forventede tid
3.  En liste over anbefalede handlinger for brugere 

![Ressource er tilgængelig](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>Ikke tilgængelig-kunde, der er startet
Ressourcen er ikke tilgængelig på grund af en kunde anmodning som stoppe en ressource eller anmoder om en genstart. Dette er angivet med en blå informativ ikon. 

![Ressource er tilgængelig på grund af brugeren en startet handling](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>Ukendt
Tjenesten har ikke modtaget oplysninger om denne ressource til mere end fem minutter. Dette er angivet med et ikon for grå spørgsmålstegn. 

Det er vigtigt at være opmærksom på, at dette ikke er en endelig angivelse, der er noget galt med en ressource, så kunderne skal følge disse anbefalinger:

* Hvis ressourcen, der kører som forventet, men dens tilstand er indstillet til ukendt i ressource tilstand, der er ingen problemer, og du kan forvente status for ressourcen til at opdatere til sund efter et par minutter.
* Hvis der er problemer med adgang til ressourcen og dens tilstand er angivet til ukendt i ressource tilstand, det kan skal være en tidlig angivelse, der kan være et problem og yderligere undersøgelser udføres indtil tilstanden er opdateret med sund eller beskadiget

![Ressource-tilstand er ukendt](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>Tjenesten, som påvirker beskyttelsen begivenheder
Hvis ressourcen kan blive påvirket af en igangværende tjeneste, som påvirker beskyttelsen begivenhed, vises et banner øverst i bladet ressource tilstand. At klikke på banneret åbnes bladet overvågningshændelser, der vises yderligere oplysninger om afbrydelse.

![Ressource tilstand kan blive påvirket af en SIE](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>Hvad skal jeg ellers vide om ressource tilstand?

### <a name="signal-latency"></a>Signal ventetid
De signaler, feed ressource tilstand, kan være op til 15 min forsinket, hvilket kan medføre uoverensstemmelser mellem den aktuelle tilstand status for ressourcen og den faktiske tilgængelighed. Det er vigtigt at huske dette på, når det kan hjælpe med at undgå unødvendig tid behandlende eventuelle problemer. 

### <a name="special-case-for-sql"></a>Særlige tilfælde for SQL 
Ressource tilstandsrapporter status for SQL-database ikke SQL server. Gå dette giver et mere realistisk sundhed billede, kræver det, at flere komponenter og -tjenester tages hensyn til at bestemme tilstanden af databasen. Det aktuelle signal er afhængig af logon til den database, hvilket betyder, at for databaser, der modtager normalt logon (som indeholder blandt andet modtager udførelse af forespørgsler) tilstanden regelmæssig vises. Hvis databasen ikke har været åbnet i en periode på 10 minutter eller mere, flyttes den til den ukendte tilstand. Det betyder ikke, at databasen er tilgængeligt, skal du bare, ingen signal er blevet udledes, fordi ingen logon er blevet udført. Oprette forbindelse til databasen og kører en forespørgsel udsender signaler behov for at finde ud af, og opdatere tilstand af databasen.

## <a name="feedback"></a>Feedback
Vi er altid åbne feedback og forslag! Kan du sende os din [forslag](https://feedback.azure.com/forums/266794-support-feedback). Desuden kan du deltage med os via [Twitter-](https://twitter.com/azuresupport) eller [MSDN-foraene](https://social.msdn.microsoft.com/Forums/azure).

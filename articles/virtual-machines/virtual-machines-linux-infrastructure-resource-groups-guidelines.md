<properties
    pageTitle="Ressource grupperer retningslinjer | Microsoft Azure"
    description="Få mere at vide om de vigtige design og implementering retningslinjer for implementering af grupper i Azure infrastrukturtjenester."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="azure-resource-group-guidelines"></a>Azure ressource gruppe retningslinjer

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

I denne artikel fokuserer på forstå, hvordan logisk udvidelse dit miljø og gruppere alle komponenter i grupper.


## <a name="implementation-guidelines-for-resource-groups"></a>Implementering retningslinjer for grupper

Beslutninger:

- Vil du udvidelse ressourcegrupper ved core infrastrukturkomponenter eller ved at fuldført programmet installation?
- Har du brug at begrænse adgang til ressourcegrupper ved hjælp af rollebaseret adgangskontrol?

Opgaver:

- Definere, hvad core infrastrukturkomponenter og dedikeret ressourcegrupper, du vil bruge.
- Gennemgå, hvordan du implementere ressourcestyring skabeloner til ensartet genskabes installationer.
- Definere hvilke bruger access roller, du har brug for til at styre adgang til grupper.
- Oprette ressourcegrupper ved hjælp af din navngivningskonvention. Du kan bruge Azure CLI eller portal.


## <a name="resource-groups"></a>Ressourcegrupper

I Azure gruppere du logisk relaterede ressourcer som lager konti, virtuelle netværk og virtuelle maskiner (VM'er) til at installere, administrere og vedligeholde dem som et enkelt objekt. Denne fremgangsmåde gør det nemmere at implementere programmer, mens at holde alle de relaterede ressourcer fra et management perspektiv eller for at give andre adgang til den pågældende gruppe af ressourcer. Du kan læse [Azure ressourcestyring oversigt](../azure-resource-manager/resource-group-overview.md)for en mere omfattende overblik over grupper.

En nøglefunktionerne til ressourcegrupper er muligheden for at opbygge dit miljø ved hjælp af en JSON-fil, der erklærer storage, netværk, og beregne ressourcer. Du kan også angive alle relaterede brugerdefinerede scripts eller konfigureringer for at anvende. Ved hjælp af skabelonerne JSON, kan du oprette ensartede og genskabes installationer til dine programmer. Denne metode kan du oprette et miljø er under udvikling og derefter bruge den samme skabelon til at oprette et produktionsmiljø eller omvendt. Læs [gennemgangen skabelon](../resource-manager-template-walkthrough.md) , der fører dig gennem hvert trin i opbygge en JSON-skabelon til en bedre forståelse om at bruge skabeloner.

Der er to forskellige metoder, du kan udføre, når du designer dit miljø med ressourcegrupper:

- Ressourcegrupper for hvert Programinstallation, der kombinerer lagerplads konti, virtuelle netværk og undernet, FOS, indlæse balancere osv.
- Centralt ressourcegrupper, der indeholder dine core virtuelle netværk og undernet eller lagerplads konti. Dine programmer er derefter i deres egen ressourcegrupper, der kun indeholder FOS, Indlæs balancere, netværksgrænseflader osv.

Når du skala, oprette centraliserede grupper til virtuelle netværk og undernet er det nemt at opbygge netværksforbindelser tværs det lokale miljø for hybride connectivity indstillinger. Alternativ metode egner sig for hvert program have sin egen virtuelle netværk, der kræver konfiguration og vedligeholdelse. [Rollebaseret adgangskontrol](../active-directory/role-based-access-control-what-is.md) giver en detaljeret metode til at styre adgangen til grupper. Du kan styre de brugere, der kan få adgang til disse ressourcer til fremstilling programmer, eller for ressourcerne, core infrastruktur kan du begrænse kun infrastruktur teknikere til at arbejde med dem. Dit program ejere kun har adgang til programkomponenter inden for deres ressourcegruppe og ikke core Azure infrastruktur i dit miljø. Når du designer dit miljø, skal du overveje de brugere, der kræver adgang til ressourcerne og designe dit ressourcegrupper i overensstemmelse hermed. 


## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 
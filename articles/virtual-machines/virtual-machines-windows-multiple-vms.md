<properties
    pageTitle="Oprette flere virtuelle maskiner | Microsoft Azure"
    description="Indstillinger for oprettelse af flere virtuelle maskiner i Windows"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="guybo"/>

# <a name="create-multiple-azure-virtual-machines"></a>Oprette flere Azure virtuelle maskiner

Der er mange situationer, hvor du vil oprette et stort antal lignende virtuelle maskiner (VM'er). Eksempler kan nævnes high-performance computing (HPC), store dataanalyse, SVG og ofte uden status midterste niveau eller back end-servere (såsom webservers) og fordelt databaser.

I denne artikel beskrives de tilgængelige indstillinger til at oprette flere FOS i Azure. Disse indstillinger kom ud over de simple tilfælde, hvor du manuelt oprette en række FOS. Du kan oprette mange FOS ved skalere processer, du normalt bruger ikke godt, hvis du vil oprette mere end en håndfuld FOS.

En metode til at oprette mange lignende FOS er at bruge Azure ressourcestyring konstruktion af _ressource løkker_.

## <a name="resource-loops"></a>Ressource løkker

Ressource løkker er en syntaksfejl korte i Azure ressourcestyring skabeloner. Ressource løkker kan oprette et sæt på samme måde konfigurerede ressourcer i en løkke. Du kan bruge ressource løkker til at oprette flere lagerplads konti, netværksgrænseflader eller virtuelle maskiner. Se [oprette FOS i tilgængelighed angiver ved hjælp af ressource løkker](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/)kan finde flere oplysninger om ressource løkker.

## <a name="challenges-of-scale"></a>Udfordringer i forbindelse med skala

Selvom ressource løkker gør det nemmere at udvidelse en skyinfrastruktur skaleres og frembringe mere præcis skabeloner, forbliver visse udfordringer. Hvis du bruger en ressource løkke til at oprette 100 virtuelle maskiner, skal du eksempelvis koordinere netværkscontrollere (NIC) med tilsvarende FOS og lagerplads konti. Da antallet af FOS der sandsynligvis er forskelligt fra antallet af lagerplads konti, skal du skal håndtere anden ressource løkke størrelser for. Disse er solvable problemer, men kompleksitet øges markant med målestoksforhold.

En anden udfordring opstår, når du har brug for en infrastruktur, der skaleres elastically. For eksempel du måske en Autoskalering infrastruktur, der automatisk forøger eller formindsker antallet af FOS som svar på arbejdsbelastningen. FOS ikke med en integreret funktion til varierer i tal (Skaler ud og skalering i). Hvis du skalere i ved at fjerne FOS, er det svært at garantere høj tilgængelighed ved at kontrollere, at FOS afstemmes på tværs af opdatering og fejl domæner.

Til sidst, når du bruger en ressource løkke, flere opkald til at oprette ressourcer gå til den underliggende struktur. Når flere opkald opretter ensartede ressourcer, har Azure implicit mulighed for at forbedre på dette design og optimere installation pålideligheden og ydeevnen. Dette er hvor _virtuelt skala angiver_ kommer.

## <a name="virtual-machine-scale-sets"></a>Virtuelt skala sæt

Virtuelt skala sæt er en Azure beregne ressource til at installere og administrere et sæt af identiske FOS. Konfigureret det samme, VM målestoksforhold, der er nemt at skalere i og skalere ud med alle FOS. Du skal blot ændre antallet af FOS i dialogboksen Angiv. Du kan også konfigurere VM skala sæt til Autoskalering baseret på kravene til arbejdsbelastningen.

For programmer, der har brug for at skalere Beregn ressourcer ind og ud, afstemmes skala handlinger implicit på tværs af fejl og opdatering af domæner.

I stedet for korrelere flere ressourcer som netværkskort og FOS, har et VM skala sæt netværk, lagring, virtuelt og lokalnummer egenskaber, som du kan konfigurere centralt.

Referere til den [virtuelle maskine skala angiver produkt side](https://azure.microsoft.com/services/virtual-machine-scale-sets/)for at få en introduktion til VM skala sæt. Gå til den [virtuelle maskiner skala angiver dokumentation](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)for mere detaljerede oplysninger.

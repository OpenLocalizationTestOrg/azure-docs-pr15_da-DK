<properties
   pageTitle="Oversigt over operationer Management pakke (OMS) | Microsoft Azure"
   description="Microsoft Operations Management pakke (OMS) er Microsofts skybaseret IT management-løsning, der hjælper dig med at administrere og beskytte dine lokale og infrastruktur i skyen.  I denne artikel identificerer de forskellige tjenester, der er inkluderet i OMS og indeholder links til deres detaljerede indhold."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="bwren" />

# <a name="what-is-operations-management-suite-oms"></a>Hvad er handlinger Management pakke (OMS)?

Microsoft Operations Management pakke (OMS) er Microsofts skybaseret IT management-løsning, der hjælper dig med at administrere og beskytte dine lokale og infrastruktur i skyen.  Da OMS implementeres som en skybaseret tjeneste, kan du have det op at køre hurtigt med minimale investering i infrastrukturtjenester.  Nye funktioner er leveret automatisk, så du løbende vedligeholdelse og opgradere omkostninger.

Ud over at give værdifulde services på sin egen, integreres OMS med System Center komponenter som System Center Operations Manager til at udvide dine eksisterende administration af investeringer i skyen.  System Center og OMS kan arbejde sammen for at give en fuld hybrid management oplevelse.

De følgende afsnit indeholder en højt niveau beskrivelse af anden værdi områderne OMS og de tjenester, der implementerer dem.  Du kan referere til OMS arkitektur for en oversigt over de forskellige OMS komponenter før gennemgå den detaljerede dokumentation for hver.


## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Indsigt og analyse](media/operations-management-suite-overview/icon-insight-analytics.png) Indsigt og analyse

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) hjælper dig med at indsamle og koordinere, søge og reagere på log og ydeevne data, der genereres af operativsystemer og programmer. Det giver dig realtid funktionsdygtige indsigt ved hjælp af integreret søgning og brugerdefinerede dashboards til at analysere let millioner af poster på tværs af alle dine arbejdsmængder og servere uanset deres fysiske placering.

Du kan nemt tilføje løsninger Log Analytics, der definerer data, der indsamles, og logikken for analysen.  Løsninger, der kan indeholde flere funktioner, der leveres automatisk til supportmedarbejdere med minimale eller ingen konfiguration.  Ud over at bruge analyseværktøjerne leveres af individuelle løsninger, kan du udføre brugerdefinerede søgninger på tværs af hele datasættet for at koordinere data mellem systemer og programmer.  


## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Automatisering og kontrol](media/operations-management-suite-overview/icon-automation-control.png) Automatisering og kontrol

Azure automatisering automatisere administrative processer med [runbooks](../automation/automation-runbook-types.md) , der er baseret på PowerShell og køre i Azure skyen.  Runbooks kan få adgang til en hvilken som helst produkt eller tjeneste, som kan administreres med PowerShell, herunder ressourcer i andre skyer som Amazon Web Services (AWS).  Runbooks kan også udføres på en server i dit lokale datacenter til at administrere lokale ressourcer.

Azure automatisering indeholder Konfigurationsstyring med [PowerShell DTK](../automation/automation-dsc-overview.md).  Du kan oprette og administrere DTK ressourcer, der er hostet i Azure og anvende dem i skyen og lokale systemer til at definere og automatisk gennemtvinge deres konfiguration.


## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Beskyttelse og gendannelse](media/operations-management-suite-overview/icon-protection-recovery.png) Beskyttelse og nedbrud

[Azure sikkerhedskopi](http://azure.microsoft.com/documentation/services/backup) beskytter programmets data og bevarer for år uden en hvilken som helst kapital investering og med minimale løbende omkostninger.  Det kan sikkerhedskopiere data fra fysiske og virtuelle Windows-servere ud over arbejdsbelastninger såsom SQL Server og SharePoint.  Det kan også bruges ved System Center Data Protection Manager (DPM) kan replikeres beskyttede data til Azure for redundans og lang sigt lager.

[Gendannelse af websteder Azure](http://azure.microsoft.com/documentation/services/site-recovery) bidrager til din Forretningskontinuitet og en strategi for genoprettelse efter genoprettelse (BCDR) ved orchestrating gentagelse, failover og gendannelse af lokale Hyper-V virtuelle maskiner, VMware virtuelle maskiner og fysiske Windows/Linux-servere. Du kan gentage computere til et sekundært center eller udvide dit datacenter ved replikering dem til Azure. Gendannelse af websteder indeholder også enkel failover og gendannelse til arbejdsbelastninger. Den integreres med nedbrud Kompensationsordninger såsom SQL Server AlwaysOn og giver gendannelse planer for nem failover af arbejdsbelastninger, som er udviklet på tværs af flere computere.


## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS sikkerhed og overholdelse](media/operations-management-suite-overview/icon-security-compliance.png) Sikkerhed og overholdelse
Sikkerhed og overholdelse af regler hjælper dig med at identificere, vurdere og minimere sikkerhedsrisici til infrastrukturen.  Disse funktioner i OMS implementeres gennem flere løsninger i Log Analytics, analyserer logdata og konfigurationer fra en agent systems hjælper dig med at sikre igangværende sikkerheden for dit miljø.

- [Sikkerhed og Overvåg løsning](oms-security-getting-started.md ) indsamler og analyserer sikkerhed begivenheder i administrerede systemer til at identificere mistænkelig aktivitet.
- [Antimalwareprogram løsning](log-analytics-malware.md ) rapporter på status for Antimalwareprogram beskyttelse under administrerede systemer.  
- Systemopdateringer løsningen udfører en analyse af sikkerhedsopdateringerne og andre opdateres på dine administrerede systemer, så du nemt identificere systemer kræver retter.


## <a name="next-steps"></a>Næste trin
- Få mere at vide om [Log analyser](http://azure.microsoft.com/documentation/services/log-analytics).
- Få mere at vide om [Azure automatisering](../automation/automation-intro.md).
- Få mere at vide om [Azure sikkerhedskopi](http://azure.microsoft.com/documentation/services/backup).
- Få mere at vide om [gendannelse af Azure websteder](http://azure.microsoft.com/documentation/services/site-recovery).

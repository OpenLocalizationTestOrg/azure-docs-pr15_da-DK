<properties
   pageTitle="Integration med handlinger Management-pakken (OMS) | Microsoft Azure"
   description="Ud over at bruge standardfunktionerne i OMS, kan du integrere den med andre administrationsprogrammer, og services for at give et management-hybridmiljø at angive brugerdefinerede management scenarier entydige for dit miljø eller for at angive en brugerdefineret management oplevelse ved dine kunder.  I denne artikel giver et overblik over forskellige muligheder for integration med OMS og links til artikler med detaljerede tekniske oplysninger."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="bwren" />

# <a name="integrating-with-operations-management-suite-oms"></a>Integration med handlinger Management-pakken (OMS)

Handlinger Management Suite er Microsofts skybaseret IT management-løsning, der hjælper dig med at administrere og beskytte dine lokale og infrastruktur i skyen.  Ud over at bruge standardfunktionerne i OMS, kan du integrere den med andre administrationsprogrammer, og services for at give et management-hybridmiljø at angive brugerdefinerede management scenarier entydige for dit miljø eller for at angive en brugerdefineret management oplevelse ved dine kunder.  I denne artikel giver et overblik over forskellige muligheder for integration med OMS tjenester og links til artikler med detaljerede tekniske oplysninger. 



## <a name="log-analytics"></a>Log Analytics
Af administrationsdata indsamles af Log Analytics er gemt i et lager, som er hostet i Azure.  Alle de data, der er gemt i lageret er tilgængelig i log søgninger som understøtter hurtig analyse på tværs af meget store datamængder.  Dine integrationskrav til kan være til at udfylde lageret med nye data, der gør det tilgængeligt for analyse eller til at udtrække data i lageret til at give en ny visualisering, eller du integrere systemet med et andet værktøj til styring.

Hver enkelt dataelement i lageret er gemt som en post.  Når du udfylder lageret, kan du give brugere med den posttype, der bruger din løsning og en beskrivelse af dens egenskaber.  Når du henter data, kan du bruge disse oplysninger om de data, du arbejder med.

![Udfylde OMS lager](media/operations-management-suite-integration/repository.png)


### <a name="populate-the-log-analytics-repository"></a>Udfylde Log Analytics-lager
Der findes flere metoder til at udfylde OMS-lager.  Den metode, du bruger, afhænger af faktorer såsom hvor kildedataene er placeret, formatet for dataene, og som du har brug for at understøtte klienter.  Når data er gemt i lageret, betyder det ikke noget, hvor det er indsamlet.

I følgende afsnit beskrives de forskellige indstillinger for at udfylde OMS-lager.

#### <a name="connected-sources-and-data-sources"></a>Forbundne kilder og datakilder 
Tilknyttede kilder er de placeringer, hvor du kan hente data for OMS-lager.  Datakilder og løsninger, der kører på forbindelse datakilder og definere de data, der indsamles.  Hvis dit program skriver data til en af disse datakilder, kan du samle det ved at konfigurere datakilden.  Eksempelvis hvis dit program opretter Syslog begivenheder, kan derefter de indsamles af datakilden Syslog på en agent for Linux.

- [Datakilder i Log Analytics](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Løsninger

Løsninger, der udvider funktionerne i OMS.  En løsning kan Indsaml data fra den forbundne kilde eller den kan udføre analyse på poster, der allerede har indsamlet i lageret.  Hver løsning, der leveres af Microsoft har en individuelle artikel, der indeholder oplysninger om de data, der indsamles.

- [Løsninger i Log Analytics](../log-analytics/log-analytics-add-solutions.md)



#### <a name="http-data-collector-api"></a>HTTP-Data indsamler API

Log Analytics HTTP Data indsamler API er en REST-API, hvor du kan føje JSON data til Log Analytics-lager.  Når du har et program, der indeholder ikke data via en af de andre datakilder eller løsninger, kan du udnytte denne API.  Det kan bruges til at udfylde lager fra en klient, der kan ringe til API og ikke afhænger af tidsplanen samling af enhver datakilde eller løsning.

- [Log Analytics HTTP-Data indsamler API](../log-analytics/log-analytics-data-collector-api.md)


### <a name="retrieve-data-from-the-log-analytics-repository"></a>Hente data fra Log Analytics-lager

Der findes flere metoder til at hente data fra OMS-lager.  Du kan brugerne skal hente data ved hjælp af konsollen OMS og give dem forskellige typer af visualiseringer og analyse.  Du kan også hente data fra en ekstern proces som en anden management-løsning.

#### <a name="log-searches"></a>Log søgninger

Alle de data, der er gemt i OMS lager er tilgængelig via log søgninger.  Brugere kan udføre deres egne ad hoc-analyse i konsollen OMS eller oprette et dashboard med visuelle effekter til en bestemt log søgning.  Løsninger, der kan indeholde brugerdefinerede visninger med visualiseringer baseret på foruddefinerede søgninger.  Du kan bruge Log Søg API til access-data i OMS lager fra et eksternt værktøj til programmet eller administration.  

- [Log søgninger i Log Analytics](../log-analytics/log-analytics-log-searches.md)
- [Log Analytics log search REST-API](../log-analytics/log-analytics-log-search-api.md)
- [Log Analytics-cmdletter](https://msdn.microsoft.com/library/mt188224.aspx)



#### <a name="custom-views"></a>Brugerdefinerede visninger 
Vis Designer kan du oprette brugerdefinerede visninger i konsollen OMS, som giver brugere med visualisering og analyse af data i din løsning.  Hver visning omfatter et felt, der vises på hovedsiden for konsollen og et vilkårligt antal visualisering dele, som er baseret på log søgninger, som du definerer.
  
- [Log Analytics visning Designer](../log-analytics/log-analytics-view-designer.md)


#### <a name="power-bi"></a>Power BI

Log Analytics kan automatisk eksportere data fra OMS lager i Power BI så kan du udnytte dens visualiseringer og analyseværktøjer.  Det udfører denne eksport på en tidsplan, så dataene er opdateret. 

- [Eksportere Log Analytics-data til Power BI](../log-analytics/log-analytics-powerbi.md)




## <a name="automation"></a>Automatisering

OMS kan automatisere processerne til at reagere på indsamlede data eller til at udføre andre funktioner.  Det kan Indsaml data fra dit program og indsætte den i OMS lager, eller du kan automatisere rettelse af et kendt problem som svar på data, der er fundet i lageret. 

![Automatisering](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooks

Runbooks i Azure Automation køre PowerShell-scripts og arbejdsprocesser i Azure skyen.  Du kan bruge dem til at administrere ressourcer i Azure eller andre ressourcer, der kan åbnes fra skyen.  Runbooks kan også køre i en lokal datacenter ved hjælp af Hybrid Runbook arbejder.  Du kan starte en runbook fra Azure-portalen eller fra eksterne processer ved hjælp af en række metoder som PowerShell eller automatisering API.

- [Starte en runbook fra Azure automatisering](../automation/automation-starting-a-runbook.md)
- [Azure-cmdletter automatisering](https://msdn.microsoft.com/library/dn690262.aspx)
- [Automatisering REST-API](https://msdn.microsoft.com/library/mt662285.aspx)
- [Automatisering .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Beskeder

Beskeder om regler køres automatisk log søger efter en tidsplan.  Hvis resultaterne opfylder bestemte kriterier kan resulterende beskeden starte en runbook i Azure automatisering eller ringe til en webhook, som kan starte en ekstern proces.  Begge af disse svar kan indeholde oplysninger på den besked, herunder data, der returneres i feltet log Søg.

- [Beskeder i Log Analytics](../log-analytics/log-analytics-alerts.md)
- [Log Analytics beskeder om API](../log-analytics/log-analytics-api-alerts.md)


## <a name="backup-and-site-recovery"></a>Sikkerhedskopiering og gendannelse af websteder

Tilbyd tjenester til beskyttelse af dine virksomhedsdata og sikring af tilgængeligheden af servere og programmer, Azure sikkerhedskopiering og gendannelse af websteder.  Du kan udnytte disse tjenester for at udføre scenarier, som tilbyder ekstra funktioner til dit program eller start af en failover af en virtuel maskine f.eks.

- [Azure-cmdletter sikkerhedskopi](https://msdn.microsoft.com/library/mt619253.aspx)
- [Gendannelse af Azure websteder REST-API](https://msdn.microsoft.com/library/azure/mt750497.aspx)
- [Azure websted gendannelse cmdletter](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Tilpassede løsninger

Du kan indkapsle integration logik i en brugerdefineret løsning for at køre i dit arbejdsområde eller i en kundes arbejdsområde.  Din løsning kan omfatter alle metoderne integration i denne artikel ud over andre ressourcer til at levere en komplet scenarie.  Ressourcer i løsningen er pakket, så når løsningen er fjernet, fjernes alle de ressourcer, der er oprettet fra OMS arbejdsområdet og Azure-abonnement.

Din løsning kan for eksempel omfatter en automatisering runbook for at indsamle og behandle data og derefter udfylde Log Analytics lageret ved hjælp af HTTP Data indsamler API.  Du kan også indsætte en brugerdefineret visning, der viser og analyserer de indsamlede data.  

- Oprettelse af tilpassede løsninger (kommer snart)    

## <a name="next-steps"></a>Næste trin
- Henvise til [OMS SDK](operations-management-suite-sdk.md) tekniske oplysninger om automatisere OMS tjenester.  

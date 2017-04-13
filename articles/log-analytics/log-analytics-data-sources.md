<properties 
   pageTitle="Datakilder i Log Analytics | Microsoft Azure"
   description="Datakilder definere de data, Log Analytics indsamler fra supportmedarbejdere og andre forbundet kilder.  I denne artikel beskrives begrebet hvordan Log Analytics bruger datakilder, forklarer, oplysninger om hvordan du konfigurerer dem og indeholder en oversigt over de forskellige datakilder, der er tilgængelige."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="data-sources-in-log-analytics"></a>Datakilder i Log Analytics

Log Analytics indsamler data fra kilder forbindelse i arbejdsområdet OMS og gemmer den i OMS lager.  De data, der indsamles fra hver er defineret af de datakilder, du konfigurerer.  Data i OMS lager er gemt som et sæt poster.  Hver datakilde opretter poster af en bestemt type med hver type, der har sit eget sæt af egenskaber.

![Log Analytics dataindsamling](./media/log-analytics-data-sources/overview.png)

Datakilder er anderledes end OMS løsninger, der også Indsaml data fra forbundne datakilder og oprette poster i OMS-lager.  Løsninger, der kan føjes til dit arbejdsområde fra løsningsgalleriet og typisk giver yderligere analyseværktøjerne i portalen OMS.  

## <a name="summary-of-data-sources"></a>Oversigt over datakilder

De datakilder, der er tilgængelige i Log Analytics findes i den følgende tabel.  Hver har et link til en separat artikel give detaljer for datakilden.

| Datakilde | Hændelsestype | Beskrivelse |
|:--|:--|:--|
| [Brugerdefineret logfiler](log-analytics-data-sources-custom-logs.md) | \<Lognavn\>_CL | Tekstfiler på Windows eller Linux supportmedarbejdere, der indeholder logoplysninger. |
| [Windows-hændelseslogge](log-analytics-data-sources-windows-events.md) | Begivenhed | Hændelser, der indsamles fra hændelsesloggen på Windows-computere. |
| [Tællere i Windows ydeevne](log-analytics-data-sources-performance-counters.md) | Performance | Tællere i ydeevne, der indsamles fra Windows-computere. |
| [Tællere i Linux ydeevne](log-analytics-data-sources-performance-counters.md) | Performance | Tællere i ydeevne, der indsamles fra Linux-computere. |
| [IIS-logfilerne](log-analytics-data-sources-iis-logs.md) | W3CIISLog | Internet Information Services logfiler i W3C-format. |
| [Syslog](log-analytics-data-sources-syslog.md) | Syslog | Syslog begivenheder i Windows eller Linux-computere. |

## <a name="configuring-data-sources"></a>Konfiguration af datakilder

Du kan konfigurere datakilder i menuen **Data** i Log Analytics- **Indstillinger**.  En hvilken som helst konfiguration er leveret til alle tilknyttede kilder i arbejdsområdet OMS.  Du kan ikke i øjeblikket udelades en hvilken som helst supportmedarbejdere fra denne konfiguration.

![Konfigurere hændelser i Windows](./media/log-analytics-data-sources/configure-events.png)

2. Vælg feltet **Indstillinger** i konsollen OMS.
3. Vælg **Data**.
4. Klik på datakilden for at konfigurere.
5. Følg linket i dokumentationen til hver datakilde i ovenstående tabel for at få oplysninger på deres konfiguration.

## <a name="data-collection"></a>Dataindsamling

Datakilde konfigurationer er leveret til supportmedarbejdere, der er direkte forbundet med OMS efter et par minutter.  De angivne data indsamles fra en agent og leveret direkte til Log Analytics med intervaller, der er specifikke for hver datakilde.  Se i dokumentationen til hver datakilde for disse specifikke oplysninger.

System Center Operations Manager (SCOM) supportmedarbejdere i en gruppe af forbundne administration, er datakilde konfigurationer oversættes til management packs og leveret til gruppen management hver 5 minutter som standard.  Agenten henter management pack som alle andre og indsamler de angivne data. Dataene bliver enten sendes til en indholdsstyringsserver som videresender dataene til Log analyser afhængigt af datakilden, eller agenten sender dataene til Log Analytics uden at gå gennem management-serveren. Se [Detaljer om samling af data for OMS funktioner og løsninger](log-analytics-add-solutions.md#data-collection-details-for-oms-features-and-solutions) for detaljer.  Du kan læse om oplysninger om forbindelse SCOM og OMS og ændre hyppigheden konfigurationen leveres på [Konfigurere Integration med System Center Operations Manager](log-analytics-om-agents.md).

## <a name="log-analytics-records"></a>Log Analytics poster

Alle de data, der indsamles via Log Analytics er gemt i OMS lager som poster.  Poster, der indsamles via forskellige datakilder, har deres egne sæt af egenskaber og blive identificeret efter deres **Type** egenskab.  Se i dokumentationen til hver datakilde og løsning for detaljer på hver posttype.


## <a name="next-steps"></a>Næste trin

- Få mere at vide om [løsninger](log-analytics-add-solutions.md) , der føje funktioner til Log analyser og også samle data i OMS-lager.
- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at analysere de data, der indsamles fra datakilder og løsninger.  
- Konfigurere [beskeder](log-analytics-alerts.md) for at proaktiv give dig besked om vigtige data, der indsamles fra datakilder og løsninger.

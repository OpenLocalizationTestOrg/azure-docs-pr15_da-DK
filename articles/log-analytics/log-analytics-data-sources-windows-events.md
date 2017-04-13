<properties 
   pageTitle="Windows hændelseslogfiler i Log Analytics | Microsoft Azure"
   description="Windows-hændelseslogge er en af de mest almindelige datakilder, der bruges af Log analyser.  I denne artikel beskrives, hvordan du konfigurerer samling af Windows-hændelseslogge og oplysninger om de poster, de opretter i OMS-lager."
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

# <a name="windows-event-log-data-sources-in-log-analytics"></a>Windows hændelseslog datakilder i Log Analytics

Windows-hændelseslogge er en af de mest almindelige [datakilder](log-analytics-data-sources.md) , der bruges til Windows-supportmedarbejdere, da dette er den metode, der bruges af de fleste programmer til at logge oplysninger og fejl.  Ud over at angive en brugerdefineret logfiler, der er oprettet af programmer, du har brug for til at overvåge, kan du indsamle begivenheder fra standard logfiler som System- og.

![Windows-hændelser](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfiguration af hændelseslogfiler Windows

Konfigurere Windows-hændelseslogge i [menuen Data i indstillinger for analyser](log-analytics-data-sources.md#configuring-data-sources).

Log Analytics indsamler kun begivenheder fra de Windows-hændelseslogge, der er angivet i indstillingerne for.  Du kan tilføje en ny log ved at skrive navnet på loggen og klikke på **+**.  For hver log samles kun hændelser med de valgte severities.  Se severities for den bestemte logfil, du vil indsamle.  Du kan ikke angive eventuelle yderligere kriterier til filtrere hændelser.

![Konfigurere hændelser i Windows](media/log-analytics-data-sources-windows-events/configure.png)


## <a name="data-collection"></a>Dataindsamling

Log Analytics indsamler hver enkelt hændelse, der svarer til en markerede alvorlighed fra en overvåget hændelseslog, som hændelsen er oprettet.  Agenten vil registrere placering i hver hændelseslog, der indsamles fra.  Hvis agenten går offline for et bestemt tidsrum, derefter indsamler Log Analytics begivenheder fra hvor den sidst blev afbrudt, selvom hændelser, der er oprettet, mens agenten var offline.


## <a name="windows-event-records-properties"></a>Egenskaber for Windows begivenhed poster

Windows begivenhed poster har en type **hændelse** og har egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| Computer            | Navnet på den computer, der er indsamlet begivenheden fra. |
| EventCategory       | Kategori af begivenheden. |
| EventData           | Alle begivenhed data i raw-format. |
| Begivenhedid             | Antallet af begivenheden. |
| EventLevel          | Alvorlighed af hændelsen i numeriske formular. |
| EventLevelName      | Alvorlighed af hændelsen i form af tekst. |
| Hændelseslog            | Navnet på den hændelseslog, som hændelsen er indsamlet fra. |
| ParameterXml        | Begivenhed parameterværdier i XML-format. |
| ManagementGroupName | Navnet på gruppen management for SCOM supportmedarbejdere.  I forbindelse med andre supportmedarbejdere er AOI-<workspace ID> |
| RenderedDescription | Beskrivelse af begivenhed med parameterværdier |
| Kilde              | Kilden for begivenheden. |
| SourceSystem  | Type af agent begivenheden er indsamlet fra. <br> Oprette forbindelse OpsManager – Windows agent, enten direkte eller SCOM <br> Linux – alle Linux supportmedarbejdere  <br> AzureStorage – Azure diagnosticering |
| TimeGenerated       | Dato og klokkeslæt for hændelsen blev oprettet i Windows. |
| Brugernavn            | Brugernavnet for den konto, logget på begivenheden. |



## <a name="log-searches-with-windows-events"></a>Log søgninger med Windows-hændelser

Den følgende tabel indeholder forskellige eksempler på log søgninger, der henter Windows begivenhed poster.

| Forespørgsel | Beskrivelse |
|:--|:--|
| Skriv = begivenhed | Alle hændelser i Windows. |
| Skriv = begivenhed EventLevelName = fejl | Alle hændelser i Windows med alvorlighed af fejl. |
| Skriv = begivenhed & #124; Måle count() efter kilde | Antallet af Windows begivenheder efter kilde. |
| Skriv = begivenhed EventLevelName = fejl & #124; Måle count() efter kilde | Antallet af Windows fejl begivenheder efter kilde. |

## <a name="next-steps"></a>Næste trin

- Konfigurere Log analyser for at indsamle andre [datakilder](log-analytics-data-sources.md) til analyse.
- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at analysere de data, der indsamles fra datakilder og løsninger.  
- Bruge [Brugerdefinerede felter](log-analytics-custom-fields.md) til at fortolke begivenhed poster i individuelle felter.
- Konfigurere [samling af tællere i ydeevne](log-analytics-data-sources-performance-counters.md) fra din Windows-supportmedarbejdere.
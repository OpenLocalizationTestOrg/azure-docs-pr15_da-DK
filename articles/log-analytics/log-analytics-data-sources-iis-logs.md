<properties
   pageTitle="IIS logfører i Log Analytics | Microsoft Azure"
   description="Internet Information Services (IIS) gemmer brugeraktivitet i logfiler, der kan indsamles af Log analyser.  I denne artikel beskrives, hvordan du konfigurerer samling af IIS-logfiler og oplysninger om de poster, de opretter i OMS-lager."
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

# <a name="iis-logs-in-log-analytics"></a>Logge på IIS i Log Analytics
Internet Information Services (IIS) gemmer brugeraktivitet i logfiler, der kan indsamles af Log analyser.  

![IIS-logfilerne](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfiguration af IIS logfiler
Log Analytics indsamler fra logfiler, der er oprettet af IIS, så du skal [konfigurere IIS til logføring](https://technet.microsoft.com/library/hh831775.aspx).

Log Analytics kun understøtter IIS-logfiler, der er gemt i W3C-format og understøtter ikke brugerdefinerede felter eller IIS-Avanceret logføring.  
Log Analytics indsamler ikke logge i NCSA eller IIS oprindelige format.

Konfigurere IIS logfiler i Log Analytics i [menuen Data i indstillinger for analyser](log-analytics-data-sources.md#configuring-data-sources).  Der er ingen konfiguration påkrævet bortset fra at vælge **indsamling W3C format IIS logfiler**.

Vi anbefaler, at når du aktiverer IIS log af websteder, skal du konfigurere indstillingen IIS log overgang på hver server.


## <a name="data-collection"></a>Dataindsamling

Log Analytics indsamler IIS logposter fra hver forbundne kilde cirka hver 15 minutter.  Agenten poster placering i hver hændelseslog, der indsamles fra.  Hvis agenten går offline, derefter indsamler Log Analytics begivenheder fra hvor den sidst blev afbrudt, selvom hændelser, der er oprettet, mens agenten var offline.


## <a name="iis-log-record-properties"></a>IIS log postegenskaber

IIS logføringsposter har en type af **W3CIISLog** og har egenskaberne i den følgende tabel:

| Egenskaben | Beskrivelse |
|:--|:--|
| Computer | Navnet på den computer, der er indsamlet begivenheden fra. |
| cIP | IP-adressen på klienten. |
| csMethod | Metode for din anmodning som GET eller POST. |
| csReferer | Websted, at brugeren har fulgt et link fra til det aktuelle websted. |
| csUserAgent | BrowserType af klienten. |
| csUserName | Navnet på den godkendte bruger, adgang til serveren. Anonyme brugere er angivet med en bindestreg. |
| csUriStem | Destination for din anmodning som en webside. |
| csUriQuery | Forespørgsel, hvis der overhovedet skal, klienten forsøgte at udføre. |
| ManagementGroupName | Navnet på gruppen management til Operations Manager supportmedarbejdere.  I forbindelse med andre supportmedarbejdere er AOI -\<arbejdsområde-ID\> |
| RemoteIPCountry | Land af IP-adressen på klienten. |
| RemoteIPLatitude | Længde på klienten IP-adresse. |
| RemoteIPLongitude | Breddegrader klient IP-adresse. |
| scStatus | HTTP-statuskode. |
| scSubStatus | Understatus fejlkode. |
| scWin32Status | Windows statuskode. |
| sIP | IP-adressen på webserveren. |
| SourceSystem  | OpsMgr |
| sportsgren | Port på serveren klienten forbindelse til. |
| sSiteName | Navnet på IIS-webstedet. |
| TimeGenerated | Dato og klokkeslæt, der blev registreret posten. |
| TimeTaken | Lang tid at behandle anmodningen i millisekunder. |

## <a name="log-searches-with-iis-logs"></a>Log søgninger med IIS-logfilerne

Den følgende tabel indeholder forskellige eksempler på log forespørgsler, der henter IIS logposter.

| Forespørgsel | Beskrivelse |
|:--|:--|
| Skriv = IISLog | Alle IIS log poster. |
| Skriv = IISLog EventLevelName = fejl | Alle hændelser i Windows med alvorlighed af fejl. |
| Skriv = W3CIISLog & #124; Måle count() efter cIP | Antallet af IIS log poster efter klient IP-adresse. |
| Skriv = W3CIISLog csHost = "www.contoso.com" & #124; Måle count() ved csUriStem | Antallet af IIS log elementer af URL-adresse for host www.contoso.com. |
| Skriv = W3CIISLog & #124; Måle Sum(csBytes) ved Computer og #124; toppen 500000| Samlet antal byte, der modtages af alle IIS-computere. |

## <a name="next-steps"></a>Næste trin

- Konfigurere Log analyser for at indsamle andre [datakilder](log-analytics-data-sources.md) til analyse.
- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at analysere de data, der indsamles fra datakilder og løsninger.
- Konfigurere beskeder i Log Analytics proaktiv besked om vigtige betingelser, der findes i IIS-logfiler.

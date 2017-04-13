<properties
    pageTitle="Azure Government dokumentation | Microsoft Azure"
    description="Dette indeholder en sammenligning af funktioner og vejledning om udviklingsprogrammer til Azure Government."
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-monitoring-and-management"></a>Azure Government overvågning og administration

I denne artikel beskrives overvågning og administration tjenester variationer og overvejelser i forbindelse med Azure Government-miljø.

## <a name="automation"></a>Automatisering

Automatisering er alment tilgængelig i Azure Government.

### <a name="variations"></a>Variationer

Følgende automatiseringsfunktioner er ikke tilgængelige i Azure Government.

+ Oprettelse af en tjeneste princippet legitimationsoplysningerne for godkendelse

Du kan finde flere oplysninger i dokumentationen [automatisering offentlige](../automation/automation-intro.md).

## <a name="log-analytics"></a>Log Analytics

Log Analytics er alment tilgængelig i Azure Government.

### <a name="variations"></a>Variationer

De følgende Log Analytics funktioner og -løsninger, der er ikke tilgængelige i Azure Government.

+ Løsninger, der er i udskrift på Microsoft Azure, herunder:
  - Overvågnings netværksløsning
  - Programmet afhængighed overvågning løsning
  - Office 365-løsning
  - Windows 10 opgradere Analytics-løsning
  - Programmet indsigt løsning
  - Azure netværk Analytics-løsning
  - Azure automatisering Analytics-løsning
  - Tasten samling Analytics-løsning
+ Løsninger og funktioner, der kræver opdateringer til lokale software, herunder:
  - Integration med System Center Operations Manager 2016 (tidligere versioner af Operations Manager understøttes)
  - Computere grupper fra System Center Configuration Manager
  - Surface Hub løsning
+ Funktioner, der er i udskrift på offentlige Azure, herunder:
  - Eksport af data til Power BI
+ Azure målepunkter og Azure diagnosticering
+ Handlinger Management Suite mobilprogrammer

URL-adresser for Log analyser er forskellige Azure Government:

| Azure offentlige | Azure Government | Noter |
|--------------|------------------|-------|
| MMS.Microsoft.com | OMS.Microsoft.us | Log Analytics-portalen |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [Dataindsamler API](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Agent kommunikation - [konfiguration af firewall-indstillinger](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Agent kommunikation - [konfiguration af firewall-indstillinger](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Agent kommunikation - [konfiguration af firewall-indstillinger](../log-analytics/log-analytics-proxy-firewall.md) |


De følgende Log Analytics-funktioner fungerer anderledes i Azure Government:

+ Windows-Agent skal hentes fra [Log Analytics-portalen](https://oms.microsoft.us) til Azure Government.
+ For at forbinde dit System Center Operations Manager indholdsstyringsserver til Log Analytics, skal du hente og importere opdaterede management packs.
  1. Download og Gem den [opdateret management packs](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Udpakke den fil, du har hentet.
  3. Importere management packs til Operations Manager. Du kan finde oplysninger om, hvordan du importerer en management pack fra en disk, skal du se [Sådan importeres en Management Pack til Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) i Microsoft TechNet-webstedet.
  4. Følg trinnene i [Forbinde Operations Manager til Log analyser](../log-analytics/log-analytics-om-agents.md)for at forbinde Operations Manager til Log Analytics.


## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

+ Kan jeg overføre data fra Log Analytics i Microsoft Azure til Azure Government?
  - Nej. Det er ikke muligt at flytte data eller dit arbejdsområde fra Microsoft Azure til Azure Government.
+ Kan jeg skifte mellem Microsoft Azure og Azure Government arbejdsområder fra portalen handlinger Management pakke Log Analytics?
  - Nej. Portaler til Microsoft Azure og Azure Government er separat og Undlad at dele oplysninger.

Du kan finde flere oplysninger i dokumentationen [Log Analytics offentlige](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Næste trin

Supplerende oplysninger og opdateringer, du abonnerer på den <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog.</a>

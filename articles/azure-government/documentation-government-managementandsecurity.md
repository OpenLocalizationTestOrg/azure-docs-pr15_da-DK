<properties
    pageTitle="Azure Government dokumentation | Microsoft Azure"
    description="Dette giver en sammenligning af funktioner og vejledning om udvikling af programmer til Azure Government"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="scooxl"
    manager="zakramer"
    editor=""/>
<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="scooxl"/>
#  <a name="azure-government-management-and-security"></a>Azure Government administration og sikkerhed

## <a name="automation"></a>Automatisering

Automatisering er alment tilgængelig i Azure Government.

### <a name="variations"></a>Variationer

Følgende automatiseringsfunktioner er ikke tilgængelige i Azure Government.

+ Oprettelse af en tjeneste princippet legitimationsoplysningerne for godkendelse

Du kan finde flere oplysninger i dokumentationen [automatisering offentlige](../automation/automation-intro.md).


##  <a name="key-vault"></a>Vigtige samling
Oplysninger om denne tjeneste og hvornår det skal bruges, finder du de <a href="https://azure.microsoft.com/documentation/services/key-vault">Azure-tasten samling offentlige dokumentation.</a>
### <a name="data-considerations"></a>Overvejelser i forbindelse med data
Følgende oplysninger identificerer i Azure Government rammen til Azure-tasten samling:

| Omfattet/kontrolleres data, der er tilladt | Omfattet/kontrolleres data, der er ikke tilladt |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alle de data, der er krypteret med en Azure-tasten samling nøgle kan indeholde Regulated/kontrolleres data. | Azure-tasten samling metadata er ikke tilladt til at indeholde kontrolleres Eksporter data. Disse metadata omfatter alle konfigurationsdata, der er angivet, da at oprette og vedligeholde din nøgle samling.  Kan ikke indsætte Regulated/kontrolleres data i følgende felter: gruppe ressourcenavne, nøgle samling feltnavne, Abonnementsnavnet |

Tasten samling er alment tilgængelig i Azure Government. Som offentligheden er der uden filtypenavn, så nøgle samling er kun tilgængelig via PowerShell og CLI.
## <a name="log-analytics"></a>Log Analytics
Log Analytics er alment tilgængelig i Azure Government. 

### <a name="variations"></a>Variationer

De følgende Log Analytics funktioner og -løsninger, der er ikke tilgængelige i Azure Government. Denne liste opdateres, når status for funktioner / løsninger ændres.

+ Løsninger, der er i Vis udskrift i offentlige Azure, herunder:
  - Overvågnings netværksløsning
  - Programmet afhængighed overvågning
  - Office 365-løsning
  - Windows 10 opgradere Analytics-løsning
  - Programmet indsigt
  - Azure netværk Analytics-løsning
  - Azure automatisering Analytics
  - Vigtige samling Analytics
+ Løsninger og funktioner, der kræver opdateringer til lokale software, herunder
  - Integration med System Center Operations Manager 2016 (tidligere versioner af Operations Manager understøttes)
  - Computere grupper fra System Center Configuration Manager
  - Surface Hub løsning
+ Funktioner, der er i udskrift på offentlige Azure, herunder
  - Eksport af data til PowerBI
+ Azure målepunkter og Azure diagnosticering
+ OMS Mobile-programmer

URL-adresser for Log analyser er forskellige Azure Government:

| Azure offentlige | Azure Government | Noter |
|--------------|------------------|-------|
| MMS.Microsoft.com | OMS.Microsoft.us | Log Analytics-portalen |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [Dataindsamler API](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Agent kommunikation - [konfiguration af firewall-indstillinger](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Agent kommunikation - [konfiguration af firewall-indstillinger](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Agent kommunikation - [konfiguration af firewall-indstillinger](../log-analytics/log-analytics-proxy-firewall.md) |


Følgende funktioner, der Log Analytics har forskellig adfærd i Azure Government:

+ Windows-agent skal hentes fra [Log Analytics-portalen](https://oms.microsoft.us) til Azure Government.
+ For at forbinde dit System Center Operations Manager indholdsstyringsserver til Log Analytics, skal du hente og importere opdaterede Management Packs.
  1. Hent og Gem den [opdateret management packs](http://go.microsoft.com/fwlink/?LinkId=828749)
  2. Udpakke den fil, du har hentet
  3. Importere management packs til Operations Manager. Du kan finde oplysninger om, hvordan du importerer en management pack fra en disk, skal du se emnet [Sådan importeres en Management Pack til Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) i Microsoft TechNet-webstedet.
  4. For at forbinde Operations Manager til Log Analytics, følge trinene i [Forbinde Operations Manager til Log Analytics](../log-analytics/log-analytics-om-agents.md) 



### <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

+ Kan jeg overføre data fra Log Analytics i offentlige Azure til Azure Government?
  - Nej. Det er ikke muligt at flytte data eller dit arbejdsområde fra offentlige Azure til Azure Government.
+ Kan jeg skifte mellem offentlige Azure og Azure Government arbejdsområder fra portalen OMS Log Analytics?
  - Nej. Portaler for offentlige Azure og Azure Government er separat og Undlad at dele oplysninger. 

Du kan finde flere oplysninger i dokumentationen [Log Analytics offentlige](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Næste trin

Supplerende oplysninger og opdateringer, du abonnerer på den <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog.</a>
 

<properties
    pageTitle="Azure netværk Analytics-løsning i Log Analytics | Microsoft Azure"
    description="Du kan bruge Azure netværk Analytics-løsning i Log Analytics gennemgå Azure netværk sikkerhed gruppe logfiler og Azure Application Gateway hændelseslogge."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="richrund"/>

# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Azure netværk Analytics (Preview)-løsning i Log Analytics

>[AZURE.NOTE] Dette er et [eksempel løsning](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Du kan bruge Azure netværk Analytics-løsning i Log Analytics Gennemse Azure netværk sikkerhed gruppe logfiler og Azure Application Gateway logfiler.

Du kan aktivere logføring til Azure Application Gateway logfiler og Azure netværk sikkerhedsgrupper. Disse logfiler skrives til Blob storage, hvor de kan derefter indekseres af Log Analytics til søgning og analyse.

Følgende loggene understøttes for programmet Gateways:

+ ApplicationGatewayAccessLog
+ ApplicationGatewayPerformanceLog

Følgende loggene understøttes til netværk sikkerhedsgrupper:

+ NetworkSecurityGroupEvent
+ NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>Installere og konfigurere løsningen

Brug følgende fremgangsmåde til at installere og konfigurere Azure netværk Analytics-løsning:

1.  Aktivere logføring af diagnosticering for de ressourcer, du vil overvåge:
  + [Application Gateway](../application-gateway/application-gateway-diagnostics.md)
  + [Netværk sikkerhedsgruppe](../virtual-network/virtual-network-nsg-manage-log.md)
2.  Konfigurere Log analyser for at læse loggene fra Blob-lager ved hjælp af processen beskrevet i [JSON-filer i blob-lager](../log-analytics/log-analytics-azure-storage-json.md).
3.  Aktivere Azure netværk Analytics-løsning ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).  

Hvis du ikke aktiverer logføring af diagnostik for en bestemt ressourcetype, vil dashboard blade for den pågældende ressource være tomt.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Gennemgå oplysningerne om Azure netværk Analytics data

Azure netværk Analytics løsningen indsamler diagnosticering logfiler fra Azure Blob-lager til Azure programmet Gateways og netværk sikkerhedsgrupper.
Ingen agent er påkrævet for indsamling af data.

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for Azure netværk analyser.

| Platform | Direkte agent | Agent for Systems Center Operations Manager (SCOM) | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Azure|![Nej](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Nej](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Ja](./media/log-analytics-azure-networking/oms-bullet-green.png)|            ![Nej](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Nej](./media/log-analytics-azure-networking/oms-bullet-red.png)| 10 minutter|

## <a name="use-azure-networking-analytics"></a>Bruge Azure netværk Analytics

Når du har installeret løsningen, kan du se oversigt over klient og serverfejl for din overvåget programmet Gateways ved hjælp af **Azure netværk Analytics** side om side på siden **Oversigt** i Log Analytics.

![Billede af Azure netværk Analytics-felt](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

Når du klikker på feltet **Oversigt** , kan du få vist oversigter over logfilerne og derefter analysere i detaljer for følgende kategorier:

+ Adgang til programmer Gateway logfiler
  - Klient- og fejl for Application Gateway access loggene
  - Anmodninger i timen for hver Application Gateway
  - Mislykkede anmodninger i sekundet for hver Application Gateway
  - Fejl ved brugeragent til program-Gateways
+ Programmet Gateway-ydeevne
  - Host tilstand for Application Gateway
  - Største og 95th fraktil Application Gateway mislykkedes anmodninger om
+ Netværk sikkerhedsgruppe blokeret flyder
  - Netværk sikkerhed gruppe regler med blokerede flyder
  - MAC-adresser med blokerede flyder
+ Netværk sikkerhedsgruppe tilladt flyder
  - Netværk sikkerhed gruppe regler med tilladte flyder
  - MAC-adresser med tilladte flyder


![Billede af Azure netværk Analytics dashboard](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![Billede af Azure netværk Analytics dashboard](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![Billede af Azure netværk Analytics dashboard](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![Billede af Azure netværk Analytics dashboard](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>Til at få vist detaljerne for en oversigt over

1. Klik på feltet **Azure netværk Analytics** på siden **Oversigt** .
2. Gennemgå oversigtsoplysningerne i en af blade på dashboardet til **Azure netværk Analytics** , og klik derefter på en for at få vist detaljerede oplysninger om det på siden log søgning.

    På en af siderne log søgning, du kan få vist resultater ved klokkeslæt, detaljerede resultater og en oversigt over dine log søgning. Du kan også filtrere efter facetter og indsnævre resultaterne.

## <a name="next-steps"></a>Næste trin

- Brug [Log søgninger i Log Analytics](log-analytics-log-searches.md) til at få vist detaljerede Azure netværk Analytics-data.

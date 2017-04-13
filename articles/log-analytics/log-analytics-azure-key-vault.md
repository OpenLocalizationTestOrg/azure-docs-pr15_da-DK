<properties
    pageTitle="Azure-tasten samling løsning i Log Analytics | Microsoft Azure"
    description="Du kan bruge løsningen Azure-tasten samling i Log Analytics til Azure-tasten samling logfilerne."
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
    ms.date="07/12/2016"
    ms.author="richrund"/>

# <a name="azure-key-vault-preview-solution-in-log-analytics"></a>Azure-tasten samling (Preview)-løsning i Log Analytics

>[AZURE.NOTE] Dette er et [eksempel løsning](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Du kan bruge løsningen Azure-tasten samling i Log Analytics til Azure-tasten samling AuditEvent logfilerne.

Du kan aktivere logføring af overvågningshændelser til Azure-tasten samling. Disse logfiler skrives til Azure Blob-lager, hvor de kan derefter indekseres af Log Analytics til søgning og analyse.

## <a name="install-and-configure-the-solution"></a>Installere og konfigurere løsningen

Brug følgende fremgangsmåde til at installere og konfigurere Azure-tasten samling løsningen:

1.  Aktivere [logføring af diagnosticering til tasten samling](../key-vault/key-vault-logging.md) ressourcer, som du vil overvåge
2.  Konfigurere Log analyser for at læse loggene fra blob-lager ved hjælp af processen beskrevet i [JSON-filer i blob-lager](../log-analytics/log-analytics-azure-storage-json.md).
3.  Aktivere løsningen Azure-tasten samling ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).  

## <a name="review-azure-key-vault-data-collection-details"></a>Gennemgå oplysningerne om Azure-tasten samling data

Azure-tasten samling løsning indsamler diagnosticering logfiler fra Azure blob-lager til Azure-tasten samling.
Ingen agent er påkrævet for indsamling af data.

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for Azure-tasten samling.

| Platform | Direkte agent | Agent for Systems Center Operations Manager (SCOM) | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Azure|![Nej](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Nej](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Ja](./media/log-analytics-azure-keyvault/oms-bullet-green.png)|            ![Nej](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Nej](./media/log-analytics-azure-keyvault/oms-bullet-red.png)| 10 minutter|

## <a name="use-azure-key-vault"></a>Bruge Azure vigtige samling

Når du har installeret løsningen, kan du se oversigt over anmodning om statusser over tid for din overvåget nøgle Vaults ved hjælp af **Azure nøgle samling** af flisen på siden **Oversigt** over Log analyser.

![Billede af Azure-tasten samling felt](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Når du klikker på feltet **Oversigt** , kan du få vist oversigter over logfilerne og derefter analysere i detaljer for følgende kategorier:

- Lydstyrken for alle vigtige samling handlinger over tid
- Mislykkedes handlingen enheder over tid
- Forsinkelse på gennemsnitligt driften af handling
- Kvalitet af tjenesten for handlinger med antallet af handlinger, der tager mere end 1000 ms og en liste over handlinger, der tager mere end 1000 ms

![Billede af Azure-tasten samling dashboard](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Billede af Azure-tasten samling dashboard](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Til at få vist oplysninger om en handling

1. Klik på feltet **Azure nøgle samling** på siden **Oversigt** .
2. Gennemgå oversigtsoplysningerne i en af blade på dashboardet til **Azure nøgle samling** , og klik derefter på en for at få vist detaljerede oplysninger om det på siden log søgning.

    På en af siderne log søgning, du kan få vist resultater ved klokkeslæt, detaljerede resultater og en oversigt over dine log søgning. Du kan også filtrere efter facetter og indsnævre resultaterne.

## <a name="log-analytics-records"></a>Log Analytics poster

Azure-tasten samling løsningen analyserer poster, der har en type af **KeyVaults** , der indsamles fra [AuditEvent logfiler](../key-vault/key-vault-logging.md) i Azure diagnosticering.  Egenskaber for disse poster er i den følgende tabel.  

| Egenskaben | Beskrivelse |
|:--|:--|
| Type | *KeyVaults* |
| SourceSystem | *AzureStorage* |
| CallerIpAddress | IP-adressen på den klient, der har foretaget anmodningen |
| Kategori      | AuditEvent er enkelt, som findes værdien for nøgle samling loggene.|
| CorrelationId | Et valgfrit GUID, der kan gå klienten for at koordinere klientsiden logfiler med logfiler over service-side (tasten samling). |
| DurationMs | Tid, det tog for at behandle REST-API-anmodning, i millisekunder. Dette omfatter ikke netværksventetid, så det tidspunkt, hvor du måle på klientsiden ikke kan svarer til nuværende tidspunkt. |
| HttpStatusCode_d | HTTP-statuskode anmodningen returnerede |
| Id_s       | Entydigt ID for din anmodning |
| Identity_o | Identitet fra det token, der blev angivet, når du foretager REST-API anmodningen. Det er normalt en "brugere", en "service sikkerhedskonto" eller en kombination "bruger + sikkerhedskontrol" som er tilfældet med en anmodning om resultatet af en Azure PowerShell-cmdlet. |
| OperationName      | Navnet på handlingen, som beskrevet i [Azure nøgle samling logføring](../key-vault/key-vault-logging.md)|
| OperationVersion      | REST-API version anmodet af klienten|
| RemoteIPLatitude | Længde af den klient, der har foretaget anmodningen |
| RemoteIPLongitude | Længde af den klient, der har foretaget anmodningen |
| RemoteIPCountry | Land, hvor den klient, der har foretaget anmodningen  |
| RequestUri_s | URI for din anmodning |
| Ressource   | Navnet på den vigtige samling af legitimationsoplysninger |
| ResourceGroup | Ressourcegruppe af den vigtigste samling af legitimationsoplysninger |
| ResourceId | Azure ressourcestyring ressource-ID. For nøgle samling loggene er dette altid er tasten samling ressource-ID. |
| ResourceProvider | *MICROSOFT. KEYVAULT* |
| ResultSignature  | HTTP-status|
| ResultType      | Resultatet af REST-API-anmodning|
| SubscriptionId | Azure abonnement ID'ET for det abonnement, der indeholder den nøgle samling af legitimationsoplysninger |


## <a name="next-steps"></a>Næste trin

- Brug [Log søgninger i Log Analytics](log-analytics-log-searches.md) til at få vist detaljerede Azure-tasten samling data.

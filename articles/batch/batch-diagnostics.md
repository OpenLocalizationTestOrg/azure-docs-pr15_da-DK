<properties
   pageTitle="Azure logføring til diagnosticering af batchen | Microsoft Azure"
   description="Optage og analysere diagnosticeringsloggene begivenheder til Azure batchen konto ressourcer som grupper og opgaver."
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="10/12/2016"
   ms.author="marsma"/>

# <a name="azure-batch-diagnostic-logging"></a>Azure logføring til diagnosticering af batchen

Som med mange Azure tjenester, udsender tjenesten batchen loghændelser for bestemte ressourcer i ressourcen levetid. Du kan aktivere Azure batchen logge til diagnosticering til at registrere hændelser for de ressourcer, som grupper og opgaver, og derefter bruge logfiler for overvågning og diagnosticering evaluering. Begivenheder som puljen Opret, Slet puljen, opgavens startdato, opgaven er fuldført og andre er inkluderet i batchen diagnosticeringslogfiler.

>[AZURE.NOTE] I denne artikel beskrives logføring af hændelser for batchen konto ressourcerne selv, ikke job og opgave outputdata. Du kan finde oplysninger om lagring af outputdata fra dit job og opgaver, [fastholdes Azure batchen sag og output](batch-task-output.md).

## <a name="prerequisites"></a>Forudsætninger

* [Azure batchen-konto](batch-account-create-portal.md)

* [Azure lagerplads-konto](../storage/storage-create-storage-account.md#create-a-storage-account)

  Kan du bevare batchen diagnosticeringslogfiler, skal du oprette en Azure-lager-konto, hvor Azure skal gemmes loggene. Du angiver denne lagerplads konto når du [aktivere logføring af diagnostik](#enable-diagnostic-logging) for kontoen batchen. Kontoen lagerplads, du angiver, når du aktiverer log af websteder er ikke den samme som en sammenkædede lagerplads-konto, der er nævnt i artiklerne [-programpakker](batch-application-packages.md) og [opgave output brugerdata](batch-task-output.md) .

  >[AZURE.WARNING] Du kan **betale** for de data, der er gemt på kontoen Azure-lager. Dette omfatter diagnosticeringslogfiler, der er beskrevet i denne artikel. Husk dette på, når du designer dit [log opbevaringspolitik](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).

## <a name="enable-diagnostic-logging"></a>Aktivere logføring af diagnostik

Logføring af diagnostik er ikke aktiveret som standard for kontoen batchen. Eksplicit skal du aktivere logføring af diagnostik for hver batchen-konto, du vil overvåge:

[Sådan aktiveres samling af logge til diagnosticering](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)

Vi anbefaler, at du læser komplet [Oversigt over Azure diagnosticeringslogfiler](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artiklen for at få en forståelse af ikke kun hvordan til at aktivere logføring, men kategorierne log understøttes af de forskellige Azure tjenester. For eksempel Azure batchen på nuværende tidspunkt understøtter én log kategori: **Service logfiler**.

## <a name="service-logs"></a>Registrerer tjenesten

Azure Service logfiler over batchen indeholder hændelser, der udsendes af tjenesten Azure batchen i en batchen ressource som en gruppe eller opgave levetid. Hver enkelt hændelse, der udsendes af batchen er gemt i den angivne lagerplads konto i JSON-format. Dette er for eksempel brødteksten i en stikprøve **puljen oprette begivenhed**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Hver begivenhed brødtekst er placeret i en .json fil i den angivne Azure-lager-konto. Hvis du vil have adgang til loggene direkte, kan du gennemse [skemaet for diagnosticeringslogfiler i kontoen lagerplads](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Loggen for tjenesten begivenheder

Tjenesten batchen udsender aktuelt følgende hændelser i loggen for tjenesten. Denne liste kan ikke være omfattende, da yderligere hændelser kan være blevet tilføjet siden i denne artikel sidst blev opdateret.

| **Loggen for tjenesten begivenheder** |
| ------------------ |
| [Oprettelse af gruppe][pool_create] |
| [Puljen Slet start][pool_delete_start] |
| [Puljen Slet fuldført][pool_delete_complete] |
| [Puljen tilpasning af størrelsen start][pool_resize_start] |
| [Tilpasse størrelsen puljen fuldført][pool_resize_complete] |
| [Opgavens startdato][task_start] |
| [Opgaven er fuldført][task_complete] |
| [Opgave-fejl][task_fail] |

## <a name="next-steps"></a>Næste trin

Ud over at gemme diagnosticeringsloggene begivenheder i en Azure-lager-konto, kan du også streame batchen Service Log begivenheder til en [Azure begivenhed Hub](../event-hubs/event-hubs-what-is-event-hubs.md)og sende dem til [Azure Log analyser](../log-analytics/log-analytics-overview.md).

* [Streame Azure diagnosticeringslogfiler til begivenhed hubber](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)

  Du kan streame batchen diagnosticering begivenheder til meget SVG data vandindtrængen tjeneste, begivenhed Hubs. Begivenhed Hubs kan indtager millioner af begivenheder sekundet, som du kan derefter transformere og gemme ved hjælp af enhver realtid analytics-udbyder.

* [Analysere Azure diagnosticeringslogfiler ved hjælp af Log Analytics](../log-analytics/log-analytics-azure-storage-json.md)

  Sende din diagnosticeringslogfiler til Log Analytics, hvor du kan analysere dem i portalen handlinger Management pakke (OMS) eller eksportere dem til analyse i Power BI eller Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx

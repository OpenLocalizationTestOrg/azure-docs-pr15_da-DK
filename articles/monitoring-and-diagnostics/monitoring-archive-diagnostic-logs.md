<properties
    pageTitle="Arkivere Azure diagnosticeringslogfiler | Microsoft Azure"
    description="Lær at arkivere dine Azure diagnosticeringslogfiler for langsigtede opbevaring med en konto med lagerplads."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="johnkem"/>

# <a name="archive-azure-diagnostic-logs"></a>Arkivere Azure diagnosticeringslogfiler
I denne artikel viser vi, hvordan du kan bruge til Azure-portalen, PowerShell Cmdlets, CLI eller REST-API til at arkivere [Azure diagnosticeringslogfiler](monitoring-overview-of-diagnostic-logs.md) på en lagerplads konto. Denne indstilling er nyttig, hvis du gerne vil beholde dine diagnosticeringslogfiler med en valgfri opbevaringspolitik for overvågning, statisk analyser eller sikkerhedskopi.

## <a name="prerequisites"></a>Forudsætninger
Før du begynder, skal du [oprette en lagerplads-konto](../storage/storage-create-storage-account.md#create-a-storage-account) , kan du arkivere dine diagnosticeringslogfiler. Det anbefales, at du ikke bruger en eksisterende lagerplads-konto, der er andre, ikke-overvågning data, der er gemt i den, så du bedre kan styre adgangen til overvågning af data. Hvis du også arkiverer din aktivitetslog og diagnosticering målepunkter til en lagerplads konto, kan det være en god ide at bruge lagerplads konto til din diagnosticeringslogfiler samt til at holde alle overvågningsdata en central placering. Lagerplads kontoen, du bruger skal være et almindelige formål lagerplads konto, ikke en konto til blob-lager.

## <a name="diagnostic-settings"></a>Indstillinger for diagnosticering
Hvis du vil arkivere dine diagnosticeringslogfiler ved hjælp af nedenstående fremgangsmåder, kan du angive en **Diagnosticering indstilling** for en bestemt ressource. En diagnosticering indstilling for en ressource definerer de forskellige kategorier af logfiler, der er, der er gemt eller streamet og output – lagerplads firma og/eller begivenhed hub. Den definerer også opbevaringspolitikken (antallet af dage til at bevare) for hændelser for hver log kategori, der er gemt i en lagerplads konto. Hvis en opbevaringspolitik er indstillet til nul, er begivenheder for den pågældende log kategori gemt på ubestemt tid (dvs, uendelig). En opbevaringspolitik kan ellers være en hvilken som helst antallet af dage mellem 1 og 2147483647. [Du kan læse mere om diagnosticering indstillinger her](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).

## <a name="archive-diagnostic-logs-using-the-portal"></a>Arkiv diagnosticeringslogfiler ved hjælp af portalen

1. Klik på ressource blade for den ressource, som du vil aktivere arkivering af diagnosticeringslogfiler på portalen.
2. Vælg **diagnosticering**i sektionen **overvågning** i menuen Indstillinger for ressourcen.

    ![Overvågning af afsnit i menuen ressourcer](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. Markér afkrydsningsfeltet for at **eksportere til lagerplads konto**og derefter vælge en lagerplads konto. Du kan også angive et antal dage til at bevare disse logfiler ved hjælp af den **opbevaring (dage)** skyderne. En opbevaring af nul dage gemmer logge på ubestemt tid.

    ![Diagnosticering logfiler blade](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. Klik på **Gem**.

Diagnosticeringslogfiler arkiveres med det pågældende lagerplads firma, som genereres nye begivenhed data.

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>Arkiv diagnosticeringslogfiler via PowerShell-cmdletter

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Egenskaben         | Påkrævet | Beskrivelse                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| ResourceId       | Ja      | Ressource-ID på den ressource, som du vil angive en indstilling for diagnosticering.                            |
| StorageAccountId | Nej       | Ressource-ID for kontoen lagerplads, diagnosticeringslogfiler skal gemmes.                          |
| Kategorier       | Nej       | Kommasepareret liste over log kategorier for at aktivere.                                                     |
| Aktiveret          | Ja      | Boolesk værdi, der angiver, om diagnosticering er aktiveret eller deaktiveret på denne ressource.                  |
| RetentionEnabled | Nej       | Boolesk værdi, der angiver, om en opbevaringspolitik er aktiveret for denne ressource.                            |
| RetentionInDays  | Nej       | Antallet af dage, hændelser, der skal bevares mellem 1 og 2147483647. En værdi på nul gemmer logge på ubestemt tid. |

## <a name="archive-the-activity-log-via-the-cross-platform-cli"></a>Arkivere loggen aktivitet via på tværs af platforme CLI

```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| Egenskaben         | Påkrævet | Beskrivelse                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| resourceId       | Ja      | Ressource-ID på den ressource, som du vil angive en indstilling for diagnosticering.                            |
| storageId        | Nej       | Ressource-ID for kontoen lagerplads, diagnosticeringslogfiler skal gemmes.                          |
| kategorier       | Nej       | Kommasepareret liste over log kategorier for at aktivere.                                                     |
| aktiveret          | Ja      | Boolesk værdi, der angiver, om diagnosticering er aktiveret eller deaktiveret på denne ressource.                  |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Arkiv diagnosticeringslogfiler via REST-API
[Se dette dokument](https://msdn.microsoft.com/library/azure/dn931931.aspx) for at finde oplysninger om, hvordan du kan konfigurere en diagnosticering indstilling ved hjælp af Azure skærm REST-API.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Skemaet for diagnosticeringslogfiler i kontoen lagerplads
Når du har konfigureret arkivering, oprettes der en objektbeholder til lagring i kontoen lagerplads, så snart en hændelse indtræffer på en af kategorierne log, du har aktiveret. BLOB inde i beholderen skal du følge de samme formater på tværs af logge til diagnosticering og aktivitet logfilen. Strukturen i disse BLOB er:

> indsigt - logfiler-{lognavnet kategori} / = resourceId/ABONNEMENTER / {abonnement-ID} /RESOURCEGROUPS/ {ressourcenavn gruppe} /PROVIDERS/ {ressourcenavn udbyder} / {ressource Indtast} / {ressourcenavn} / y = {firecifret numerisk år} / m = {tocifret numerisk måned} / d = {tocifret numerisk dag} / h = {tocifret 24-timers ur hour}/m=00/PT1H.json

Eller mere blot

> indsigt - logfiler-{lognavnet kategori} / resourceId = / {ressourcen Id} / y = {firecifret numerisk år} / m = {tocifret numerisk måned} / d = {tocifret numerisk dag} / h = {tocifret 24-timers ur hour}/m=00/PT1H.json

For eksempel være et blob-navn:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Hver PT1H.json blob indeholder en JSON blob af hændelser, der er indtruffet inden for en time, der er angivet i blob URL-adressen (for eksempel, t = 12). Under Præsenter timetallet føjes begivenheder til filen PT1H.json, efterhånden som de opstår. Værdien minut (m = 00) er altid 00, da Diagnosticeringsloggene begivenheder er opdelt individuelle BLOB i timen.

Hver enkelt hændelse gemmes i filen PT1H.json i matrixen "poster", følge dette format:

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Elementnavn  | Beskrivelse                                                                                                 |
|---------------|-------------------------------------------------------------------------------------------------------------|
| tid          | Tidsstemplet for, hvornår hændelsen blev oprettet af tjenesten Azure behandling af anmodningen, svarer begivenheden. |
| resourceId    | Ressource-ID på den påvirkede ressource.                                                                       |
| operationName | Navnet på handlingen.                                                                                      |
| kategori      | Log kategori af begivenheden.                                                                                  |
| Egenskaber    | Sæt `<Key, Value>` par (det vil sige Dictionary), der beskriver oplysningerne om begivenheden.                            |

> [AZURE.NOTE] Egenskaber for og brugen af disse egenskaber kan variere, afhængigt ressourcen.

## <a name="next-steps"></a>Næste trin
- [Hente BLOB til analyse](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Stream diagnosticeringslogfiler til begivenhed hubber](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Få mere at vide om diagnosticeringslogfiler](monitoring-overview-of-diagnostic-logs.md)

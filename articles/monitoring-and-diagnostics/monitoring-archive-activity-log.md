<properties
    pageTitle="Arkivere Azure aktivitet loggen | Microsoft Azure"
    description="Lær at arkivere dine Azure aktivitet logfilen for langsigtede opbevaring med en konto med lagerplads."
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
    ms.date="08/23/2016"
    ms.author="johnkem"/>

# <a name="archive-the-azure-activity-log"></a>Arkivere loggen Azure aktivitet
I denne artikel viser vi, hvordan du kan bruge til Azure-portalen, PowerShell Cmdlets eller på tværs af platforme CLI arkivere dine [**Azure aktivitetslog**](monitoring-overview-activity-logs.md) på en lagerplads konto. Denne indstilling er nyttig, hvis du vil bevare din aktivitetslog mere end 90 dage (med fuld kontrol over opbevaringspolitikken) til overvågning, sikkerhedskopiering eller statisk analyse. Hvis du kun vil beholde dine begivenheder i 90 dage eller mindre du behøver ikke at konfigurere arkivering en lagerplads konto, da aktivitetslog begivenheder bevares i Azure-platformen til 90 dage uden at aktivere arkivering.

## <a name="prerequisites"></a>Forudsætninger
Før du begynder, skal du [oprette en lagerplads-konto](../storage/storage-create-storage-account.md#create-a-storage-account) , kan du arkivere din aktivitet logon. Det anbefales, at du ikke bruger en eksisterende lagerplads-konto, der er andre, ikke-overvågning data, der er gemt i den, så du bedre kan styre adgangen til overvågning af data. Hvis du også arkiverer logge til diagnosticering og målepunkter til en lagerplads konto, kan det være en god ide at bruge lagerplads konto til din aktivitet logon til at holde alle overvågningsdata en central placering. Lagerplads kontoen, du bruger skal være et almindelige formål lagerplads konto, ikke en konto til blob-lager.

## <a name="log-profile"></a>Log profil
Hvis du vil arkivere aktivitet loggen ved hjælp af nedenstående fremgangsmåder, du har indstillet **Log profil** til et abonnement. Log profilen definerer typen af hændelser, der er gemt eller streames og output – lagerplads firma og/eller begivenhed hub. Den definerer også opbevaringspolitikken (antallet af dage til at bevare) for hændelser, der er gemt i en lagerplads konto. Hvis opbevaringspolitikken er indstillet til nul, gemmes begivenheder på ubestemt tid. Dette kan Ellers skal konfigureres til en hvilken som helst værdi mellem 1 og 2147483647. [Du kan læse mere om log profiler her](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles).

## <a name="archive-the-activity-log-using-the-portal"></a>Arkivere aktivitet loggen ved hjælp af portalen
1. Klik på linket **Log aktivitet** i den venstre navigationslinje på portalen. Hvis du ikke kan se et link til loggen aktivitet, skal du først klikke linket **Flere tjenester** .

    ![Gå til aktivitetslog blade](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. Klik på **Eksporter**øverst i bladet.

    ![Klik på knappen Eksportér](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. Markér afkrydsningsfeltet for at **eksportere til en lagerplads konto** i bladet, der vises, og vælg en lagerplads konto.

    ![Angive en lagerplads konto](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Ved hjælp af skyderen eller tekstfeltet, definere et antal dage, der skal beholdes aktivitetslog begivenheder i din lagerplads konto. Hvis du foretrækker at have dine data bevares i kontoen lagerplads på ubestemt tid, skal du angive dette nummer til nul.
5. Klik på **Gem**.

## <a name="archive-the-activity-log-via-powershell"></a>Arkivere loggen aktivitet via PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Egenskaben         | Påkrævet | Beskrivelse                                                                                                                                                                                                                                                                                       |
|------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorageAccountId | Nej       | Ressource-ID for kontoen lagerplads, aktivitetslogfiler skal gemmes.                                                                                                                                                                                                                        |
| Placeringer        | Ja      | Kommasepareret liste over områder, du vil indsamle aktivitetslog hændelser. Du kan få vist en liste over alle områder [ved at besøge denne side](https://azure.microsoft.com/en-us/regions) eller ved hjælp af [Azure Management REST-API](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays  | Ja      | Antallet af dage, for hvilke hændelser skal bevares, mellem 1 og 2147483647. En værdi på nul gemmer logge på ubestemt tid (altid).                                                                                                                                                                                             |
| Kategorier       | Ja      | Kommasepareret liste over kategorier, der indsamles. Mulige værdier er skrive, slette og handling.                                                                                                                                                                                 |
## <a name="archive-the-activity-log-via-cli"></a>Arkivere loggen aktivitet via CLI
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Egenskaben        | Påkrævet | Beskrivelse                                                                                                                                                                                                                                                                                       |
|-----------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Navn            | Ja      | Navnet på din log profil.                                                                                                                                                                                                                                                                         |
| storageId       | Nej       | Ressource-ID for kontoen lagerplads, aktivitetslogfiler skal gemmes.                                                                                                                                                                                                                        |
| placeringer       | Ja      | Kommasepareret liste over områder, du vil indsamle aktivitetslog hændelser. Du kan få vist en liste over alle områder [ved at besøge denne side](https://azure.microsoft.com/en-us/regions) eller ved hjælp af [Azure Management REST-API](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays | Ja      | Antallet af dage, for hvilke hændelser skal bevares, mellem 1 og 2147483647. En værdi på nul skal gemmes loggene på ubestemt tid (altid).                                                                                                                                                                                             |
| kategorier      | Ja      | Kommasepareret liste over kategorier, der indsamles. Mulige værdier er skrive, slette og handling.                                                                                                                                                                                 |

## <a name="storage-schema-of-the-activity-log"></a>Lagerplads skemaet for aktivitet logfilen
Når du har konfigureret arkivering, oprettes en objektbeholder til lagring i kontoen lagerplads, så snart en aktivitet loghændelse finder sted. BLOB inde i beholderen skal du følge de samme formater på tværs af aktivitetslog og diagnosticeringslogfiler. Strukturen i disse BLOB er:

> indsigt-funktionsdygtige-logfiler/navn = standard/resourceId = / ABONNEMENTER / {abonnement-ID} / y = {firecifret numerisk år} / m = {tocifret numerisk måned} / d = {tocifret numerisk dag} / h = {tocifret 24-timers ur hour}/m=00/PT1H.json

For eksempel være et blob-navn:

> insights-Operational-Logs/Name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.JSON

Hver PT1H.json blob indeholder en JSON blob af hændelser, der er indtruffet inden for en time, der er angivet i blob URL-adressen (fx h = 12). Under Præsenter timetallet føjes begivenheder til filen PT1H.json, efterhånden som de opstår. Værdien minut (m = 00) er altid 00, da aktivitetslog begivenheder er opdelt individuelle BLOB i timen.

Hver enkelt hændelse gemmes i filen PT1H.json i matrixen "poster", følge dette format:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Elementnavn    | Beskrivelse                                                                                                    |
|-----------------|----------------------------------------------------------------------------------------------------------------|
| tid            | Tidsstemplet for, hvornår hændelsen blev oprettet af tjenesten Azure behandling af anmodningen, svarer begivenheden.    |
| resourceId      | Ressource-ID på den påvirkede ressource.                                                                          |
| operationName   | Navnet på handlingen.                                                                                         |
| kategori        | Kategori for handlingen, f.eks. Skriv, læse, handling.                                                               |
| resultType      | Typen af resultatet, f.eks. Lykkedes, mislykkedes, Start                                                            |
| resultSignature | Afhænger af ressourcetypen.                                                                                  |
| durationMs      | Varigheden af handlingen i millisekunder                                                                      |
| callerIpAddress | IP-adressen på den bruger, der er udført handlingen, UPN-kravet eller SPN krav baseret på tilgængelighed.         |
| correlationId   | Normalt et GUID i strengformatet. Hændelser, der deler en correlationId hører til den samme uber handling.         |
| identitet        | JSON blob, der beskriver godkendelse og krav.                                                             |
| godkendelse   | BLOB af RBAC egenskaber for begivenheden. Normalt indeholder "handling", "rolle" og "omfang" egenskaber.            |
| niveau           | Niveau for begivenheden. En af følgende værdier: "Kritiske", "Fejl", "Advarsel", "Information" og "Detaljeret" |
| placering        | Område i som placeringen opstod (eller global).                                                             |
| Egenskaber      | Sæt `<Key, Value>` par (det vil sige Dictionary), der beskriver oplysningerne om begivenheden.                             |

> [AZURE.NOTE] Egenskaber for og brugen af disse egenskaber kan variere, afhængigt ressourcen.

## <a name="next-steps"></a>Næste trin
- [Hente BLOB til analyse](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Streame aktivitet loggen til begivenhed hubber](monitoring-stream-activity-logs-event-hubs.md)
- [Få mere at vide om aktivitet logfilen](monitoring-overview-activity-logs.md)

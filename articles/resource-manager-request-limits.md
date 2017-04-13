<properties
   pageTitle="Azure ressourcestyring anmodning om begrænsninger | Microsoft Azure"
   description="Beskriver, hvordan du bruger (throttling) med Azure ressourcestyring anmodninger, når du har nået abonnement begrænsninger."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="tomfitz"/>

# <a name="throttling-resource-manager-requests"></a>(Throttling) ressourcestyring anmodninger

For hver abonnement og lejer ressourcestyring begrænsninger læse anmodninger om at 15.000 timen og skrive anmodninger til 1.200 i timen. Hvis dit program eller script når disse begrænsninger, skal du begrænse dine anmodninger. Dette emne viser Sådan afgør de resterende anmodninger, du har før nå grænsen, og hvordan skal reagere, når du har nået grænsen.

Når du har nået grænsen, får du vist statuskode HTTP **429 for mange anmodninger**.

Antallet af anmodninger er fastsat til dit abonnement eller din lejer. Hvis du har flere samtidige programmer foretager anmodninger om i dit abonnement, anmodninger fra disse programmer føjes sammen til at bestemme antallet af resterende anmodninger.

Anmodninger om abonnement fastsat er dem på involve, der passerer dit abonnement-id, som henter ressourcen grupperer i dit abonnement. Lejer fastsat anmodninger skal ikke medtage dit abonnement-id, som henter gyldige Azure placeringer.

## <a name="remaining-requests"></a>Resterende anmodninger

Du kan bestemme antallet af resterende anmodninger ved at undersøge svar sidehoveder. Hver enkelt anmodning indeholder værdier for antallet resterende læse og skriveanmodninger. I følgende tabel beskrives svar overskrifterne du kan undersøge efter disse værdier:

| Svarheaderen | Beskrivelse |
| --------------- | ----------- |
| x-MS-ratelimit-Remaining-Subscription-reads | Abonnement fastsat læser resterende |
| x-MS-ratelimit-Remaining-Subscription-writes | Abonnement fastsat skriver resterende |
| x-MS-ratelimit-Remaining-tenant-reads | Lejer fastsat læser resterende |
| x-MS-ratelimit-Remaining-tenant-writes | Lejer fastsat skriver resterende |
| x-MS-ratelimit-Remaining-Subscription-Resource-Requests | Abonnement fastsat ressource type anmodninger om resterende.<br /><br />Denne sidehoved værdi returneres kun, hvis en tjeneste har tilsidesat standardgrænsen på. Ressourcestyring tilføjer denne værdi i stedet for abonnement læser eller skriver. |
| x-MS-ratelimit-Remaining-Subscription-Resource-ENTITIES-Read | Abonnement fastsat ressource type af websteder anmodninger resterende.<br /><br />Denne sidehoved værdi returneres kun, hvis en tjeneste har tilsidesat standardgrænsen på. Denne værdi giver antallet af resterende samling anmodninger (listen ressourcer). |
| x-MS-ratelimit-Remaining-tenant-Resource-Requests | Lejer fastsat ressource type anmodninger om resterende.<br /><br />Denne overskrift tilføjes kun for anmodninger på lejer niveau, og kun, hvis en tjeneste har tilsidesat standardgrænsen på. Ressourcestyring tilføjer denne værdi i stedet for lejer læser eller skriver. |
| x-MS-ratelimit-Remaining-tenant-Resource-ENTITIES-Read | Lejer fastsat ressource type af websteder anmodninger resterende.<br /><br />Denne overskrift tilføjes kun for anmodninger på lejer niveau, og kun, hvis en tjeneste har tilsidesat standardgrænsen på. |

## <a name="retrieving-the-header-values"></a>Henter sidehoved værdier

Hentning af disse sidehoved værdier i din kode eller script er ikke anderledes end at hente en sidehoved-værdi. 

I **C#**, skal du eksempelvis henter værdien sidehoved fra et **HttpWebResponse** objekt med navnet **svar** med følgende kode:

    response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)

I **PowerShell**henter du værdien sidehoved fra en Aktiver-WebRequest handling.

    $r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
    $r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
    
Eller, hvis vil have vist de resterende anmodninger om fejlfinding, som du kan angive den **-fejlfinding af** parameter på **PowerShell** -cmdlet.

    Get-AzureRmResourceGroup -Debug
    
Som returnerer en masse oplysninger, herunder svar følgende værdi:

    ...
    DEBUG: ============================ HTTP RESPONSE ============================

    Status Code:
    OK

    Headers:
    Pragma                        : no-cache
    x-ms-ratelimit-remaining-subscription-reads: 14999
    ...

I **Azure CLI**, kan du hente værdien sidehoved ved hjælp af indstillingen mere detaljeret.

    azure group list -vv --json

Som returnerer en masse oplysninger, herunder følgende objekt:

    ...
    silly: returnObject
    {
      "statusCode": 200,
      "header": {
        "cache-control": "no-cache",
        "pragma": "no-cache",
        "content-type": "application/json; charset=utf-8",
        "expires": "-1",
        "x-ms-ratelimit-remaining-subscription-reads": "14998",
        ...

## <a name="waiting-before-sending-next-request"></a>Venter før afsendelse af næste anmodning

Når du kommer til anmodning om grænsen, ressourcestyring returnerer statuskode **429** HTTP og et **Forsøg igen efter** værdien i sidehovedet. Den **Forsøg igen efter** værdi angiver antallet sekunder, der skal vente dit program (eller deres sengetider) før afsendelse af den næste anmodning. Hvis du sender en anmodning, før værdien forsøg er gået, din anmodning behandles ikke, og der returneres en ny værdi prøv igen.

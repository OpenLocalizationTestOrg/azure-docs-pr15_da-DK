<properties
    pageTitle="Brug Autoskalering handlinger til at sende mail og webhook påmindelser. | Microsoft Azure"
    description="Se, hvordan du bruger Autoskalering handlinger til at ringe til URL-webadresser eller send besked via mail i Azure skærm. "
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="ashwink"/>

# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Bruge Autoskalering handlinger til at sende mail og webhook påmindelser i Azure skærm

I denne artikel viser du hvordan konfigurere udløsere så du kan ringe til bestemte URL-webadresser eller send mails baseret på Autoskalering handlinger i Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks gør det muligt at omdirigere i Azure beskeder om meddelelser til andre systemer til efterbehandling eller brugerdefinerede meddelelser. For eksempel routing den vigtige besked til tjenester, der kan håndtere en indgående web anmodning om at sende SMS, log fejl besked et team med at bruge chat eller messaging services osv. Webhook URI skal være et gyldigt HTTP eller HTTPS slutpunkt.

## <a name="email"></a>Mail
Mail sendes til en gyldig mailadresse. Administratorer og medadministratorer af det abonnement, hvor reglen kører får også besked.


## <a name="cloud-services-and-web-apps"></a>Cloud Services og Webapps
Du kan vælge i fra Azure-portalen for Cloud Services og serverfarme (online).

- Vælg **skala ved** metrikværdien.

![skalere med](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## <a name="virtual-machine-scale-sets"></a>Virtuelt skala sæt
Til nyere virtuelle maskiner oprettet med ressourcestyring (Virtual Machine skala sæt), kan du konfigurere det ved hjælp af REST-API, ressourcestyring skabeloner, PowerShell og CLI. En portalen grænseflade er ikke tilgængelig.
Når du bruger skabelonen REST-API eller Ressourcestyring, omfatte meddelelser elementet med følgende indstillinger.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
|Felt                              |Obligatoriske? |Beskrivelse|
|---                                |---        |---|
|handling                          |Ja        |Værdien skal være "Skala"|
|sendToSubscriptionAdministrator    |Ja        |Værdien skal være "sand" eller "falsk"|
|sendToSubscriptionCoAdministrators |Ja        |Værdien skal være "sand" eller "falsk"|
|customEmails                       |Ja        |Værdien kan være null [] eller strengmatrix af mails|
|webhooks                           |Ja        |Værdien kan være null eller er gyldig Uri|
|serviceUri                         |Ja        |en gyldig https Uri|
|Egenskaber                         |Ja        |Værdien skal være tom {} eller kan indeholde nøgle-værdi-par|


## <a name="authentication-in-webhooks"></a>Godkendelse i webhooks
Der er to godkendelse URI formularer:

1. Godkendelse af token base, hvor du gemmer webhook URI med en token ID som en parameter i forespørgslen. For eksempel https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Basisgodkendelse, hvor du kan bruge et bruger-ID og din adgangskode. For eksempelhttps://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## <a name="autoscale-notification-webhook-payload-schema"></a>Autoskalering meddelelse webhook data skema
Når Autoskalering meddelelsen er oprettet, medtages de følgende metadata i webhook data:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|Felt  |Obligatoriske?|    Beskrivelse|
|---|---|---|
|status |Ja    |Status, der angiver, at en Autoskalering handling blev oprettet|
|handling| Ja |For et større over de forekomster af det vil være "Skala ud" og for en nedsat forekomster, vil det være "Skala i"|
|kontekst|   Ja |Konteksten Autoskalering handling|
|tidsstempel| Ja |Tidsstempel, når handlingen Autoskalering blev udløst|
|id |Ja|   Indstillingen Autoskalering ressourcestyring ID|
|Navn   |Ja|   Navnet på indstillingen Autoskalering|
|detaljer|   Ja |Forklaring af den handling, der oprindeligt brugte for tjenesten Autoskalering og ændring af i forekomst antallet|
|subscriptionId|    Ja |Abonnement-ID på den destinationsadresse-ressource, der skaleres|
|resourceGroupName| Ja|    Ressourcegruppe navnet på den destinationsadresse-ressource, der skaleres|
|resourceName   |Ja|   Navnet på den destinationsadresse-ressource, der skaleres|
|Ressourcetypen   |Ja|   Tre understøttes værdier: "microsoft.classiccompute/domainnames/slots/roles" - skytjeneste roller, "microsoft.compute/virtualmachinescalesets" - virtuelt skala sæt, og "Microsoft.Web/serverfarms" - Web App|
|resourceId |Ja|Ressourcestyring ID for den destinationsadresse-ressource, der skaleres|
|portalLink |Ja    |Azure portalen link til siden Oversigt på ressourcen, mål|
|oldCapacity|   Ja |Den aktuelle (gamle) forekomst tæller når Autoskalering tog handlingen skala|
|newCapacity|   Ja |Den nye forekomst optælling, der Autoskalering skaleret ressourcen|
|Egenskaber|    Nej| Valgfrit. Sæt af < nøgle, værdi > par (for eksempel ordbog < streng, streng >). Egenskaber for felt er valgfrit. Du kan angive nøgler og værdier, der kan overføres ved hjælp af data i en brugerdefineret brugergrænseflade eller logik app baseret arbejdsproces. En anden måde til at overføre brugerdefinerede egenskaber tilbage til udgående opkald webhook er at bruge webhook URI selve (som forespørgselsparametre)|

<properties
   pageTitle="Log Analytics beskeder om webhook eksempel"
   description="En af de handlinger, du kan køre som svar på en Log Analytics besked er en *webhook*, som gør det muligt at kalde en ekstern proces gennem en enkelt HTTP-anmodning. I denne artikel vejledes gennem et eksempel for at oprette en webhook handling i en Log Analytics besked ved hjælp af slæk."
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
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="webhooks-in-log-analytics-alerts"></a>Webhooks i Log Analytics-beskeder

En af de handlinger, du kan køre som svar på en [Log Analytics besked](log-analytics-alerts.md) er en *webhook*, som gør det muligt at kalde en ekstern proces gennem en enkelt HTTP-anmodning.  Du kan læse mere om oplysninger om beskeder og webhooks i [beskeder i Log Analytics](log-analytics-alerts.md)

I denne artikel gennemgår vi for et eksempel for at oprette en webhook handling i en Log Analytics besked ved hjælp af slæk, som er en SMS-tjeneste.

>[AZURE.NOTE] Du skal have en slæk konto til at udføre dette eksempel.  Du kan tilmelde dig en gratis konto på [slack.com](http://slack.com).

## <a name="step-1---enable-webhooks-in-slack"></a>Trin 1 - Aktivér webhooks i slæk
2.  Log på slæk på [slack.com](http://slack.com).
3.  Vælge en kanal i sektionen **kanaler** i den venstre rude.  Dette er den kanal, sendes meddelelsen til.  Du kan vælge en af standard kanalerne som **generelle** eller **tilfældigt**.  I et produktionsmiljø, vil du sandsynligvis oprette en særlig kanal som **criticalservicealerts**. <br>

    ![Slæk kanaler](media/log-analytics-alerts-webhooks/oms-webhooks01.png)

3. Klik på **Tilføj en app eller brugerdefineret integration** for at åbne App-katalog.
3.  Skriv *webhooks* i søgefeltet, og vælg derefter **Indgående WebHooks**. <br>

    ![Slæk kanaler](media/log-analytics-alerts-webhooks/oms-webhooks02.png)

4.  Klik på **Installer** ud for dit teamnavn.
5.  Klik på **Tilføj konfiguration**.
6.  Vælg den den kanal, du skal bruge til dette eksempel, og klik derefter på **Tilføj indgående WebHooks integration**.  
6. Kopiere **Webhook URL-adresse**.  Du kommer til at indsætte det i konfigurationen af beskeder. <br>

    ![Slæk kanaler](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Trin 2 – oprette reglen i Log Analytics
1.  [Oprette en besked om](log-analytics-alerts.md) med følgende indstillinger.
    - Forespørgsel:```    Type=Event EventLevelName=error ```
    - Kontrollere, om denne besked hver: 5 minutter
    - Antallet af resultater er: større end 10
    - Over denne tidsramme: 60 minutter
    - Vælg **Ja** for **Webhook** og **ingen** for andre handlinger.
7. Indsæt slæk URL-adressen i feltet **Webhook URL-adresse** .
8. Vælg indstillingen for at **medtage en brugerdefineret JSON-data**.
9. Slæk forventer en data, der er formateret i JSON med en parameter med navnet *tekst*.  Dette er den tekst, det der vises i den meddelelse, der oprettes.  Du kan bruge en eller flere af beskeder om parametre ved hjælp af den *#* symbolet, sådan som i følgende eksempel.

    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```

    ![eksempel JSON data](media/log-analytics-alerts-webhooks/oms-webhooks07.png)

9.  Klik på **Gem** for at gemme reglen.

10. Vent tilstrækkelig tid til en besked til oprettes, og markér derefter afkrydsningsfeltet slæk for en meddelelse, der ligner følgende.

    ![Eksempel på webhook i slæk](media/log-analytics-alerts-webhooks/oms-webhooks08.png)


### <a name="advanced-webhook-payload-for-slack"></a>Avancerede webhook data for slæk

Stort omfang kan du tilpasse indgående meddelelser med slæk. Yderligere oplysninger finder du se [Indgående Webhooks](https://api.slack.com/incoming-webhooks) på webstedet for slæk. Følgende er en mere komplekse data til at oprette en omfattende meddelelse med formatering:

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


Dette vil oprette en meddelelse i slæk stil med følgende.

![Eksempel på meddelelse i slæk](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Oversigt

Med denne besked regel på plads, skal du har en meddelelse, der er sendt til slæk, hver gang kriterierne er opfyldt.  

Dette er kun et eksempel på en handling, som du kan oprette svar til en besked.  Du kan oprette en webhook handling, der kalder en anden ekstern tjeneste, en runbook handling til at starte en runbook i Azure Automation eller en e-mail-handling til at sende en mail til dig selv eller andre modtagere.   

## <a name="next-steps"></a>Næste trin

- Lære mere om [beskeder i Log Analytics](log-analytics-alerts.md) , herunder andre handlinger.
- [Opret runbooks i Azure automatisering](../automation/automation-webhooks.md) , der kan ringes fra en webhook.

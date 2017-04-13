<properties
    pageTitle="Lodret skalere Azure virtuelt skala sæt | Microsoft Azure"
    description="Sådan lodret skalere et virtuelt i svar til overvågning beskeder med Automation Azure"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="madhana"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="guybo"/>

# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Lodret Autoskalering med virtuelt skala datasæt

I denne artikel beskrives, hvordan lodret skalere Azure [Virtuelt skala sæt](https://azure.microsoft.com/services/virtual-machine-scale-sets/) med eller uden reprovisioning. Se [lodret skalerer Azure virtuelt med Automation Azure](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md)af lodret skalering af FOS som ikke er i skala sæt.

Lodret skalering, også kaldet _skalere op_ og _indskrænke_, betyder, at øge eller mindske VM (virtual machine) størrelser som svar på en arbejdsbyrde. Sammenlign det med [vandret skalering](./virtual-machine-scale-sets-autoscale-overview.md), også kaldet _Skaler ud_ og _skalering i_, hvor antallet FOS ændres afhængigt af arbejdsbelastningen.

Reprovisioning betyder, at fjerne en eksisterende VM og erstatte den med et nyt kort. Når du forøge eller formindske størrelsen på FOS i en VM skala sæt, i nogle tilfælde vil du ændre størrelsen på eksisterende FOS og bevare dine data, mens i andre tilfælde skal du installere nye FOS af den nye størrelse. Dette dokument omhandler begge tilfælde.

Lodret skalering kan være nyttige, når:

- En tjeneste, der er bygget på virtuelle maskiner er under anvendes (for eksempel på weekender). Reducere størrelsen VM kan reducere månedlige omkostninger.
- Større VM at imødegå større behov uden at oprette ekstra FOS.

Du kan konfigurere lodret skalering til at være udløste baseret på metriske basis beskeder fra din VM skala angive. Når påmindelsen aktiveres udløses den en webhook, der udløser en runbook, som kan skalere dit målestoksforhold angive op eller ned. Lodret skalering kan konfigureres ved at følge disse trin:

1. Opret en Azure automatisering konto med mulighed for Kør som.
2. Importere Azure automatisering lodret skalering runbooks for VM skala sæt til dit abonnement.
3. Føje en webhook til din runbook.
4. Føje en besked til din VM skala sæt ved hjælp af en meddelelse om webhook.

> [AZURE.NOTE] Lodret Autoskalering kan kun foretages i visse områder af VM størrelser. Sammenligne specifikationer af hver størrelse inden du beslutter, at skalere fra en til en anden (højere tal altid angiver ikke VM større). Du kan vælge at skalere mellem de følgende par af størrelser:

>| VM størrelser skalering par |   |
|---|---|
|  Standard_A0 | Standard_A11 |
|  Standard_D1 |  Standard_D14 |
|  Standard_DS1 |  Standard_DS14 |
|  Standard_D1v2 |  Standard_D15v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Oprette en Azure automatisering konto med mulighed for Kør som

Det første, du skal gøre, er at oprette en Azure automatisering-konto, der er vært for runbooks bruges til at skalere VM skala angive forekomster. Senest introduceret [Azure automatisering](https://azure.microsoft.com/services/automation/) funktionen "Kør som konto", som gør indstilling af tjenesten hovedstolen for automatisk kører på runbooks på vegne af en bruger meget nemt. Du kan læse mere om dette i følgende artikel:

* [Godkende Runbooks med Azure Kør som konto](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importere Azure automatisering lodret skalering runbooks til dit abonnement

Runbooks bruges til at skalere lodret din VM skala sæt allerede er udgivet i galleriet Azure automatisering Runbook. Udfør trinnene i denne artikel for at importere dem til dit abonnement:

* [Modulet og Runbook gallerier til Azure automatisering](../automation/automation-runbook-gallery.md)

Vælg indstillingen Gennemse galleriet i menuen Runbooks:

![Runbooks der skal importeres][runbooks]

De runbooks, der skal importeres vises. Vælg runbook baseret på, om du vil lodret skalering med eller uden reprovisioning:

![Runbooks galleri][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Føje en webhook til din runbook

Når du har importeret føje runbooks skal du en webhook til runbook, så den kan udløses af en påmindelse fra en VM skala angive. Oplysninger om oprettelse af en webhook til din Runbook er beskrevet i denne artikel:

* [Azure automatisering webhooks](../automation/automation-webhooks.md)

> [AZURE.NOTE] Kontrollér, at du kopierer webhook URI før du lukker dialogboksen webhook, som du skal bruge dette i næste afsnit.

## <a name="add-an-alert-to-your-vm-scale-set"></a>Tilføje en besked til at angive din VM skala

Nedenfor vises en PowerShell-script, som viser, hvordan du tilføjer en besked til at angive en VM skala. Refererer til at finde navnet på metrisk forespørgselsreglen den vigtige besked på følgende artikel: [Azure skærm Autoskalering almindelige målepunkter](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [AZURE.NOTE] Det anbefales at konfigurere et begrundet tidsramme for den vigtige besked for at undgå at udløse lodret skalering, og alle tilknyttede tjenesten blev afbrudt, for ofte. Overvej et vindue med mindst 20-30 minutter eller mere. Overvej at vandret skalering, hvis du har brug at undgå afbrydelser.

Du kan finde flere oplysninger om, hvordan du Opret beskeder referere til i følgende artikler:

* [Eksempler på Azure skærm PowerShell Hurtig start](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Eksempler på Azure skærm på tværs af platforme CLI Hurtig start](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Oversigt

I denne artikel viste enkle lodret skalering eksempler. Med disse dokumentkomponenter - automatisering firma, runbooks, webhooks, beskeder - kan du forbinde en bred vifte af hændelser med et brugerdefineret sæt handlinger.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png

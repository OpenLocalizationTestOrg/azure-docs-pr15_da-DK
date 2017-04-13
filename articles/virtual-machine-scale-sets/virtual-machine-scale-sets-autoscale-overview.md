<properties
    pageTitle="Automatisk skalering og virtuelt skalere sæt | Microsoft Azure"
    description="Lære at bruge diagnosticering og Autoskalering ressourcer automatisk skalere virtuelle maskiner i et sæt skala."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>Automatisk skalering og virtuelt skalere sæt

Automatisk skalering af virtuelle maskiner i et sæt skala er oprettelse eller sletning af computere i dialogboksen Angiv efter behov for at matche krav til ydeevne. Efterhånden som mængde arbejde vokser, kan et program kræver yderligere ressourcer til at aktivere det til at udføre opgaver effektivt.

Automatisk skalering er en automatiseret proces, som gør det muligt at lette administration. Ved at reducere spild, behøver du ikke at kontinuerligt overvåge systemets ydeevne eller beslutte, hvordan du administrerer ressourcer. Skalering er en elastiske proces. Flere ressourcer kan tilføjes som Indlæs øges, men som behov afgang ressourcer kan fjernes for at minimere omkostninger og vedligeholde ydeevne.

Konfigurere automatisk skalering på en skala, angive ved hjælp af en skabelon til Azure Ressourcestyring, Azure PowerShell, Azure CLI eller Azure portalen.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Konfigurere skalering ved hjælp af Ressourcestyring skabeloner

I stedet for implementering og administration af hver ressource i dit program separat, kan du bruge en skabelon, der installerer alle ressourcer i en enkelt, koordineret handling. Programmet ressourcer er defineret i skabelonen, og installation parametre er angivet for forskellige miljøer. Skabelonen består af JSON og udtryk, som du kan bruge til at oprette værdier til din installation. Se på [redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md)for at få mere for at vide.

På skabelonen, kan du angive elementet kapacitet:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

Kapacitet identificerer antallet af virtuelle maskiner i dialogboksen Angiv. Du kan manuelt ændre kapaciteten ved at anvende en skabelon med en anden værdi. Hvis du anvender en skabelon til at ændre kun kapaciteten, kan du medtage kun SKU elementet med den opdaterede kapacitet.

Automatisk ændre kapaciteten af din målestoksforhold, der er angivet ved hjælp af kombinationen af autoscaleSettings ressourcen og filtypenavnet diagnosticering.

### <a name="configure-the-azure-diagnostics-extension"></a>Konfigurere filtypenavnet Azure diagnosticering

Automatisk skalering kan kun udføres, hvis målepunkter af websteder er gået igennem på hver virtuel maskine i sættet skala. Filtypenavnet Azure diagnosticering leverer overvågning og diagnosticering funktioner, der opfylder Autoskalering ressourcen målepunkter samling behov. Du kan installere filtypenavnet som en del af skabelonen ressourcestyring.

I dette eksempel vises de variabler, der bruges i skabelonen til at konfigurere filtypenavnet diagnosticering:

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Parametre er angivet, når skabelonen, der er installeret. I dette eksempel er navnet på kontoen lagerplads, hvor dataene lagres og navnet på sættet skala, hvor data indsamles angivet. I dette eksempel Windows Server indsamles kun tæller det antal tråde ydeevne også. Alle de tilgængelige tællere i ydeevne i Windows eller Linux kan bruges til at indsamle diagnosticeringsoplysninger og kan medtages i konfigurationen filtypenavn.

I dette eksempel vises definitionen af filtypenavnet i skabelonen:

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Når filtypenavnet diagnosticering kører, indsamles dataene i en tabel, der er placeret i kontoen lagerplads, du angiver. Du kan finde de indsamlede data i tabellen WADPerformanceCounters:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Konfigurere autoScaleSettings ressourcen

AutoscaleSettings ressourcen bruger oplysningerne fra filtypenavnet diagnosticering til at beslutte, om du vil øge eller mindske antallet af virtuelle maskiner i sættet skala.

I dette eksempel vises konfigurationen af ressourcen i skabelonen:

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

I det foregående eksempel oprettes to regler til at definere de automatiske skalering handlinger. Den første regel definerer handlingen skala ud, og den anden regel definerer handlingen skala i. Disse værdier leveres i reglerne:

- **metricName** - denne værdi er den samme som tælleren ydeevne, som du angav i variablen wadperfcounter for filtypenavnet diagnosticering. I eksemplet ovenfor bruges tælleren antal tråde.  
- **metricResourceUri** - denne værdi er resource identifier for sættet virtuelt skala. Dette id indeholder navnet på ressourcegruppen, navnet på den ressource udbyder og navnet på den skala, der er angivet til at skalere.
- **timeGrain** – denne værdi er granularitet målene, der er indsamlet. I det foregående eksempel, der indsamles data på et interval af et minut. Denne værdi bruges sammen med timeWindow.
- **Statistik** – denne værdi bestemmer, hvordan målene kombineres for at tage højde for den automatiske skalering handling. De mulige værdier er: gennemsnit, Min og Max.
- **timeWindow** – denne værdi er området af tid, der indsamles forekomst af data. Det skal være mellem 5 minutter og 12 timer.
- **timeAggregation** – denne værdi bestemmer, hvordan de data, der indsamles skal kombineres med tiden. Standardværdien er gennemsnit. De mulige værdier er: gennemsnit, Minimum, maksimum, sidste, Total, antal.
- **operatoren** – denne værdi er den operator, der bruges til at sammenligne metriske dataene og i grænseværdi for. De mulige værdier er: er lig med, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
- **grænseværdi for** – denne værdi er den værdi, der udløser handlingen skala. Sørg for at give tilstrækkelige forskel i grænseværdi for handlingen skala ud og grænsen for handlingen skala i. Hvis du angiver værdierne, der skal være den samme, forventer systemet konstant ændring, der forhindrer implementere en skalering handling. For eksempel virker angive begge til 600 tråde i det foregående eksempel ikke.
- **retning** – denne værdi angiver den handling, der udføres, når grænseværdien opnås. De mulige værdier er Forøg eller Formindsk.
- **type** – denne værdi er typen handling, der skal udføres, og skal være indstillet til ChangeCount.
- **værdi** – denne værdi er antallet af virtuelle maskiner, der er tilføjet eller fjernet fra sættet skala. Denne værdi skal være 1 eller større.
- **cooldown** – denne værdi er mængden tid der skal ventes siden den sidste skalering handling, før den næste handling opstår. Denne værdi skal være mellem et minut og en uge.

Afhængigt af den ydeevne tæller, du bruger, er nogle af elementerne i konfigurationen af skabelonen bruges anderledes. Tælleren ydeevne er antal tråde i det foregående eksempel, grænseværdien er 650 for skala ud af en handling, og grænseværdien er 550 til handlingen skala i. Hvis du bruger en tæller som % Processortid, er angivet i grænseværdi til procentdelen af CPU'en, der bestemmer en skalering handling.

Når der oprettes en belastning på de virtuelle maskiner, der udløser handlingen skalering, øges kapaciteten af sæt baseret på værdien i skabelonen. For eksempel i en skala er angive, hvor kapaciteten, der er angivet til 3 og værdien skala handling angivet til 1:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Når afkrydsningsfeltet Indlæs der oprettes, der medfører, at den gennemsnitlige tråd tælle for at gå over grænseværdi 650:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

En skala fra handling udløses, som udløser kapaciteten af sæt skal øges med en:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

Og en virtuel maskine føjes til sættet skala:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Når du har en cooldown periode på fem minutter, og det gennemsnitlige antal tråde på maskiner forbliver over 600, føjes en anden computer til sættet. Hvis den gennemsnitlige tråd Tæl forbliver under 550, kapaciteten af sættet skala reduceres med en og en maskine er fjernet fra sættet.

## <a name="set-up-scaling-using-azure-powershell"></a>Konfigurere skalering ved hjælp af Azure PowerShell

For at se eksempler på brug af PowerShell til at konfigurere Autoskalering skal du se på [Azure skærm PowerShell Hurtig start eksempler](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="set-up-scaling-using-azure-cli"></a>Konfigurere skalering ved hjælp af Azure CLI

For at se eksempler på brug af Azure CLI til konfiguration af Autoskalering skal du se på [Azure skærm på tværs af platforme CLI Hurtig start eksempler](../monitoring-and-diagnostics/insights-cli-samples.md).

## <a name="set-up-scaling-using-the-azure-portal"></a>Konfigurere skalering ved hjælp af portalen Azure

For at se et eksempel på ved hjælp af portalen Azure til konfiguration af Autoskalering skal du se på [Opret et virtuelt skala angive ved hjælp af portalen Azure](virtual-machine-scale-sets-portal-create.md).

## <a name="investigate-scaling-actions"></a>Undersøge skalering handlinger

- [Azure portal]() - i øjeblikket kan du få en begrænset mængde oplysninger ved hjælp af portalen.
- [Azure ressource Explorer]() - dette værktøj er bedst for udforske den aktuelle tilstand for dit skala sæt. Følg denne sti og skal du se afsnittet visningen forekomst af sættet skala, du har oprettet: abonnementer > {dit abonnement} > resourceGroups > {ressourcegruppe} > udbydere > Microsoft.Compute > virtualMachineScaleSets > {skala sættet} > virtualMachines
- Azure PowerShell - Brug denne kommando for at få nogle oplysninger:

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput

- Oprette forbindelse til virtuelt jumpbox, ligesom du ville gøre en hvilken som helst anden computer og derefter kan du få fjernadgang til de virtuelle maskiner i det målestoksforhold, der er angivet til at overvåge særskilte processer.

## <a name="next-steps"></a>Næste trin

- Se på [automatisk skalere computere i et virtuelt skala angive](virtual-machine-scale-sets-windows-autoscale.md) at se et eksempel på, hvordan du opretter en skala, der er konfigureret med automatisk skalering, som er konfigureret.
- Finde eksempler på Azure skærm overvågningsfunktioner i [Azure skærm PowerShell Hurtig start eksempler](../monitoring-and-diagnostics/insights-powershell-samples.md)
- Få mere at vide om besked om funktioner i [Brug af Autoskalering handlinger for at sende mail og webhook påmindelser i Azure skærm](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
- Få mere at vide om, hvordan du [logger Brug overvågningslog til at sende mail og webhook påmindelser i Azure skærm](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
- Få mere at vide om [Avancerede Autoskalering scenarier](./virtual-machine-scale-sets-advanced-autoscale.md).

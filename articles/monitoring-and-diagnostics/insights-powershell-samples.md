<properties
    pageTitle="Azure eksempler på skærm PowerShell Hurtig start. | Microsoft Azure"
    description="Bruge PowerShell til at få adgang til Azure skærm funktioner som Autoskalering, beskeder, webhooks og søgning aktivitetslogfiler."
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
    ms.date="10/26/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-powershell-quick-start-samples"></a>Eksempler på Azure skærm PowerShell Hurtig start

Denne artikel kan du indsamle PowerShell-kommandoer kan hjælpe dig med at få adgang til Azure skærm funktioner. Azure skærm giver dig mulighed for at Autoskalering Cloud Services, virtuelle maskiner og Web Apps og til at sende påmindelser eller ringe URL-webadresser, der er baseret på værdierne af konfigurerede telemetridata.

>[AZURE.NOTE] Azure skærm er det nye navn til Hvad hedder "Azure indsigt" indtil Sept 25 2016. Navneområder og dermed kommandoerne nedenfor indeholder stadig "viden".

## <a name="set-up-powershell"></a>Konfigurere PowerShell
Hvis du ikke allerede har gjort det, du konfigurere PowerShell til at køre på din computer. Se, [hvordan du installere og konfigurere PowerShell](../powershell-install-configure.md) kan finde flere oplysninger.

## <a name="examples-in-this-article"></a>Eksemplerne i denne artikel

Eksemplerne i artiklen illustrerer, hvordan du kan bruge-cmdletter Azure skærm. Du kan også gennemse hele listen over Azure skærm PowerShell-cmdlet'er på [Azure skærm (indsigt) cmdletter](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).


## <a name="sign-in-and-use-subscriptions"></a>Logge på og bruge abonnementer

Først skal du logge på abonnementet Azure.

```PowerShell
Login-AzureRmAccount
```

Dette kræver, at du til at logge på. Når du gør, din konto, vises TenantId og standard abonnement-Id. Alle Azure-cmdletter arbejde i konteksten for din standard-abonnement. Brug følgende kommando for at få vist listen over de abonnementer, du har adgang til.

```PowerShell
Get-AzureRmSubscription
```

Hvis du vil ændre dit arbejde kontekst til et andet abonnement, skal du bruge følgende kommando.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-audit-logs-for-a-subscription"></a>Hente overvågningslogge til et abonnement
Brug den `Get-AzureRmLog` cmdlet.  Her er nogle almindelige eksempler.

Få logposter fra denne dato og klokkeslæt til at præsentere:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Få logposter mellem datasættet dato og klokkeslæt:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Få logposter fra en bestemt ressourcegruppe:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Få logposter fra en bestemt ressource udbyder mellem datasættet dato og klokkeslæt:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Få alle logposter til en bestemt person:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

Følgende kommando henter de sidste 1000 begivenheder fra overvågningsloggen:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog`understøtter mange andre parametre. Se den `Get-AzureRmLog` reference kan finde flere oplysninger.

>[AZURE.NOTE] `Get-AzureRmLog`kun indeholder 15 dage efter oversigt. Bruge parameteren **- MaxEvents** giver dig mulighed at forespørge på de sidste N hændelser, ud over 15 dage. Brug REST-API eller SDK (C# eksempel ved hjælp af SDK) til access hændelser er ældre end 15 dage. Hvis du ikke skal medtage **Starttidspunkt**, er standardværdien **Sluttidspunkt** minus en time. Hvis du ikke skal medtage **Sluttidspunkt**, er standardværdien aktuelt klokkeslæt. Hele tiden er i UTC.

## <a name="retrieve-alerts-history"></a>Hente oversigt over beskeder
For at få vist alle beskeder om begivenheder, kan du forespørge loggene Azure ressourcestyring ved hjælp af eksemplerne nedenfor.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

For at få vist historikken for en bestemt beskeder om regel, du kan bruge den `Get-AzureRmAlertHistory` cmdlet, der passerer i feltet ressource-ID for reglen.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Den `Get-AzureRmAlertHistory` cmdlet understøtter forskellige parametre. Du kan finde flere oplysninger, se [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## <a name="retrieve-information-on-alert-rules"></a>Hente oplysninger om regler for påmindelser
Alle af følgende kommandoer fungerer på en ressourcegruppe med navnet "montest".

Få vist alle egenskaber for reglen:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Hente alle vigtige beskeder for en ressourcegruppe:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Hent alle de regler, der er angivet for en målliste ressource. For eksempel angive regler for alle påmindelser på en VM.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule`understøtter andre parametre. Du kan få flere oplysninger i [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) .

## <a name="create-alert-rules"></a>Oprette regler for påmindelser
Du kan bruge den `Add-AlertRule` til at oprette, opdatere eller deaktivere en besked om.

Du kan oprette mail og webhook egenskaber ved hjælp af `New-AzureRmAlertRuleEmail` og `New-AzureRmAlertRuleWebhook`henholdsvis. Tildele dem som handlinger i cmdlet reglen til egenskaben **Handlinger** for reglen.

Den næste sektion indeholder et eksempel, der viser, hvordan du opretter en besked om med forskellige parametre.

### <a name="alert-rule-on-a-metric"></a>Beskeder om regel for en metrikværdi
I følgende tabel beskrives de parametre og værdier, der bruges til at oprette en besked, ved hjælp af en metrikværdi.


|parameter|værdi|
|---|---|
|Navn|  simpletestdiskwrite|
|Placeringen af reglen besked|   Indtastning af østasiatiske USA|
|ResourceGroup| montest|
|TargetResourceId|  /subscriptions/S1/resourceGroups/montest/Providers/Microsoft.Compute/virtualMachines/testconfig|
|MetricName på den besked, der er oprettet|   \PhysicalDisk (_Total) \Disk/sekund. Se den `Get-MetricDefinitions` cmdlet under om, hvordan du hente de nøjagtige metriske navne|
|operatoren|  Størreend|
|Grænseværdi (antal/sec i for denne metrikværdi)|    1|
|WindowSize (hh:mm:ss format)|  00:05:00|
|aggregator (statistikken for metrikværdi, som bruger gennemsnitlige antal i dette tilfælde)|  Gennemsnit|
|brugerdefineret mails (strengmatrix)|'foo@example.com','bar@example.com'|
|sende mail til ejere, bidragydere og læsere|    -SendToServiceOwners|

Oprette en mail-handling

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Oprette en Webhook handling

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Oprette reglen på CPU % metrisk under en klassisk VM

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Hente reglen

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Tilføj besked cmdlet opdaterer også reglen, hvis der allerede findes en reglen for de angivne egenskaber. Hvis du vil deaktivere en besked om, at medtage parameteren **- DisableRule**.

### <a name="alert-on-audit-log-event"></a>Giv besked om overvågningslog loghændelse

>[AZURE.NOTE] Denne funktion er stadig i Vis udskrift.

I dette scenarie skal du sende mail, når et websted er blevet startet i mit abonnement i ressource gruppe *abhingrgtest123*.

Konfiguration af en mailregel

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Konfiguration af en regel for webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Oprette reglen på begivenheden

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Hente reglen

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

Den `Add-AlertRule` cmdlet giver mulighed for forskellige andre parametre. Du kan finde flere oplysninger, se [Tilføj AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Få en liste over tilgængelige målepunkter for beskeder
Du kan bruge den `Get-AzureRmMetricDefinition` til at få vist en liste over alle måleredskaber for en bestemt ressource.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

I følgende eksempel genererer en tabel med en metrikværdi navn og enheden for den.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

En komplet liste over tilgængelige indstillinger for `Get-AzureRmMetricDefinition` er aktiveret i [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## <a name="create-and-manage-autoscale-settings"></a>Oprette og administrere Autoskalering indstillinger
En ressource, som en WebApp, VM, skybaseret tjeneste eller VM skala indstille kan have kun én Autoskalering indstilling, der er konfigureret til den.
Men hver Autoskalering indstilling kan have flere profiler. Én efter profilen, ydeevne-baserede skala og en anden opgave for en plan baseret profil. Hver profil kan have flere regler, der er konfigureret på den. Du kan finde flere oplysninger om Autoskalering, se, [hvordan du Autoskalering et program](../cloud-services/cloud-services-how-to-scale.md).

Her er de trin, som vi bruger:

1. Oprette regler.
2. Oprette profil(er) tilknytte de regler, du har oprettet tidligere til profilerne.
3. Valgfrit: Opret meddelelser i Autoskalering ved at konfigurere egenskaberne webhook og mail.
4. Oprette en Autoskalering indstilling med et navn på destinationsprogram ressourcen ved at knytte profiler og meddelelser, du oprettede i de forrige trin.

Følgende eksempler viser, hvordan du kan oprette en Autoskalering indstilling af en VM målestoksforhold, der er angivet for en Windows-operativsystemet baseret ved hjælp af CPU anvendelsen metrisk.

Først skal du oprette en regel, der skala ud med et forekomst Tæl større.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```     

Dernæst skal du oprette en regel, der skala på, med en forekomst Tæl Formindsk.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

Derefter oprette en profil for reglerne.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Oprette en webhook egenskab.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Oprette egenskaben meddelelse for at indstillingen Autoskalering, herunder mail og webhook, som du tidligere har oprettet.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Til sidst skal oprette Autoskalering indstillingen for at tilføje den profil, som du oprettede ovenfor.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Du kan finde flere oplysninger om administration af Autoskalering indstillinger [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Oversigt over Autoskalering
I følgende eksempel viser, hvordan du kan få vist seneste Autoskalering og beskeder om begivenheder. Bruge overvågningslog log Søg til at få vist historikken Autoskalering.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Du kan bruge den `Get-AzureRmAutoScaleHistory` -cmdlet til hentning Autoskalering oversigt.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Du kan finde yderligere oplysninger finder [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Få vist oplysninger om en Autoskalering indstilling
Du kan bruge den `Get-Autoscalesetting` til at hente flere oplysninger om indstillingen Autoskalering.

I følgende eksempel viser detaljer om alle Autoskalering indstillinger i ressource gruppe 'myrg1'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

I følgende eksempel viser detaljer om alle Autoskalering indstillinger i ressource gruppe 'myrg1' og specifikt Autoskalering indstillingen med navnet 'MyScaleVMSSSetting'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Fjerne en Autoskalering indstilling
Du kan bruge den `Remove-Autoscalesetting` -cmdlet til at slette en Autoskalering indstilling.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-audit-logs"></a>Administrer log profiler til overvågningslogge

Du kan oprette en *logfil profil* og eksportere data fra din overvågningslogge til en lagerplads konto, og du kan konfigurere dataopbevaring til den. Du kan eventuelt også streame dataene til din begivenhed Hub. Bemærk, at denne funktion er i øjeblikket i Vis udskrift, og du kan kun oprette én log profil per abonnement. Du kan bruge følgende cmdletter med dit aktuelle abonnement til at oprette og administrere log profiler. Du kan også vælge et bestemt abonnement. Selvom PowerShell standarder til det aktuelle abonnement, du kan altid ændre den ved hjælp af `Set-AzureRmContext`. Du kan konfigurere overvågningslogge til distribution data med en hvilken som helst lagerplads firma eller en begivenhed Hub i dette abonnement. Data skrives som blob-filer i JSON-format.

### <a name="get-a-log-profile"></a>Få en log-profil
For at hente dine eksisterende log profiler, bruge den `Get-AzureRmLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Tilføj en log profil uden dataopbevaring

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Fjerne en log-profil

```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Tilføje en log profil med dataopbevaring

Du kan angive egenskaben **- RetentionInDays** med antallet af dage, som et positivt heltal, hvor dataene bevares.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Tilføj log profil med opbevaring og EventHub
Ud over routing dine data til lagerplads konto, kan du også streame den til en begivenhed Hub. Bemærk, at i dette preview-versionen og opbevaring konfiguration af konto er obligatorisk, men begivenhed Hub konfiguration er valgfrit.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurere logfiler til diagnosticering
Mange Azure tjenester giver yderligere logfiler og telemetri, herunder Azure netværk sikkerhedsgrupper, Software Indlæs balancere, nøgle samling, Azure Søg Services og logik Apps og de kan være konfigureret til at gemme data i kontoen Azure-lager. Denne handling kan kun udføres på en ressourceniveau og kontoen lagerplads skal være til stede i det samme område som destination ressource, hvor indstillingen diagnosticering er konfigureret.

### <a name="get-diagnostic-setting"></a>Få diagnosticering til at

```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Diagnosticering indstillingen Deaktiver

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Aktiver diagnosticering indstilling uden opbevaring

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Aktiver diagnosticering indstilling med tilbageholdelse

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktiver diagnosticering indstilling med tilbageholdelse for en bestemt log kategori

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

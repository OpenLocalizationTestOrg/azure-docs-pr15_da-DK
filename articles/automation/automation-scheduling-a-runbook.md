<properties 
   pageTitle="Planlægge en runbook i Azure Automation | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du opretter en tidsplan i Azure automatisering, så du kan starte en runbook automatisk, på et bestemt tidspunkt eller en tilbagevendende tidsplan."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/05/2016"
   ms.author="bwren" />

# <a name="scheduling-a-runbook-in-azure-automation"></a>Planlægge en runbook i Azure Automation

Hvis du vil planlægge et runbook i Azure automatisering til at starte på et bestemt tidspunkt, sammenkæder du den med en eller flere tidsplaner. En tidsplan kan konfigureres til at køre én gang eller på en opstår igen hver time eller dagligt tidsplan for runbooks i portalen Azure klassisk og runbooks i portalen Azure, du kan desuden planlægge dem til ugentligt, månedligt, bestemte dage i ugen eller dage på måneden eller en bestemt dag i måneden.  En runbook kan sammenkædes med flere tidsplaner, og en tidsplan kan have flere runbooks, der er knyttet til den.


## <a name="creating-a-schedule"></a>Oprette en tidsplan

Du kan oprette en ny tidsplan for runbooks i portalen Azure, i portalen klassisk eller med Windows PowerShell. Du har også mulighed for at oprette en ny tidsplan, når du sammenkæder en runbook til en tidsplan for portalen Azure klassiske eller Azure.

>[AZURE.NOTE] Når du knytter en tidsplan med et runbook, automatisering gemmer de aktuelle versioner af moduler i din konto og links dem til samme skema.  Det betyder, at hvis du havde et modul med version 1.0 på din konto, når du har oprettet en tidsplan og derefter opdatere modulet til version 2.0, tidsplanen fortsætter med at bruge 1.0.  Hvis du vil bruge den opdaterede modul version, skal du oprette en ny tidsplan. 

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Oprette en ny tidsplan i portalen Azure klassisk

1. Vælg automatisering i Azure klassisk portalen, og derefter vælge navnet på en konto med automation.
1. Vælg fanen **Aktiver** .
1. Klik på **Tilføj indstillingen**nederst i vinduet.
1. Klik på **Tilføj tidsplan**.
1. Skriv et **navn** og eventuelt en **Beskrivelse** til den nye schedule.your tidsplan kører **Én gang**, **hver time**, **dagligt**, **Ugentligt**eller **månedlige**.
1. Angiv et **Starttidspunkt** og andre indstillinger afhængigt af typen plan, du har valgt.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Oprette en ny tidsplan i portalen Azure

1. Klik på feltet **Aktiver** for at åbne bladet **Aktiver** i portalen Azure fra kontoen automatisering.
2. Klik på feltet **tidsplaner** for at åbne bladet **tidsplaner** .
3. Klik på **Tilføj en tidsplan** i øverst del af bladet.
4. Skriv et **navn** og eventuelt en **Beskrivelse** til den nye tidsplan på bladet **ny tidsplan** .
5. Vælg om planen skal køre én gang eller på en reoccurring tidsplan ved at markere **én gang** eller **Gentagelse**.  Hvis du markerer **én gang** angive et **Starttidspunkt** og klik derefter på **Opret**.  Hvis du vælger **Gentagelse**, Angiv et **Starttidspunkt** og hyppigheden for, hvor ofte du vil runbook skal gentages - efter **time**, **dag**, **uge**eller **måned**.  Hvis du vælger **uge** eller **måned** fra på rullelisten, **indstillingen gentagelse** vises i bladet og efter valg af bladet **indstillingen gentagelse** der præsenteres og du kan vælge dag i ugen, hvis du har valgt **uge**.  Hvis du valgte **måned**, kan du vælge ved **ugedage** eller bestemte dage i måneden i kalenderen og til sidst skal du gøre du vil køre den på den sidste dag i måneden eller ej, og klik derefter på **OK**.   

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Oprette en ny tidsplan med Windows PowerShell

Du kan bruge den [Ny AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) til at oprette en ny tidsplan i Azure automatisering til klassisk runbooks eller [Ny AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) cmdlet til runbooks i portalen Azure. Du skal angive starttidspunktet for tidsplanen, og hvor ofte der skal udføres.

Kommandoerne følgende eksempel viser, hvordan du opretter en ny tidsplan, der kører hver dag på 3:30 PM starter på 20 januar 2015 med en Azure Service Management-cmdlet.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

Kommandoerne følgende eksempel viser, hvordan du oprette en tidsplan for 15 og 30 i hver måned, ved hjælp af en Azure ressourcestyring cmdlet.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
    

## <a name="linking-a-schedule-to-a-runbook"></a>Sammenkæde en tidsplan med et runbook

En runbook kan sammenkædes med flere tidsplaner, og en tidsplan kan have flere runbooks, der er knyttet til den. Hvis en runbook har parametre, kan du angive værdier for dem. Du skal angive værdier for de obligatoriske parametre og fastsætte værdier for en hvilken som helst valgfrie parametre.  Disse værdier der, bruges hver gang runbook er startet af denne tidsplanen.  Du kan vedhæfte samme runbook til en anden tidsplan og angive forskellige parameterværdier.


### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Sammenkæde en tidsplan med et runbook ved hjælp af Azure klassisk portal

1. Vælg **automatisering** i Azure klassisk portalen, og klik derefter på navnet på en konto med automation.
2. Vælg fanen **Runbooks** .
3. Klik på navnet på runbook til at planlægge.
4. Klik på fanen **tidsplan** .
5. Hvis runbook ikke i øjeblikket er knyttet til en tidsplan, derefter får du indstillingen **Link til en ny tidsplan** eller **Link til en eksisterende tidsplan**.  Hvis runbook er i øjeblikket er sammenkædet med en tidsplan, skal du klikke på **linket** i bunden af vinduet for at få adgang til disse indstillinger.
6. Hvis runbook har parametre, vil du blive bedt om deres værdier.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Sammenkæde en tidsplan med et runbook ved hjælp af Azure portal

1. Klik på feltet **Runbooks** for at åbne bladet **Runbooks** i portalen Azure fra kontoen automatisering.
2. Klik på navnet på runbook til at planlægge.
3. Hvis runbook ikke i øjeblikket er knyttet til en tidsplan, derefter får du mulighed for at oprette en ny tidsplan eller oprette kæde til en eksisterende tidsplan.  
4. Hvis runbook har parametre, kan du vælge indstillingen **Rediger køre indstillinger (standard: Azure)** og bladet **parametre** præsenteres, hvor du kan skrive oplysningerne i overensstemmelse hermed.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Sammenkæde en tidsplan med et runbook med Windows PowerShell

Du kan bruge [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) sammenkæde en tidsplan til en klassisk runbook eller [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) -cmdlet til runbooks i portalen Azure.  Du kan angive værdier for den runbook parametre med parameteren parametre. Se [starte en Runbook i Azure automatisering](automation-starting-a-runbook.md) kan finde flere oplysninger om angivelse af parameterværdier.

Kommandoerne følgende eksempel viser, hvordan du sammenkæde en tidsplan for ved hjælp af en Azure Service Management cmdlet med parametre.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

Kommandoerne følgende eksempel viser, hvordan du knytte en tidsplan til en runbook ved hjælp af en Azure ressourcestyring cmdlet med parametre.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"

## <a name="disabling-a-schedule"></a>Deaktivere en tidsplan

Når du deaktiverer en tidsplan, køre en hvilken som helst runbooks, der er knyttet til den ikke længere på samme skema. Du kan manuelt deaktiverer en tidsplan eller angive et udløbstidspunkt for tidsplaner med en frekvens, når du opretter dem. Når udløbsdatoen er nået, deaktiveres tidsplanen.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Deaktivere en tidsplan fra portalen Azure klassisk

Du kan deaktivere en tidsplan i Azure klassisk portalen fra siden tidsplan for tidsplanen.

1. Vælg automatisering i Azure klassisk portalen, og klik derefter på navnet på en konto med automation.
1. Vælg fanen aktiver.
1. Klik på navnet på en tidsplan for at åbne dets detaljesiden.
2. Ændre **aktiveret** til **Nej**.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Deaktivere en tidsplan fra Azure-portalen

1. Klik på feltet **Aktiver** for at åbne bladet **Aktiver** i portalen Azure fra kontoen automatisering.
2. Klik på feltet **tidsplaner** for at åbne bladet **tidsplaner** .
2. Klik på navnet på en tidsplan for at åbne bladet detaljer.
3. Ændre **aktiveret** til **Nej**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Deaktivere en tidsplan med Windows PowerShell

Du kan bruge cmdlet'en [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) til at ændre egenskaberne for en eksisterende tidsplan til en klassisk runbook eller et [Sæt AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) -cmdlet til runbooks i portalen Azure. Hvis du vil deaktivere tidsplanen, Angiv **Falsk** for parameteren **IsEnabled** .

Kommandoerne følgende eksempel viser, hvordan du deaktiverer en tidsplan for ved hjælp af Cmdletten Azure Service Management.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

Kommandoerne følgende eksempel viser, hvordan du deaktiverer en tidsplan for en runbook ved hjælp af en Azure ressourcestyring cmdlet.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"


## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om at arbejde med tidsplaner, skal du se [Tidsplan aktiver i Azure automatisering](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- For at komme i gang med runbooks i Azure automatisering, se [starte en Runbook i Azure Automation](automation-starting-a-runbook.md) 
<properties 
    pageTitle="Redigere tekst runbooks i Azure Automation"
    description="I denne artikel indeholder forskellige procedurer til at arbejde med PowerShell og PowerShell arbejdsprocessen runbooks i Azure Automation ved hjælp af redigeringsprogrammet indeholder tekst."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren" />

# <a name="editing-textual-runbooks-in-azure-automation"></a>Redigere tekst runbooks i Azure Automation

Tekstbaseret editor i Azure automatisering kan bruges til at redigere [PowerShell runbooks](automation-runbook-types.md#powershell-runbooks) og [PowerShell arbejdsproces runbooks](automation-runbook-types.md#powershell-workflow-runbooks). Dette er typisk funktionerne i andre kode redaktører som intellisense og farvekodning med flere særlige funktioner hjælper dig med at få adgang til ressourcer, der er fælles for runbooks.  Denne artikel indeholder detaljeret vejledning for at udføre forskellige funktioner med denne editor.

Den indeholder tekst editor indeholder en funktion for at indsætte koden for aktiviteter, værdier og underordnede runbooks i en runbook. I stedet for at indtaste koden dig selv, kan du vælge på en liste over tilgængelige ressourcer og har den relevante kode, der er indsat i runbook.

Hver runbook i Azure Automation har to versioner, kladde og Published. Du kan redigere den kladdeversion af runbook og derefter publicere den, så den kan udføres. Den udgivne version kan ikke redigeres. Se [publicere en runbook](automation-creating-importing-runbook.md#publishing-a-runbook) for flere oplysninger.

Hvis du vil arbejde med [Grafiske Runbooks](automation-runbook-types.md#graphical-runbooks), se [grafiske redigering i Azure automatisering](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Redigere en runbook ved hjælp af Azure portal

Brug følgende fremgangsmåde til at åbne en runbook til redigering i editoren indeholder tekst.

1. Vælg din konto med automation i Azure-portalen.
2. Klik på feltet **Runbooks** for at åbne listen over runbooks.
3. Klik på navnet på den runbook, du vil redigere, og klik derefter på knappen **Rediger** .
6. Udføre den nødvendige redigering.
7. Klik på **Gem** , når ændringerne er fuldført.
8. Klik på **Publicer** , hvis du vil have den nyeste kladdeversion af runbook til at blive udgivet.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Indsætte en-cmdlet til en runbook

2. Placer markøren, hvor du vil placere Cmdletten, i på lærredet i redigeringsprogrammet indeholder tekst.
3. Udvid noden **cmdletter** i kontrolelementet bibliotek. 
3. Udvid det modul, der indeholder Cmdletten, du vil bruge.
4. Højreklik på cmdlet for at indsætte og vælge **Føj til lærredet**.  Hvis cmdlet har mere end én parameter, der er angivet, tilføjes standardsættet.  Du kan også udvide cmdlet for at vælge et andet parametersæt.
4. Koden for cmdlet indsættes med hele listen over parametre.
5. Angiv en passende værdi i stedet for datatypen omgivet af klammeparenteser <> for eventuelle nødvendige parametre.  Fjerne alle parametre, du ikke har brug for.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Indsætte kode for en underordnet runbook i en runbook

2. Placer markøren, hvor du vil placere koden for [underordnede runbook](automation-child-runbooks.md), i på lærredet i redigeringsprogrammet indeholder tekst.
3. Udvid noden **Runbooks** i kontrolelementet bibliotek. 
3. Højreklik på runbook for at indsætte og vælge **Føj til lærredet**.
4. Koden for underordnede runbook indsættes med eventuelle pladsholdere for en hvilken som helst runbook parametre.
5. Erstat pladsholderne med relevante værdier for hver parameter.

### <a name="to-insert-an-asset-into-a-runbook"></a>Indsætte et aktiv i en runbook

2. Placer markøren, hvor du vil placere koden for underordnede runbook, i på lærredet i redigeringsprogrammet indeholder tekst.
3. Udvid noden **Aktiver** i kontrolelementet bibliotek. 
4. Udvid noden for typen aktiv ønskede.
3. Højreklik på aktivet for at indsætte og vælge **Føj til lærredet**.  Vælg **Tilføj "få variabel" til lærredet** eller **tilføje "Angiv variabel" til lærredet** for [variable Aktiver](automation-variables.md), afhængigt af om du vil hente eller angive variablen.
4. Koden for aktivet er indsat i runbook.



## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Redigere en runbook ved hjælp af Azure portal

Brug følgende fremgangsmåde til at åbne en runbook til redigering i editoren indeholder tekst.

1. Vælg **automatisering** i portalen Azure, og klik derefter på navnet på en konto med automation.
2. Vælg fanen **Runbooks** .
3. Klik på navnet på den runbook, du vil redigere, og derefter vælge fanen **forfatter** .
5. Klik på knappen **Rediger** i bunden af skærmen.
6. Udføre den nødvendige redigering.
7. Klik på **Gem** , når ændringerne er fuldført.
8. Klik på **Publicer** , hvis du vil have den nyeste kladdeversion af runbook til at blive udgivet.

### <a name="to-insert-an-activity-into-a-runbook"></a>Indsætte en aktivitet i en Runbook

1. Placer markøren, hvor du vil placere aktiviteten, i på lærredet i redigeringsprogrammet indeholder tekst.
1. Klik på **Indsæt** , og klik derefter på **aktivitet**nederst på skærmen.
1. Vælg det modul, der indeholder aktiviteten i kolonnen **Integration modul** .
1. Vælg en aktivitet i ruden **aktivitet** .
1. Bemærk beskrivelse af aktiviteten i kolonnen **Beskrivelse** . Du kan også du kan klikke på Vis detaljeret hjælp til at starte Hjælp til aktiviteten i browseren.
1. Klik på den højre pil.  Hvis aktiviteten har parametre, vises de til dine oplysninger.
1. Klik på knappen Kontrollér.  Kode til at køre aktiviteten, indsættes i runbook.
1. Hvis aktiviteten kræver parametre, skal du give en passende værdi i stedet for datatypen omgivet af klammeparenteser <>.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Indsætte kode for en underordnet runbook i en runbook

1. Placer markøren, hvor du vil placere [underordnede runbook](automation-child-runbooks.md), i på lærredet i redigeringsprogrammet indeholder tekst.
2. Klik på **Indsæt** , og klik derefter på **Runbook**nederst på skærmen.
3. Vælg runbook indsætte fra i den midterste kolonne, og klik på den højre pil.
4. Hvis runbook har parametre, vises de til dine oplysninger.
5. Klik på knappen Kontrollér.  Kode til at køre den valgte runbook indsættes i den aktuelle runbook.
7. Hvis runbook kræver parametre, skal du give en passende værdi i stedet for datatypen omgivet af klammeparenteser <>.

### <a name="to-insert-an-asset-into-a-runbook"></a>Indsætte et aktiv i en runbook

1. Placer markøren, hvor du vil placere aktiviteten for at hente aktivet, i på lærredet i redigeringsprogrammet indeholder tekst.
1. Klik på **Indsæt** , og klik derefter på **indstilling**nederst på skærmen.
1. Vælg den handling, du vil, i kolonnen **Indstilling handling** .
1. Vælg de tilgængelige aktiver i den midterste kolonne.
1. Klik på knappen Kontrollér.  Koden for at hente eller angive aktivet indsættes i runbook.



## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Redigere en Azure automatisering runbook ved hjælp af Windows PowerShell

Hvis du vil redigere en runbook med Windows PowerShell, du bruger editor efter eget valg og gemme den på en .ps1 fil. Du kan bruge cmdlet'en [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) til at hente indholdet af runbook og derefter [Angive AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) til at erstatte eksisterende kladde runbook med et ændret.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Til at hente indholdet af en Runbook ved hjælp af Windows PowerShell

Kommandoerne følgende eksempel viser, hvordan du hente scriptet til en runbook og gemme den på en scriptfil. I dette eksempel hentes kladde-version. Det er også muligt at hente den udgivne version af runbook, selvom denne version ikke kan ændres.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Ændre indholdet af en Runbook ved hjælp af Windows PowerShell

Kommandoerne følgende eksempel viser, hvordan du erstatte de eksisterende indholdet af en runbook med indholdet af en script-fil. Bemærk, at dette er den samme eksempelprocedure som [til at importere en runbook fra en script-fil med Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## <a name="related-articles"></a>Relaterede artikler

- [Oprette eller importere en runbook i Azure Automation](automation-creating-importing-runbook.md)
- [Learning PowerShell arbejdsproces](automation-powershell-workflow.md)
- [Grafiske redigering i Azure automatisering](automation-graphical-authoring-intro.md)
- [Certifikater](automation-certificates.md)
- [Forbindelser](automation-connections.md)
- [Legitimationsoplysninger](automation-credentials.md)
- [Tidsplaner](automation-schedules.md)
- [Variabler](automation-variables.md)
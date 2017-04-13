<properties
    pageTitle="Oprette eller importere en runbook i Azure Automation"
    description="I denne artikel beskrives, hvordan du opretter en ny runbook i Azure Automation eller importere et fra en fil."
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
    ms.date="09/12/2016"
    ms.author="magoedte;bwren" />

# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Oprette eller importere en runbook i Azure Automation

Du kan føje en runbook til Azure automatisering ved enten at [oprette en ny](#creating-a-new-runbook) eller ved at importere en eksisterende runbook fra en fil eller fra [Runbook galleriet](automation-runbook-gallery.md). I denne artikel indeholder oplysninger om oprettelse og importere runbooks fra en fil.  Du kan få alle oplysningerne om at få adgang til community runbooks og moduler i [modulet og Runbook gallerier til Azure automatisering](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Oprette en ny runbook

Du kan oprette en ny runbook i Azure automatisering ved hjælp af en af de Azure portaler eller Windows PowerShell. Når runbook er blevet oprettet, kan du redigere den ved hjælp af oplysningerne i [Learning PowerShell arbejdsproces](automation-powershell-workflow.md) og [grafiske redigering i Azure automatisering](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>Oprette en ny Azure automatisering runbook ved hjælp af Azure klassisk portal

Du kan kun arbejde med [PowerShell arbejdsproces runbooks](automation-runbook-types.md#powershell-workflow-runbooks) i portalen Azure.

1. I portalen Azure klassisk, klik på, **Ny** **App Services**, **automatisering**, **Runbook**, **Hurtig oprettelse**.
2. Angiv de nødvendige oplysninger, og klik derefter på **Opret**. Runbook navnet skal starte med et bogstav og kan have bogstaver, tal, understregningstegn og stiplet.
3. Hvis du vil redigere runbook nu, og klik derefter på **Rediger Runbook**. Ellers skal du klikke på **OK**.
4. Din nye runbook vises under fanen **Runbooks** .


### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Oprette en ny Azure automatisering runbook ved hjælp af Azure portal

1. Åbn din konto med Automation i Azure-portalen.
2. Klik på feltet **Runbooks** for at åbne listen med runbooks.
3. Klik på knappen **Tilføj en runbook** og klik derefter på **Opret en ny runbook**.
2. Skriv et **navn** til runbook, og vælg den [Type](automation-runbook-types.md). Runbook navnet skal starte med et bogstav og kan have bogstaver, tal, understregningstegn og stiplet.
3. Klik på **Opret** for at oprette runbook og åbne editor.


### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Oprette en ny Azure automatisering runbook med Windows PowerShell

Du kan bruge cmdlet'en [Ny AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) til at oprette en tom [PowerShell arbejdsproces runbook](automation-runbook-types.md#powershell-workflow-runbooks). Du kan enten angive parameteren **navn** for at oprette en tom runbook, som du senere kan redigere, eller du kan angive parameteren **sti** for at importere en runbook fil. Også bør medtaget parameteren **Type** for at angive en af de fire typer af runbook.

Kommandoerne følgende eksempel viser, hvordan du opretter en ny tom runbook.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importere en runbook fra en fil til Azure automatisering

Du kan oprette en ny runbook i Azure automatisering ved at importere en PowerShell-script eller PowerShell arbejdsproces (.ps1 lokalnummer) eller en eksporterede grafiske runbook (.graphrunbook).  Du skal angive den [type runbook](automation-runbook-types.md) , der oprettes fra importen tages højde for følgende overvejelser.

- Filen .graphrunbook kan kun importeres til en ny [grafisk runbook](automation-runbook-types.md#graphical-runbooks), og grafiske runbooks kan kun oprettes en .graphrunbook fil.
- En .ps1-fil, der indeholder en PowerShell-arbejdsproces kan kun importeres til en [arbejdsproces for PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Hvis filen indeholder flere PowerShell arbejdsprocesser, mislykkes importen. Du skal gemme hver enkelt arbejdsgang i sin egen fil og importere hver separat.
- En .ps1-fil, der ikke indeholder en arbejdsproces kan importeres til en [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) eller en [arbejdsproces for PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Hvis det er importeret i en arbejdsproces for PowerShell runbook, derefter den konverteres til en arbejdsproces, og kommentarer, der skal medtages i runbook angive de ændringer, der er foretaget.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>Importere en runbook fra en fil ved hjælp af Azure klassisk portal
Du kan bruge følgende procedure til at importere en scriptfil til Azure automatisering.  Bemærk, at du kan kun importere en .ps1 fil i en arbejdsproces for PowerShell runbook ved hjælp af denne portal.  Du skal bruge Azure portal for andre typer.

1. Vælg **automatisering** i portalen Azure Management, og vælg derefter en konto med Automation.
2. Klik på **Importér**.
3. Klik på **Søg efter filen** , og Find scriptfilen til at importere.
4. Hvis du vil redigere runbook nu, og klik derefter på **Rediger Runbook**. Ellers skal du klikke på OK.
5. Ny runbook vises under fanen **Runbooks** for kontoen automatisering.
6. Du skal [publicere runbook](#publishing-a-runbook) , før du kan køre programmet.


### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Importere en runbook fra en fil ved hjælp af Azure portal
Du kan bruge følgende procedure til at importere en scriptfil til Azure automatisering.  

>[AZURE.NOTE] Bemærk, at du kan kun importere en .ps1 fil i en arbejdsproces for PowerShell runbook ved hjælp af portalen.

1. Åbn din konto med Automation i Azure-portalen.
2. Klik på feltet **Runbooks** for at åbne listen med runbooks.
3. Klik på knappen **Tilføj en runbook** og derefter på **Importér**.
4. Klik på **Runbook fil** for at markere filen, der importeres
2. Hvis **navnefeltet** er aktiveret, har du mulighed for at ændre den.  Runbook navnet skal starte med et bogstav og kan have bogstaver, tal, understregningstegn og stiplet.
3. [Runbook type](automation-runbook-types.md) vælges automatisk, men du kan ændre typen efter at have de relevante begrænsninger i betragtning. 
3. Ny runbook vises på listen over runbooks for kontoen automatisering.
4. Du skal [publicere runbook](#publishing-a-runbook) , før du kan køre programmet.

>[AZURE.NOTE] Når du importerer en grafisk runbook eller en grafisk PowerShell arbejdsproces runbook, har du mulighed for at konvertere til anden typen, hvis ønskede. Du kan ikke konvertere til tekst.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importere en runbook fra en script-fil med Windows PowerShell

Du kan bruge cmdlet'en [Importér AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) til at importere en scriptfil som en kladde PowerShell arbejdsproces runbook. Hvis runbook allerede findes, mislykkes importen, medmindre du bruger den *-kraft* parameter. 

Kommandoerne følgende eksempel viser, hvordan du importere en script-fil til en runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publicere en runbook

Når du opretter eller importere en ny runbook, skal du publicere den, før du kan afspille den.  Hver runbook i Automation har en kladde og en udgivet version. Kun den udgivne version er tilgængelig til at blive kørt, og kun kladde-versionen kan redigeres. Den udgivne version er ikke påvirket af eventuelle ændringer til kladde-versionen. Når den kladdeversion skal gøres tilgængelige, derefter publicere du den, overskriver den udgivne version med kladde-versionen.

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>Publicere en runbook ved hjælp af portalen Azure klassisk

1. Åbn runbook i portalen Azure klassisk.
1. Klik på **forfatter**øverst på skærmen.
1. Klik på **Publicer** og klik derefter på **Ja** til bekræftelsesmeddelelsen nederst på skærmen.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Publicere en runbook ved hjælp af portalen Azure

1. Åbn runbook i portalen Azure.
1. Klik på knappen **Rediger** .
1. Klik på **Publicer** knappen og derefter på **Ja** for at bekræftelsesmeddelelsen.


## <a name="to-publish-a-runbook-using-windows-powershell"></a>Publicere en runbook ved hjælp af Windows PowerShell

Du kan bruge cmdlet'en [Publicer AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) til at publicere en runbook med Windows PowerShell. Kommandoerne følgende eksempel viser, hvordan du publicerer en eksempel runbook.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Næste trin
- For at få mere for at vide om, hvordan du kan få glæde af Runbook og PowerShell-modulet galleriet, se [modulet og Runbook gallerier til Azure automatisering](automation-runbook-gallery.md)
- Få flere oplysninger om redigering af PowerShell og PowerShell arbejdsprocessen runbooks med en tekstbaseret editor under [redigering tekstdata runbooks i Azure Automation](automation-edit-textual-runbook.md)
- Hvis du vil vide mere om redigering af grafiske runbook, se [grafiske redigering i Azure automatisering](automation-graphical-authoring-intro.md)

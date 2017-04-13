<properties 
   pageTitle="Azure automatisering Runbook typer"
   description="I denne artikel beskrives de forskellige typer runbooks, som du kan bruge i Azure automatisering samt de overvejelser, du skal tage hensyn til at afgøre, hvilken type du skal bruge. "
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
   ms.author="bwren" />

# <a name="azure-automation-runbook-types"></a>Azure automatisering runbook typer

Azure automatisering understøtter fire typer runbooks, som er beskrevet i følgende tabel.  I nedenstående afsnit indeholder yderligere oplysninger om hver type, herunder overvejelser i forbindelse med til at bruge de.


| Type |  Beskrivelse |
|:---|:---|
| [Grafiske](#graphical-runbooks) | Baseret på Windows PowerShell og oprettes og redigeres helt i grafiske editor Azure-portalen. | 
| [Grafiske PowerShell-arbejdsproces](#graphical-runbooks) | Baseret på Windows PowerShell arbejdsproces og oprettes og redigeres helt i den grafiske editor Azure-portalen. 
| [PowerShell](#powershell-runbooks) | Tekst runbook baseret på Windows PowerShell-script.
| [PowerShell arbejdsproces](#powershell-workflow-runbooks) | Tekst runbook baseret på Windows PowerShell arbejdsproces. |


## <a name="graphical-runbooks"></a>Grafiske runbooks

[Grafiske](automation-runbook-types.md#graphical-runbooks) og grafiske PowerShell arbejdsprocessen runbooks oprettes og redigeres med den grafiske editor i portalen Azure.  Du kan eksportere dem til en fil og derefter importere dem til en anden konto med automation, men du kan ikke oprette eller redigere dem med et andet værktøj.  Grafiske runbooks generere PowerShell-kode, men du ikke direkte se eller redigere koden. Grafiske runbooks kan ikke konverteres til en af de [tekstformater](automation-runbook-types.md), og ikke kan konverteres runbook en tekst til grafisk format. Grafiske runbooks kan konverteres til grafiske PowerShell arbejdsproces runbooks under import og omvendt.

### <a name="advantages"></a>Fordele

- Oprette runbooks med minimale kendskab til [PowerShell](automation-powershell-workflow.md).
- Visuelt at repræsentere processer for styring.
- Vedhæft andre runbooks som underordnet runbooks at oprette arbejdsprocesser på højt niveau.


### <a name="limitations"></a>Begrænsninger

- Kan ikke redigere runbook uden for Azure-portalen.
- Kræve en kode aktivitet, der indeholder PowerShell-koden for at udføre kompleks logik.
- Kan ikke få vist eller redigere den PowerShell-kode, der er oprettet af grafiske arbejdsprocessen direkte. Bemærk, at du kan få vist den kode, du har oprettet i en hvilken som helst kode aktiviteter.


## <a name="powershell-runbooks"></a>PowerShell runbooks

PowerShell runbooks er baseret på Windows PowerShell.  Du redigere direkte koden for runbook ved hjælp af tekstredigeringsprogrammet i portalen Azure.  Du kan også bruge en hvilken som helst offline tekstredigeringsprogram og [importere runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) til Azure automatisering.

### <a name="advantages"></a>Fordele

- Implementere alle kompleks logik med PowerShell kode uden yderligere lede PowerShell arbejdsproces. 
- Runbook starter hurtigere end grafiske eller PowerShell arbejdsproces runbooks, fordi det ikke behøver at være kompileret, før du kører.

### <a name="limitations"></a>Begrænsninger

- Skal have kendskab til PowerShell scripting.
- Kan ikke bruge [parallel behandling](automation-powershell-workflow.md#parallel-processing) til at udføre flere handlinger parallelt.
- Kan ikke bruge [kontrolpunkter](automation-powershell-workflow.md#checkpoints) fortsætte med at runbook i tilfælde af fejl.
- PowerShell arbejdsproces runbooks og grafiske runbooks kan kun medtages som underordnet runbooks ved hjælp af Start-AzureAutomationRunbook-cmdlet, der opretter et nyt job.

### <a name="known-issues"></a>Kendte problemer
Følgende er aktuelle kendte problemer med PowerShell runbooks.

- PowerShell runbooks kan ikke kan ikke hentes et ikke-krypteret [variable Aktiver](automation-variables.md) med en null-værdi.
- PowerShell runbooks kan ikke hentes et [variabelt aktiv](automation-variables.md) med *~* i navnet.
- Get-proces i en løkke i en PowerShell runbook kan gå ned efter om 80 gentagelser. 
- En PowerShell-runbook kan mislykkes, hvis den forsøger at skrive en meget stor mængde data til output strømmen på én gang.   Du kan typisk kan løse dette problem ved at udføre netop de oplysninger, du skal bruge, når du arbejder med store objekter.  For eksempel i stedet for skriver noget i retning af *Get-proces*, du kan udskrive de påkrævede felter med *Get-proces | Vælg ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>PowerShell arbejdsproces runbooks

PowerShell arbejdsproces runbooks er tekst runbooks baseret på [Windows PowerShell arbejdsproces](automation-powershell-workflow.md).  Du redigere direkte koden for runbook ved hjælp af tekstredigeringsprogrammet i portalen Azure.  Du kan også bruge en hvilken som helst offline tekstredigeringsprogram og [importere runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) til Azure automatisering.

### <a name="advantages"></a>Fordele

- Implementere alle kompleks logik med PowerShell arbejdsproces kode.
- Brug [kontrolpunkterne](automation-powershell-workflow.md#checkpoints) til at genoptage runbook i tilfælde af fejl.
- Bruge [parallel behandling](automation-powershell-workflow.md#parallel-processing) til at udføre flere handlinger parallelt.
- Kan indeholde andre grafiske runbooks og PowerShell arbejdsproces runbooks som underordnet runbooks at oprette arbejdsprocesser på højt niveau.


### <a name="limitations"></a>Begrænsninger

- Forfatter skal have kendskab til PowerShell arbejdsproces.
- Runbook skal håndtere yderligere kompleksitet PowerShell arbejdsproces som [deserialiseres objekter](automation-powershell-workflow.md#code-changes).
- Runbook tager længere tid at starte end PowerShell runbooks, da det skal være kompileret, før du kører.
- PowerShell runbooks kan kun medtages som underordnet runbooks ved hjælp af Start-AzureAutomationRunbook-cmdlet, der opretter et nyt job.


## <a name="considerations"></a>Overvejelser i forbindelse med

Du skal tage hensyn følgende yderligere overvejelser når afgøre, hvilken type du skal bruge til en bestemt runbook.

- Du kan ikke konvertere runbooks fra grafiske tekstdata type eller omvendt.
- Der er begrænsninger ved hjælp af runbooks forskellige typer som en underordnet runbook.  Du kan få flere oplysninger i [underordnede runbooks i Azure Automation](automation-child-runbooks.md) .

  
## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om redigering af grafiske runbook, se [grafiske redigering i Azure automatisering](automation-graphical-authoring-intro.md)
- At forstå forskellene mellem PowerShell og PowerShell arbejdsprocesser for runbooks, se [Learning Windows PowerShell arbejdsgang](automation-powershell-workflow.md)
- Finde flere oplysninger om, hvordan du oprette eller importere en Runbook, [oprette eller importere en Runbook](automation-creating-importing-runbook.md)




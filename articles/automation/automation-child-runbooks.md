<properties 
   pageTitle="Underordnet runbooks i Azure Automation | Microsoft Azure"
   description="I denne artikel beskrives de forskellige metoder til at starte en runbook fra Azure automatisering fra en anden runbook og deling af oplysninger mellem dem."
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
   ms.date="08/17/2016"
   ms.author="magoedte;bwren" />

# <a name="child-runbooks-in-azure-automation"></a>Underordnet runbooks i Azure Automation

Det er en god fremgangsmåde i Azure automatisering til at skrive der kan genbruges, modul runbooks med en dedikeret funktion, der kan bruges af andre runbooks. En overordnet runbook kalder ofte en eller flere underordnede runbooks for at udføre nødvendige funktionalitet. Der er to måder til at ringe til en underordnet runbook, og de har klare forskelle, skal du vide, så du kan bestemme, som skal være passer bedst til dine forskellige scenarier.

##  <a name="invoking-a-child-runbook-using-inline-execution"></a>Aktivering af en underordnet runbook ved hjælp af indbyggede udførelse af

Hvis du vil anvende en runbook indbygget fra en anden runbook, du bruger navnet på runbook og give værdier til dens parametre, præcis som du vil bruge en aktivitet eller cmdlet.  Alle runbooks i den samme konto med Automation er tilgængelige for alle andre skal anvendes på denne måde. Overordnede runbook vil vente runbook underordnede at fuldføre før du går videre til den næste linje, og der returneres en hvilken som helst output direkte til overordnet.

Når du starter en runbook indbygget, kører det samme job som overordnede runbook. Der vil ikke være tegn i jobs på underordnede runbook, som den kørte. Eventuelle undtagelser og en hvilken som helst stream output fra underordnede runbook vil være knyttet til overordnet. Dette resulterer i færre job og gør dem lettere at spore og foretage fejlfinding af fordi eventuelle undtagelser udløst af underordnede runbook og en af dens stream output er knyttet til det overordnede job.

Når en runbook er udgivet, skal alle underordnede runbooks, der kalder allerede udgivet. Dette skyldes, at Azure automatisering opbygger en tilknytning til en hvilken som helst underordnet runbooks, når en runbook er kompileret. Hvis de ikke er, overordnede runbook vises publicere korrekt, men genererer en undtagelse, når den er startet. Hvis dette sker, kan du genudgive overordnede runbook for at kunne korrekt henviser til underordnet runbooks. Du behøver ikke at genudgive overordnede runbook, hvis nogen af underordnede runbooks er blevet ændret, fordi tilknytningen der allerede er oprettet.

Parametrene for en underordnet runbook kaldet indbygget kan være en hvilken som helst datatype, herunder komplekse objekter, og der er ingen [JSON serialisering](automation-starting-a-runbook.md#runbook-parameters) , som er der, når du starter ved hjælp af portalen Azure Management runbook eller med Start AzureRmAutomationRunbook cmdlet.


### <a name="runbook-types"></a>Typer af Runbook

Hvilke typer kan ringe til hinanden:

- En [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) og [grafiske runbooks](automation-runbook-types.md#graphical-runbooks) kan ringe til andre hver indbygget (begge er PowerShell baseret).
- En [runbook PowerShell arbejdsproces](automation-runbook-types.md#powershell-workflow-runbooks) og arbejdsproces med grafiske PowerShell runbooks kan ringe til andre hver indbygget (begge er PowerShell arbejdsproces baseret)
- Typerne PowerShell og PowerShell arbejdsprocestyper kan ringe til hinanden i selve teksten, og Start-AzureRmAutomationRunbook skal bruge.
    
Når du publicere noget ud af rækkefølge:

- Publicer rækkefølgen af runbooks kun har betydning for PowerShell arbejdsproces og arbejdsproces med grafiske PowerShell runbooks.


Når du ringe til en grafiske eller PowerShell arbejdsproces underordnede runbook ved hjælp af indbyggede udførelse af skal bruge du blot navnet på runbook.  Når du ringer til et PowerShell underordnede runbook, skal du foran navnet med *.\\ * du vil angive, at scriptet er placeret i den lokale mappe. 

### <a name="example"></a>Eksempel

I følgende eksempel kalder en test underordnede runbook, der accepterer tre parametre, en kompleks objekt, et heltal og en boolesk værdi. Output fra underordnede runbook er tildelt til en variabel.  I dette tilfælde er underordnede runbook en arbejdsproces for PowerShell runbook

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Følgende er den samme eksempel på brug af en PowerShell-runbook som underordnede.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true



##  <a name="starting-a-child-runbook-using-cmdlet"></a>Starte en underordnet runbook ved hjælp af cmdlet

Du kan bruge cmdlet'en [Start AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) til at starte en runbook, som beskrevet i [at starte en runbook med Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Der er to måder at bruge for denne cmdlet.  I en tilstand returnerer cmdlet job-id'et, som underordnet jobbet oprettes til underordnet runbook.  I de andre tilstand, der aktiveres ved at angive den **-vente** parameter, cmdlet skal vente, indtil underordnede job er afsluttet, og returnere output fra underordnede runbook.

Jobbet fra et underordnet runbook i gang med en cmdlet kører, i et separat job fra overordnede runbook. Dette resulterer i flere job end aktivering af runbook indbygget og gør dem sværere at spore. Overordnet kan starte flere underordnede runbooks asynkront uden at vente på hver at fuldføre. I den samme slags parallel udførelse ringe underordnede runbooks indbygget, skal overordnede runbook Brug [parallelle nøgleord](automation-powershell-workflow.md#parallel-processing).

Parametre for en underordnet runbook i gang med en cmdlet er angivet en hashtabel som beskrevet i [Runbook parametre](automation-starting-a-runbook.md#runbook-parameters). Kun simple datatyper kan bruges. Hvis runbook har en parameter med en kompleks datatype, kaldes det skal indbygget.

### <a name="example"></a>Eksempel

I følgende eksempel starter en underordnet runbook med parametre og derefter venter på den færdige ved hjælp af Start-AzureRmAutomationRunbook-vente parameter. Når afsluttet indsamles dens output fra underordnede runbook.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResouceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Sammenligning af metoder til at ringe til en underordnet runbook

Den følgende tabel opsummerer forskellene mellem de to metoder for opkald til en runbook fra en anden runbook.

| | Indbygget| Cmdlet|
|:---|:---|:---|
|Job|Underordnet runbooks køre i det samme job som overordnet.|Der oprettes en separat job for underordnede runbook.|
|Udførelse af|Overordnede runbook venter underordnede runbook til at fuldføre før du fortsætter.|Overordnede runbook fortsætter umiddelbart efter underordnede runbook startes *eller* overordnede runbook venter underordnede jobbet at afslutte.|
|Output|Overordnede runbook kan direkte få output fra underordnede runbook.|Overordnet runbook skal hente output fra underordnede runbook job *eller* overordnede runbook direkte få output fra underordnede runbook.|
|Parametre|Værdier for de underordnede runbook parametre er angivet separat og kan bruge en hvilken som helst datatype.|Værdier for de underordnede runbook parametre skal kombineres til en enkelt hashtabel og kan kun indeholde simple, matrix, og objekt datatyper, der udnyttelse JSON serialisering.|
|Automatisering konto|Overordnede runbook kan kun bruge underordnede runbook i den samme konto med automation.|Overordnede runbook kan bruge underordnede runbook fra en hvilken som helst automatisering konto fra samme Azure-abonnement og et andet abonnement, hvis du har forbindelse til den.|
|Publicering|Underordnet runbook skal publiceres, før overordnede runbook er blevet publiceret.|Underordnet runbook skal publiceres til enhver tid, før overordnede runbook startes.|

## <a name="next-steps"></a>Næste trin

- [Starte en runbook fra Azure automatisering](automation-starting-a-runbook.md)
- [Runbook output og meddelelser i Azure automatisering](automation-runbook-output-and-messages.md)

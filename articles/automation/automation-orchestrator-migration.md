<properties
   pageTitle="Skifte fra Orchestrator til Azure Automation | Microsoft Azure"
   description="Beskriver, hvordan du overføre packs runbooks og integration fra System Center Orchestrator til Azure automatisering."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />


# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Skifte fra Orchestrator til Azure Automation (Beta)

Runbooks i [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) er baseret på aktiviteter fra integration packs, der er skrevet specielt til Orchestrator, mens runbooks i Azure Automation er baseret på Windows PowerShell.  [Grafiske runbooks](automation-runbook-types.md#graphical-runbooks) i Azure Automation har et ens udseende til Orchestrator runbooks med deres aktiviteter, der repræsenterer PowerShell-cmdletter, underordnede runbooks og aktiver.

[System Center Orchestrator overførsel værktøjskassen](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) indeholder værktøjer til at hjælpe dig med at konvertere runbooks fra Orchestrator til Azure automatisering.  Ud over konvertering af runbooks selv, skal du konvertere packs til integration med de aktiviteter, der bruger runbooks til integrationsmoduler med Windows PowerShell-cmdlet'er.  

Følgende er den grundlæggende proces til konvertering af Orchestrator runbooks til Azure automatisering.  Hver af disse trin er beskrevet mere detaljeret i afsnittene herunder.

1.  Hente [System Center Orchestrator overførsel værktøjskassen](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) som indeholder de værktøjer og moduler, der er beskrevet i denne artikel.
2.  Importere [Standard aktiviteter modul](#standard-activities-module) til Azure automatisering.  Dette omfatter konverterede versioner af standard Orchestrator aktiviteter, der kan bruges af konverterede runbooks.
3.  Importere [System Center Orchestrator integrationsmoduler](#system-center-orchestrator-integration-modules) til Azure automatisering til disse integration packs bruges af din runbooks, få adgang til System Center.
4.  Konvertere brugerdefineret fra tredjepart integration packs og ved hjælp af [Integration Pack konverteringsprogram](#integration-pack-converter) og importere til Azure automatisering.
5.  Konvertere Orchestrator runbooks ved hjælp af [Runbook konverteringsprogram](#runbook-converter) og installere i Azure Automation.
6.  Manuelt oprette kræves Orchestrator aktiver i Azure automatisering, da Runbook konverteringsprogram ikke konverterer disse ressourcer.
7.  Konfigurere en [Hybrid Runbook arbejder](#hybrid-runbook-worker) i din lokale datacenter til at køre konverterede runbooks, der skal have adgang til lokale ressourcer.

## <a name="service-management-automation"></a>Service Management automatisering

[Service Management automatisering](http://technet.microsoft.com/library/dn469260.aspx) (SMA) gemmer og kører runbooks i dit lokale datacenter som Orchestrator, og den anvender de samme integrationsmoduler som Azure automatisering. [Runbook konverteringsprogram](#runbook-converter) konverterer Orchestrator runbooks til grafiske runbooks via som ikke understøttes i SMA.  Du kan stadig installere de [Aktiviteter standardmodul](#standard-activities-module) og [System Center Orchestrator integrationsmoduler](#system-center-orchestrator-integration-modules) til SMA, men du skal manuelt [opdatere din runbooks](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook arbejder

Runbooks i Orchestrator er gemt på en databaseserver og køre på runbook servere, både i dit lokale datacenter.  Runbooks i Azure Automation er gemt i skyen, Azure og kan køre i din lokale datacenter, ved hjælp af en [Hybrid Runbook arbejder](automation-hybrid-runbook-worker.md).  Dette er hvordan vil du normalt afspille runbooks konverteret fra Orchestrator, da de er udviklet til at køre på lokale servere.

## <a name="integration-pack-converter"></a>Integration Pack konverteringsprogram

Integration Pack konverteringsprogram konverterer integration packs, der er oprettet ved hjælp af [Orchestrator Integration værktøjskassen (OIT)](http://technet.microsoft.com/library/hh855853.aspx) til integrationsmoduler baseret på Windows PowerShell, som kan importeres til Azure automatisering eller Service Management automatisering.  

Når du kører Integration Pack konverteringsprogram, vises der med en guide, der gør det muligt at vælge en integration pack (.oip)-fil.  Guiden viser en liste over de aktiviteter, der er inkluderet i denne integration pack derefter og kan du vælge, hvor der overføres.  Når du har fuldført guiden, oprettes der en integration modul, der indeholder en tilsvarende cmdlet for hver af aktiviteterne i den oprindelige integration pakke.


### <a name="parameters"></a>Parametre

En hvilken som helst egenskaberne for en aktivitet i pakken integration konverteres til parametrene for tilsvarende cmdlet i integrationsmodulet.  Windows PowerShell-cmdlet'er har et sæt af [fælles parametre](http://technet.microsoft.com/library/hh847884.aspx) , der kan bruges med alle cmdletter.  For eksempel detaljeret parameteren - medfører en-cmdlet til at få vist detaljerede oplysninger om sine handlinger.  Ingen cmdlet kan have en parameter med samme navn som en fælles parameter.  Hvis en aktivitet har en egenskab med samme navn som en fælles parameter, beder guiden dig om at angive et andet navn for parameteren.

### <a name="monitor-activities"></a>Overvåge aktiviteter

Overvåge runbooks i Orchestrator starte med en [skærm aktivitet](http://technet.microsoft.com/library/hh403827.aspx) og køre løbende venter skal aktiveres af en bestemt hændelse.  Azure automatisering understøtter ikke skærm runbooks, så ikke vil blive konverteret til en hvilken som helst skærm aktiviteter i integration pakken.  I stedet oprettes en pladsholder-cmdlet i integrationsmodulet for skærm aktiviteten.  Denne cmdlet har ingen funktionalitet, men det gør det muligt for en hvilken som helst konverterede runbook, der bruges til at være installeret.  Denne runbook vil ikke kunne køre i Azure automatisering, men det kan være installeret, så du kan redigere den.

### <a name="integration-packs-that-cannot-be-converted"></a>Integration packs, der ikke kan konverteres

Integration packs, der ikke blev oprettet med OIT kan ikke konverteres med Integration Pack konverteringsprogram. Der er også nogle integration sprogpakker, der leveres af Microsoft, der ikke kan aktuelt konverteres til dette værktøj.  Konverterede versioner af disse integration packs er [angivet til hentning](#system-center-orchestrator-integration-modules) , så de kan være installeret på Azure automatisering eller Service Management automatisering.


## <a name="standard-activities-module"></a>Standard aktiviteter modul

Orchestrator indeholder et sæt [standard aktiviteter](http://technet.microsoft.com/library/hh403832.aspx) , der medtages ikke i en integration sprogpakke, men der bruges af mange runbooks.  Modulet Standard aktiviteter er en integration modul, der indeholder en tilsvarende cmdlet for hver af disse aktiviteter.  Før du importerer en hvilken som helst konverterede runbooks, der bruger en standard aktivitet, skal du installere dette integration modul i Azure Automation.

Ud over at understøtte konverterede runbooks kan cmdletter i modulet standard aktiviteter bruges af en fortrolig med Orchestrator til at oprette nye runbooks i Azure Automation.  Mens funktionaliteten i alle de almindelige aktiviteter kan udføres med cmdletter, kan de fungere anderledes.  Cmdletter i modulet konverterede standard aktiviteter vil fungerer på samme måde som deres tilsvarende aktiviteter og bruge de samme parametre.  Dette kan hjælpe eksisterende Orchestrator runbook forfatteren i deres overgang til Azure automatisering runbooks.

## <a name="system-center-orchestrator-integration-modules"></a>System Center Orchestrator integrationsmoduler

Microsoft yder [integration packs](http://technet.microsoft.com/library/hh295851.aspx) opbygning af runbooks for at automatisere System Center komponenter og andre produkter.  Nogle af disse integration packs er i øjeblikket er baseret på OIT, men kan aktuelt ikke konverteres til integrationsmoduler på grund af kendte problemer.  [System Center Orchestrator integrationsmoduler](https://www.microsoft.com/download/details.aspx?id=49555) indeholder konverterede versioner af disse integration servicepakker, som kan importeres til Azure automatisering og Service Management automatisering.  

Ved RTM-versionen af dette værktøj, vil blive udgivet opdaterede versioner af de integration servicepakker baseret på OIT, der kan konverteres med Integration Pack konverteringsprogram.  Vejledning får også hjælper dig med at konvertere runbooks ved hjælp af aktiviteterne fra de integration packs ikke er baseret på OIT.

## <a name="runbook-converter"></a>Runbook konverteringsprogram

Runbook konverteringsprogram konverterer Orchestrator runbooks til [grafiske runbooks](automation-runbook-types.md#graph-runbooks) , som kan importeres til Azure automatisering.  

Runbook konverteringsprogram er implementeret som en PowerShell-modulet med en-cmdlet kaldet **ConvertFrom SCORunbook** , som udfører konverteringen.  Når du installerer værktøjet, opretter en genvej til en PowerShell-session, der indlæser cmdlet.   

Følgende er den grundlæggende proces til at konvertere en Orchestrator runbook og importere den til Azure automatisering.  De følgende afsnit indeholder yderligere oplysninger på ved hjælp af værktøjet og arbejde med konverterede runbooks.

1. Eksportere en eller flere runbooks fra Orchestrator.
2. Få integrationsmoduler for alle aktiviteter i runbook.
3. Konvertere Orchestrator runbooks i den eksporterede fil.
4. Gennemse oplysningerne i logfiler til at validere konverteringen og til at bestemme eventuelle nødvendige manuel opgaver.
4. Importere konverterede runbooks til Azure automatisering.
5. Oprette en hvilken som helst påkrævet aktiver i Azure automatisering.
6. Redigere runbook i Azure automatisering til at ændre eventuelle nødvendige aktiviteter.

### <a name="using-runbook-converter"></a>Brug af Runbook konverteringsprogram

Syntaksen for **ConvertFrom SCORunbook** er som følger:

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string> 

- RunbookPath - sti til eksportfilen, der indeholder runbooks til at konvertere.
- Modul - kommasepareret liste over integrationsmoduler, der indeholder aktiviteter i runbooks.
- OutputFolder - stien til mappen til at oprette konverteres grafiske runbooks. 


Følgende eksempelkommando konverterer runbooks i en fil kaldet **MyRunbooks.ois_export**.  Disse runbooks bruge Active Directory og Data Protection Manager integration packs.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks" 


### <a name="log-files"></a>Logfiler

Runbook konverteringsprogram opretter følgende logfiler i den samme placering som konverterede runbook.  Hvis filerne, der allerede findes, derefter overskrives de med oplysninger fra den sidste konvertering.

| Fil | Indholdsfortegnelse |
|:---|:---|
| Runbook konverteringsprogram - Progress.log | Detaljeret vejledning i konverteringen, herunder oplysninger for hver aktivitet har konverteret og advarsel for hver aktivitet, konverteres ikke. |
| Runbook konverteringsprogram - Summary.log  | Oversigt over de sidste konvertering, herunder eventuelle advarsler og opgaver, du skal bruge til at udføre som oprettelse af en variabel, der kræves til konverterede runbook for opfølgning.  |

### <a name="exporting-runbooks-from-orchestrator"></a>Eksportere runbooks fra Orchestrator

Runbook konverteringsprogram fungerer med en fil fra Orchestrator, der indeholder en eller flere runbooks.  Det opretter en tilsvarende Azure automatisering runbook for hver Orchestrator runbook i eksportfilen.  

Eksportere en runbook fra Orchestrator, højreklik på navnet på runbook i Runbook Designer, og vælg **Eksporter**.  Højreklik på navnet på mappen for at eksportere alle runbooks i en mappe, og vælg **Eksporter**.


### <a name="runbook-activities"></a>Runbook aktiviteter

Runbook konverteringsprogram konverterer hver aktivitet i Orchestrator runbook til en tilsvarende aktivitet i Azure Automation.  Til disse aktiviteter, der ikke kan konverteres, der oprettes en pladsholder aktivitet i runbook med advarselstekst.  Når du importerer konverterede runbook til Azure automatisering, skal du erstatte nogen af disse aktiviteter med gyldige aktiviteter, som udfører den nødvendige funktionalitet.

En hvilken som helst Orchestrator aktiviteter i [Aktiviteter standardmodul](#standard-activities-module) konverteres.  Der er nogle almindelige Orchestrator aktiviteter, der ikke er i dette modul via og konverteres ikke.  For eksempel har **Sende Platform begivenhed** ingen tilsvarende Azure automatisering, da hændelsen er specifikke for Orchestrator.

[Overvåge aktiviteter](https://technet.microsoft.com/library/hh403827.aspx) konverteres ikke, fordi der ikke er nogen svarende til dem i Azure Automation.  Undtagelsen er skærm aktiviteter i [konverteret integration packs](#integration-pack-converter) , konverteres til pladsholder aktiviteten.

En hvilken som helst aktivitet fra en [konverteret integration pack](#integration-pack-converter) konverteres, hvis du angiver stien til integrationsmodulet med parameteren **moduler** .  Du kan bruge [System Center Orchestrator integrationsmoduler](#system-center-orchestrator-integration-modules)for System Center Integration Packs.


### <a name="orchestrator-resources"></a>Orchestrator ressourcer

Runbook konverteringsprogram konverterer kun runbooks, ikke andre Orchestrator ressourcer som tællere, variabler eller forbindelser.  Tællere understøttes ikke i Azure Automation.  Variabler og forbindelser, der understøttes, men du skal oprette dem manuelt.  Logfilerne kan give dig besked, hvis runbook kræver disse ressourcer og angive tilsvarende ressourcer, som du vil oprette i Azure Automation for konverterede runbook til at fungere korrekt.

En runbook kan for eksempel bruge en variabel til at udfylde en bestemt værdi i en aktivitet.  Konverterede runbook konverterer aktiviteten og angive et variabelt aktiv i Azure automatisering med det samme navn som Orchestrator variablen.  Dette vil være beskrevet i filen **Runbook konverteringsprogram - Summary.log** , som oprettes, når er konverteringen.  Du skal oprette denne variable aktiv i Azure automatisering manuelt, inden du bruger runbook. 

 
### <a name="input-parameters"></a>Inputparametre

Runbooks i Orchestrator Acceptér inputparametre med aktiviteten **Initialiseret Data** .  Hvis den er konverteret runbook omfatter denne aktivitet, oprettes en [Inputparameteren](automation-graphical-authoring-intro.md#runbook-input-and-output) i Azure automatisering runbook for hver parameter i aktiviteten.  Der oprettes en [arbejdsproces Script kontrolelement](automation-graphical-authoring-intro.md#activities) aktivitet i konverterede runbook, der henter og returnerer hver parameter.  En hvilken som helst aktiviteter i runbook, der bruger en Inputparameteren referere til output fra denne aktivitet.

Årsagen til, at denne strategi bruges er bedst spejle funktionalitet i Orchestrator runbook.  Aktiviteter i nye grafiske runbooks skal refererer direkte til inputparametre ved hjælp af en Runbook input-datakilde.


### <a name="invoke-runbook-activity"></a>Kalde Runbook aktivitet

Runbooks i Orchestrator start andre runbooks med **Kalde Runbook** aktivitet. Hvis runbook konverteres indeholder denne aktivitet, og **Vent fuldførelse** indstillingen er angivet, oprettes en runbook aktivitet til det i konverterede runbook.  Hvis indstillingen **Vent fuldførelse** ikke er angivet, derefter oprettes arbejdsproces Script en aktivitet, der bruger **Start AzureAutomationRunbook** til at starte runbook.  Når du importerer konverterede runbook til Azure automatisering, skal du ændre denne aktivitet med de oplysninger, der er angivet i aktiviteten.




## <a name="related-articles"></a>Relaterede artikler

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Service Management automatisering](https://technet.microsoft.com/library/dn469260.aspx)
- [Hybrid Runbook arbejder](automation-hybrid-runbook-worker.md)
- [Orchestrator Standard aktiviteter](http://technet.microsoft.com/library/hh403832.aspx)
- [Download System Center Orchestrator overførsel værktøjskassen](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
 

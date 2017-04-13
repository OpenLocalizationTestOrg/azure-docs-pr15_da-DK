<properties 
    pageTitle="Overvåge og administrere Stream Analytics job med PowerShell | Microsoft Azure" 
    description="Lær, hvordan du bruger Azure PowerShell og cmdlet'er til at overvåge og administrere Stream Analytics job." 
    keywords="Azure powershell, azure powershell-cmdletter, powershell-kommando powershell scripting" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Overvåge og administrere Stream Analytics sager med Azure PowerShell-cmdlet'er

Lær at overvåge og administrere Stream Analytics ressourcer med Azure PowerShell-cmdletter og powershell scripting, udfører grundlæggende Stream Analytics opgaver.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Forudsætninger for at køre Azure PowerShell-cmdlet'er for Stream analyser

 - Oprette en Azure ressourcegruppe i dit abonnement. Følgende er et eksempel Azure PowerShell-script. Azure PowerShell kan finde oplysninger i [installere og konfigurere Azure PowerShell](../powershell-install-configure.md);  

Azure PowerShell 0.9.8:  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>
 
        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0:  

        # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
        
        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
        


> [AZURE.NOTE] Stream Analytics job oprettet fra et program har ikke overvågning som standard aktiveret.  Du kan manuelt aktivere overvågning på portalen Azure ved at navigere til det job skærm side og klikke på knappen Aktivér, eller du kan gøre dette fra et program ved at følge trinnene findes i [Azure Stream Analytics - skærm Stream Analytics job programmeringsmæssigt](stream-analytics-monitor-jobs.md).

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-cmdlet'er til Stream Analytics
De følgende Azure PowerShell-cmdletter kan bruges til at overvåge og administrere Azure Stream Analytics job. Bemærk, at Azure PowerShell har forskellige versioner. 
**I eksemplerne er den første kommando til Azure PowerShell 0.9.8, er den anden kommando til Azure PowerShell 1.0.** Kommandoerne Azure PowerShell 1.0 har altid "AzureRM" i kommandoen.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Viser en liste over alle Stream Analytics-job, der er defineret i den Azure abonnement eller angivne ressourcegruppe eller henter joboplysninger om et bestemt job i en ressourcegruppe.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

Denne PowerShell-kommando returnerer oplysninger om alle Stream Analytics-job i Azure-abonnement.

**Eksempel 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Denne PowerShell-kommando returnerer oplysninger om alle Stream Analytics-job i ressourcegruppe StreamAnalytics-standard-Central-US.

**Eksempel 3**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Denne PowerShell-kommando returnerer oplysninger om Stream Analytics jobbet StreamingJob i ressourcegruppen StreamAnalytics-standard-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Viser alle de materialer, der er defineret i et bestemt Stream Analytics-job, eller henter oplysninger om en bestemt input.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Denne PowerShell-kommando returnerer oplysninger om alle de materialer, der er defineret i jobbet StreamingJob.

**Eksempel 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Denne PowerShell-kommando returnerer oplysninger om input med navnet EntryStream, der er defineret i jobbet StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Viser alle de output, der er defineret i et bestemt Stream Analytics-job, eller henter oplysninger om en bestemt output.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Denne PowerShell-kommando returnerer oplysninger om de output, der er defineret i jobbet StreamingJob.

**Eksempel 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Denne PowerShell-kommando returnerer oplysninger om output med navnet Output, der er defineret i jobbet StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Henter oplysninger om kvoten streaming enheder i et bestemt område.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Denne PowerShell-kommando returnerer oplysninger om kvote for og brugen af streaming enheder i det centrale USA område.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Henter oplysninger om en bestemt transformering, der er defineret i et Stream Analytics-job.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Denne PowerShell-kommando returnerer oplysninger om transformation kaldet StreamingJob i jobbet StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>Ny AzureStreamAnalyticsInput | Ny AzureRMStreamAnalyticsInput
Opretter en ny input i et Stream Analytics-job, eller opdaterer en eksisterende angivne input.
  
Navnet på inputtet, der kan angives i .json filen eller på kommandolinjen. Hvis begge er angivet, skal navnet på kommandolinjen være den samme som i filen.

Hvis du angiver et input, der allerede findes, og Angiv ikke – Tving parameteren, Cmdletten bliver du spurgt, om der skal erstatte den eksisterende input.

Hvis du angiver den – Tving parameter og angive en eksisterende Skriv navn, input erstattes uden bekræftelse.

Kan finde detaljerede oplysninger om JSON-filstruktur og indhold, der refererer til [Oprette Input (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-input] sektion af [Stream Analytics Management RESTEN API Reference Library][stream.analytics.rest.api.reference].

**Eksempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Denne PowerShell-kommando opretter en ny input fra filen Input.json. Hvis der allerede er defineret en eksisterende input med det navn, der er angivet i definitionsfilen input, bede cmdlet hvorvidt skal erstatte den.

**Eksempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Denne PowerShell-kommando opretter en ny input i sagen med navnet EntryStream. Hvis der allerede er defineret en eksisterende input med dette navn, bede cmdlet hvorvidt skal erstatte den.

**Eksempel 3**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Denne PowerShell-kommando erstatter definitionen af eksisterende inputkilden kaldet EntryStream med definitionen fra filen.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>Ny AzureStreamAnalyticsJob | Ny AzureRMStreamAnalyticsJob
Opretter et nyt Stream Analytics-job i Microsoft Azure, eller opdaterer definitionen af et eksisterende angivne job.

Navnet på sag kan angives i .json filen eller på kommandolinjen. Hvis begge er angivet, skal navnet på kommandolinjen være den samme som i filen.

Hvis du angiver et jobnavn, der allerede findes, og Angiv ikke – Tving parameteren, Cmdletten bliver du spurgt, om der skal erstatte den eksisterende sag.

Hvis du angiver den – gennemtvinge parameter og angive Jobnavnet på en eksisterende, erstattes definitionen af job uden bekræftelse.

Kan finde detaljerede oplysninger om JSON-filstruktur og indhold, der refererer til [Oprette Stream Analytics Job] [ msdn-rest-api-create-stream-analytics-job] sektion af [Stream Analytics Management RESTEN API Reference Library][stream.analytics.rest.api.reference].

**Eksempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Denne PowerShell-kommando opretter et nyt job fra definitionen i JobDefinition.json. Hvis der allerede er defineret et eksisterende job med det navn, der er angivet i filen job definition, bede cmdlet hvorvidt skal erstatte den.

**Eksempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Denne PowerShell-kommando erstatter jobdefinitionen for StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>Ny AzureStreamAnalyticsOutput | Ny AzureRMStreamAnalyticsOutput
Opretter en ny output inden for et Stream Analytics-job, eller opdaterer en eksisterende output.  

Navnet på output kan angives i .json filen eller på kommandolinjen. Hvis begge er angivet, skal navnet på kommandolinjen være den samme som i filen.

Hvis du angiver en output, der allerede findes, og Angiv ikke – Tving parameteren, Cmdletten bliver du spurgt, om der skal erstatte den eksisterende output.

Hvis du angiver den – Tving parameter og angive en eksisterende output navn, output erstattes uden bekræftelse.

Kan finde detaljerede oplysninger om JSON-filstruktur og indhold, der refererer til [Oprette Output (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-output] sektion af [Stream Analytics Management RESTEN API Reference Library][stream.analytics.rest.api.reference].

**Eksempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Denne PowerShell-kommando opretter en ny output kaldet "output" i jobbet StreamingJob. Hvis der allerede er defineret en eksisterende output med dette navn, bede cmdlet hvorvidt skal erstatte den.

**Eksempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Denne PowerShell-kommando erstatter definitionen af "output" i jobbet StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>Ny AzureStreamAnalyticsTransformation | Ny AzureRMStreamAnalyticsTransformation
Opretter en ny transformation inden for et Stream Analytics-job, eller opdaterer eksisterende transformation.
  
Navnet på transformationen kan angives i .json filen eller på kommandolinjen. Hvis begge er angivet, skal navnet på kommandolinjen være den samme som i filen.

Hvis du angiver en transformering, der allerede findes, og Angiv ikke – Tving parameteren, Cmdletten bliver du spurgt, om der skal erstatte den eksisterende transformation.

Hvis du angiver den – Tving parameter og angive navnet på en eksisterende transformation, transformation erstattes uden bekræftelse.

Kan finde detaljerede oplysninger om JSON-filstruktur og indhold, der refererer til [Oprette Transformation (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-transformation] sektion af [Stream Analytics Management RESTEN API Reference Library][stream.analytics.rest.api.reference].

**Eksempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Denne PowerShell-kommando opretter en ny transformation kaldet StreamingJobTransform i jobbet StreamingJob. Hvis en eksisterende transformation er allerede defineret med dette navn, bede cmdlet hvorvidt skal erstatte den.

**Eksempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Denne PowerShell-kommando erstatter definitionen af StreamingJobTransform i jobbet StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Fjern AzureStreamAnalyticsInput | Fjern AzureRMStreamAnalyticsInput
Asynkront sletter en bestemt input fra en Stream Analytics sag i Microsoft Azure.  
Hvis du angiver parameteren – gældende for input, slettes uden bekræftelse.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Denne PowerShell-kommando fjerner input EventStream i jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Fjern AzureStreamAnalyticsJob | Fjern AzureRMStreamAnalyticsJob
Asynkront sletter et bestemt Stream Analytics-job i Microsoft Azure.  
Hvis du angiver – Tving parameteren jobbet slettes uden bekræftelse.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Denne PowerShell-kommando fjerner jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Fjern AzureStreamAnalyticsOutput | Fjern AzureRMStreamAnalyticsOutput
Asynkront sletter en bestemt output fra en Stream Analytics sag i Microsoft Azure.  
Hvis du angiver – Tving parameteren, output, slettes uden bekræftelse.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Denne PowerShell kommandoen fjerner output Output på jobbet StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Start AzureStreamAnalyticsJob | Start-AzureRMStreamAnalyticsJob
Asynkront installeres og starter en Stream Analytics-job i Microsoft Azure.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Denne PowerShell-kommando starter jobbet StreamingJob med et brugerdefineret output starttidspunkt, der er angivet til 12 December 2012 12:12:12 UTC.


### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Stop AzureStreamAnalyticsJob | Stop AzureRMStreamAnalyticsJob
Asynkront stopper et Stream Analytics-job i at køre i Microsoft Azure og deaktivere allokerer ressourcer, der var, der blev brugt. Definition af job og metadata, forbliver tilgængelig i dit abonnement via både Azure portalen og management API'er, så jobbet kan redigeres og genstartet. Du skal ikke betale for en sag i stoppet tilstand.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Denne PowerShell-kommando stopper jobbet StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Test AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
Tester Stream Analytics evne til at oprette forbindelse til en bestemt input.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Denne PowerShell-kommando tester forbindelsesstatus for input EntryStream i StreamingJob.  

###<a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Test AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
Tester Stream Analytics evne til at oprette forbindelse til en bestemt output.

**Eksempel 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Denne PowerShell kommandoen test forbindelsesstatus for output Output på StreamingJob.  

## <a name="get-support"></a>Få support
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forummet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics). 


## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

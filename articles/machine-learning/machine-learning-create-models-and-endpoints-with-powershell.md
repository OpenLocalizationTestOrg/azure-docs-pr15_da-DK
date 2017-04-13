<properties
pageTitle="Oprette flere modeller fra én forsøg | Microsoft Azure"
description="Bruge PowerShell til at oprette flere Machine Learning-modeller og web service slutpunkter med den samme algoritme, men forskellige kursus datasæt."
services="machine-learning"
documentationCenter=""
authors="hning86"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/03/2016"
ms.author="garye;haining"/>

# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Oprette mange Machine Learning-modeller og web service slutpunkter fra én forsøg ved hjælp af PowerShell

Her er et almindeligt machine learning problem: Du kan oprette mange modeller, der har samme kursus arbejdsprocessen og bruge den samme algoritme, men har forskellige kursus datasæt som input. I denne artikel beskrives, hvordan du gør dette, skaleres til brug i Azure Machine Learning Studio ved hjælp af en enkelt forsøg.

For eksempel Antag, at du ejer en global cykler leje voksende virksomhed. Du vil oprette en regressionsmodel for at forudsige leje behovet baseret på historiske data. Du har 1.000 leje placeringer på tværs af hele verden, og du har indsamlet et datasæt for hver placering, der indeholder vigtige funktioner som dato, klokkeslæt, vejr og trafik, der er specifikke for hver placering.

Du kan træne din model én gang ved hjælp af en flettet version af alle datasæt på tværs af alle placeringer. Men fordi hver af dine placeringer har et entydigt miljø, der ville være en bedre fremgangsmåde til at undervise regressionsmodellen separat ved hjælp af datasættet for hver placering. På denne måde hver erfaren model kan tage hensyn, forskellige store størrelser, lydstyrke, geografi, populationen, cykler-venlig trafik miljø, *osv*.

Der kan være den bedste fremgangsmåde, men du vil ikke oprette 1.000 kursus forsøg i Azure Machine Learning med hver af dem der repræsenterer en entydig placering. Ud over at blive en forvirrende opgave, det er også ser ret ineffektiv, da hvert forsøg ville have alle de samme komponenter med undtagelse af kursus datasættet.

Heldigvis kan vi udføre dette ved at bruge den [Azure Machine Learning omskoling API](machine-learning-retrain-models-programmatically.md) og automatisere opgaver med [Azure Machine Learning PowerShell](machine-learning-powershell-module.md).

> [AZURE.NOTE] Hvis du vil gøre vores eksempel køres hurtigere, får vi reducere antallet af placeringer fra 1.000 til 10. Men de samme principper og procedurer gælder for 1.000 placeringer. Den eneste forskel er, at hvis du vil undervise fra 1.000 datasæt du sandsynligvis vil betragte køre følgende PowerShell-scripts parallelt. Hvordan du gør, der er ikke medtaget i denne artikel, men du kan finde eksempler på PowerShell multitrådning på internettet.  

## <a name="set-up-the-training-experiment"></a>Konfigurere kursus forsøget

Vi bruge et eksempel [kursus eksperimentere](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) , som vi allerede har oprettet i [Cortana Intelligence galleriet](http://gallery.cortanaintelligence.com). Åbn dette forsøg i arbejdsområdet [Azure Machine Learning Studio](https://studio.azureml.net) .

>[AZURE.NOTE] Hvis du vil følge dette eksempel, kan du vil bruge et standard arbejdsområde i stedet for et gratis arbejdsområde. Vi vil oprette et slutpunkt for hver kunde - i alt 10 slutpunkter - og, der kræver et standard-arbejdsområde, da et gratis arbejdsområde er begrænset til 3 slutpunkter. Hvis du kun har en gratis arbejdsområdet, kun ændre de nedenfor for at tillade for kun 3 placeringer scripts.

Forsøget bruger et modul **Importere Data** til at importere kursus datasæt *customer001.csv* fra en Azure-lager-konto. Lad os antage vi har samlet kursus datasæt fra alle cykler leje steder og gemt dem i den samme blob storage placering med filnavne, lige fra *rentalloc001.csv* til *rentalloc10.csv*.

![Billede af](./media/machine-learning-create-models-and-endpoints-with-powershell/reader-module.png)

Bemærk, at et **Web Service Output** modul er blevet føjet til modulet **Tog Model** .
Når dette forsøg installeres som en webtjeneste, slutpunktet der er knyttet til, at output vil returnere erfaren modellen i formatet af en .ilearner fil.

Bemærk også, at vi konfigurere en web service-parameter til URL-adressen, der bruger modulet **Importdata** . Dette giver os mulighed at bruge parameteren for at angive individuelle kursus datasæt for at træne model for hver placering.
Der findes andre måder, vi kunne har gjort det, som bruger en SQL-forespørgsel med en web-tjenesten for at hente data fra en SQL Azure-database eller blot ved hjælp af et **Web Service Input** -modul til at overføre i et datasæt til webtjenesten.

![Billede af](./media/machine-learning-create-models-and-endpoints-with-powershell/web-service-output.png)

Nu, Lad os køre dette kursus forsøg ved hjælp af standard værdi *rental001.csv* som kursus datasættet. Hvis du får vist output fra modulet **Evaluer** (Klik på output, og vælg **visuelle effekter**), kan du se vi få en op. ydeevnen af *AUC* = 0.91. På dette tidspunkt er vi klar til at implementere en webtjeneste af dette kursus forsøg.

## <a name="deploy-the-training-and-scoring-web-services"></a>Installere uddannelse og vundne webtjenester

Klik på knappen **Angiv webtjeneste** under forsøg lærredet for at installere webtjenesten kursus, og vælg **Installere webtjeneste**. Ringe til webtjenesten "" cykler leje quot.".

Nu har vi brug for til at udrulle vurdering webtjenesten.
For at gøre dette, kan vi skal du klikke på **Angiv webtjeneste** under lærredet og vælge **Skønnet webtjeneste**. Dette opretter en vurdering forsøg.
Vi skal foretage nogle mindre ændringer for at tilpasse det til at fungere som en webtjeneste, som fjerne etiket kolonne "cnt" fra inputdataene og begrænse output til kun forekomst-id og den tilhørende skønsmæssigt beregnet værdi.

Hvis du vil gemme dig selv, der fungerer sammen, kan du blot åbne [skønnet forsøg](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) i galleriet, der er allerede blevet forberedt.

Køre skønnet forsøget for at installere webtjenesten, og derefter klikke på knappen **Installere webtjeneste** under lærredet. Navngive vurdering webtjenesten "Cykler leje vundne" ".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Oprette 10 identiske web service slutpunkter med PowerShell

Denne webtjeneste leveres med et standardslutpunkt. Men vi kan ikke interesse standardslutpunktet, da den ikke kan opdateres. Hvad vi skal gøre er at oprette 10 yderligere slutpunkter, en for hver placering. Vi skal du gøre med PowerShell.

Først skal konfigurere vi vores PowerShell-miljøet:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Derefter køre følgende PowerShell-kommando:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Nu har vi oprettede 10 slutpunkter og de alle indeholder samme erfaren model oplæring i *customer001.csv*. Du kan se dem i portalen Azure administration.

![Billede af](./media/machine-learning-create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Opdatere slutpunkterne for at bruge separat kursus datasæt ved hjælp af PowerShell

Næste trin er at opdatere slutpunkterne med modeller entydigt oplæring i hver enkelt kundedata. Men først vi giver disse modeller fra webtjeneste **Cykler leje kursus** . Lad os gå tilbage til webtjenesten **Cykler leje kursus** . Vi har brug for til at ringe til dens BES slutpunkt 10 gange med 10 forskellige kursus datasæt for at producere 10 forskellige modeller. Vi bruger **InovkeAmlWebServiceBESEndpoint** PowerShell-cmdlet til at gøre dette.

Du skal også angive legitimationsoplysninger for kontoen blob-lager til `$configContent`, nemlig på felterne `AccountName`, `AccountKey` og `RelativeLocation`. Den `AccountName` kan være en af dine kontonavne, som det fremgår **Klassisk Azure Management-Portal** (*lagerplads* fanen). Når du klikker på en konto med lagerplads, dens `AccountKey` kan findes ved at trykke på knappen **Administrer hurtigtaster** nederst og kopiere den *Primærnøgle i Access*. Den `RelativeLocation` er stien i forhold til din lagerplads, hvor en ny model skal gemmes. For eksempel stien `hai/retrain/bike_rental/` i script under peger på en objektbeholder med navnet `hai`, og `/retrain/bike_rental/` er undermapper. I øjeblikket, du kan ikke oprette undermapper via portalen Brugergrænsefladen, men der er [Flere Azure lagerplads stifindere](../storage/storage-explorers.md) , så du kan gøre dette. Det anbefales, at du opretter en ny objektbeholder i din lagerplads til at gemme de nye erfaren modeller (.ilearner filer) på følgende måde: Klik på knappen **Tilføj** nederst på Lagersiden og kald den `retrain`. Kort sagt de nødvendige ændringer til scriptet nedenfor vedrører `AccountName`, `AccountKey` og `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

>[AZURE.NOTE] BES slutpunkt er den eneste understøttede tilstand for denne handling. Ressourceposter kan ikke bruges til at lave erfaren modeller.

Som du kan se ovenfor, i stedet for bygning af 10 forskellige BES job konfiguration json filer, vi dynamisk oprette config-streng i stedet og feed den til parameteren *jobConfigString* af cmdlet **InvokeAmlWebServceBESEndpoint** , da det er det virkelig ikke nødvendigt at beholde en kopi på disk.

Hvis alt fungerer godt, efter et stykke tid bør du undersøge 10 .ilearner filer fra *model001.ilearner* til *model010.ilearner*i kontoen Azure-lager. Nu er vi klar til at opdatere vores 10 vurdering web service slutpunkter med disse modeller, ved hjælp af **Programrettelse AmlWebServiceEndpoint** PowerShell-cmdlet. Husk igen, at vi kan kun programrettelse er ikke-standard slutpunkterne vi automatisk oprettede tidligere.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Dette bør kunne køre ret hurtigt. Når udførelsen afsluttes, skal vil vi har oprettet 10 skønnet web service slutpunkter, hver med en erfaren modellen entydigt oplæring i datasættet bestemt til en placering, leje skal alle fra en enkelt kursus forsøg. For at bekræfte, kan du prøve at ringe til disse slutpunkter ved hjælp af cmdlet **InvokeAmlWebServiceRRSEndpoint** at give dem samme inputdataene, og du kan forvente at få vist forskellige forudsigelse resultater, da modellerne modtager oplæring med forskellige kursus datasæt.

## <a name="full-powershell-script"></a>Fuld PowerShell-script

Her er listen over den fulde kildekode:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

<properties
   pageTitle="Oprette en skabelon til distribution af logik app | Microsoft Azure"
   description="Lær at oprette en skabelon til distribution af logik app og bruge den til versionsstyring"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="create-a-logic-app-deployment-template"></a>Oprette en skabelon til distribution af logik app

Når en logik app er blevet oprettet, kan du vil oprette den som en skabelon til Azure ressourcestyring. Denne måde, kan du nemt installere appen logik til en hvilken som helst miljø eller ressourcegruppe, hvor du muligvis skal. Sørg for at se artiklerne på [redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md) og [implementere ressourcer ved hjælp af Azure ressourcestyring skabeloner](../resource-group-template-deploy.md)for at få en introduktion til Ressourcestyring skabeloner.

## <a name="logic-app-deployment-template"></a>Skabelon til distribution af logik app

En logik app består af tre grundlæggende komponenter:

* **Logik app ressource**. Denne ressource indeholder oplysninger om ting som priser plan, placering og Arbejdsprocesdefinitionen.
* **Arbejdsprocesdefinitionen**. Dette er, hvad kan ses i visningen programkode. Den indeholder definitionen af trinnene, strømmen, og hvordan programmet skal udføre. Dette er den `definition` egenskab for logik app ressourcen.
* **Forbindelser**. Dette er separat ressourcer, gemmer sikkert metadata om en hvilken som helst forbindelse forbindelser, som en forbindelsesstreng og et adgangstoken. Du refererer til disse i en logik app i den `parameters` sektion på ressourcen, logik app.

Du kan få vist alle disse elementer til eksisterende logik apps ved hjælp af et værktøj som [Azure ressource Explorer](http://resources.azure.com).

Hvis du vil oprette en skabelon til en logik app til brug med ressource gruppe installationer, skal du definere ressourcerne og standardelementet efter behov. Eksempelvis hvis du anvender for en udvikling, test og produktionsmiljø, skal du sandsynligvis vil bruge forskellige strenge til en SQL-database i de enkelte miljøer. Eller du måske installere inden for forskellige abonnementer eller grupper.  

## <a name="create-a-logic-app-deployment-template"></a>Oprette en skabelon til distribution af logik app

Den nemmeste måde at har en gyldig logik app installation skabelon er at bruge [Visual Studio Tools til logik Apps](./app-service-logic-deploy-from-vs.md).  Visual Studio-værktøjer genererer en gyldig installation skabelon, der kan bruges på tværs af en hvilken som helst abonnement eller placering.

Et par andre værktøjer kan hjælpe dig, når du opretter en skabelon til distribution af logik app. Du kan redigere manuelt, det vil sige, ved hjælp af de ressourcer, der allerede nævnt her for at oprette parametre efter behov. En anden mulighed er at bruge en [logik app skabelon creator](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell-modulet. Dette open source-modul evaluerer først logik app og alle forbindelser, den bruger, og derefter genererer skabelonressourcer med de nødvendige parametre til installation. Hvis du har en logik app, der modtager en meddelelse fra en Azure Service Bus kø og føjer data til en Azure SQL-database, vil værktøjet for eksempel opbevare alle organisering logik og standardelementet SQL og Service Bus forbindelse strengene, så de kan være indstillet til installation.

>[AZURE.NOTE] Forbindelser skal være i samme ressourcegruppe som logik app.

### <a name="install-the-logic-app-template-powershell-module"></a>Installere logik app skabelon PowerShell-modulet

Den nemmeste måde at installere modulet er via [PowerShell-galleriet](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), ved hjælp af kommandoen `Install-Module -Name LogicAppTemplate`.  

Du kan også installere PowerShell-modulet manuelt:

1. Hent den nyeste version af [logik app skabelon creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Udtrække mappen i mappen PowerShell-modul (som regel `%UserProfile%\Documents\WindowsPowerShell\Modules`).

For modulet til at arbejde med en hvilken som helst lejeradministration og abonnement adgang adgangstoken, anbefaler vi, at du bruger den med værktøjet [ARMClient](https://github.com/projectkudu/ARMClient) kommandolinjen.  Dette [blogindlæg](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) diskuterer ARMClient mere detaljeret.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Oprette en skabelon til app logik ved hjælp af PowerShell

Når PowerShell er installeret, kan du oprette en skabelon ved hjælp af følgende kommando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId`er Azure abonnement-ID. Denne linje først får en adgang token via ARMClient, og derefter pipes igennem til PowerShell-script, og derefter opretter skabelonen i en JSON-fil.

## <a name="add-parameters-to-a-logic-app-template"></a>Tilføj parametre i en logik app-skabelon

Når du opretter skabelonen logik app, kan du fortsætte med at tilføje eller redigere parametre, du skal muligvis. Eksempelvis hvis definitionen for din omfatter en ressource-ID for en Azure funktion eller indlejret arbejdsproces, som du planlægger at implementere i en enkelt installation, kan du føje flere ressourcer til skabelonen og standardelementet id'er efter behov. På samme måde gælder for eventuelle referencer til brugerdefinerede API'er eller Swagger slutpunkter du forventer at installere med hver ressourcegruppe.

## <a name="deploy-a-logic-app-template"></a>Installere en logik app-skabelon

Du kan installere skabelonen ved hjælp af en række værktøjer, herunder PowerShell, REST-API, Visual Studio versionsstyring og Azure Portal skabelon installation. Se denne artikel om [installation af ressourcer ved hjælp af Azure ressourcestyring skabeloner](../resource-group-template-deploy.md) for at få yderligere oplysninger. Vi anbefaler også, at du opretter en [parameterfil](../resource-group-template-deploy.md#parameter-file) for at gemme værdier for parameteren.

### <a name="authorize-oauth-connections"></a>Godkend OAuth forbindelser

Efter installation virker appen logik til en komplet med gyldige parametre. Dog skal OAuth forbindelser stadig have tilladelse til at oprette en gyldig adgangstoken. Du kan gøre dette ved at åbne appen logik i designer og derefter godkende forbindelser. Eller hvis du vil automatisere, kan du bruge et script til samtykke til hver OAuth-forbindelse. Der er et eksempel på et script på GitHub under [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projektet.

## <a name="visual-studio-release-management"></a>Visual Studio versionsstyring

Et almindeligt scenarie for implementering og administration af et miljø er at bruge et værktøj som Visual Studio versionsstyring, med en skabelon til distribution af logik app. Visual Studio Team Services indeholder en [Installere Azure ressourcegruppe](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) opgave, du kan føje til en hvilken som helst build eller slip pipeline. Du skal have en [tjeneste vigtigste](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) om tilladelse til at installere, og derefter kan du oprette release definitionen.

1. Hvis du vil oprette en ny definition, Vælg i versionsstyring, **tom** til at starte med en tom definition.

    ![Oprette en ny, tom definition][1]   

1. Vælg en hvilken som helst ressourcer, du har brug for til dette. Dette vil sandsynligvis være logik app skabelonen oprettes manuelt eller som en del af processen build.
1. Føje en opgave til **Azure ressource gruppe installation** .
1. Konfigurere med en [tjeneste vigtigste](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)og henvise til skabelonen og Skabelonparametre filerne.
1. Fortsæt med at opbygge trin i processen release for eventuelle andre miljø, automatiseret test eller godkendere efter behov.

<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG

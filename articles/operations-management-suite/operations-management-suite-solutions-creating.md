<properties
   pageTitle="Oprette management-løsninger i handlinger Management pakke (OMS) | Microsoft Azure"
   description="Management-løsninger, der udvider funktionaliteten af handlinger Management pakke (OMS) ved at indsende pakket management scenarier, som kunder kan føje til deres OMS arbejdsområde.  I denne artikel indeholder oplysninger om, hvordan du kan oprette management-løsninger, der skal bruges i dit eget miljø eller stilles til rådighed for dine kunder."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Oprette management-løsninger i handlinger Management pakke (OMS) (Preview)

>[AZURE.NOTE]Dette er foreløbig dokumentation til oprettelse af management-løsninger i OMS, der aktuelt findes i Vis udskrift. Et skema, der er beskrevet nedenfor kan blive ændret.  

Management-løsninger, der udvider funktionaliteten af handlinger Management pakke (OMS) ved at indsende pakket management scenarier, som kunder kan føje til deres OMS arbejdsområde.  I denne artikel indeholder oplysninger om at oprette dine egne management-løsninger, du kan bruge i dit eget miljø eller gøre tilgængelig for kunder via community'et.

## <a name="planning-your-management-solution"></a>Planlægning management-løsning
Management-løsninger i OMS indeholde flere ressourcer, der understøtter en bestemt scenarie.  Når du planlægger din løsning, skal du fokusere på dette scenario, du forsøger at opnå og alle de nødvendige ressourcer til at understøtte den.  Hver løsning skal være uafhængigt og Angiv hver ressource, der kræves, selvom en eller flere ressourcer er også defineret af andre løsninger.  Når en management-løsning er installeret, oprettes hver ressource, medmindre den allerede findes, og du kan definere, hvad sker der med ressourcer, når en løsning er fjernet.  

En management-løsning indeholder muligvis en [Azure automatisering runbook](../automation/automation-intro.md) , der indsamles data til den Log Analytics lager ved hjælp af en [tidsplan](../automation/automation-schedules.md) og en [visning](../log-analytics/log-analytics-view-designer.md) , der indeholder forskellige visualiseringer af de indsamlede data.  Den samme plan kan bruges af en anden løsning.  Som management-løsning forfatter, skal du definere alle tre ressourcer, men angiver, at runbook og få vist skal fjernes automatisk når løsningen fjernes.    Du skal også definere tidsplanen, men du kan angive, den skal forblive på plads, hvis løsningen er blevet fjernet i tilfælde af, at den er stadig er i brug af anden løsningen.

## <a name="management-solution-files"></a>Management-løsningsfiler
Management-løsninger, der er implementeret som [ressourcestyring skabeloner](../resource-manager-template-walkthrough.md).  Den primære opgave i lære at redigere management-løsninger læ Sådan [redigering af en skabelon](../resource-group-authoring-templates.md).  Denne artikel indeholder entydige oplysninger om skabeloner, der bruges til løsninger og hvordan du definerer typisk løsning ressourcer.

Den grundlæggende struktur af en management-løsningsfil er den samme som en [Ressourcestyring skabelon](resource-group-authoring-templates.md#template-format) , som er på følgende måde.  Hver af de følgende afsnit beskrives elementer på øverste niveau og og deres indhold i en løsning.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametre

[Parametre](../resource-group-authoring-templates.md#parameters) er værdier, du har brug for brugeren, når de installerer management-løsning.  Der er standard parametre, der har alle løsninger, og du kan føje yderligere parametre efter behov for din bestemte løsning.  Hvordan brugere kan give parameterværdier, når de installerer din løsning der er afhængige af parameteren bestemt, og hvordan løsningen skal installeres.


Når en bruger installerer management-løsning via [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-solutions) eller [Azure Hurtig start skabeloner](operations-management-suite-solutions.md#finding-and-installing-solutions) de bliver bedt om at vælge et [OMS workspace og automatisering konto](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Disse bruges til at udfylde værdierne i hver af de almindelige parametre.  Brugeren er ikke bedt om at angive direkte værdier for parametrene standard, men de bliver bedt om at angive værdier for eventuelle yderligere parametre.

Når brugeren installerer din løsning til [en anden metode](operations-management-suite-solutions.md#finding-and-installing-solutions), skal de angive en værdi for alle parametre for standard og alle yderligere parametre.

En parameter for eksempel er vist nedenfor.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

I følgende tabel beskrives attributterne for en parameter.

| Attribut | Beskrivelse |
|:--|:--|
| type        | Datatypen for parameteren. Kontrolelementet input vises for brugeren, afhænger af datatypen.<br><br>Boolesk - rullemenuen<br>streng - tekstfelt<br>int - tekstfelt<br>SecureString - felt til adgangskode<br> |
| kategori    | Valgfri kategori for parameteren.  Parametre i den samme kategori er grupperet. |
| kontrolelement     | Ekstra funktioner til strengparametre.<br><br>DateTime - Datetime kontrolelement vises.<br>GUID - Guid værdi genereres automatisk, og parameteren vises ikke. |
| Beskrivelse | Valgfri beskrivelse til parameteren.  Vises i en oplysninger taleboble ud for parameteren. |


### <a name="standard-parameters"></a>Standard parametre
I følgende tabel vises de standard parametre for alle management-løsninger.  Disse værdier er udfyldt for brugeren, i stedet for at bede om dem, når din løsning er installeret fra Azure Marketplace eller Hurtig start skabeloner.  Brugeren skal angive værdier for dem, hvis løsningen er installeret med en anden metode.

>[AZURE.NOTE]Brugergrænsefladen i Azure Marketplace og hurtig start skabeloner forventer parameternavne i tabellen.  Hvis du bruger forskellige parameternavne derefter brugeren vil blive bedt om dem, og de udfyldes ikke automatisk.


| Parameter | Type | Beskrivelse |
|:--|:--|:--|
| Kontonavn       | streng |  Azure automatisering kontonavn. |
| pricingTier       | streng | Priser niveau i både Log Analytics arbejdsområde og Azure automatisering konto. |
| regionId          | streng | Område til kontoen, Azure automatisering. |
| solutionName      | streng | Navnet på løsningen. |
| workspaceName     | streng | Log Analytics arbejdsområdenavn. |
| workspaceRegionId | streng | Område i arbejdsområdet Log analyser. |





### <a name="sample"></a>Eksempel
Følgende er et eksempel parameter objekt for en løsning.  Dette omfatter alle de almindelige parametre og to yderligere parametre i samme kategori.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "A valid Azure Automation account name"
            }
        },
        "workspaceRegionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Du referere til parameterværdier i andre elementer i løsning med følgende syntaks **parametre ('parameternavn')**.  For eksempel for at få adgang til navnet på arbejdsområdet, vil du bruge **parameters('workspaceName')** 

## <a name="variables"></a>Variabler

**Variabler** elementet indeholder værdier, du vil bruge i resten af management-løsning.  Disse værdier vises ikke den bruger, der installerer løsningen.  De er beregnet til at give forfatteren med en enkelt placering, hvor de kan administrere værdier, der kan bruges flere gange i hele løsningen. Du skal indsætte en hvilken som helst værdier bestemte din løsning i variabler i modsætning til hardcoding dem i elementet **ressourcer** .  Dette gør koden lettere at læse og kan du nemt ændre disse værdier i senere versioner.

Følgende er et eksempel på et **variabler** element med typisk parametre bruges i løsninger.

    "variables": { 
        "SolutionVersion": "1.1", 
        "SolutionPublisher": "Contoso", 
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Du referere til variable værdier gennem løsningen med syntaksen **variabler ('variabelnavn')**.  For eksempel for at få adgang til SolutionName variablen, vil du bruge **variables('solutionName')** 


## <a name="resources"></a>Ressourcer

Elementet **ressourcer, der** definerer de forskellige ressourcer, der er inkluderet i din management-løsning.  Dette er den største og mest komplekse del af skabelonen.  Ressourcer, der er defineret med følgende struktur.  

    "resources": [
        {
            "name": "<name-of-the-resource>",           
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",     
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Afhængigheder
Elementerne, der **dependsOn** angiver en [afhængighed](../resource-group-define-dependencies.md) på en anden ressource.  Når løsningen er installeret, oprettes der ikke en ressource indtil alle de tilhørende afhængigheder er blevet oprettet.  For eksempel kan din løsning [starte en runbook](operations-management-suite-solutions-resources-automation.md#runbooks) når den er installeret ved hjælp af en [sag ressource](operations-management-suite-solutions-resources-automation.md#automation-jobs).  Jobstatus ressourcen ville være afhængig af runbook ressourcen at sikre dig, at der oprettes runbook, før der oprettes jobbet.

### <a name="oms-workspace-and-automation-account"></a>OMS workspace og automatisering konto
Management-løsninger, der kræver et [OMS arbejdsområde](../log-analytics/log-analytics-manage-access.md) til at indeholde visninger og en [automatisering konto](../automation/automation-security-overview.md#automation-account-overview) skal indeholde runbooks og relaterede ressourcer.  Disse skal være tilgængelige, før ressourcerne i løsningen oprettes og skal ikke defineres i løsningen selve.  Brugeren skal [angive et arbejdsområde og konto](operations-management-suite-solutions.md#oms-workspace-and-automation-account) når de installere løsningen, men som forfatter bør du overveje følgende punkter.


## <a name="solution-resource"></a>Løsning ressource
Hver løsning kræver en ressourcepost i elementet **ressourcer** , der definerer løsningen sig selv.  Dette får en type af **Microsoft.OperationsManagement/solutions**.  Følgende er et eksempel på en løsning.  Dens forskellige elementer er beskrevet i afsnittene herunder.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Løsningsnavn
Navnet på løsningen skal være entydige i abonnementet Azure. Den anbefalede værdi bruge er følgende.  Dette bruger en variabel, kaldet **SolutionName** til grundlæggende navnet og parameteren **workspaceName** til at sikre, at navnet er entydige.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Dette vil løse til et navn ud som følger.

    My Solution Name [MyWorkspace]
 

### <a name="dependencies"></a>Afhængigheder
Løsning ressourcen skal have en [afhængighed](../resource-group-define-dependencies.md) på hver anden ressource i løsningen, da de skal findes, før løsningen kan oprettes.  Du kan gøre dette ved at tilføje en post for hver ressource i elementet **dependsOn** .


### <a name="properties"></a>Egenskaber
Løsning ressourcen har egenskaberne i den følgende tabel.  Dette omfatter de ressourcer, der refereres til og opbevares ved den løsning, der definerer, hvordan ressourcen administreres, når løsningen er installeret.  Hver ressource i løsningen skal være angivet i **referencedResources** eller egenskaben **containedResources** .

| Egenskaben | Beskrivelse |
|:--|:--|
| workspaceResourceId | ID for OMS arbejdsområdet i formularen * <Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<navn på arbejdsområde\>*. |
| referencedResources   | Liste over ressourcer i den løsning, der ikke kan fjernes, når løsningen er fjernet. |
| containedResources   | Liste over ressourcer i en løsning, der skal fjernes, når løsningen fjernes. |

Ovenstående eksempel er en løsning med en runbook, en tidsplan og visning.  Planlægge og runbook er *der refereres til* i elementet **Egenskaber** , så de ikke fjernes, når løsningen fjernes.  Visningen er *indeholdt* , så den fjernes, når løsningen er fjernet.


### <a name="plan"></a>Planlægge
Objektet **plan** af løsning ressourcen har egenskaberne i den følgende tabel. 

| Egenskaben | Beskrivelse |
|:--|:--|
| Navn          | Navnet på løsningen. |
| version       | Version af løsningen som fastsat af forfatteren. |
| produkt       | Entydig streng til at identificere løsningen. |
| Publisher     | Udgiver af løsningen. |


## <a name="other-resources"></a>Andre ressourcer
Du kan få mere at vide og eksempler på ressourcer, der er fælles for management-løsninger i følgende artikler.

- [Visninger og dashboards](operations-management-suite-solutions-resources-views.md)
- [Automatisering ressourcer](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Teste en management-løsning
Før du anvender management-løsning, anbefales det, at du tester den ved hjælp af [Test-AzureRmResourceGroupDeployment](../resource-group-template-deploy.md#deploy-with-powershell).  Dette validerer din løsningsfil og Hjælp du identificere problemer, før du forsøger at installere den.



## <a name="next-steps"></a>Næste trin

- Se oplysninger om [redigering af Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md).
- Søg [Azure Hurtig start skabeloner](https://azure.microsoft.com/documentation/templates) til eksempler på forskellige ressourcestyring skabeloner.
- Få vist oplysninger til [at tilføje visninger, som en løsning](operations-management-suite-solutions-resources-views.md).
- Få vist oplysninger om [tilføjelse af automatisering ressourcer til en management-løsning](operations-management-suite-solutions-resources-automation.md).
 
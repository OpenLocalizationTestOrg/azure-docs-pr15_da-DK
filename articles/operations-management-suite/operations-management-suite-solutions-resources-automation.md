<properties
   pageTitle="Automatisering ressourcer i OMS løsninger | Microsoft Azure"
   description="Løsninger i OMS indeholder typisk runbooks i Azure automatisering til at automatisere processer som indsamling og behandling af overvågningsdata.  I denne artikel beskrives, hvordan du kan medtage runbooks og deres relaterede ressourcer i en løsning."
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
   ms.date="10/19/2016"
   ms.author="bwren" />

# <a name="automation-resources-in-oms-solutions-preview"></a>Automatisering ressourcer i OMS løsninger (Preview)

>[AZURE.NOTE]Dette er foreløbig dokumentation til oprettelse af management-løsninger i OMS, der aktuelt findes i Vis udskrift. Et skema, der er beskrevet nedenfor kan blive ændret.   

[Management-løsninger i OMS](operations-management-suite-solutions.md) indeholder typisk runbooks i Azure automatisering til at automatisere processer som indsamling og behandling af overvågningsdata.  Ud over runbooks omfatter automatisering konti medieaktiver som variabler og tidsplaner, der understøtter runbooks bruges i en løsning.  I denne artikel beskrives, hvordan du kan medtage runbooks og deres relaterede ressourcer i en løsning.

>[AZURE.NOTE]Eksemplerne i denne artikel bruge parametre og variabler, der kræves eller er fælles for management-løsninger og er beskrevet i [oprette management-løsninger i handlinger Management pakke (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Forudsætninger
Denne artikel forudsætter, at du allerede er kendskab til, hvordan du kan [oprette en løsning til administration](operations-management-suite-solutions-creating.md) og strukturen i en løsningsfil.

## <a name="samples"></a>Eksempler
Du kan hente ressourcestyring eksempelskabeloner til automatisering ressourcer fra [Hurtig start skabeloner i GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Automatisering konto
Alle ressourcer i Azure automatisering er indeholdt i en [automatisering konto](../automation/automation-security-overview.md#automation-account-overview).  Som beskrevet i [OMS arbejdsområdet og automatisering konto](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account) ikke er medtaget i management-løsning kontoen automatisering, men skal findes, før løsningen er installeret.  Hvis det er ikke tilgængelig, mislykkes løsning installationen.

Navnet på deres automatisering konto er navnet på hver automatisering ressource.  Dette gøres i løsningen med parameteren **kontonavn** som i følgende eksempel på en runbook ressource.
    
    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
[Azure automatisering runbook](../automation/automation-runbook-types.md) ressourcer har en type af **Microsoft.Automation/automationAccounts/runbooks** og bruge egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| runbookType | Angiver typerne af runbook. <br><br> Script - PowerShell-script <br>PowerShell - PowerShell arbejdsproces <br> GraphPowerShell - grafiske PowerShell-script runbook <br> GraphPowerShellWorkflow - grafiske PowerShell arbejdsproces runbook   |
| logProgress | Angiver, om [fremskridt poster](../automation/automation-runbook-output-and-messages.md) skal genereres for runbook. |
| logVerbose  | Angiver, om [detaljeret poster](../automation/automation-runbook-output-and-messages.md) skal genereres for runbook. |
| Beskrivelse | Valgfri beskrivelse af runbook. |
| publishContentLink | Angiver indholdet af runbook. <br><br>URI - Uri til indholdet af runbook.  Dette er en .ps1 fil til PowerShell og Script runbooks og en eksporterede grafiske runbook fil til en graf runbook.  <br> version - Version af runbook til din egen sporing. |

Et eksempel på en runbook ressource er nedenfor.  I dette tilfælde skal hentes runbook fra [PowerShell galleriet](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Starte en runbook
Der findes to metoder til at starte en runbook i en løsning.

- For at starte runbook, når løsningen er installeret, skal du oprette en [sag ressource](#automation-jobs) , som beskrevet nedenfor.
- For at starte runbook efter en tidsplan, skal du oprette en [tidsplan ressource](#schedules) , som beskrevet nedenfor. 


## <a name="automation-jobs"></a>Automatisering job
For at starte en runbook, når management-løsning er installeret, skal oprette du en **sag** ressource.  Jobbet ressourcer har en type af **Microsoft.Automation/automationAccounts/jobs** og bruge egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| runbook    | Enkelt **navn** enhed med navnet på runbook.  |
| parametre | Enhed for hver parameter, der kræves af runbook. |


Jobbet indeholder runbook navn og en hvilken som helst parameterværdier skal sendes til runbook.  Jobbet skal [afhænger af](operations-management-suite-solutions-creating.md#resources) runbook, som den starter siden runbook skal oprettes før jobbet.  Du kan også oprette afhængigheder på andre job i runbooks, der skal udføres, før den aktuelle.

Navnet på en sag skal indeholde en GUID, der tildeles typisk af en parameter.  Du kan læse mere om GUID parametre i [oprettelse af løsninger i handlinger Management pakke (OMS)](operations-management-suite-solutions-creating.md#parameters).  

Følgende er et eksempel på en sag ressource, der starter en runbook, når management-løsning er installeret.  En anden runbooks skal være fuldført, før den pågældende en starter, så den er afhængig af sager for denne runbook.  Oplysninger om jobbet er tilgængelige via parametre og variabler i stedet for der angives direkte.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certifikater
[Azure automatisering certifikater](../automation/automation-certificates.md) har en type af **Microsoft.Automation/automationAccounts/certificates** og har egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| base64Value   | Base 64 værdi for certifikatet. |
| miniature    | Miniature for certifikatet. |

Et eksempel på et certifikat ressource er nedenfor.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Legitimationsoplysninger
[Azure automatisering legitimationsoplysninger](../automation/automation-credentials.md) har en type af **Microsoft.Automation/automationAccounts/credentials** og har egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| Brugernavn   | Brugernavn for legitimationsoplysninger. |
| adgangskode   | Adgangskode til legitimationsoplysninger. |

Et eksempel på en ressource, legitimationsoplysninger er nedenfor.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Tidsplaner
[Azure automatisering tidsplaner](../automation/automation-schedules.md) har en type af **Microsoft.Automation/automationAccounts/schedules** og har egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| Beskrivelse | Valgfri beskrivelse af tidsplanen. |
| Starttidspunkt   | Angiver starttidspunktet for en plan som et dato/klokkeslæt-objekt. En streng, der kan leveres, hvis det kan konverteres til en gyldig DateTime. |
| isEnabled   | Angiver, om planen er aktiveret. |
| interval    | Hvilken type interval for tidsplanen.<br><br>dag<br>time |
| frekvens   | Frekvens, der skal aktiveres tidsplanen i antal dage eller timer. |

Et eksempel på en tidsplan ressource er nedenfor.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variabler
[Azure automatisering variabler](../automation/automation-variables.md) har en type af **Microsoft.Automation/automationAccounts/variables** og har egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| Beskrivelse | Valgfri beskrivelse af variablen. |
| isEncrypted | Angiver, om variablen skal krypteres. |
| type        | Datatypen for variablen. |
| værdi       | Værdi for variablen. |

Et eksempel på en variabel ressource er nedenfor.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Moduler
Management-løsning behøver ikke at definere [globale moduler](../automation/automation-integration-modules.md) bruges af din runbooks, fordi de altid er tilgængelig.  Du har brug for at medtage en ressource til et andet modul, der anvendes af din runbooks og runbook skal afhænger af modul ressourcen til at sikre, at den er oprettet før runbook.

[Integrationsmoduler](../automation/automation-integration-modules.md) har en type af **Microsoft.Automation/automationAccounts/modules** og har egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| contentLink | Angiver indholdet i modulet. <br><br>URI - Uri til indholdet af runbook.  Dette er en .ps1 fil til PowerShell og Script runbooks og en eksporterede grafiske runbook fil til en graf runbook.  <br> version - Version af runbook til din egen sporing. |

Et eksempel på en modul ressource er nedenfor.

    {       
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Opdatere moduler
Hvis du opdaterer en management-løsning, der indeholder en runbook, der bruger en tidsplan, og den nye version af din løsning er et nyt modul, der anvendes af runbook, kan derefter runbook bruge den gamle version af modulet.  Du skal medtage følgende runbooks i din løsning og oprette et job for at køre dem, før alle andre runbooks.  Dette sikrer, at opdateres moduler, som kræves, før runbooks indlæses.

- [Opdater ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) sikrer, at alle de moduler, der bruges af runbooks i din løsning er den nyeste version.  
- [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) registrere alle tidsplan ressourcerne til at sikre, at runbooks sammenkædet med dem med brug de nyeste moduler.


Følgende er en stikprøve af de nødvendige elementer i en løsning til at understøtte opdateringen modul.
    
    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Næste trin

- [Tilføje en visning til din løsning](operations-management-suite-solutions-resources-views.md) til visualisering af indsamlede data.
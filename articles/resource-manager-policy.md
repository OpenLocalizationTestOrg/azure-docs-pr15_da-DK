<properties
    pageTitle="Azure ressourcestyring politik | Microsoft Azure"
    description="Beskriver, hvordan du bruger Azure ressourcestyring politik til at forhindre overtrædelse af på forskellige områder som abonnement, grupper eller individuelle ressourcer."
    services="azure-resource-manager"
    documentationCenter="na"
    authors="ravbhatnagar"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="gauravbh;tomfitz"/>

# <a name="use-policy-to-manage-resources-and-control-access"></a>Brug politik til at administrere ressourcer og kontrollere adgangen

Azure ressourcestyring kan du nu til at styre adgangen til brugerdefinerede politikker. Med politikker, kan du forhindre brugere i organisationen fra Bryd konventioner, der skal bruges til at administrere din organisations ressourcer. 

Du opretter Politikdefinitioner, der beskriver de handlinger eller ressourcer, der er specielt nægtet. Du tildele definitionerne politik på det ønskede område, som abonnementet, ressourcegruppe eller en enkelt ressource. 

I denne artikel forklarer vi den grundlæggende struktur for politik definition-sprog, du kan bruge til at oprette politikker. Vi beskriver, hvordan du kan anvende disse politikker på forskellige områder.

## <a name="how-is-it-different-from-rbac"></a>Hvordan er det adskiller sig fra RBAC?

Der er nogle vigtige forskelle mellem politik og rollebaseret adgangskontrol, men det første at forstå er politikker og RBAC arbejde sammen. Hvis du vil bruge politikker, skal du godkendes via RBAC. I modsætning til RBAC, politik er et standard Tillad og eksplicit nægte system. 

RBAC fokuserer på de handlinger, der er en **bruger** kan udføre i forskellige områder. For eksempel føjes en bestemt bruger til bidragyderrollen for en ressourcegruppe på det ønskede område, så brugeren kan foretage ændringer i ressourcegruppen. 

Politik fokuserer på **ressource** handlinger på forskellige områder. Gennem politikker, kan du kontrollere type ressourcer, der kan klargøres eller begrænse de steder, hvor ressourcerne, der kan klargøres.

## <a name="common-scenarios"></a>Almindelige scenarier

Et almindelige scenarie er at kræver afdelings mærker til kortejeren formål. En organisation måske tillade handlinger, når den relevante ressource, der er knyttet; Ellers skal afvise de anmodningen. Denne politik hjælper dem med at tage et gebyr den relevante bærer for de handlinger, der udføres.

En anden almindelige scenarie er, at organisationen måske til at styre de placeringer, hvor ressourcer, der er oprettet. Eller de måske til at styre adgangen til ressourcer ved at tillade kun visse typer ressourcer skal klargøres.

På samme måde, en organisation kan styre kataloget tjenesten eller gennemtvinge de ønskede navngivningskonventioner for ressourcerne.

Ved hjælp af politikker, kan disse scenarier nemt opnås.

## <a name="policy-definition-structure"></a>Politik Definition struktur

Politikdefinition af er oprettet ved hjælp af JSON. Det består af en eller flere betingelser/logiske operatorer, der definerer handlingerne, og en effekt, der fortæller, hvad der sker, når betingelserne er opfyldt. Skemaet er blevet publiceret på [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

Grundlæggende, en politik indeholder følgende elementer:

**Betingelse/logiske operatorer:** et sæt af betingelser, som kan være ændres ved hjælp af et sæt af logiske operatorer.

**Effekt:** Hvad sker der, når betingelsen er opfyldt – enten nægte eller overvåge. En overvågningslog effekt udsender en advarsel hændelseslog tjeneste. For eksempel kan en administrator oprette en politik, der forårsager en overvågningslog begivenhed, hvis alle opretter en stor VM. Administratoren kan gennemgå loggene senere.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }
    
## <a name="policy-evaluation"></a>Evaluering af politikker

Politikker evalueres, når der oprettes ressourcer. I tilfælde af installation af skabelon evalueres politikker under oprettelse af hver ressource i skabelonen. 

> [AZURE.NOTE] Politik evalueres i øjeblikket ikke ressourcetyper, der ikke understøtter mærker, type og placering, som ressourcetype Microsoft.Resources/deployments. Denne understøttelse tilføjes på et senere tidspunkt. For at undgå problemer med bagudkompatibilitet, skal du eksplicit angive type ved redigering politikker. For eksempel anvendes en politik for mærke, der ikke angiver typer for alle typer. Det er tilfældet, en skabelon installation mislykkes muligvis, hvis der er en indlejret ressource, som ikke understøtter mærker, og ressourcetype installation er blevet føjet til evaluering af politikker. 

## <a name="logical-operators"></a>Logiske operatorer

Understøttede logiske operatorer sammen med syntaksen er:

| Operatoren navn     | Syntaksen for         |
| :------------- | :------------- |
| Ikke            | "not": {&lt;betingelse eller operator &gt;}             |
| Og           | "allOf": [{&lt;betingelse eller operator &gt;}, {&lt;betingelse eller operator &gt;}] |
| Eller                         | "anyOf": [{&lt;betingelse eller operator &gt;}, {&lt;betingelse eller operator &gt;}] |

Ressourceleder, der gør det muligt at angive kompleks logik i din politik gennem indlejrede operatorer. Du kan for eksempel nægte oprettelse af ressource i en bestemt placering til en bestemt ressourcetype. Et eksempel på indlejret operatorer er vist nedenfor.

## <a name="conditions"></a>Betingelser

En betingelse evalueres om et **felt** eller **kilde** opfylder bestemte kriterier. Understøttede betingelse navne og syntaksen er:

| Navnet på tilstanden | Syntaksen for                |
| :------------- | :------------- |
| Er lig med             | "er lig med": "&lt;værdi&gt;"               |
| Synes godt om                  | "som": "&lt;værdi&gt;"                   |
| Indeholder          | "indeholder": "&lt;værdi&gt;"|
| I                        | "i": ["&lt;værdi1&gt;","&lt;værdi2&gt;"]|
| ContainsKey    | "containsKey": "&lt;nøglenavn&gt;" |
| Der findes     | "findes": "&lt;boolesk&gt;" |

### <a name="fields"></a>Felter

Betingelser der dannes ved hjælp af felter og kilder. Et felt repræsenterer egenskaber i ressource anmodning om data, der bruges til at beskrive tilstanden for ressourcen. En kilde repræsenterer egenskaber for din anmodning sig selv. 

Følgende felter og kilder understøttes:

Felter: **navn**, **type**, **type**, **placering**, **mærker**, **mærker.** *, and * *egenskaben alias**. 

### <a name="property-aliases"></a>Egenskaben aliasser 
Egenskaben alias er et navn, der kan bruges i en Politikdefinition til at få adgang til de ressource type specifikke egenskaber, som indstillinger og lagerenheder. Det fungerer på tværs af alle API-versioner, hvor egenskaben findes. Aliasser kan hentes ved hjælp af REST-API, der er vist nedenfor (Powershell føjes understøttelse i fremtiden):

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
    
Definitionen af et alias er vist nedenfor. Som du kan se, definerer et alias stier i forskellige versioner af API, selv når der er en ændring af egenskaber for navn. 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

Understøttede aliasser er i øjeblikket:

| Aliasnavn | Beskrivelse |
| ---------- | ----------- |
| {resourceType}/sku.name | Understøttede ressourcetyper er: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family | Understøttede ressourcetype er Microsoft.Cache/Redis |
| {resourceType}/sku.capacity | Understøttede ressourcetype er Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher |  |
| Microsoft.Compute/virtualMachines/imageOffer  |  |
| Microsoft.Compute/virtualMachines/imageSku  |  |
| Microsoft.Compute/virtualMachines/imageVersion  |  |
| Microsoft.Cache/Redis/enableNonSslPort |  |
| Microsoft.Cache/Redis/shardCount |  |
| Microsoft.SQL/servers/version |  |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId |  |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName |  |
| Microsoft.SQL/servers/databases/edition |  |
| Microsoft.SQL/servers/databases/elasticPoolName |  |
| Microsoft.SQL/servers/elasticPools/dtu |  |
| Microsoft.SQL/servers/elasticPools/edition |  |

Politik fungerer i øjeblikket kun på læg anmodninger. 

## <a name="effect"></a>Effekt
Politik understøtter tre typer effekt - **nægte**, **overvåge**og **tilføje**. 

- Nægte genererer en begivenhed i overvågningsloggen og mislykkes anmodningen
- Overvågningslog genererer en begivenhed i overvågningsloggen, men mislykkes ikke anmodningen
- Tilføje føjer det definerede sæt felter til anmodningen 

For at **tilføje**, skal du angive følgende oplysninger:

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

Værdien, der kan være enten en streng eller et JSON Formatér objekt. 

## <a name="policy-definition-examples"></a>Politik Definition eksempler

Nu Lad os se på hvordan vi definere politik for at opnå de foregående scenarier.

### <a name="chargeback-require-departmental-tags"></a>Kortejeren: Kræv afdelings mærker

Følgende politik afviser anmodninger, der ikke har et mærke, der indeholder "costCenter" nøgle.

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

Følgende politik føjer costCenter mærke med en foruddefineret værdi, når ingen mærker er til stede. 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }
    
Følgende politik føjer costCenter mærke med en foruddefineret værdi, når mærket costCenter er ikke til stede, men andre mærker er til stede. 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]
    
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>Geografisk kompatibilitet: Sikre ressource placeringer

I følgende eksempel viser en politik, der afviser anmodninger, hvor placering er ikke North Europa eller Vest Europe.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>Tjenesten Curation: Vælg service-katalog

I følgende eksempel viser en politik, der tillader handlinger kun på tjenesterne af typen Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\*, Microsoft.Network/\* er tilladt. Andet nægtet.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>Brug godkendt lagerenheder

I følgende eksempel vises brugen af egenskaben alias til at begrænse lagerenheder. I eksemplet godkendt kun Standard_LRS og Standard_GRS for lagerplads konti.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
    

### <a name="naming-convention"></a>Naming Convention

I følgende eksempel viser brug af jokertegn, som understøttes i betingelsen "som". Betingelsen angiver, at hvis navnet overens med den omtalte mønster (namePrefix\*nameSuffix) derefter afvise anmodningen.

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }
    
### <a name="tag-requirement-just-for-storage-resources"></a>Mærke kravet lige om lagerplads ressourcer

I følgende eksempel viser, hvordan du indlejre logiske operatorer for at kræve en programmet mærke for kun lagerplads ressourcer.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="policy-assignment"></a>Politik for opgave

Politikker, der anvendes på de forskellige områder som abonnement, grupper og individuelle ressourcer. Politikker nedarves af alle underordnede ressourcer. Så, hvis en politikken anvendes på en ressourcegruppe, er det gælder for alle ressourcer i denne ressourcegruppe.

## <a name="creating-a-policy"></a>Oprette en politik

Dette afsnit indeholder oplysninger om hvordan en politik kan være oprettet ved hjælp af REST-API.

### <a name="create-policy-definition-with-rest-api"></a>Oprette Politikdefinition med REST-API

Du kan oprette en politik med [REST-API til Politikdefinitioner](https://msdn.microsoft.com/library/azure/mt588471.aspx). REST-API gør det muligt at oprette og slette Politikdefinitioner, og få oplysninger om eksisterende definitioner.

Hvis du vil oprette en politik, skal du køre:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Med en anmodningsteksten ligner det følgende eksempel:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }


Brug *2016-04-01*til api-version. Eksempler og få mere at vide under [REST-API til Politikdefinitioner](https://msdn.microsoft.com/library/azure/mt588471.aspx).

### <a name="create-policy-definition-using-powershell"></a>Oprette Politikdefinition ved hjælp af PowerShell

Du kan oprette en for Politikdefinition af ved hjælp af ny AzureRmPolicyDefinition cmdlet. I følgende eksempel oprettes en politik for tillade ressourcer kun i North Europa og Vest Europe.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{  
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'          

Output fra udførelse af er gemt i $policy objekt og kan bruges senere under politik. Parameteren politik, kan stien til filen .json, som indeholder politikken også leveres i stedet for at angive politik indbygget.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain    regions" -Policy "path-to-policy-json-on-disk"

### <a name="create-policy-definition-using-azure-cli"></a>Oprette politikdefinitionen af med Azure CLI

Du kan oprette en politikdefinitionen af med azure CLI med kommandoen politik definition som vist nedenfor. I følgende eksempel oprettes en politik for tillade ressourcer kun i North Europa og Vest Europe.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{   
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    
    

Det er muligt at angive stien til filen .json, som indeholder politikken i stedet for at angive politik indbygget som vist nedenfor.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"


## <a name="applying-a-policy"></a>Anvende en politik

### <a name="policy-assignment-with-rest-api"></a>Politik med REST API'ER

Du kan anvende politikdefinitionen på det ønskede område gennem [REST-API for politik for tildelinger](https://msdn.microsoft.com/library/azure/mt588466.aspx). REST-API gør det muligt at oprette og slette politik tildelinger og få at vide om eksisterende tildelinger.

For at oprette en politik for opgave skal du køre:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{Politik-tildelingen} er navnet på politik. Brug *2016-04-01*til api-version. 

Med en anmodningsteksten ligner det følgende eksempel:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

Eksempler og få mere at vide under [REST-API for politik for tildelinger](https://msdn.microsoft.com/library/azure/mt588466.aspx).

### <a name="policy-assignment-using-powershell"></a>Politik for opgave ved hjælp af PowerShell

Du kan anvende politikken oprettet over via PowerShell til det ønskede område ved hjælp af ny AzureRmPolicyAssignment cmdlet:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
Her er $policy politikobjektet, der blev returneret som et resultat af afholde Cmdletten ny AzureRmPolicyDefinition, som vist ovenfor. Området her er navnet på den ressourcegruppe, du angiver.

Hvis du vil fjerne den ovenstående politik, kan du gøre det på følgende måde:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Du kan få, ændre eller fjerne Politikdefinitioner igennem Get-AzureRmPolicyDefinition, angive AzureRmPolicyDefinition, og fjern AzureRmPolicyDefinition cmdletter henholdsvis.

På samme måde, kan du få, ændre eller fjerne politik tildelinger igennem Get-AzureRmPolicyAssignment, angive AzureRmPolicyAssignment, og fjern AzureRmPolicyAssignment cmdletter henholdsvis.

### <a name="policy-assignment-using-azure-cli"></a>Politik for opgave ved hjælp af Azure CLI

Du kan anvende politikken oprettet over via Azure CLI til det ønskede område ved hjælp af kommandoen politik for opgave:

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
Området her er navnet på den ressourcegruppe, du angiver. Hvis værdien af parameter politik-definition-id er ukendt, er det muligt at opnå den gennem Azure CLI, som vist nedenfor: 

    azure policy definition show <policy-name>

Hvis du vil fjerne den ovenstående politik, kan du gøre det på følgende måde:

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Du kan få, ændre eller fjerne politik definitioner via Politikdefinition angivet, få vist og slette kommandoer henholdsvis.

På samme måde, kan du få, ændre eller fjerne politik tildelinger gennem politik vise og slette kommandoer henholdsvis.

##<a name="policy-audit-events"></a>Politik overvågningshændelser

Når du har anvendt din politik, kan du begynde at se politik-relaterede hændelser. Du kan enten gå til portalen, bruge PowerShell eller Azure CLI for at få disse data. 

### <a name="policy-audit-events-using-powershell"></a>Politik overvågningshændelser ved hjælp af PowerShell

For at få vist alle hændelser, der er relateret til at afvise effekt, kan du bruge følgende PowerShell-kommando:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

For at få vist alle hændelser, der er relateret til overvåge effekt, kan du bruge følgende kommando:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="policy-audit-events-using-azure-cli"></a>Politik overvågningshændelser ved hjælp af Azure CLI

At få vist alle begivenheder fra en ressource gruppere, der vedrører nægte effekt, kan du bruge følgende kommando for CLI:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

For at få vist alle hændelser, der er relateret til overvåge effekt, kan du bruge følgende kommando for CLI:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"

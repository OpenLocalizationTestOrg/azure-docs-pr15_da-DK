<properties 
    pageTitle="Nye skema version 2015-08-01-preview" 
    description="Lær, hvordan du kan skrive JSON definitionen for den seneste version af logik apps" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>
    
# <a name="new-schema-version-2015-08-01-preview"></a>Nye skema version 2015-08-01-preview

Ny skemaet og API-versionen for logik apps har en række forbedringer, forbedre pålideligheden og øget for anvendelse af logik apps. Der er 4 vigtige forskelle:

1. Handlingstype **APIApp** er blevet opdateret til en ny **APIConnection** handlingstype.
2. **Gentag** er blevet omdøbt til **Foreach**.
3. **Http-lytteren** API app er ikke længere påkrævet.
4. Ringe til underordnede arbejdsprocesser bruger et nyt skema.

## <a name="1-moving-to-api-connections"></a>1. flytte til API forbindelser

Den største ændring er, at du ikke længere skal bruge til at udrulle API apps til abonnementet Azure bruge API'EN. Der er 2 måder, du kan bruge API'er:
* Administrerede API
* Din brugerdefinerede Web APIS

Hver af disse håndteres en smule anderledes, fordi deres administration og vært modeller er forskellige. En af fordelene ved denne model er du ikke længere er begrænset til ressourcer, der er installeret i din ressourcegruppe. 

### <a name="managed-apis"></a>Administrerede API'er

Der er et antal API, der administreres af Microsoft på dine vegne, såsom Office 365, Salesforce, Twitter, FTP-osv... Nogle af disse administrerede API kan bruges som-– som Bing oversætte, er, mens andre kræver konfiguration. Denne konfiguration kaldes en *forbindelse*.

For eksempel, når du bruger Office 365, skal du oprette en forbindelse, der indeholder din Office 365-logon token. Dette token er sikkert gemt og opdateres, så din logik app kan altid ringe til Office 365 API. Du kan også, hvis du vil oprette forbindelse til din SQL- eller FTP-server, skal du oprette en forbindelse, der indeholder forbindelsesstrengen. 

Inde i definitionen disse handlinger kaldes `APIConnection`. Her er et eksempel på en forbindelse, der ringer op til Office 365 for at sende en mail:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

Den del af materialer, der er entydige for API forbindelser er den `host` objekt. Dette indeholder to dele: `api` og `connection`.

Den `api` har runtime URL-adressen til hvor, der administreres API er hostet. Du kan se alle de tilgængelige administrerede API'er for dig ved at ringe til `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Når du bruger en API, du kan eller kan ikke have en hvilken som helst **forbindelsesparametre** , der er defineret. Hvis det ikke derefter er ingen **forbindelse** påkrævet. Hvis det er tilfældet, skal du have til at oprette en forbindelse. Når du opretter den forbindelse, det har det navn, du vælger, og derefter du henvise til, i den `connection` objekt i den `host` objekt. Hvis du vil oprette en forbindelse i en ressourcegruppe, skal du ringe:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Med følgende brødteksten:


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues" : {
        "accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location" : "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>Implementere administreres API'er i en skabelon til Azure ressource manager

Du kan oprette en komplette program i en skabelon til ARM, så længe den ikke kræver interaktive logon. Hvis det kræver logon, kan du kan konfigurere det hele med skabelonen ARM, men du stadig har skal du gå til portalen for at godkende forbindelserne. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/',parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:,Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

Du kan se i dette eksempel, at forbindelserne er lige normal ressourcer, direkte i din ressourcegruppe. De referere til den managedAPIs, der er tilgængelige i dit abonnement.

### <a name="your-custom-web-apis"></a>Din brugerdefineret Web-API'er

Hvis du bruger din egen API'EN (nærmere betegnet kan ikke Microsoft-administreret dem), skal du bruge den indbyggede **HTTP** -handling til at ringe til dem. Hvis du vil have en ideel oplevelse, skal du vise et swagger slutpunkt for din API. Dette vil aktivere logik app designer til at gengive input og output til din API. Uden et swagger kan designer kun vise input og output som uigennemsigtig JSON-objekter.

Her er et eksempel, der viser den nye `metadata.apiDefinitionUrl` egenskab:
```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Hvis du hoster dine Web API på **App Service** derefter vises den automatisk på listen over handlinger, der er tilgængelige i designer. Hvis ikke, er du nødt til at indsætte URL-adressen direkte. Swagger slutpunkt skal være ikke til-godkendt for at kunne bruges inde i logik apps designer (selvom du kan sikre API selve med uanset metoder understøttes i Swagger).

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>Brug af din allerede installeret API-apps med 2015-08-01-eksempel

Hvis du tidligere har installeret en API-app, kan du kontakte den via en **HTTP** -handling.

Eksempelvis hvis du bruger Dropbox til liste over filer, har du måske nogenlunde sådan ud i dit **2014-12-01-preview** skemadefinitionen version:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Du kan oprette den tilsvarende HTTP handling som nedenfor (afsnittet parametre i de logik app definition forbliver uændret):

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Gennemgang af disse egenskaber én ad gangen:

| Egenskaben handling |  Beskrivelse |
| --------------- | -----------  |
| `type` | `Http`I stedet for`APIapp` |
| `metadata.apiDefinitionUrl` | Hvis du vil bruge denne handling i logik apps designer, får du vil medtage metadata slutpunkt. Dette er opbygget fra:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Dette er opbygget fra:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Altid`POST` |
| `inputs.body` | Identisk med parametrene api app | 
| `inputs.authentication` | Identisk med api app godkendelse |

Denne metode skal arbejde for alle API app handlinger. Skal du huske på, understøttes ikke længere disse forrige API-apps, og du bør flytte til en af de to andre indstillinger ovenfor (enten en administreret API, eller vært for din brugerdefinerede Web API).

## <a name="2-repeat-renamed-to-foreach"></a>2. omdøbt til Foreach Gentag

Til den forrige skemaversion vi har modtaget en masse kundefeedback pågældende **Gentag** er forvirrende og registrere ikke korrekt, er det virkelig en for hver løkke. Som et resultat, har vi omdøbte dem til **Foreach**. Eksempel:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Vil nu blive skrevet som:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

Tidligere funktionen `@repeatItem()` blev brugt til at referere til den aktuelle vare blive nævnt. Dette er blevet forenklet for at lige `@item()`. 

### <a name="referencing-the-outputs-of-the-foreach"></a>Henvisninger til output af Foreach
For at forenkle ombrudt output **Foreach** handlinger ikke i et objekt, kaldet **repeatItems**. Det betyder, mens output af ovenstående Gentag er:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Det vil nu være:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Når der henvises til disse output, for at få adgang til brødteksten i handlingen skulle du gøre:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Nu kan du gøre i stedet:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Med disse ændringer, funktionerne `@repeatItem()`, `@repeatBody()` og `@repeatOutputs()` fjernes.

## <a name="3-native-http-listener"></a>3. oprindelige HTTP lytteren 
HTTP-lytteren funktionerne er nu indbygget, så du ikke længere behøver at installere en HTTP lytteren API-app. Læs mere om [alle detaljer om, hvordan du foretager dine logik app slutpunkt kan kaldes her](app-service-logic-http-endpoint.md). 

Med disse ændringer, funktionen `@accessKeys()` fjernes og er blevet erstattet med den `@listCallbackURL()` funktionen til at udføre for at få slutpunktet (når det er nødvendigt). Desuden skal nu du angive mindst én udløser i din logik app nu. Hvis du vil `/run` arbejdsprocessen, skal du have en `manual`, `apiConnectionWebhook` eller `httpWebhook` udløsere. 

## <a name="4-calling-child-workflows"></a>4. opkald underordnede arbejdsprocesser

Tidligere påkrævet opkald underordnede arbejdsprocesser gå til arbejdsprocessen, få adgangstoken, og derefter indsætte, i at definitionen af den logik app, du vil ringe op, underordnede. Logik apps program genereres automatisk en SAS på kørselstidspunktet for den underordnede arbejdsproces, hvilket betyder, at du ikke behøver at indsætte en hvilken som helst hemmeligheder i definitionen med den nye skemaversion.  Her er et eksempel:

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

En anden forbedring er vi giver underordnede arbejdsprocesser fuld adgang til den indgående anmodning. Det betyder, at du kan overføre parametre i sektionen *forespørgsler* og i objektet *sidehoveder* og, som du kan definere hele brødteksten fuldt ud.

Til sidst, der er nødvendige ændringer i den underordnede arbejdsproces. Mens før du kan kun kalde en underordnet arbejdsproces direkte; Nu skal du angive et udløser slutpunkt i arbejdsprocessen for overordnet til at ringe til. Det betyder generelt, du vil tilføje en udløser af typen **Manuel** og derefter bruge, i den overordnede definition. Bemærk, at den `host` egenskaben specifikt har en `triggerName`, fordi du skal altid angive, hvilke udløser, du starter.

## <a name="other-changes"></a>Andre ændringer

### <a name="new-queries-property"></a>Ny egenskab i forespørgsler
Alle handlingstyper understøtter nu en ny input, kaldet **forespørgsler**. Det kan være en strukturerede objekt i stedet for du behøver at samle strengen manuelt.

### <a name="parse-function-renamed"></a>parse() funktionen omdøbt
Som vi vil snart tilføje flere indholdstyper på `parse()` funktionen er blevet omdøbt til `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Kommer snart: Enterprise Integration API'er
På dette tidspunkt, vi det endnu ikke har administrerede versioner af Enterprise Integration API'er tilgængelig (såsom AS2). Disse kommer snart dækket i [vejledning](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). I mellemtiden, kan du bruge din eksisterende udløst BizTalk APIs via HTTP-handling, som er beskrevet ovenfor i "Brug af din allerede installeret API apps."

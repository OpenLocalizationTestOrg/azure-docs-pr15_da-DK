<properties 
    pageTitle="Forfatternavn logik App definitioner | Microsoft Azure" 
    description="Lær, hvordan du kan skrive JSON definitionen af logik apps" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="author-logic-app-definitions"></a>Forfatter logik App definitioner
Dette emne beskrives, hvordan du bruger [Azure logik Apps](app-service-logic-what-are-logic-apps.md) definitioner, som er et simpelt, deklarative JSON-sprog. Hvis du ikke har gjort det endnu, Kontrollér først ud af, [hvordan du opretter en ny logik app](app-service-logic-create-a-logic-app.md) . Du kan også læse den [fulde referencemateriale for definition af sprog på MSDN](http://aka.ms/logicappsdocs).

## <a name="several-steps-that-repeat-over-a-list"></a>Flere trin, der gentages på en liste

Du kan udnytte [foreach type](app-service-logic-loops-and-scopes.md) for at gentage over en matrix med op til 10 k elementer og udføre en handling for hver.

## <a name="a-failure-handling-step-if-something-goes-wrong"></a>En manglende fejlhåndtering trin, hvis noget går galt

Du ofte vil kunne skrive et *afhjælpning trin* – nogle logik, der udføres, hvis **og kun, hvis**en eller flere af dine opkald mislykkedes. Vi henter data fra flere forskellige steder i dette eksempel, men hvis opkaldet mislykkes, jeg vil SENDE en meddelelse et sted, så jeg kan spore manglende senere:  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "postToErrorMessageQueue": {
            "type": "ApiConnection",
            "inputs": "...",
            "runAfter": {
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

Du kan gøre brug af den `runAfter` egenskab til at angive den `postToErrorMessageQueue` bør kun køre `readData` er **mislykket**.  Det kan være en liste over mulige værdier, så `runAfter` kan være `["Succeeded", "Failed"]`.

Til sidst, fordi du har nu håndteres fejlen, vi ikke længere markere afkrydsningsfeltet Kør som **mislykkedes**. Som du kan se her, er denne kørsel **lykkedes** selvom ét trin mislykkedes, fordi jeg har skrevet trinnet for at håndtere fejlen.

## <a name="two-or-more-steps-that-execute-in-parallel"></a>To (eller flere) trin, der udføres parallelt

Have udførelse af flere handlinger parallelt og den `runAfter` egenskaben skal svare på kørselstidspunktet. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "branch1": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

Som du kan se i eksemplet ovenfor, begge `branch1` og `branch2` er indstillet til at køre `readData`. Som et resultat kører begge af disse forgreninger parallelt:

![Parallel](./media/app-service-logic-author-definitions/parallel.png)

Du kan se tidsstemplet for begge forgreninger er identisk. 

## <a name="join-two-parallel-branches"></a>Deltage i to parallelle grene

Du kan deltage i to handlinger, der er angivet til at udføre parallelt ved at tilføje elementer til den `runAfter` egenskaben ligner ovenfor.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Parallel](./media/app-service-logic-author-definitions/join.png)

## <a name="mapping-items-in-a-list-to-some-different-configuration"></a>Tilknytte elementer på en liste til en anden konfiguration

Næste, Lad os sige, at vi vil få helt andet indhold afhængigt af en værdi på en egenskab. Vi kan oprette et kort over værdier til destinationer som en parameter:  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/en-us/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

I dette tilfælde vi først få en liste over artikler, og klik derefter på andet trin søger efter i et kort, baseret på den kategori, der blev defineret som en parameter, hvilken URL-adresse til at hente indhold fra. 

To elementer, der skal være opmærksom her: den [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) bruges funktionen til at kontrollere, om kategorien svarer til en af de kendte kategorier, der er defineret. Andet, når vi få kategorien, kan vi trække elementet på kortet ved hjælp af kantede parenteser: `parameters[...]`. 

## <a name="working-with-strings"></a>Arbejde med strenge

Der findes forskellige funktioner, der kan bruges til at manipulere streng. Lad os tage et eksempel, hvor vi har en streng, vi vil overføre til et system, men vi kan ikke sikker på, at tegnkodning skal håndteres korrekt. En af mulighederne er til base64 kodes denne streng. For at undgå slippe i en URL-adresse vi dog skal du skifte nogle få tegn. 

Vi vil også en understreng af i rækkefølgen navngive, fordi de første 5 tegn ikke bruges.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

Arbejde indefra og ud:

1. Få den [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) af den orderer navn, dette returnerer tilbage er det samlede antal tegn

2. Subtrahere 5 (da vi vil en kortere streng)

3. Overtage den [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) . Vi starter Index `5` og gå resten af strengen.

4. Konvertere denne understreng til en [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) streng

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)alle de `+` tegn med`-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)alle de `/` tegn med`_`

## <a name="working-with-date-times"></a>Arbejde med dato klokkeslæt

Dato klokkeslæt kan være praktiske, særligt, når du forsøger at hente data fra en datakilde, der ikke understøtter naturligt **Udløsere**.  Du kan også bruge dato klokkeslæt til at finde ud af, hvor længe forskellige trin tager. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "actions" {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
                },
                "runAfter": {}
            }
            "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
        }
    },
    "outputs": {}
}
```

I dette eksempel vi udtrække den `startTime` for det forrige trin. Derefter vi vil hente det aktuelle klokkeslæt og trække et sekund:[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (du kan bruge andre tidsenheder såsom `minutes` eller `hours`). Til sidst, kan vi sammenligne disse to værdier. Hvis først er mindre end andet, og klik derefter betyder mere end ét sekund er gået, siden rækkefølgen blev første placeret. 

Også note, kan vi bruge streng formatters til at formatere datoer: i forespørgselsstreng, jeg bruge [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) at få RFC1123. Alle kun formatering, der [er beskrevet på MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). 

## <a name="using-deployment-time-parameters-for-different-environments"></a>Ved hjælp af parametre for installation og klokkeslæt for forskellige miljøer

Det er fælles for har en installationslivscyklus, hvor du har et udviklingsmiljø, et midlertidigt miljø og derefter et produktionsmiljø. I alle disse kan du vil have den samme definition, men du kan bruge forskellige databaser, f.eks. På samme måde, kan du bruge den samme definition på tværs af mange forskellige regioner til høj tilgængelighed, men vil hver logik app forekomst at tale, område-database. 

Bemærk, at dette er forskelligt fra det tager forskellige parametre på *kørselstidspunktet*, at du skal bruge den `trigger()` fungerer som vist ovenfor. 

Du kan starte med en meget simplistic definition som denne:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

Derefter skal du i den faktiske `PUT` Anmod om logik app, du kan angive parameteren `uri`. Bemærk, så der ikke længere en standardværdi denne parameter er påkrævet i logik app data:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

I de enkelte miljøer kan du derefter angive en anden værdi for den `connection` parameter. 

Se [REST-API dokumentation](https://msdn.microsoft.com/library/azure/mt643787.aspx) for alle de indstillinger, du har til oprettelse og administration af logik apps. 

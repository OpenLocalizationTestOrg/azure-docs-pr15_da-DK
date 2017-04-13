<properties 
    pageTitle="Ny skemaversion 2016-06-01 | Microsoft Azure" 
    description="Lær, hvordan du kan skrive JSON definitionen for den seneste version af logik apps" 
    authors="jeffhollan" 
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
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="new-schema-version-2016-06-01"></a>Ny skemaversion 2016-06-01

Ny skemaet og API-versionen for logik apps har en række forbedringer, forbedre pålideligheden og øget for anvendelse af logik apps. Der er 3 vigtige forskelle:

1. Tilføjelse af områder, der er handlinger, der indeholder en samling af handlinger.
1. Betingelser og løkker er førsteklasses handlinger
1. Udførelse af ordning mere detaljeret via `runAfter` egenskaben (hvilket erstatter `dependsOn`)

Oplysninger om opgradering dine logik apps fra 2015-08-01-preview skemaet til skemaet 2016-06-01 [Se afsnittet opgradering nedenfor.](#upgrading-to-2016-06-01-schema)


## <a name="1-scopes"></a>1. områder

En af de største ændringer i dette skema er tilføjelse af områder og muligheden for at indlejre handlinger i hinanden.  Dette er nyttigt, når du grupperer et sæt handlinger sammen, eller når behøver at indlejre handlinger i hinanden (for eksempel en betingelse kan indeholde en anden betingelse).  Flere oplysninger om syntaksen for omfang kan være fundet [her](app-service-logic-loops-and-scopes.md), men en simpel omfang eksempel kan ses nedenfor:


```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2. betingelser og løkker ændringer

I de tidligere versioner af skemaet var betingelser og løkker parametre, der er knyttet til en enkelt handling.  Denne begrænsning er ophævet i skemaet og nu betingelser og løkker vises som en handling.  Kan finde flere oplysninger [i denne artikel](app-service-logic-loops-and-scopes.md), og et eksempel på en betingelse-handling er vist nedenfor:

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. RunAfter egenskab

Den nye `runAfter` egenskaben erstatter `dependsOn` til at tillade mere præcist i køre rækkefølge.  `dependsOn`var synonym med "Handlingen kørte og lykkedes,", men mange gange, du skal udføre en handling, hvis den forrige handling er gået igennem, mislykkedes eller ignoreret.  `runAfter`giver denne fleksibilitet.  Det er et objekt, der angiver alle handling navnene det kører efter, og du definerer en matrix med status', der kan accepteres til udløser fra.  For eksempel, hvis du vil køre efter trin, der blev oprettet A og B er vellykkede eller mislykkede, du vil oprette følgende `runAfter` egenskab:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>Opgradere til 2016-06-01-skema

Opgradere til det nye 2016-06-01-skema kun kræver et par trin.  Få at vide om ændringer fra skemaet kan findes [i denne artikel](app-service-logic-schema-2016-04-01.md).  Opgraderingsprocessen omfatter køre opgradering scriptet, gemme som en ny logik app og potentielt overskrive gamle logik app, hvis det er nødvendigt.

1. Åbn din aktuelle logik app.
1. Klik på knappen **Opdater skema** på værktøjslinjen
   
    ![][1]
   
    Opgraderede definitionen returneres.  Du kan kopiere og sætte det ind i en ressource definition, hvis det er nødvendigt, men vi **anbefaler, at** du bruge knappen **Gem som** til at sikre, at alle referencer er gyldige i appen opgraderede logik.
1. Klik på knappen **Gem som** i værktøjslinjen i bladet opgradering.
1. Udfyld navn og logik app status, og klik på **Opret** for at installere din opgradering logik app.
1. Kontrollér din opgraderede logik app fungerer som forventet.

    >[AZURE.NOTE] Hvis du bruger en manuel eller anmodning udløser, der har ændret tilbagekald URL-adressen i din nye logik app.  Brug den nye URL-adresse til at bekræfte det fungerer til slut, og du kan klone over din eksisterende logik app til at bevare tidligere URL-adresser.

1. *Valgfri* Brug knappen **Klon** på værktøjslinjen (støder op til ikonet **Opdater skema** på billedet ovenfor) til at overskrive din forrige logik app med den nye skemaversion.  Det er nødvendigt, kun, hvis du vil bevare den samme ressource-ID eller anmode om udløser URL-adressen for din logik app.

### <a name="upgrade-tool-notes"></a>Værktøj til opgradering af noter

#### <a name="condition-mapping"></a>Betingelse tilknytning

Værktøjet vil gøre en bedste indsats for at gruppere SAND og FALSK gren handlinger sammen i et område i den opgraderede definition.  Specifikt Forespørgselsdesigner anvendelse af `@equals(actions('a').status, 'Skipped')` skal vises som en `else` handling.  Men hvis værktøjet registrerer mønstre det genkender ikke oprettes der potentielt separat betingelser for både sandt og FALSK gren.  Handlinger kan være igen tilknyttede sende opgraderingen, hvis det er nødvendigt.

#### <a name="foreach-with-condition"></a>ForEach betingelse
  
Den forrige anvendelse af en foreach løkke med en betingelse per element kan replikeres i det nye skema med filterhandlingen.  Dette sker automatisk under opgraderingen.  Betingelsen bliver en filterhandling før foreach løkke (til at returnere kun en matrix med elementer, der opfylder betingelsen), og denne matrix, der overføres til handlingen foreach.  Du kan få vist et eksempel på denne [i denne artikel](app-service-logic-loops-and-scopes.md)

#### <a name="resource-tags"></a>Ressource-mærker

Ressource mærker fjernes under opgraderingen, og du skal angive dem igen for den opgraderede arbejdsproces.

## <a name="other-changes"></a>Andre ændringer

### <a name="manual-trigger-renamed-to-request-trigger"></a>Manuel udløser omdøbt til anmodning om udløser

Typen `manual` er forældet og omdøbt til `request` med typen af `http`.  Dette er mere ensartet med typen mønster udløseren bruges til at opbygge.

### <a name="new-filter-action"></a>Ny '' filterhandling

Hvis du arbejder med en stor matrix og har du brug for at filtrere ned til et mindre antal elementer, kan du bruge den nye 'filter' type.  Den vil accepterer en matrix og en betingelse og evaluere betingelsen for hvert element og returnerer en matrix med elementer, der opfylder betingelsen.

### <a name="foreach-and-until-action-restrictions"></a>ForEach og indtil handling begrænsninger

Foreach og indtil løkke er begrænset til en enkelt handling.

### <a name="trackedproperties-on-actions"></a>TrackedProperties på Handlinger

Handlinger kan nu have en ekstra egenskab (sidestillet til `runAfter` og `type`) kaldet `trackedProperties`.  Det er et objekt, der angiver bestemte handling input eller output medtages i Azure diagnosticering telemetri, der udsendes som en del af en arbejdsproces.  Eksempel:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Næste trin
- [Brug Arbejdsprocesdefinitionen logik app](app-service-logic-author-definitions.md)
- [Oprette en skabelon til distribution af logik app](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png

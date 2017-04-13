<properties
    pageTitle="Azure funktioner timer udløser | Microsoft Azure"
    description="Forstå, hvordan du bruger timer udløsere i Azure funktioner."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fungerer, funktioner, begivenhed behandling, dynamisk Beregn, ikke-serverbaseret arkitektur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-timer-trigger"></a>Azure funktioner timer udløser

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

I denne artikel beskrives det, hvordan du konfigurerer timer udløsere i Azure funktioner. Timer udløser opkald funktioner, der er baseret på en tidsplan, én gang eller tilbagevendende.  

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-timer-trigger"></a>Function.JSON for timer udløser

Filen *function.json* indeholder et udtryk i tidsplanen. For eksempel kører følgende tidsplan funktionen hvert minut:

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Timer udløser håndterer med flere forekomster skala fra automatisk: kun en enkelt forekomst af en bestemt timer funktion skal køre på tværs af alle forekomster.

## <a name="format-of-schedule-expression"></a>Formatet af tidsplan udtryk

Udtrykket tidsplan er [CRON udtryk](http://en.wikipedia.org/wiki/Cron#CRON_expression) , der indeholder 6 felter: `{second} {minute} {hour} {day} {month} {day of the week}`. 

Bemærk, at mange af de cron udtryk, du finder online udelader feltet {anden}, så hvis du kopierer fra en af dem, du har til at justere for feltet ekstra. 

Her er nogle andre tidsplan eksempler på udtryk:

Udløses én gang hver 5 minutter:

```json
"schedule": "0 */5 * * * *"
```

Udløse en gang øverst på hver time:

```json
"schedule": "0 0 * * * *",
```

Udløses én gang hver to timer:

```json
"schedule": "0 0 */2 * * *",
```

Udløses én gang hver time fra 9 AM til 5 PM:

```json
"schedule": "0 0 9-17 * * *",
```

Udløses på 9:30 AM hver dag:

```json
"schedule": "0 30 9 * * *",
```

Udløses på 9:30 AM hver ugedag:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="timer-trigger-c-code-example"></a>Eksempel på timer udløser C#-kode

Dette C#-kodeeksempel skriver en enkelt log, hver gang funktionen udløses.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 

<properties
   pageTitle="Brug af Azure funktioner med logik Apps | Microsoft Azure"
   description="Se, hvordan du bruger Azure funktioner med logik Apps"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="using-azure-functions-with-logic-apps"></a>Brug af Azure funktioner med logik Apps

Du kan køre brugerdefineret kodestykker af C# eller node.js ved hjælp af Azure funktioner fra i en logik app.  [Azure funktioner](../azure-functions/functions-overview.md) tilbyder server-ledig computing i Microsoft Azure. Dette er nyttigt for at udføre følgende opgaver:

* Avanceret formatering eller Beregn af felter i en logik App
* Udføre beregninger i en arbejdsproces
* Hvis du vil udvide funktionaliteten i logik Apps med funktioner, der understøttes i C# eller node.js

## <a name="create-a-function-for-logic-apps"></a>Oprette en funktion til logik Apps

Vi anbefaler, at du opretter en ny funktion i portalen Azure-funktioner ved hjælp af **Generisk Webhook - Node** eller **Generisk Webhook - C#** -skabeloner. Dette automatisk-udfylder en skabelon, der accepterer `application/json` fra en logik app.  Hvis du vælger fanen **integrere** i Azure funktioner har den **tilstand** , der er angivet til **Webhook** og **Generisk JSON** **Webhook type** .  Funktioner, der bruger disse skabeloner har opdaget automatisk og vises i logik Apps designer under **Azure funktioner i mit område.**

Webhook funktioner acceptere en anmodning og sende dem til metoden via en `data` variabel. Du kan få adgang til egenskaberne for dine data ved hjælp af prik notation som `data.foo`.  For eksempel ligner en simpel JavaScript-funktion, der konverterer en dato / klokkeslætsværdi til en datostreng i følgende eksempel:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>Ringe til Azure funktioner fra en logik app

Hvis du klikker på menuen **Handlinger** i designeren, kan du vælge **Azure funktioner i mit område**.  Dette viser beholdere i dit abonnement og gør det muligt at vælge den funktion, du vil ringe op.  

Når du har valgt funktionen, bliver du bedt om at angive et objekt, Indtast data. Dette er den meddelelse, der sender appen logik til funktionen, og det skal være et JSON-objekt. Eksempelvis hvis du vil overføre i **Senest ændret** datoen fra en Salesforce-udløser, kan funktionen data se sådan ud:

![Sidste modfied dato][1]

## <a name="trigger-logic-apps-from-a-function"></a>Udløser logik apps fra en funktion

Det er også muligt at udløse en logik-app fra en funktion.  For at gøre dette, skal du blot oprette en logik app med en manuel udløser. Du kan finde yderligere oplysninger finder [logik apps som kaldes slutpunkter](app-service-logic-http-endpoint.md).  Oprette en HTTP-POST til manuel udløser URL-adressen med de data, du vil sende til appen logik i dine funktion.

### <a name="create-a-function-from-the-designer"></a>Oprette en funktion fra designer

Du kan også oprette en node.js webhook funktion fra i designer. Først skal Vælg **Azure funktioner i mit område,** og vælg derefter en objektbeholder for funktionen.  Hvis du endnu ikke har en objektbeholder, skal du oprette en fra [Azure funktioner portal](https://functions.azure.com/signin). Vælg derefter **Opret ny**.  

Angiv det kontekstobjekt, du planlægger at sende til en funktion for at oprette en skabelon baseret på de data, du vil beregne. Det skal være et JSON-objekt. Hvis du sender i indholdet af filen fra en FTP-handling, ser kontekst data for eksempel sådan ud:

![Kontekst data][2]

>[AZURE.NOTE] Fordi dette objekt ikke blev konverteret som en streng, føjes indholdet direkte til JSON-data. Det vil imidlertid fejl ud, hvis det ikke er et JSON-token (det vil sige, en streng eller et JSON objekt/matrix). Hvis du vil konverteret den som en streng, du blot tilføje anførselstegn, som vist i den første illustration i denne artikel.

Designer derefter genererer en funktionen skabelon, du kan oprette i selve teksten. Variabler oprettes udfyldt baseret på den kontekst, du planlægger at videregive til funktionen.




<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

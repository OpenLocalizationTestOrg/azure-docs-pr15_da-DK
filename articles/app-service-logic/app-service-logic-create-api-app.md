<properties 
    pageTitle="Oprette en API til logik Apps" 
    description="Oprette en brugerdefineret API til brug med logik Apps" 
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
    ms.date="10/18/2016"
    ms.author="jehollan"/>
    
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>Oprette en brugerdefineret API til brug med logik Apps

Hvis du vil udvide logik Apps platformen, findes der mange måder, du kan ringe op til API'er eller systemer, der ikke er tilgængelige som en af vores mange out box forbindelser.  En af disse metoder til at oprette en API-App, kan du ringe fra i en arbejdsproces for logik App.

## <a name="helpful-tools"></a>Praktiske værktøjer

Vi anbefaler, at du opretter et [swagger](http://swagger.io) dokument, der beskriver de understøttede handlinger og parametre for din API for API'er til at fungere bedst med logik Apps.  Der findes mange biblioteker (som [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)), der genereres automatisk swagger for dig.  Du kan også bruge [TRex](https://github.com/nihaue/TRex) til at oprette anmærkninger i swagger til at fungere sammen med logik Apps (Vis navne, egenskabstyper osv.).  Nogle eksempler på API-Apps, der er bygget til logik Apps, skal du se vores [GitHub lager](http://github.com/logicappsio) eller din [blog](http://aka.ms/logicappsblog).

## <a name="actions"></a>Handlinger

Den grundlæggende handling til en logik App er en controller, der skal acceptere en HTTP-anmodning og returnere et svar (som regel 200).  Men der er forskellige mønstre, du kan følge for at udvide handlinger, der er baseret på dine behov.

Som standard logik App-program anvender timeout for en anmodning om efter 1 minut.  Du kan dog have din API udføre på Handlinger, der tager længere tid, og have programmet vente på, er afsluttet, ved at følge en asynkron eller webhook mønster, som beskrevet nedenfor.

Til almindelige handlinger, skal du blot skrive en HTTP-anmodning om metode i din API, der vises via swagger.  Du kan se eksempler på API-apps, der arbejder med logik Apps i vores [GitHub lager](https://github.com/logicappsio).  Nedenfor finder du måder til at udføre almindelige mønstre med en brugerdefineret forbindelse.

### <a name="long-running-actions---async-pattern"></a>Længerevarende handlinger - asynkron mønster

Når du kører en lang trin eller opgave, er det første, du skal gøre Sørg for, at programmet kender du ikke fik timeout. Du skal også kommunikere med programmet, hvordan det der, når du er færdig med opgaven, og til sidst skal du har brug at vende tilbage relevante data til programmet, så den kan fortsætte med at arbejdsprocessen. Du kan udføre, via et API ved at følge nedenstående strømmen. Disse trin er fra punkt-for-visningen af brugerdefinerede API:

1. Når der modtages en anmodning, straks tilbage et svar, (før der arbejdes). Dette svar er en `202 ACCEPTED` svar, så programmet ved, du har fået dataene, har accepteret data, og nu behandler. 202 svaret skal indeholde følgende overskrifterne: 
 * `location`sidehoved (påkrævet): Dette er en absolut sti til de URL-adressen logik Apps kan bruge til at kontrollere status for jobbet.
 * `retry-after`(valgfrit, vil standard til 20 for handlinger). Dette er antallet sekunder, der skal vente på program med forespørgsel blandt placering sidehoved URL-adressen for at kontrollere status for.

2. Når en jobstatus er markeret, skal du udføre følgende Kontroller: 
 * Hvis sagen er færdig: returnere en `200 OK` svar med svar data.
 * Hvis sagen stadig behandler: returnere et andet `202 ACCEPTED` svar med de samme sidehoveder som indledende svaret

Dette mønster giver dig mulighed at køre meget lang opgaver i en tråd af din brugerdefinerede API, men beholde en aktiv forbindelse Live med logik Apps-program, så det ikke timeout eller fortsætte før arbejde er fuldført. Når du tilføjer dette i din logik App, er det vigtigt at bemærke du ikke behøver foretage dig noget i din definition for logik App kan fortsætte med at afstemning og kontrollere status. Når programmet registrerer et 202 ACCEPTEREDE svar med en gyldig placering sidehoved, den imødekomme asynkron mønster og fortsætte med at afstemning placering overskriften, indtil der returneres en ikke-202.

Du kan se et eksempel på dette mønster i GitHub [her](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### <a name="webhook-actions"></a>Webhook handlinger

Under arbejdsprocessen, kan du få Appen logik standse og vente på et "tilbagekald" at fortsætte.  Denne tilbagekald leveres i form af en HTTP POST.  For at implementere denne mønster, skal du angive to slutpunkter på din controller: Abonner og opsige abonnementet.

På 'abonnerer', Appen logik oprettes og registrere en tilbagekald URL-adresse, som kan gemme din API og tilbagekald med klar som en HTTP POST.  En hvilken som helst indhold/sidehoveder vil blive overført til Appen logik og kan bruges i resten af arbejdsprocessen.  Logik App program ringer punktet Abonner på udførelse af som det rammer dette trin.

Hvis afkrydsningsfeltet Kør blev annulleret, vil logik App program foretage et opkald til slutpunktet 'opsige abonnementet'.  Din API kan derefter unregister tilbagekald URL-adressen, som det er nødvendigt.

Logik App Designer understøtter ikke opdager et webhook slutpunkt gennem swagger, så hvis du vil bruge denne type handling skal du tilføje handlingen "Webhook" og Angiv URL-adresse, overskrifter og brødtekst for din anmodning.  Du kan bruge den `@listCallbackUrl()` arbejdsproces funktionen i nogen af disse felter efter behov for at overføre tilbagekald URL-adressen.

Du kan se et eksempel på et webhook mønster i GitHub [her](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="triggers"></a>Udløsere

Ud over handlinger har du din brugerdefinerede API act som en udløser til en logik App.  Der er to mønstre, du kan følge nedenfor til at udløse en logik App:

### <a name="polling-triggers"></a>Afstemning udløsere

Afstemning udløsere fungerer ligesom de ovenstående lang kører asynkrone handlinger.  Logik App-program kalder udløser slutpunktet efter et bestemt tidsrum, der er forløbet (afhænger af SKU, 15 sekunder til Premium, 1 minut til Standard, og 1 time for ledig).

Hvis der er ingen data tilgængelige, returnerer udløseren en `202 ACCEPTED` svar, med en `location` og `retry-after` sidehoved.  Dog udløsere det anbefales for den `location` sidehoved indeholder en forespørgselsparameter af `triggerState`.  Dette er nogle id for din API at vide, når sidste gang Appen logik udløses.  Hvis der er data, returnerer udløseren en `200 OK` svar med det indhold af data.  Derved startes logik App.

For eksempel, hvis jeg forespørgsel blandt for at se, om en fil er tilgængelig, kan du oprette en afstemning udløser, skal du gøre følgende:

* Hvis en anmodning om blev modtaget med ingen triggerState API vil returnere en `202 ACCEPTED` med en `location` sidehoved, der indeholder en triggerState af det aktuelle klokkeslæt og en `retry-after` på 15.
* Hvis blev modtaget en anmodning om med en triggerState:
 * Kontrollér, hvis nogen filer er tilføjet efter triggerState DateTime. 
  * Hvis der er 1-fil, en `200 OK` svar med indhold dataene, forøge triggerState til dato/klokkeslæt i filen jeg returneret, og Angiv den `retry-after` til 15.
  * Hvis der er flere filer, jeg kan returnere 1 ad gangen med en `200 OK`, stiger min triggerState i den `location` sidehoved, og sæt `retry-after` til 0.  Dette får programmet, der kender der er flere data, og det med det samme anmoder om den på den `location` sidehoved, der er angivet.
  * Hvis der er ingen filer, returnerer en `202 ACCEPTED` svar, og lad den `location` triggerState på samme måde.  Angive `retry-after` til 15.

Du kan se et eksempel på en afstemning udløser i GitHub [her](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### <a name="webhook-triggers"></a>Webhook udløsere

Webhook udløsere fungerer ligesom Webhook handlinger ovenfor.  Logik App program ringer slutpunktet 'abonnere', når en webhook udløser er tilføjet og gemt.  Din API kan registrere webhook URL-adressen og kald den via HTTP POST, når dataene er tilgængelige.  Indhold af data og sidehoveder vil blive overført til Appen logik køre.

Hvis en webhook udløser slettes nogensinde (Appen logik helt eller blot webhook udløser), programmet, foretage et opkald til 'opsige abonnementet' URL-adressen hvor din API kan unregister tilbagekald URL-adressen og stopper alle processer, som det er nødvendigt.

Logik App Designer understøtter ikke opdager en webhook udløser gennem swagger, så hvis du vil bruge denne type handling skal du tilføje "Webhook" udløser og Angiv URL-adresse, overskrifter og brødtekst for din anmodning.  Du kan bruge den `@listCallbackUrl()` arbejdsproces funktionen i nogen af disse felter efter behov for at overføre tilbagekald URL-adressen.

Du kan se et eksempel på en webhook udløser i GitHub [her](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)
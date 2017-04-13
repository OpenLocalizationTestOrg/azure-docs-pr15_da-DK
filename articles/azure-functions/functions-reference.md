<properties
    pageTitle="Azure funktioner Udviklerreference | Microsoft Azure"
    description="Forstå Azure funktioner begreber og -komponenter, der er fælles for alle sprog og bindinger."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure-funktioner, funktioner, behandling af hændelse, webhooks, dynamisk Beregn, ikke-serverbaseret arkitektur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-developer-reference"></a>Azure funktioner Udviklerreference

Azure funktioner dele nogle grundlæggende tekniske begreber og -komponenter, uanset sproget eller indbinding, du bruger. Før du hoppe ind i læ specifikt for et bestemt sprog eller binding detaljer, skal du læse denne oversigt, der gælder for dem alle.

Denne artikel forudsætter, at du allerede har læst [Azure funktioner oversigt](functions-overview.md) og kender [WebJobs SDK begreber som udløsere, bindinger, og JobHost runtime](../app-service-web/websites-dotnet-webjobs-sdk.md). Azure-funktioner er baseret på WebJobs SDK. 


## <a name="function-code"></a>Funktionen kode

*Funktionen* er primære konceptet i Azure funktioner. Du skrive programkode for en funktion i et sprog efter eget valg og gemme filen/filerne kode og en konfigurationsfil i samme mappe. Konfiguration er i JSON, og filen hedder `function.json`. En række forskellige sprog understøttes, og hver har en lidt anderledes oplevelse, der er optimeret til at fungere bedst for det pågældende sprog. 

Den `function.json` filen indeholder konfiguration, der er specifikke for en funktion, herunder dets bindinger. Kørsel af læser filen for at finde ud af, hvilke hændelser, der skal udløses af, hvilke data der skal medtages, når du ringer til funktionen, og hvor du vil sende data overføres langs fra selve funktionen. 

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Du kan forhindre kørsel af funktionen køres ved at angive den `disabled` egenskab, der skal `true`.

Den `bindings` egenskaben er, hvor du kan konfigurere både udløsere og bindinger. Hver binding deler et par almindelige indstillinger og nogle indstillinger, som er specifikke for en bestemt type binding. Alle bindinger kræver følgende indstillinger:

|Egenskaben|Værdier/typer|Kommentarer|
|---|-----|------|
|`type`|streng|Bindende type. For eksempel `queueTrigger`.
|`direction`|'i', 'ud'| Angiver, om bindingen er for modtagelse af data i funktionen eller sende data fra funktionen.
| `name` | streng | Det navn, der skal bruges til bundne dataene i funktionen. For C# vil dette være et argumentnavn Det vil være nøglen i en liste med-tasten/værdier for JavaScript.

## <a name="function-app"></a>Funktionen app

En funktionen app består af en eller flere bestemte funktioner, der administreres sammen med Azure App-tjenesten. Alle funktionerne i en funktion app deler de samme priser plan, fortløbende installation og runtime-version. Funktioner, der er skrevet på flere sprog kan alle dele den samme funktion app. Tænk på en funktionen app som en metode til at organisere og administrere dine funktioner. 

## <a name="runtime-script-host-and-web-host"></a>Runtime (script host og web host)

Runtime eller script host, er den underliggende WebJobs SDK-vært, der lytter efter begivenheder, indsamler og sender data og i sidste ende kører din kode. 

For at lette HTTP udløsere, er der også en webvært, som er udviklet til at sidde foran script host i fremstilling scenarier. Dette hjælper med at isolere script host fra front-end trafikken administreres af webværten.

## <a name="folder-structure"></a>Mappestrukturen

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Når opsætning af et projekt til implementering af funktioner til en funktionen app i Azure App-tjeneste, kan du behandle denne mappestrukturen som dit websted kode. Du kan bruge eksisterende funktioner som sammenhængende integration og installation eller brugerdefineret installation scripts til gøre udføre tid pakkeinstallation eller kode transpilation.

>[AZURE.NOTE] Den `wwwroot` mappen her er, hvor dine filer skal installeres til. Du skal dog ikke medtage denne mappe i de filer, du installerer, hvilket resulterer med `wwwroot\wwwroot`. I stedet din `host.json` funktionen filer og mapper skal være direkte i roden af du installerer.

## <a id="fileupdate"></a>Sådan opdaterer du funktionen app-filer

Funktionen editoren indbygget i portalen Azure kan du opdatere filen *function.json* og filen kode for en funktion. For at overføre eller opdatere andre filer som *package.json* eller *project.json* eller afhængigheder, skal du bruge andre installationsmetoder.

Funktionen apps er udviklet på App-tjenesten, så alle [installationsindstillinger, der er tilgængelige til standard webapps](../app-service-web/web-sites-deploy.md) er tilgængelige for funktionen-apps. Her er nogle metoder, du kan bruge til at overføre eller opdatere funktionen app-filer. 

#### <a name="to-use-app-service-editor"></a>Bruge App Service Editor

1. Klik på **Indstillinger for funktionen**i portalen Azure funktioner.

2. Klik på **Gå til App Tjenesteindstillinger**i sektionen **Avancerede indstillinger** .

3. Klik på **App Service Editor** Appmenuen i navigationen under **værktøjer til udvikling af**.

4.  Klik på **Gå**.

    Når App Service Editor indlæser, ser du *host.json* fil og funktionen mapperne under *wwwroot*. 

5. Åbne filer til at redigere dem, eller træk og slip fra computeren udvikling til at overføre filer.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Bruge appen funktionen SCM (Kudu) slutpunkt

1. Gå til: `https://<function_app_name>.scm.azurewebsites.net`.

2. Klik på **fejlfinding Console > CMD**.

3. Gå til `D:\home\site\wwwroot\` opdatere *host.json* eller `D:\home\site\wwwroot\<function_name>` at opdatere en funktion filer.

4. Træk og slip en fil, du vil overføre til den relevante mappe i gitteret fil. Der findes to områder i gitteret fil, hvor du kan placere en fil. Til *.zip-* filer vises der et felt med navnet "Træk her kan du overføre og udpakke." Slip i gitteret filer, men uden for feltet "Udpak filen" for andre filtyper.

#### <a name="to-use-ftp"></a>Brug af FTP

1. Følg vejledningen [her](../app-service-web/web-sites-deploy.md#ftp) at få konfigureret FTP.

2. Når du har forbindelse til funktionen app-websted, kan du kopiere en opdateret *host.json* fil til `/site/wwwroot` eller kopiere funktionen filer til `/site/wwwroot/<function_name>`.

#### <a name="to-use-continuous-deployment"></a>Bruge fortløbende installation

Følg vejledningen i emnet [fortløbende installation til Azure-funktioner](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Parallel udførelse

Når flere udløsende hændelse indtræffer hurtigere, end en enkelt-threaded funktionen runtime kan behandle dem, kan runtime kalde funktionen flere gange parallelt.  Hvis en funktion app bruger [Dynamiske Service planlægge](functions-scale.md#dynamic-service-plan), kan funktionen app skalere ud automatisk.  Hver forekomst af app funktionen kan om app'en kører på kørslen dynamiske Service Plan eller et almindelige [App Service Plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), behandle samtidige funktionen starter parallelt ved at bruge flere tråde.  Det maksimale antal samtidige funktionen starter i hver funktion app forekomst varierer afhængigt af størrelsen af funktionen app hukommelse. 

## <a name="azure-functions-pulse"></a>Azure funktioner Pulse  

Pulse er en direkte begivenhed stream som viser, hvor ofte funktionen kører, samt succeser og fejl. Du kan også overvåge dine gennemsnitlige kørselstid. Vi tilføjer hele tiden flere funktioner og tilpasning til den over tid. Du kan få adgang til siden **Pulse** fra fanen **overvågnings** .

## <a name="repositories"></a>Lagre

Koden for Azure funktioner er Åbn kilde og gemmes i GitHub typer lagre:

* [Azure funktioner runtime](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure funktioner portal](https://github.com/projectkudu/AzureFunctionsPortal)
* [Azure funktioner skabeloner](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK filtypenavne](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bindinger

Her er en liste over alle understøttede bindinger.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>Rapportere problemer

[AZURE.INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)] 

## <a name="next-steps"></a>Næste trin

Yderligere oplysninger finder du se følgende ressourcer:

* [Azure funktioner C# Udviklerreference](functions-reference-csharp.md)
* [Azure funktioner F # Udviklerreference](functions-reference-fsharp.md)
* [Azure funktioner NodeJS Udviklerreference](functions-reference-node.md)
* [Azure funktioner udløsere og bindinger](functions-triggers-bindings.md)
* [Azure funktioner: feltet rejse](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) på Azure App Service-teamets blog. En oversigt over hvordan Azure funktioner blev udviklet.






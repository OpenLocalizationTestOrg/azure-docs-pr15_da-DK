<properties 
    pageTitle="Streaming logfiler og console" 
    description="Oversigt over console og streaming logfiler" 
    authors="btardif" 
    manager="wpickett" 
    editor="" 
    services="app-service\web" 
    documentationCenter=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="byvinyal"/>

# <a name="streaming-logs-and-the-console"></a>Streaming logfiler og konsollen

## <a name="streaming-logs"></a>Streaming logfiler

**Azure-portalen** indeholder en integreret streaming log Fremviser, hvor du kan se sporing begivenheder fra **App Service** programmerne i realtid.  

Konfiguration af denne funktion kræver et par enkle trin:

- Skrive sporinger i din kode
- Aktivere programmet **diagnosticeringslogfiler** for din app
- Få vist strømmen fra indbyggede **Streaming logfiler** Brugergrænsefladen på **Azure-portalen**.

### <a name="how-to-write-traces-in-your-code"></a>Sådan skrives sporinger i din kode ###

Det er nemt at skrive sporinger i din kode.  Det er lige så nemt som at skrive følgende kode i C#:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

Klassen sporing bor i navneområdet System.Diagnostics.

Du kan skrive denne kode for at opnå det samme resultat i en node.js app:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Sådan aktiveres og få vist den streaming logfiler
![][BrowseSitesScreenshot]Diagnosticering er aktiveret på grundlag af hver app. Start med at gennemse til det websted, du vil aktivere denne funktion på.  
  
![][DiagnosticsLogs]Rul ned til sektionen **overvågning** , og klik på **(1) diagnosticeringslogfiler**fra menuen Indstillinger. Derefter **(2) Aktivér** **Program logføring (filsystem)** eller **Program logføring (blob)** indstillingen **niveau** kan du ændre niveauet alvorlighed af sporinger til at registrere. Hvis du prøver kun at få kendskab til funktionen, kan du indstille niveauet til **detaljeret** for at sikre, at alle spor sætninger samles.

Klik på **Gem** øverst på bladet, og du er klar til at få vist logfiler.

>[AZURE.NOTE] Jo højere er **prioritet** flere ressourcer er consumed log og flere sporinger oprettet. Sørg for, at **prioritering** er konfigureret til at rette oplysningerne for et fremstilling eller stor trafik websted. 

![][StreamingLogsScreenshot]Klik på **(1) Log Stream** også i sektionen **overvågning** i menuen Indstillinger for at få vist den **streaming logfiler** fra i portalen Azure. Hvis din app er aktivt skriver sporing sætninger, skal du se dem i den **(2) streaming logger Brugergrænsefladen** i nær realtid.

## <a name="console"></a>Console
**Azure-portalen** indeholder console adgang til din app. Du kan udforske din app-filsystemet og køre scripts, som powershell/cmd. Du er underlagt de samme tilladelser, der er angivet som din app programkode under udførelse af konsol kommandoer. Adgang til beskyttet mapper eller køre scripts, der kræver administratorrettigheder er blokeret.  

![][ConsoleScreenshot]Fra menuen Indstillinger åbnes Rul ned til sektionen **Værktøjer til udvikling af** og klikker på **(1)-konsol** og **(2) console** Brugergrænsefladen til højre.

For at få kendskab til **console**, kan du prøve grundlæggende kommandoer som:

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

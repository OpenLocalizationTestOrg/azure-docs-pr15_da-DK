<properties
    pageTitle="Bedste praksis og fejlfinding i forbindelse med vejledning til node programmer på Azure Web Apps"
    description="Lære om bedste praksis og fejlfindingstrin for node programmer på Azure Web Apps."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="ranjithr"
    manager="wadeh"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="06/06/2016"
    ms.author="ranjithr;wadeh"/>
    
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Bedste praksis og fejlfinding i forbindelse med vejledning til node programmer på Azure Web Apps

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

I denne artikel lærer du den bedste praksis og fejlfindingstrin til [node programmer](app-service-web-nodejs-get-started.md) , der kører på Azure WebApp (med [iisnode](https://github.com/azure/iisnode)).

>[AZURE.WARNING] Vær forsigtig, når du bruger fejlfindingstrin på webstedet fremstilling. Anbefales, at foretage fejlfinding af din app på en ikke-produktiv opsætning for eksempel din midlertidige slot og når problemet er løst, Ombyt din midlertidige slot med din fremstilling slot.

## <a name="iisnode-configuration"></a>IISNODE konfiguration

Denne [skemafil](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) viser alle de indstillinger, der kan konfigureres til iisnode. Nogle af de indstillinger, der kan være nyttig for dit program er:

* nodeProcessCountPerApplication

    Denne indstilling styrer antallet af node processer, der er startet i IIS-program. Standardværdien er 1. Du kan starte så mange node.exe som din VM core antal ved at værdien indstilles til 0. Anbefalede værdi er 0 for de fleste program, så du kan udnytte alle borekerner på din computer. Node.exe er enkelt trådinddelte så én node.exe vil forbruge maksimalt 1 kerne og for at få optimal ydeevne ud af dit node-program, du ønsker at udnytte alle borekernerne.

* nodeProcessCommandLine

    Denne indstilling styrer stien til node.exe. Du kan angive denne værdi til at pege på din node.exe version.

* maxConcurrentRequestsPerProcess

    Denne indstilling styrer det maksimale antal samtidige anmodninger sendes af iisnode til hver node.exe. På azure WebApp er standardværdien for dette uendeligt. Du skal ikke bekymre dig om denne indstilling. Standardværdien er uden for azure WebApp 1024. Du vil muligvis konfigurere dette afhængigt af hvor mange anmoder om dit program bliver og hvor hurtigt dit program behandler hver anmodning.

* maxNamedPipeConnectionRetry

    Denne indstilling styrer den maksimalt tilladte antal gange iisnode vil forsøge at foretage forbindelse ved brug af den navngivne pipe til at sende anmodningen til node.exe. Denne indstilling sammen med namedPipeConnectionRetryDelay bestemmer den samlede timeout for hver enkelt anmodning i iisnode. Standardværdien er 200 på Azure WebApp. Samlet Timeout i sekunder = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

* namedPipeConnectionRetryDelay

    Denne indstilling styrer mængden tid (i ms) iisnode venter på mellem hvert forsøg igen for at sende anmodning til node.exe over den navngivne pipe. Standardværdien er 250ms.
    Samlet Timeout i sekunder = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

    Den samlede timeout i iisnode på azure WebApp er som standard 200 \* 250ms = 50 sekunder.

* logDirectory

    Denne indstilling styrer den mappe, hvor iisnode logfører stdout/stderr. Standardværdien er iisnode, som er i forhold til den primære script mappe (bibliotek, hvor primære server.js er til stede)

* debuggerExtensionDll

    Denne indstilling styrer, hvilken version af node inspektion iisnode anvender, når du foretager fejlfinding programmets node. Iisnode-inspektion-0.7.3.dll og iisnode inspector.dll er i øjeblikket kun 2 gyldige værdier for denne indstilling. Standardværdien er iisnode-inspektion-0.7.3.dll. iisnode-inspektion-0.7.3.dll version bruger node-inspektion-0.7.3 og bruger websockets, så du skal aktivere websockets på din azure webapp til at bruge denne version. Se <http://www.ranjithr.com/?p=98> få mere at vide om, hvordan du konfigurerer iisnode for at bruge den nye node-inspektion.

* flushResponse

    Standardfunktionsmåden for IIS er, at den placerer svardata op til 4MB før træk, eller indtil slutningen af svaret, afhængigt af hvad der kommer først. iisnode indeholder en indstilling tilsidesætter denne funktionsmåde: Hvis du vil rydde et fragment af svar enhedsbrødtekst, så snart iisnode modtager den fra node.exe, skal du oprette den iisnode/@flushResponse attribut i web.config til 'true':
    
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```

    Aktivering træk af hver fragment af svar enhedsbrødtekst tilføjer ydeevne omkostninger, der reducerer gennemløb af systemet med ~ 5% (og v0.1.13), så det er bedst at begrænse denne indstilling for kun at slutpunkter, der kræver svar streaming (fx ved hjælp af den <location> element i web.config)

    Ud over dette skal skal streaming programmer, du også angive responseBufferLimit af din iisnode handler til 0.
    
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```

* watchedFiles

    Dette er en adskilt med semikolon liste over filer, der skal kontrolleres for ændringer. En ændring af en fil får programmet til Papirkurv. Hvert element består af en valgfri mappenavn plus påkrævet filnavn, som er i forhold til den mappe, hvor indgangspunkt hovedvinduet i programmet er placeret. Jokertegn er tilladt i fil navn del kun. Standardværdien er "\*. js;web.config"

* recycleSignalEnabled

    Standardværdien er falsk. Hvis det er aktiveret, node programmet kan oprette forbindelse til en navngivet pipe (miljøvariablen IISNODE\_KONTROLELEMENT\_PIPE), og send meddelelsen "Papirkurv". Dette medfører w3wp til Papirkurv problemfrit.

* idlePageOutTimePeriod

    Standardværdien er 0, hvilket betyder, at denne funktion er deaktiveret. Da angivet med en anden værdi større end 0, vil iisnode side af alle de underordnede processer hver 'idlePageOutTimePeriod' millisekunder. Se denne [dokumentation](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx)for at forstå, hvilken side af betyder. Denne indstilling kan være nyttige til programmer, der forbruger en masse hukommelse og vil pageout hukommelse på disk og til at frigøre nogle RAM.

>[AZURE.WARNING] Vær forsigtig, når du aktiverer følgende konfigurationsindstillinger på fremstilling programmer. Anbefales, at du ikke aktiverer dem på direkte fremstilling programmer.

* debugHeaderEnabled

    Standardværdien er falsk. Hvis indstillet til sand, iisnode vil tilføje en HTTP-svar sidehoved iisnode-fejlfinding til hver HTTP-svar sendes iisnode fejlfinding sidehoved værdien er en URL-adresse. Individuelle elementer af diagnostiske oplysninger kan opfanges ved at kigge på den URL-adresse-fragment, men en meget bedre visualisering opnås ved at åbne URL-adressen i browseren.

* loggingEnabled

    Denne indstilling styrer logføring af stdout og stderr ved iisnode. Iisnode Optag stdout/stderr fra node processer det starter, og skrive til den mappe, der er angivet i indstillingen 'logDirectory'. Når dette er aktiver, dit program vil skrive logfiler til filsystemet og afhængigt af mængden logføring, der er udført af programmet, der kan være indflydelse på ydeevnen.

* devErrorsEnabled

    Standardværdien er falsk. Når der er angivet til sand, iisnode vises HTTP-statuskode og Win32-fejlkode i din browser. Win32-koden vil være nyttig i forbindelse med fejlfinding visse typer problemer.
    
* debuggingEnabled (ikke aktiverer på direkte fremstilling websted)

    Denne indstilling styrer fejlfinding funktion. Iisnode er integreret med node inspektion. Ved at aktivere denne indstilling kan aktivere du fejlfinding programmets node. Når denne indstilling er aktiveret, vil iisnode layout, det er nødvendigt node-inspektion filerne i 'debuggerVirtualDir' mappe på den første fejlfinding anmodning node-program. Du kan indlæse node-inspektion ved at sende en anmodning til http://yoursite/server.js/debug. Du kan styre segmentet fejlfinding URL-adresse med 'debuggerPathSegment' indstilling. Efter standard debuggerPathSegment = 'fejlfinding'. Du kan angive dette til et GUID for eksempel, så det er svært at registreres af andre.

    Se dette [link](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) for flere oplysninger om fejlfinding.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scenarier og anbefalinger/fejlfinding

### <a name="my-node-application-is-making-too-many-outbound-calls"></a>Mit program node foretager for mange udgående opkald.

Mange programmer ønsker at foretage udgående forbindelser som en del af deres almindelige handling. Når en anmodning,, vil din node app for eksempel kontakte en REST-API et andet sted og få nogle oplysninger om at behandle anmodningen. Du skulle bruge en Behold Live agent, når du foretager http eller https opkald. Du kan for eksempel bruge modulet agentkeepalive som deres Behold Live agent, når disse udgående opkald. Dette sikrer, at sockets genbruges på din azure webapp VM og reducere omkostninger for at oprette nye sockets for hver udgående anmodning. Også sikrer det, at du bruger mindre antal sockets til at foretage mange udgående anmodninger og derfor du ikke overskrider den maxSockets, der er allokeret VM. Anbefaling på Azure WebApp ville være at indstille værdien agentKeepAlive maxSockets til en total af 160 sockets per VM. Det betyder, at hvis du har 4 node.exe kører på VM, du vil vil angive agentKeepAlive maxSockets til 40 per node.exe som er 160 samlede per VM.

Eksempel på konfiguration af agentKeepALive:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

I dette eksemplet antages, du har 4 node.exe kører på din VM. Hvis du har et forskelligt antal node.exe kører på VM, har du ændre maxSockets konfiguration i overensstemmelse hermed.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>Mit node program er forbrug for meget CPU.

Du får sandsynligvis en anbefaling fra Azure WebApp på din portal om høj cpu-forbrug. Du kan også konfigurere skærme skal være opmærksom på visse [målepunkter](web-sites-monitor.md). Når du kontrollerer CPU-brug på [Azure Portal Dashboard](../application-insights/app-insights-web-monitor-performance.md), skal du se Maks værdierne for CPU'EN så du ikke går glip af top-værdier.
I tilfælde, hvor du tror dit program er forbrug for meget CPU, og du kan ikke beskriver, hvorfor skal du profil programmets node.

### 

#### <a name="profiling-your-node-application-on-azure-webapps-with-v8-profiler"></a>Oprettelse af profiler programmets node på azure WebApp med V8-Profiler

For eksempel kan sig du har en Hej verden app, du vil profil, som vist nedenfor:

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Gå til din scm websted https://yoursite.scm.azurewebsites.net/DebugConsole

Du får vist en kommandoprompt, som vist nedenfor. Gå til dit websted/wwwroot mappe

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Kør kommandoen "npm installere v8-profiler"

Dette skal installere v8-profiler under node\_moduler mappe og alle dens afhængigheder.
Rediger nu, din server.js for at profil dit program.

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

De ovenstående ændringer skal profil funktionen WriteConsoleLog og derefter skrive profil output til 'profile.cpuprofile' fil under wwwroot dit websted. Sende en anmodning til dit program. Du får vist en 'profile.cpuprofile'-fil, der er oprettet under wwwroot dit websted.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Hent denne fil, og du behøver at åbne denne fil med Chrome F12 værktøjer. Tryk på F12 på chrome og derefter klikke på "Fanen profiler". Klik på "Indlæs". Vælg din profile.cpuprofile-fil, du netop har hentet. Klik på den profil, du lige har indlæst.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Du får vist, 95% af tiden blev brugt af WriteConsoleLog funktion, som vist nedenfor. Dette viser også de nøjagtige linjenumre og kildefiler, der kan medføre problemet.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Mit node program er forbrug for meget hukommelse.

Du får sandsynligvis en anbefaling fra Azure WebApp på din portal om høj hukommelsesforbrug. Du kan også konfigurere skærme skal være opmærksom på visse [målepunkter](web-sites-monitor.md). Når du kontrollerer hukommelsesforbrug på [Azure Portal Dashboard](../application-insights/app-insights-web-monitor-performance.md), skal du se Maks værdier for hukommelse, så du ikke går glip af top-værdier.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>Registrering af fejl og Heap Diffing for node.js 

Du kan bruge [node memwatch](https://github.com/lloyd/node-memwatch) kan hjælpe dig med at identificere hukommelsesfejl.
Du kan installere memwatch ligesom v8-profiler og redigere din kode til hentning og diff heaps til at identificere hukommelsen mister i programmet.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Min node.exe er få afbrudt tilfældigt 

Der er et par mulige årsager hvorfor dette kan ske noget:

1.  Dit program er aktiverende en ikke-opfanget undtagelser – skal du markere d:\\privat\\LogFiles\\programmet\\logføring errors.txt fil til oplysningerne på den udløste undtagelse. Denne fil har staksporing, så du kan løse dit program, der er baseret på dette.

2.  Dit program er forbrug for meget hukommelse, som påvirker andre processer fra Introduktion. Hvis den samlede VM hukommelse er tæt på 100%, kan din node.exe stoppes ved hjælp af processen manager til at give andre mulighed for at gøre nogle arbejde processer. Sørg for, at dit program ikke mangler hukommelse, eller hvis du programmet virkelig skal bruge en masse hukommelse, skal du skalere op til en større VM med meget mere RAM for at løse dette problem.

### <a name="my-node-application-does-not-start"></a>Mit node program starter ikke

Hvis dit program er returnere 500 fejl ved start, kan der være et par mulige årsager:

1.  Node.exe findes ikke på den korrekte placering. Kontrollere nodeProcessCommandLine indstilling.

2.  Primære scriptfil findes ikke på den korrekte placering. Markér web.config, og Sørg for, at navnet på den primære scriptfil i sektionen programmer svarer til den primære script-fil.

3.  Web.config konfiguration er ikke korrekt – se indstillinger navne/værdier.

4.  Kolde Start – dit program tager for lang tid at start. Hvis dit program tager længere tid end (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 sekunder iisnode vil returnere 500 fejl. Øge værdierne i disse indstillinger til at matche dit program starttidspunkt for at forhindre iisnode fra timeout, og returnerer fejlen 500.

### <a name="my-node-application-crashed"></a>Mit node-program, der er gået ned

Dit program er aktiverende en ikke-opfanget undtagelser – skal du markere d:\\privat\\LogFiles\\programmet\\logføring errors.txt fil til oplysningerne på den udløste undtagelse. Denne fil har staksporing, så du kan løse dit program, der er baseret på dette.

### <a name="my-node-application-takes-too-much-time-to-startup-cold-start"></a>Mit node program tager for lang tid at start (kolde Start)

De mest almindelige skyldes, at programmet, har en masse filer i noden\_moduler og programmet forsøger at indlæse de fleste af disse filer under start. Som standard, da dine filer, der er placeret på netværksshare på Azure WebApp kan indlæsning af så mange filer gå et stykke tid.
Nogle solutions for at gøre det hurtigere er:

1.  Sørg for, at du har en flad afhængighed struktur og ingen dubletter afhængigheder ved hjælp af npm3 for at installere din moduler.

2.  Forsøg at fløde indlæse din node\_moduler og indlæses ikke alle modulerne ved start. Det betyder, at opkaldet til require('module') skal udføres, når du rent faktisk skal det inden for den funktion, du forsøger at bruge modulet.

3.  Azure WebApp indeholder en funktion, der hedder lokale cache. Denne funktion kopierer indholdet fra netværkssharet til den lokale disk på VM. Da filerne, er lokale, indlæsningstiden af node\_moduler er meget hurtigere. -Denne [dokumentation](../app-service/app-service-local-cache.md) forklares det, hvordan du bruger lokale Cache mere detaljeret.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http status og understatus

Denne [kildefilen](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) viser alle mulige status/understatus kombination iisnode kan returnere i tilfælde af fejl.

Aktivere FREB for dit program at få vist fejlkoden win32 (Kontroller, at du aktiverer FREB kun på ikke-produktiv websteder for at forbedre ydeevnen).

| HTTP-Status | HTTP understatus | Mulig årsag?                                                                                                                                                                                            
|-------------|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------
| 500         | 1000           | Der opstod et problem, afsendelse anmodningen til IISNODE – se Hvis node.exe er blevet startet. Node.exe kunne har gik ned under start. Kontroller konfigurationen web.config for fejl.                                                                                                                                                                                                                                                                                     |
| 500         | 1001           | -Win32Error 0x2 - App ikke svarer til URL-adressen. Kontrollere URL-adressen omskrivning regler, eller hvis din udtrykkelig app har de korrekte omdirigerer defineret. -Win32Error 0x6d – navngivne pipe er optaget-Node.exe accepterer ikke anmodninger, fordi pipen er optaget. Markér højt cpu-brug. -Andre fejl – se Hvis node.exe gik ned.
| 500         | 1002           | Node.exe gik ned – se d:\\privat\\LogFiles\\logføring-errors.txt for staksporing.                                                                                                                                                                                                                                                                                                                                                                                        |
| 500         | 1003           | Pipe konfiguration problem – du aldrig skal se ud, men hvis du gør, at navngivne pipe-konfigurationen er forkert.                                                                                                                                                                                                                                                                                                                                                          |
| 500         | 1004-1018      | Der opstod en fejl under sende anmodningen eller behandling af svaret på til/fra node.exe. Kontrollér, om node.exe gik ned. kontrollere d:\\privat\\LogFiles\\logføring-errors.txt for staksporing.                                                                                                                                                                                                                                                                                    |
| 503         | 1000           | Der er ikke nok hukommelse til at tildele flere navngivne pipe-forbindelser. Kontrollér, hvorfor din app er bruge så meget hukommelse. Kontrollere maxConcurrentRequestsPerProcess indstillingsværdien. Hvis er ikke uendelig, og du har en masse anmodninger, ved at øge denne værdi for at undgå denne fejl.                                                                                                                                                                                                                                                                                                                  |
| 503         | 1001           | Anmodning om kunne ikke sendes til node.exe, fordi programmet genbrug. Når programmet har genbruges, served anmodninger om normalt.                                                                                                                                                                                                                                                                                                               |
| 503         | 1002           | Kontrollér Win32-fejlkode faktisk årsag-anmodning kunne ikke sendes til en node.exe.                                                                                                                                                                                                                                                                                                                                                                               |
| 503         | 1003           | Navngivne pipe er optaget – se Hvis node bruger en masse CPU                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
Der findes en indstilling i NODE.exe kaldet NODE\_AFVENTER\_PIPE\_FOREKOMSTER. Denne værdi er som standard uden for azure WebApp 4. Det betyder, node.exe kan kun acceptere 4 anmodninger ad gangen på den navngivne pipe. Denne værdi er indstillet til 5000 på Azure WebApp, og denne værdi skal være godt nok til de fleste node programmer, der kører på azure WebApp. Du skal ikke se 503.1003 på azure WebApp, fordi vi har en høj værdi for NODEN\_AFVENTER\_PIPE\_FOREKOMSTER.  |

## <a name="more-resources"></a>Flere ressourcer

Følg disse links for at få flere oplysninger om node.js programmer på Azure App Service.

* [Komme i gang med Node.js webapps i Azure App Service](app-service-web-nodejs-get-started.md)
* [Sådan foretages fejlfinding en Node.js WebApp i Azure App Service](web-sites-nodejs-debug.md)
* [Brug af Node.js moduler med Azure-programmer](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Webapps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js Developer Center](../nodejs-use-node-modules-azure-apps.md)
* [Udforske konsollen særdeles hemmeligt Kudu fejlfinding](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
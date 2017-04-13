<properties 
    pageTitle="Fejlfinding i forbindelse med program indsigt i et Java webprojekt" 
    description="Fejlfindingsvejledningen - overvågning direkte Java-apps med programmet indsigt." 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/01/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Fejlfinding og Q & A til programmet indsigt til Java

Du har spørgsmål eller problemer med [Visual Studio programmet indsigt i Java][java]? Her er nogle tip.


## <a name="build-errors"></a>Opbygge fejl

*I Eklipse, når du tilføjer programmet indsigt SDK via Maven eller Gradle, får jeg build eller kontrolsum valideringsfejl.*

* Hvis afhængigheden <version> element bruger et mønster med jokertegn (fx (Maven) `<version>[1.0,)</version>` eller (Gradle) `version:'1.0.+'`), prøv at angive en bestemt version i stedet som `1.0.2`. Se den [Produktbemærkninger](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) til den nyeste version.

## <a name="no-data"></a>Ingen data 

*Jeg har tilføjet programmet indsigt og kørte min app, men jeg aldrig har set data i portalen.*

* Vent et øjeblik, og klik på Opdater. Diagrammerne med jævne mellemrum opdatere sig selv, men du kan også opdatere manuelt. Opdateringsintervallet afhænger af tidsintervallet i diagrammet.
* Kontrollere, at du har en instrumentation nøgle, der er defineret i filen ApplicationInsights.xml (i mappen ressourcer i projektet)
* Kontrollér, at der er ingen `<DisableTelemetry>true</DisableTelemetry>` node i XML-filen.
* I din firewall, kan det være nødvendigt at åbne TCP port 80 og 443 til udgående trafik til dc.services.visualstudio.com. Se den [komplette liste over undtagelser](app-insights-ip-addresses.md)
* Start i Microsoft Azure tavle, skal du se service status kortet. Hvis der er nogle beskeder om angivelser skal du vente, indtil de er returneret til OK og derefter lukke og genåbne din programmet indsigt programmet blade.
* Slå logføring til IDE console-vinduet, ved at tilføje en `<SDKLogger />` element under noden roden i ApplicationInsights.xml filer (i mappen ressourcer i projektet), og Kontrollér, om poster med [Error] foranstillet.
* Sørg for, at den korrekte ApplicationInsights.xml-fil er blevet blevet indlæst ved SDK Java ved at kigge på den konsol output meddelelser til en "konfigurationsfil er blevet konstateret"-sætning.
* Hvis filen config ikke blev fundet, Kontrollér de output meddelelser for at se, hvor filen config der søges efter, og Sørg for, at ApplicationInsights.xml er placeret i en af disse søgeplaceringer. Du kan placere config-filen som en tommelfingerregel i nærheden af programmet indsigt SDK JARs. For eksempel: i Tomcat, det kan betyde mappen WEB-INF/bibliotek.



#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Jeg har brugt til at få data, men det fungerer ikke længere

* Kontrollér [status blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Har du klikker på den månedlige lagerkvote datapunkter? Åbn indstillinger/kvote og priser for at finde ud af. Hvis det er tilfældet, kan du opgradere din plan, eller du kan købe ekstra kapacitet. Se den [priser farveskema](https://azure.microsoft.com/pricing/details/application-insights/).

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Jeg kan ikke se alle de data, som jeg er forventer at modtage

* Åbn kvotaerne og priser blade, og Kontrollér, om [stikprøver, hvor der](app-insights-sampling.md) er i handlingen. (100% transmission betyder, at stikprøver, hvor der ikke findes i handlingen). Tjenesten programmet indsigt kan angive for at acceptere kun en brøkdel af den telemetri, som modtages fra din app. Dette hjælper dig med at holde inden for den månedlige lagerkvote af telemetri. 

## <a name="no-usage-data"></a>Ingen anvendelsesdata

*Jeg kan se data om anmodninger om og svar klokkeslæt, men ingen sidevisning, browseren eller brugerdata.*

Du angivet er blevet af din app til at sende telemetri fra serveren. Nu er det næste trin at [konfigurere dine websider, sende telemetri fra webbrowseren][usage].

Du kan også, hvis din kunde er en app i en [telefon eller en anden enhed][platforms], kan du sende telemetri derfra. 

Brug den samme instrumentation nøgle til at konfigurere både klient- og telemetri. Dataene, der vises i den samme ressource programmet viden, og du vil kunne koordinere begivenheder fra klienten og serveren.



## <a name="disabling-telemetry"></a>Deaktivere telemetri

*Hvordan kan jeg deaktivere telemetri af websteder?*

Kode:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Eller** 

Opdater ApplicationInsights.xml (i mappen ressourcer i projektet). Tilføj følgende under rodnoden:

    <DisableTelemetry>true</DisableTelemetry>

Ved hjælp af metoden XML, skal du genstart programmet, når du ændrer værdien.

## <a name="changing-the-target"></a>Ændre destinationen

*Hvordan kan jeg ændre hvilken Azure ressource mit projekt sender data til?*

* [Få nøglen instrumentation i den nye ressource.][java]
* Hvis du har tilføjet programmet indsigt til dit projekt ved hjælp af Azure-værktøjskassen til Eklipse, højreklik på webprojektet, Vælg **Azure**, **Konfigurere programmet viden**, og Skift tasten.
* Ellers skal du opdatere nøglen i ApplicationInsights.xml i mappen ressourcer i projektet.


## <a name="the-azure-start-screen"></a>Azure startskærmbilledet

*Jeg vil bruge på [portalen Azure](https://portal.azure.com). Kortet fortæller mig noget, om min app?*

Nej, det viser tilstanden for Azure servere over hele verden.

*Fra Azure start tavle (startskærm), hvordan finder jeg data om min app?*

Hvis du [konfigurere din app til programmet indsigt][java], klik på Gennemse, vælg programmet viden, og vælg den app ressource, du har oprettet for din app. Hvis du vil have kan der hurtigere fremover, du fastgøre din app til start tavle.

## <a name="intranet-servers"></a>Intranetservere

*Kan jeg overvåge en server på min intranet?*

Ja, angivet din server kan sende telemetri til portalen programmet indsigt via offentlige internettet. 

I din firewall, kan det være nødvendigt at åbne TCP port 80 og 443 til udgående trafik til dc.services.visualstudio.com og f5.services.visualstudio.com.

## <a name="data-retention"></a>Dataopbevaring 

*Hvor længe bevares data i portalen? Er det sikkert?*

Se [dataopbevaring og beskyttelse af personlige oplysninger][data].

## <a name="next-steps"></a>Næste trin

*Jeg har konfigureret programmet indsigt til min Java server app. Hvad kan jeg ellers gøre?*

* [Overvåge tilgængeligheden af dine websider][availability]
* [Overvåge brugen af webside][usage]
* [Registrere brugen og diagnosticere problemer i din enhed apps][platforms]
* [Skrive programkode for at spore brugen af din app][track]
* [Registrere diagnosticeringslogfiler][javalogs]


## <a name="get-help"></a>Få hjælp

* [Stakoverløb](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 
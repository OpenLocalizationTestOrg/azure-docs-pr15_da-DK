<properties 
    pageTitle="Fejlfinding i forbindelse med ingen data - programmet indsigt til .NET" 
    description="Ikke kan se data i Visual Studio programmet indsigt? Prøv her." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Fejlfinding i forbindelse med ingen data - programmet indsigt til .NET

## <a name="some-of-my-telemetry-is-missing"></a>Nogle af mine telemetri mangler

*I programmet viden, jeg kan kun se en brøkdel af de hændelser, der genereres af min app.*

* Hvis du får vist den samme brøk konsekvent, er det sandsynligvis på grund af tilpassede [udvalg](app-insights-sampling.md). Åbne søgning (fra bladet oversigt) for at bekræfte, og kig på en forekomst af en anmodning om eller anden hændelse. Klik på "..." for at få at vide om alle egenskaber nederst i sektionen Egenskaber. Hvis anmode om Tæl > 1, og klik derefter stikprøver, hvor der er aktiv. 
* Ellers skal er det muligt, du nå en [data rente begrænse](app-insights-pricing.md#limits-summary) for din priser plan. Disse begrænsninger, der anvendes i minuttet.

## <a name="no-data-from-my-server"></a>Ingen data fra serveren

*Jeg har installeret min app på min webserver, og nu jeg kan ikke se alle telemetri fra den. Det lykkedes OK på min computer for Udviklingscenter.*

* Sandsynligvis en firewall problem. [Angive undtagelser for programmet indsigt til at sende data](app-insights-ip-addresses.md).

*Jeg har [installeret Status skærm](app-insights-monitor-performance-live-website-now.md) på min webserver til at overvåge eksisterende apps. Jeg kan ikke se nogen resultater.*

* Se [fejlfinding af Status skærm](app-insights-monitor-performance-live-website-now.md#troubleshooting). 


## <a name="q01"></a>Indstillingen Ingen 'Tilføj programmet indsigt' i Visual Studio

*Jeg kan ikke se eventuelle indstillinger, hvis programmet viden, når jeg opretter et nyt projekt i Visual Studio, eller når jeg højreklikker på et eksisterende projekt i Solution Explorer.*

+ Ikke alle de typer af .NET project understøttes af værktøjerne. Internettet og WCF projekter understøttes. For andre projekttyper som computeren eller service-programmer kan du stadig [tilføjer et program indsigt SDK til dit projekt manuelt](app-insights-windows-desktop.md).
+ Sørg for, at du har [Visual Studio 2013 opdatering 3 eller nyere](http://go.microsoft.com/fwlink/?LinkId=397827). Det er forudinstalleret med værktøjerne Application indsigt.
+ Vælg **værktøjer**, **Extensions og opdateringer** , og Kontrollér, at **Programmet indsigt værktøjer** er installeret og aktiveret. Hvis det er tilfældet, skal du klikke på **opdateringer** for at se, om der findes en opdatering.
+ Åbn dialogboksen nyt projekt, og vælg ASP.NET-webprogram. Hvis du se indstillingen programmet indsigt der skal installeres værktøjerne. Hvis ikke, skal du prøve at fjerne og geninstallere værktøjerne Application indsigt.


## <a name="q02"></a>Tilføjelse af programmet indsigt mislykkedes

*Når jeg opretter et nyt webprojekt, eller når jeg forsøger at føje programmet indsigt til et eksisterende projekt, får jeg vist en fejlmeddelelse.*

Sandsynlige årsager:

* Kommunikation med portalen programmet indsigt mislykkedes. eller
* Der er problemer med din Azure konto
* Du kun har [læseadgang til det abonnement eller den gruppe, hvor du forsøgte at oprette den nye ressource](app-insights-resources-roles-access-control.md).

Løsning:

+ Kontrollér, at du har angivet logonoplysninger til højre Azure-konto. 
+ I din browser skal du kontrollere, at du har adgang til [Azure-portalen](https://portal.azure.com). Åbn indstillinger, og se, om der er begrænsninger.
+ [Tilføje programmet indsigt til din eksisterende projekt](app-insights-asp-net.md): I Solution Explorer, højreklik på dit projekt, og vælg "Tilføj programmet indsigt."
+ Hvis den stadig ikke virker, skal du følge [Manuel procedure](app-insights-windows-services.md) for at føje en ressource på portalen og derefter tilføje SDK til projektet. 

## <a name="emptykey"></a>Jeg får fejlen "Instrumentation nøgle må ikke være tom"

Ser ud til, men noget gik galt, mens du er ved at installere programmet indsigt eller måske en logføring adapter.

I Solution Explorer, skal du højreklikke på `ApplicationInsights.config` , og vælg **Konfigurere programmet indsigt**. Du får vist en dialogboks, der inviterer dig til at logge på Azure og Opret enten et program indsigt ressource, eller brug et eksisterende dokument igen.


##<a name="NuGetBuild"></a>"NuGet pakker mangler" på serveren build

*Alt opbygger OK, når jeg fejlfinding på min computer for udvikling, men jeg får fejlmeddelelsen NuGet på build-serveren.*

Se [NuGet pakke gendanne](http://docs.nuget.org/Consume/Package-Restore) og [Automatisk gendannelse af pakke](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Manglende menukommando at åbne programmet indsigt fra Visual Studio

*Jeg kan ikke se programmet indsigt kommandoer, når jeg højreklikker på mit projekt Solution Explorer, eller jeg kan ikke se kommandoen Åbn programmet indsigt.*

Sandsynlige årsager:

* Hvis du har oprettet programmet indsigt ressourcen manuelt, eller hvis projektet er en type, der ikke understøttes af programmet indsigt værktøjerne.
* Værktøjerne programmet indsigt er deaktiveret i dit Visual Studio.
* Din Visual Studio er ældre end 2013 Update 3.

Løsning:

* Kontrollér, at din Visual Studio-version er 2013 opdatering 3 eller nyere.
* Vælg **værktøjer**, **Extensions og opdateringer** , og Kontrollér, at **Programmet indsigt værktøjer** er installeret og aktiveret. Hvis det er tilfældet, skal du klikke på **opdateringer** for at se, om der findes en opdatering.
* Højreklik på dit projekt i Solution Explorer. Hvis du kan se kommandoen **Konfigurere programmet viden**, kan du bruge det til at oprette forbindelse dit projekt til ressourcen i tjenesten programmet indsigt.


Ellers understøttes din projekttype ikke direkte af programmet indsigt værktøjerne. For at se din telemetri skal logge på [Azure-portalen](https://portal.azure.com), vælg programmet indsigt i venstre navigationslinje, og vælg dit program.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Adgang nægtet" under åbning af programmet indsigt fra Visual Studio

*Kommandoen 'Åbn programmet indsigt' i menuen tager mig på Azure-portalen, men jeg får fejlen "adgang nægtet".*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

Den Microsoft-logon, du sidst brugte på din standardbrowser har ikke adgang til [ressourcen, der blev oprettet, når programmet indsigt blev føjet til denne app](app-insights-asp-net.md). Der er to sandsynlige årsager: 

* Du har mere end én Microsoft-konto – måske en arbejde og en personlig Microsoft-konto? På logon, du sidst brugte på din standardbrowser blev til en anden konto end den, der har adgang til at [tilføjer programmet indsigt til projektet](app-insights-asp-net.md). 

 * Løsning: Klik på dit navn øverst til højre i browservinduet, og log af. Derefter logge på med den konto, der har adgang. I den venstre navigationslinje skal du klikke på programmet indsigt og vælg derefter din app.

* En anden føjet programmet indsigt til projektet, og de har glemt at give dig [adgang til ressourcegruppen](app-insights-resources-roles-access-control.md) , hvor det blev oprettet. 

 * Løsning: Hvis de har brugt en organisationskonto, de kan føje dig til i teamet. eller de kan give dig individuelle adgang til ressourcegruppen.



## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>'Aktiv blev ikke fundet"under åbning af programmet indsigt fra Visual Studio

*Kommandoen 'Åbn programmet indsigt' i menuen tager mig på Azure-portalen, men jeg får fejlen 'aktiv blev ikke fundet'.*

Sandsynlige årsager:

* Programmet indsigt ressourcen for dit program er blevet slettet. eller
* Tasten instrumentation blev angivet eller ændret i ApplicationInsights.config ved at redigere den direkte, uden at opdatere projektfilen. 

Tasten instrumentation i ApplicationInsights.config kontrolelementer, hvor telemetri sendes. En linje i projektfilen styrer, hvilke ressource åbnes, når du bruger kommandoen i Visual Studio. 

Løsning:

* I Solution Explorer, skal du højreklikke på projektet, og vælg programmet viden, konfigurere programmet indsigt. I dialogboksen, kan du enten vælge at sende telemetri til en eksisterende ressource, eller Opret en ny. Eller:
* Åbn ressourcen direkte. Log på [portalen Azure](https://portal.azure.com), klik på program indsigt i venstre navigationslinje, og vælg derefter din app.



## <a name="where-do-i-find-my-telemetry"></a>Hvor finder jeg min telemetri?

*Jeg er logget på [Microsoft Azure-portalen](https://portal.azure.com), og jeg leder på dashboardet Azure privat. Så hvor finder jeg min programmet indsigt data?*

* Klik på programmet viden, og klik derefter appnavnet på din på venstre navigationslinje. Hvis du ikke har nogen projekter der, skal du [tilføje eller konfigurere programmet indsigt i webprojektet](app-insights-asp-net.md).

    Der kan du se nogle oversigtsdiagrammer. Du kan klikke gennem de kan se mere detaljeret.

* Klik på knappen programmet indsigt i Visual Studio, mens du fejlfinding din app.


## <a name="q03"></a>Ingen server-data (eller ingen data overhovedet)

*Jeg har kørt min app og derefter åbnes tjenesten programmet indsigt i Microsoft Azure, men alle diagrammer vises 'Lær at indsamle...' eller "Ikke konfigureret."* Eller, *kun sidevisning og brugerdata, men ingen server-data.*

+ Kør programmet i fejlsikret tilstand i Visual Studio (F5). Bruge programmet med henblik på at generere nogle telemetri. Kontrollér, at du kan se hændelser, der registreres i vinduet Visual Studio output. 

    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)

+ Åbn [Diagnosticering søgning](app-insights-diagnostic-search.md)i portalen programmet indsigt. Data vises normalt her først.
+ Klik på knappen Opdater. Bladet opdaterer selve med jævne mellemrum, men du kan også gøre det manuelt. Opdateringsintervallet er længere tid for større tidsintervaller.
+ Kontrollér, så det svarer til tasterne instrumentation. Kig på **Instrumentation nøgle**på den primære blade for din app i portalen programmet indsigt i rullemenuen **Essentials** . I dit projekt i Visual Studio, Åbn derefter ApplicationInsights.config, og find den `<instrumentationkey>`. Kontrollér, at de to nøgler er lig med. Hvis det ikke:
 + I portalen, klik på program indsigt og se efter app ressourcen med den rigtige nøgle eller
 + I Visual Studio Solution Explorer, skal du højreklikke på projektet, og vælg programmet viden, Konfigurer. Nulstil app for at sende telemetri til højre ressourcen.
 + Hvis du ikke kan finde de tilsvarende nøgler, kan du kontrollere, at du bruger de samme logonoplysninger i Visual Studio som i på portalen.

    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
    
+ Se på tjenestetilstand kortet i [Microsoft Azure privat dashboard](https://portal.azure.com). Hvis der er nogle beskeder om angivelser skal du vente, indtil de er returneret til OK og derefter lukke og genåbne din programmet indsigt programmet blade.
+ Se også [bloggen status](http://blogs.msdn.com/b/applicationinsights-status/).
+ Har du skriver en hvilken som helst kode til [serversiden SDK](app-insights-api-custom-events-metrics.md) , der muligvis ændrer nøglen instrumentation i `TelemetryClient` forekomster eller i `TelemetryContext`? Eller du selv har skrevet en [filter eller udvalg konfiguration](app-insights-api-filtering-sampling.md) , som kan filtrering ud for meget?
+ Hvis du har redigeret ApplicationInsights.config, Tjek omhyggeligt konfigurationen af [TelemetryInitializers og TelemetryProcessors](app-insights-api-filtering-sampling.md). En forkert navngivet type eller parameter kan medføre SDK til at sende ingen data.

## <a name="q04"></a>Ingen data for brug af browsere, sidevisninger

*Jeg kan se data i serverens svartid og anmodninger til serveren, men ingen data på siden Vis indlæsningstiden eller i browseren eller brugen blade.*

Dataene kommer fra scripts i websider. 

+ Hvis du har tilføjet programmet indsigt til et eksisterende webprojekt, [du skal tilføje scriptene manuelt](app-insights-javascript.md).
+ Kontrollér, at Internet Explorer ikke vises dit websted i kompatibilitetstilstand.
+ Funktionen browserens fejlfinding (F12 i nogle browsere, vælg derefter Network) til at bekræfte, at dataene sendes til `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Ingen afhængighed eller undtagelse data

Se [afhængighed telemetri](app-insights-asp-net-dependencies.md) og [undtagelse telemetri](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>Ingen ydelsesdata

Ydelsesdata (CPU, læse / skrivehastighed og osv.) er tilgængelig for [Java-webtjenester](app-insights-java-collectd.md), [Windows-programmer](app-insights-windows-desktop.md), [IIS web apps og tjenester, hvis du installerer status skærm](app-insights-monitor-performance-live-website-now.md)og [Azure Cloud Services](app-insights-azure.md). Du finder den under indstillinger for servere.

Det er ikke tilgængelig for Azure websteder.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Ingen (server) data, da jeg publiceret appen på serveren

+ Kontrollér, at du rent faktisk kopieret alle Microsoft. ApplicationInsights DLL-filer på serveren, sammen med Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
+ Det være nødvendigt at [åbne nogle TCP-porte](app-insights-ip-addresses.md#data-access-api)i din firewall.
+ Hvis du skal bruge en proxy til at sende af virksomhedens netværk, kan du angive [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) i Web.config
+ Windows Server 2008: Sørg for, at du har installeret de følgende opdateringer: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523) [KB2600217](https://support.microsoft.com/kb/2600217).


## <a name="i-used-to-see-data-but-it-has-stopped"></a>Jeg har brugt til at få data, men det fungerer ikke længere

* Kontrollér [status blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Har du klikker på den månedlige lagerkvote datapunkter? Åbn indstillinger/kvote og priser for at finde ud af. Hvis det er tilfældet, kan du opgradere din plan, eller du kan købe ekstra kapacitet. Se den [priser farveskema](https://azure.microsoft.com/pricing/details/application-insights/).


## <a name="i-dont-see-all-the-data-im-expecting"></a>Jeg kan ikke se alle de data, som jeg er forventer at modtage

Hvis dit program sender en stor mængde data, og du bruger programmet indsigt SDK til ASP.NET version 2.0.0-beta3 eller nyere, kan funktionen [tilpasset udvalg](app-insights-sampling.md) betjene og send kun en procentdel af din telemetri. 

Du kan deaktivere det, men det anbefales ikke. Stikprøver, hvor der er designet, så relaterede telemetri overføres korrekt til diagnosticering. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>Forkerte geografiske data i bruger telemetri

Den by, region og land dimensioner er udledt fra IP-adresser og altid er ikke korrekte.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Undtagelse "metoden blev ikke fundet" på kører i Azure Cloud Services

Du opretter til .NET 4.6? 4.6 understøttes ikke automatisk i Azure Cloud Services roller. [Installere 4.6 på hver rolle](../cloud-services/cloud-services-dotnet-install-dotnet.md) , før du kører din app.

## <a name="still-not-working"></a>Stadig virker ikke...

* [Programmet indsigt forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


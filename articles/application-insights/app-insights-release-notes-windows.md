<properties 
    pageTitle="Produktbemærkninger til programmet indsigt til Windows" 
    description="De seneste opdateringer til Windows Store SDK." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2016" 
    ms.author="joshweb"/>
 
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>Produktbemærkninger til programmet indsigt SDK til Windows Phone og gemme

Programmet indsigt SDK sender telemetri om din direkte app [Programmet viden](https://azure.microsoft.com/services/application-insights/), hvor du kan analysere dens brugen og ydeevne.


## <a name="version-111"></a>Version 1.1.1

### <a name="windows-sdk"></a>Windows SDK

- Løse en hænger under går ned, når du bruger på Windows Phone Silverlight SDK. Efter ændringen bevares en hvilken som helst går ned, sker der senere end ~ 2 sekunder efter opkaldet til WindowsAppInitialier.InitializeAsync(...) til disk, og vil blive sendt, næste gang appen startes. Hvis nedbrud sker før ~ 2 sekunder efter opkaldet, vil den blive ignoreret.  
- Angiv den NuGet afhængigheder til en bestemt version af Core og Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### <a name="core-sdk"></a>Core SDK

- Core administreres i github. Fremtidige produktbemærkninger af Core SDK kan findes [i github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>Version 1.1

### <a name="core-sdk"></a>Core SDK

- SDK nu introducerer nye telemetri type ```DependencyTelemetry``` som indeholder oplysninger om afhængighed opkald fra programmet på computeren
- Nye metode ```TelemetryClient.TrackDependency``` giver mulighed for at sende oplysninger om afhængighed opkald fra programmet på computeren

## <a name="version-100"></a>Version 1.0.0

### <a name="windows-app-sdk"></a>Windows-App SDK

- Ny initialisering til Windows-Apps. Ny `WindowsAppInitializer` klasse med `InitializeAsync()` giver mulighed for at starte sig selv initialisering af SDK af websteder. Denne ændring Tillad mere nøjagtig kontrol og betydeligt app initialisering forbedring af ydeevnen over forrige ApplicationInsights.config metode.
- DeveloperMode er ikke længere automatisk angivet. Hvis du vil ændre DeveloperMode funktionsmåde skal du angive i kode.
- NuGet pakke fra serveren ikke længere ApplicationInsights.config. Anbefale for at bruge den nye WindowsAppInitializer, når du manuelt tilføje NuGet pakke.
- Kun læser ApplicationInsights.config `<InstrumentationKey>`, alle andre indstillinger, ignoreres i præference for WindowsAppInitializer indstillinger.
- Store marked bliver automatisk, der indsamles via SDK.
- Mange fejlrettelser, stabilitet og forbedret ydeevne.

### <a name="core-sdk"></a>Core SDK

- ApplicationInsights.config filen er ikke længere requiered. Og ikke tilføjede ved pakken NuGet. Konfiguration kan angives fuldt ud i kode.
- NuGet pakke vil ikke længere tilføje en mål-fil til din løsning. Dette fjerner automatisk indstilling af DeveloperMode under et fejlfinding build. DeveloperMode skal angives manuelt i kode.

## <a name="version-017"></a>Version 0.17

### <a name="windows-app-sdk"></a>Windows-App SDK

- Windows-App SDK understøtter nu Universal Windows-Apps, der er oprettet med Windows 10 technical preview og med VS 2015 RC.

### <a name="core-sdk"></a>Core SDK

- TelemetryClient standardindstillinger for initialiseret med InMemoryChannel.
- Nye API, der er tilføjet, `TelemetryClient.Flush()`. Denne metode træk skal udløse en øjeblikkelig blokering overførsel af alle telemetri logget denne klient. Dette giver mulighed for manuel udløst Overfør før proces lukning.
- NuGet pakke føjet et .net 4.5 mål. Denne destination har ingen eksterne afhængigheder (fjernede BCL og EventSource afhængigheder).

## <a name="version-016"></a>Version 0,16 

2015-04-28 preview

- SDK understøtter nu DNX target platform for at aktivere overvågning af [Core .NET framework](http://www.dotnetfoundation.org/NETCore5) -programmer.
- Forekomster af ```TelemetryClient``` skal ikke cachelagres Instrumentation nøgle længere. Nu, hvis instrumentation nøglen ikke blev angivet ```TelemetryClient``` eksplicit ```InstrumentationKey``` returnerer null. Det løser et problem, når du indstiller ```TelemetryConfiguration.Active.InstrumentationKey``` når nogle telemetri allerede er indsamlet, telemetri moduler som afhængighed indsamler, web anmodninger om data indsamling og ydeevne tællere indsamler anvender ny instrumentation nøgle.

## <a name="version-015"></a>Version 0,15

- Ny egenskab ```Operation.SyntheticSource``` nu tilgængeligt på ```TelemetryContext```. Nu kan du markere dine telemetri elementer som "ikke et reelt tal bruger trafik" og angive, hvordan denne trafik blev oprettet. Du kan skelne trafik fra din test automatisering fra Indlæs test trafik som et eksempel ved at angive denne egenskab.
- Kanal logik blev flyttet til den separate NuGet kaldet Microsoft.ApplicationInsights.PersistenceChannel. Standardkanal hedder nu InMemoryChannel
- Nye metode ```TelemetryClient.Flush``` giver mulighed for at rydde telemetri elementer fra bufferen synkront

## <a name="version-013"></a>Version 0,13

Ingen produktbemærkninger til ældre versioner, der er tilgængelige. 

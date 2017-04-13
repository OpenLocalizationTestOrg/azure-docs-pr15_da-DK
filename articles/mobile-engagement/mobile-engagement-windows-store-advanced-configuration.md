<properties
    pageTitle="Avanceret konfiguration for Windows Universal Apps aftale SDK"
    description="Avancerede konfigurationsindstillinger for Azure Mobile aftale med Windows Universal Apps"                    
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Avanceret konfiguration for Windows Universal Apps aftale SDK

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Denne procedure beskriver, hvordan du kan konfigurere forskellige konfigurationsindstillinger for Azure Mobile aftale Android-apps.

## <a name="prerequisites"></a>Forudsætninger

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

##<a name="advanced-configuration"></a>Avanceret konfiguration

### <a name="disable-automatic-crash-reporting"></a>Deaktivere automatisk nedbrud rapportering

Du kan deaktivere den automatiske nedbrud rapportering funktion i aftale. Derefter, når der opstår en ikke-afviklet undtagelse, aftale der sker ikke noget.

> [AZURE.WARNING] Hvis du deaktiverer denne funktion, derefter sender når der opstår en ikke-afviklet nedbrud i din app aftale ikke nedbrud **og** ikke lukker session og sager.

Tilpasse din konfiguration afhængigt af den måde, du har defineret den for at deaktivere automatisk nedbrud rapportering skal:

#### <a name="from-engagementconfigurationxml-file"></a>Fra `EngagementConfiguration.xml` fil

Angiv rapport nedbrud til `false` mellem `<reportCrash>` og `</reportCrash>` mærker.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Fra `EngagementConfiguration` objekt på kørselstidspunktet

Angiv rapport nedbrud til falsk ved hjælp af EngagementConfiguration objektet.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Deaktivere realtid rapportering

Som standard gemmes aftale servicerapporter i realtid. Hvis dit program rapporterer logfiler ofte, er det bedre at bufferen logfiler og for at rapportere dem på én gang på grundlag af normal arbejdstid. Dette kaldes "burst tilstand".

Kalde metoden for at gøre det:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argumentet er en værdi i **millisekunder**. Når du vil genaktivere realtid logføring skal du ringe til metoden uden en parameter eller med værdien 0.

Burst-tilstand lidt øges batterilevetiden, men har indflydelse på den aftale skærm: alle sessioner og -job varighed er afrundet til grænseværdi for burst (altså sessioner og job kortere end grænseværdi for burst ikke muligvis er synlige). Det anbefales at bruge en burst grænseværdi ikke længere end 30000 (30s). Gemte logfiler er begrænset til 300 elementer. Hvis det er for lang tid at sende, kan du miste nogle logfiler.

> [AZURE.WARNING] Grænseværdi for burst kan ikke konfigureres til en periode mindre end et sekund. Hvis du gør dette, SDK viser en sporing med fejl og nulstiller automatisk til standardværdien og nul sekunder. Dette udløser SDK for at rapportere loggene i realtid.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview

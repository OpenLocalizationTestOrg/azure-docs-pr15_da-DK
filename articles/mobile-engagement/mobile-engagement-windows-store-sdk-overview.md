<properties
    pageTitle="Windows Universal SDK Integration"
    description="Windows Universal Integration for SDK til Azure Mobile aftale"                                     
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

#<a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Windows Universal SDK Integration til Azure Mobile aftale

I dette dokument beskrives alle de integration og konfiguration muligheder Azure Mobile aftale Windows Universal SDK.

## <a name="prerequisites"></a>Forudsætninger

Før du starter selvstudiet, skal du først udføre vores [15-minutters selvstudium](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Avancerede funktioner

### <a name="reporting-features"></a>Rapporteringsfunktioner
Du kan føje disse funktioner:

1. [Avancerede indstillinger til rapportering](mobile-engagement-windows-store-advanced-reporting.md)

2. [Avancerede konfigurationsindstillinger](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Beskeder

[Hvordan du integrerer rækkevidde (meddelelser om) i dine Windows Universal app](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Mærke plan implementering:

[Sådan bruges de avancerede Mobile aftale mærkning API i din Windows Universal app](mobile-engagement-windows-store-use-engagement-api.md)

##<a name="release-notes"></a>Produktbemærkninger

###<a name="340-04192016"></a>3.4.0 (04-19/2016)

-   Når overlejring forbedringer.
-   Tilføjede "TestLogLevel" API til at aktivere/deaktivere/filter console logfiler, der udsendes af SDK.
-   Fast målretning af den første aktivitet, der ikke vises på App i aktivitet meddelelser om start.

Se [komplet produktbemærkninger](mobile-engagement-windows-store-release-notes.md) til tidligere versioner

##<a name="upgrade-procedures"></a>Opgradere procedurer

Hvis du har allerede integreret en ældre version af aftale i dit program, har du overveje følgende punkter, når du opgraderer SDK.

Hvis du ikke har set flere versioner af SDK, kan du skal følge flere procedurer. Se den komplette [Opgradere procedurer](mobile-engagement-windows-store-upgrade-procedure.md). Eksempelvis hvis du overfører fra 0.10.1 til 0.11.0, du skal først følge fremgangsmåden "fra 0.9.0 til 0.10.1" derefter fremgangsmåden "fra 0.10.1 til 0.11.0".

###<a name="from-330-to-340"></a>Fra 3,3,0 til 3.4.0

####<a name="test-logs"></a>Test logfiler

Console logfiler, der er oprettet med SDK kan nu være aktiveret/deaktiveret/filtreret. Hvis du vil tilpasse, opdatere egenskaben `EngagementAgent.Instance.TestLogEnabled` til en af de værdier, der er tilgængelige fra den `EngagementTestLogLevel` optælling, for eksempel:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

####<a name="resources"></a>Ressourcer

Rækkevidde overlejring er blevet forbedret. Det er en del af SDK NuGet pakke ressourcer.

Når du opgraderer til den nye version af SDK, kan du vælge, om du vil beholde dine eksisterende filer fra mappen overlejring af dine ressourcer eller ej:

* Hvis den forrige overlejring arbejde for dig, eller du integrerer den `WebView` elementer manuelt, derefter du kan vælge at beholde dit afslutte filer, den fungerer stadig.
* Hvis du vil opdatere til den nye overlejring, skal du erstatte hele `overlay` mappe fra dine ressourcer med et nyt fra SDK-pakken (UWP apps: efter opgraderingen, kan du få den nye mappe overlejring % BRUGERPROFIL %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] Ved hjælp af den nye overlejring overskriver alle tilpasninger, der er foretaget i den tidligere version.

### <a name="upgrade-from-older-versions"></a>Opgradere fra ældre versioner

Se [opgradere procedurer](mobile-engagement-windows-store-upgrade-procedure.md)

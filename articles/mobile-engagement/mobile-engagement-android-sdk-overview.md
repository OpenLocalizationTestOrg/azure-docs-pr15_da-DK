<properties
    pageTitle="Android SDK Integration til Azure Mobile aftale"
    description="Beskriver, hvordan du integrerer Azure Mobile aftale SDK i Android-apps"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Android SDK Integration til Azure Mobile aftale

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-sdk-overview.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS](mobile-engagement-ios-sdk-overview.md)
- [Android](mobile-engagement-android-sdk-overview.md)

I dette dokument beskrives alle de integration og konfiguration muligheder Azure Mobile aftale Android SDK.

## <a name="prerequisites"></a>Forudsætninger

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Avancerede funktioner

### <a name="reporting-features"></a>Rapporteringsfunktioner

Du kan føje disse funktioner:

1. [Avancerede indstillinger til rapportering](mobile-engagement-android-advanced-reporting.md)
2. [Indstillinger for rapportering af placering](mobile-engagement-android-location-reporting.md)
3. [Avancerede konfigurationsindstillinger](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Meddelelser:
[Hvordan du integrerer rækkevidde (meddelelser om) i din Android-app](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [hvordan du integrerer GCM med Mobile aftale](mobile-engagement-android-gcm-integrate.md)

2. Amazon enhed Messaging (ADM): [hvordan du integrerer ADM med Mobile aftale](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Mærke plan implementering:
[Sådan bruges de avancerede Mobile aftale mærkning API i din Android-app](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Produktbemærkninger

### <a name="423-08102016"></a>4.2.3 (08-10/2016)

 - Ingen yderligere WIFI Lås.
 - Ret baglås, når du ringer til getDeviceId før initialisering (fejldatabase introduceret i 4.2.0).

For alle versioner, skal du se [fuldført produktbemærkninger](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Opgradere procedurer

Hvis du har allerede integreret en ældre version af vores SDK i dit program, du kontakte [Opgradere procedurer](mobile-engagement-android-upgrade-procedure.md).

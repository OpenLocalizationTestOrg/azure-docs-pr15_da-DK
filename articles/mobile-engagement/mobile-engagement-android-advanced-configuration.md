<properties
    pageTitle="Avanceret konfiguration til Azure Mobile aftale Android SDK"
    description="I denne artikel beskrives de avancerede konfigurationsindstillinger, herunder Android manifestet med Azure Mobile aftale Android SDK"
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
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Avanceret konfiguration til Azure Mobile aftale Android SDK

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Denne procedure beskriver, hvordan du kan konfigurere forskellige konfigurationsindstillinger for Azure Mobile aftale Android-apps.

## <a name="prerequisites"></a>Forudsætninger

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Tilladelseskrav
Visse indstillinger kræver specifikke tilladelser, der er angivet her til reference og i-linje i den bestemte funktion. Føj disse tilladelser til AndroidManifest.xml af projektet, umiddelbart før eller efter de `<application>` mærke.

Tilladelse koden, skal ligne følgende, hvor du udfylde de relevante tilladelser fra den tabel, der følger efter.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Tilladelse | Når den bruges |
| ---------- | --------- |
| INTERNET | Der er påkrævet. Til grundlæggende-rapportering |
| ACCESS_NETWORK_STATE | Der er påkrævet. Til grundlæggende-rapportering |
| RECEIVE_BOOT_COMPLETED | Der er påkrævet. Vise midterpunkt meddelelser efter enheden genstart |
| WAKE_LOCK | Anbefales. Gør det muligt for indsamling af data, når du bruger Wi-Fi, eller når skærm er slukket |
| VIBRATIONER | Valgfrit. Gør det muligt for vibrationer, når der modtages beskeder |
| DOWNLOAD_WITHOUT_NOTIFICATION | Valgfrit. Gør det muligt for Android overblik meddelelse |
| WRITE_EXTERNAL_STORAGE | Valgfrit. Gør det muligt for Android overblik meddelelse |
| ACCESS_COARSE_LOCATION | Valgfrit. Gør det muligt for realtid placering rapportering |
| ACCESS_FINE_LOCATION | Valgfrit. Gør det muligt for GPS-baserede placering rapportering |

Starte med Android M, [nogle tilladelser administreres på kørselstidspunktet](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Hvis du allerede bruger ``ACCESS_FINE_LOCATION``, og du ikke behøver at også bruge ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Android Manifest konfigurationsindstillinger

### <a name="crash-report"></a>Rapportering af nedbrud

For at deaktivere nedbrud rapporter skal du tilføje denne kode mellem den `<application>` og `</application>` mærker:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Grænseværdi for burst

Som standard gemmes aftale servicerapporter i realtid. Hvis programmet rapport logfilerne varierer ofte, er det bedre at bufferen logfiler og for at rapportere dem på én gang på gangen normalt base (kaldet "burst tilstand"). Gør du ved at tilføje denne kode mellem den `<application>` og `</application>` mærker:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Burst-tilstand lidt øges batterilevetiden, men har indflydelse på den skærm, aftale: alle sessioner og -job varighed er afrundet til grænseværdi for burst (altså sessioner og job kortere end grænseværdi for burst ikke muligvis er synlige). Grænseværdi for burst skal ikke være længere end 30000 (30s).

### <a name="session-timeout"></a>Sessionstimeout

 Du kan afslutte en aktivitet ved at trykke på tasten **Home** eller **tilbage** , ved at angive inaktiv telefonen eller ved at hoppe til et andet program. Som standard afsluttes en session ti sekunder efter slutningen af den sidste aktivitet. Derved undgår en session opdeling hver gang brugeren har afsluttet og vender tilbage til programmet hurtigt, som kan happen når brugeren opfanger et billede, kontrollerer en meddelelse om osv. Du vil ændre denne parameter. Gør du ved at tilføje denne kode mellem den `<application>` og `</application>` mærker:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Deaktivere log rapportering

### <a name="using-a-method-call"></a>Brug af en metode opkald

Hvis du vil aftale at ophøre med at sende logge, kan du ringe:

    EngagementAgent.getInstance(context).setEnabled(false);

Dette opkald er fast: bruges en delt indstillingsfil.

Hvis aftale er aktiv, når du ringer til denne funktion, kan det tage et minut for tjenesten stoppe. Men det ikke kan starte tjenesten overhovedet næste gang starter du programmet.

Du kan aktivere log rapportering igen ved at ringe til den samme funktion med `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integration i din egen`PreferenceActivity`

Du kan også integrere denne indstilling i stedet for opkald til denne funktion, direkte i din eksisterende `PreferenceActivity`.

Du kan konfigurere aftale at bruge din indstillingsfil (med den ønskede tilstand) i den `AndroidManifest.xml` filen med `application meta-data`:

-   Den `engagement:agent:settings:name` nøgle bruges til at definere navnet på den delte indstillingsfil.
-   Den `engagement:agent:settings:mode` nøgle bruges til at definere tilstanden af den delte indstillingsfil. Brug af samme tilstand som i din `PreferenceActivity`. Tilstanden, der skal overføres som et tal: Hvis du bruger en kombination af konstant flag i din kode, markere den samlede værdi.

Aftale altid bruger den `engagement:key` boolesk nøgle i indstillingsfil til administration af denne indstilling.

Følgende eksempel på `AndroidManifest.xml` viser standardværdierne:

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

Du kan tilføje en `CheckBoxPreference` i din præference layout som det følgende:

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />

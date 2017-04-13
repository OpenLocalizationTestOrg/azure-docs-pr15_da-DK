<properties
    pageTitle="Avancerede indstillinger til rapportering for Azure Mobile aftale Android SDK"
    description="Beskriver, hvordan du gør Avanceret rapportering til at registrere analytics til Azure Mobile aftale Android SDK"
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
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-engagement-on-android"></a>Avanceret rapportering med aftale på Android-enhed

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Dette emne beskrives yderligere reporting scenarier i programmet på Android. Du kan anvende disse indstillinger til den app, der er oprettet i [Introduktion –](mobile-engagement-android-get-started.md) selvstudium.

## <a name="prerequisites"></a>Forudsætninger

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

Selvstudiet du fuldført blev bevidst direkte og enkel, men der er avancerede indstillinger kan du vælge.

## <a name="modifying-your-activity-classes"></a>Ændre din `Activity` klasser

I [Introduktion – selvstudium](mobile-engagement-android-get-started.md)alle du skulle gøre var at gøre dit `*Activity` underklasser nedarver fra det tilsvarende `Engagement*Activity` klasser. Hvis din ældre aktivitet udvidede eksempelvis `ListActivity`, du ville få den udvide `EngagementListActivity`.

> [AZURE.IMPORTANT] Når du bruger `EngagementListActivity` eller `EngagementExpandableListActivity`, skal du sørge for et opkald til `requestWindowFeature(...);` sker inden opkaldet til `super.onCreate(...);`, ellers nedbrud opstår.

Du kan finde disse klasser i den `src` mappe, og du kan kopiere dem til dit projekt. Klasserne findes også i **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternative metode: ringe `startActivity()` og `endActivity()` manuelt

Hvis du ikke kan eller ikke vil overbelastning din `Activity` klasser, kan du i stedet starte og afslutte dine aktiviteter ved at ringe på `EngagementAgent`'s metoder direkte.

> [AZURE.IMPORTANT] Android SDK aldrig ringer på `endActivity()` metode, selvom programmet er lukket (på Android-enhed, programmer er aldrig lukket). Det er derfor *meget* anbefales til at ringe til den `startActivity()` metode i den `onResume` tilbagekald af *alle* dine aktiviteter og `endActivity()` metode i den `onPause()` tilbagekald af *alle* dine aktiviteter. Dette er den eneste måde at sikre, at der ikke er lækket sessioner. Hvis en session er lækket, tjenesten aftale aldrig afbryder forbindelsen fra aftale back-end (da tjenesten forbliver forbindelsen, så længe en session er ventende).

Her er et eksempel:

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

I dette eksempel minder om den `EngagementActivity` klasse og dens varianter, hvis kildekode findes i den `src` mappe.

## <a name="using-applicationoncreate"></a>Brug af Application.onCreate()

En hvilken som helst kode, du placerer i `Application.onCreate()` og i andre programmer kører tilbagekald for alle programmets processer, herunder tjenesten aftale. Det kan være nødvendigt uønskede side effekter som unødvendige hukommelse tildelinger og tråde i processen til den aftale, eller dublerede broadcast modtagere eller tjenester.

Hvis du tilsidesætte `Application.onCreate()`, anbefaler vi tilføje følgende kodestykke i starten af din `Application.onCreate()` funktionen:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Du kan gøre det samme til `Application.onTerminate()`, `Application.onLowMemory()`, og `Application.onConfigurationChanged(...)`.

Du kan også udvide `EngagementApplication` i stedet for at udvide `Application`: tilbagekaldet `Application.onCreate()` betyder kontrollen proces og opkald `Application.onApplicationProcessCreate()` kun hvis den aktuelle proces ikke er et værtstjeneste aftale, de samme regler gælder for de andre tilbagekald.

## <a name="tags-in-the-androidmanifestxml-file"></a>Mærker i filen AndroidManifest.xml

I tjenesten mærket i filen AndroidManifest.xml på `android:label` attribut kan du vælge navnet på tjenesten aftale, som den vises til slutbrugerne i skærmbilledet "Kører services" i deres telefon. Vi anbefaler, at denne attribut `"<Your application name>Service"` (for eksempel `"AcmeFunGameService"`).

Angive den `android:process` attribut sikrer, at aftale tjenesten kører i sin egen proces (kører aftale i den samme fremgangsmåde, som dit program gør hoved/UI-tråd potentielt mindre svarede).

## <a name="building-with-proguard"></a>Opbygning af med ProGuard

Hvis du opretter din programpakke med ProGuard, skal du holde nogle klasser. Du kan bruge den følgende konfiguration kodestykke:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
    }

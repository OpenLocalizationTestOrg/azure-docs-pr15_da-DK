<properties
    pageTitle="Windows Universal Avanceret rapportering med MobileApps aftale"
    description="Hvordan du integrerer Azure Mobile aftale med Windows Universal Apps"                  
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
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Avanceret rapportering med Windows Universal Apps aftale SDK

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-advanced-reporting.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Dette emne beskrives yderligere reporting scenarier i Windows Universal programmet. Disse scenarier omfatter indstillinger, du kan anvende på appen, der er oprettet i [Introduktion –](mobile-engagement-windows-store-dotnet-get-started.md) selvstudium.

## <a name="prerequisites"></a>Forudsætninger

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Før du starter selvstudiet, skal du først udføre selvstudiet [Introduktion](mobile-engagement-windows-store-dotnet-get-started.md) , som er bevidst direkte og enkel. Dette selvstudium dækker flere indstillinger, du kan vælge mellem.

## <a name="specifying-engagement-configuration-at-runtime"></a>Angive aftale konfiguration på kørselstidspunktet

Aftale konfigurationen er centraliserede i den `Resources\EngagementConfiguration.xml` fil af dit projekt, som er, hvor det er angivet i emnet [Kom godt i gang](mobile-engagement-windows-store-dotnet-get-started.md) .

Men du kan også angive den på kørselstidspunktet: Du kan ringe til følgende metode før aftale agent initialisering:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Anbefalet metode: overbelastning din `Page` klasser

Hvis du vil aktivere rapportering af alle de logfiler, der kræves, før aftale til at beregne brugere, sessioner, aktiviteter, går ned og tekniske statistik, få alle dine `Page` underordnede klasser nedarver fra den `EngagementPage` klasser.

Her er et eksempel på en side på dit program. Du kan gøre det samme for alle sider på dit program.

### <a name="c-source-file"></a>C#-kildefil

Rediger siden `.xaml.cs` fil:

-   Føje til din `using` sætninger:

        using Microsoft.Azure.Engagement;

-   Erstatte `Page` med `EngagementPage`:

**Uden at aftale:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Med aftale:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] Hvis siden tilsidesætter den `OnNavigatedTo` metode, skal du sørge for at ringe `base.OnNavigatedTo(e)`. Ellers aktiviteten er ikke rapporteres (den `EngagementPage` opkald `StartActivity` i dets `OnNavigatedTo` metode).

### <a name="xaml-file"></a>XAML-fil

Rediger siden `.xaml` fil:

-   Føje til din navneområder erklæringer:

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Erstatte `Page` med `engagement:EngagementPage`:

**Uden at aftale:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Med aftale:**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>Tilsidesætte standard adfærd

Som standard rapporteres klassenavnet på siden som på aktivitetens navn med uden ekstra. Hvis klassen bruger suffikset "Side", fjernes den aftale.

Hvis du vil tilsidesætte standardindstillingen for navnet, du Tilføj denne kode:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Rapportere ekstra oplysninger med din aktivitet, skal du tilføje denne kode:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Disse metoder kaldes inde fra den `OnNavigatedTo` metode til siden.

### <a name="alternate-method-call-startactivity-manually"></a>Alternative metode: ringe `StartActivity()` manuelt

Hvis du ikke kan eller ikke vil overbelastning din `Page` klasser, kan du i stedet starte dine aktiviteter ved at ringe til `EngagementAgent` metoder direkte.

Vi anbefaler opkald `StartActivity` i din `OnNavigatedTo` metode til siden.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Kontrollér, at du afslutte sessionen korrekt.
>
> Windows Universal SDK automatisk opkald på `EndActivity` metode, når programmet er lukket. Det er derfor **meget** anbefales til at ringe til den `StartActivity` metode, når aktiviteten på brugeren, ændre, og til **aldrig** kald den `EndActivity` metode. Denne metode giver besked om serveren aftale, den aktuelle bruger har forladt programmet, som påvirker alle programmet logfiler.

## <a name="advanced-reporting"></a>Avanceret rapportering

Du kan også du overveje at rapportere program-specifikke begivenheder, fejl og job, til at gøre det, skal du bruge de andre metoder findes i den `EngagementAgent` klasse. Aftale API giver mulighed for brug af alle aftale avancerede egenskaber.

Se, [hvordan du bruger de avancerede Mobile aftale mærkning API i din Windows Universal app](mobile-engagement-windows-store-use-engagement-api.md)for yderligere oplysninger.

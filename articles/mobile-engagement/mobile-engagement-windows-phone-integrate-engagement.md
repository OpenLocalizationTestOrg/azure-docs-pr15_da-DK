<properties 
    pageTitle="Windows Phone Silverlight aftale SDK Integration" 
    description="Hvordan du integrerer Azure Mobile aftale til Windows Phone Silverlight Apps"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-engagement-sdk-integration"></a>Windows Phone Silverlight aftale SDK Integration

> [AZURE.SELECTOR] 
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Denne procedure beskriver den nemmeste måde at aktivere Azure Mobile aftale analyser og overvågning funktioner i din Windows Phone Silverlight-program.

Følgende trin er nok til at aktivere rapporten af logfiler, der skal bruges til at beregne alle statistiske oplysninger om brugere, sessioner, aktiviteter, går ned og Technicals. Rapport over logfiler, der skal bruges til at beregne andre statistikker som begivenheder, fejl og job skal udføres manuelt ved hjælp af aftale API (se, [hvordan du kan bruge de avancerede Mobile aftale mærkning API i din Windows Phone Silverlight app](mobile-engagement-windows-phone-use-engagement-api.md) nedenfor) fordi disse statistikker er afhængige af programmet.

##<a name="supported-versions"></a>Understøttede versioner

Mobile aftale SDK for Windows Silverlight kan kun integreres i programmer målretning af:

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE] Hvis du målretter Windows Phone 8.1 (ikke-Silverlight), skal du henvise til [Windows Universal integration procedure](mobile-engagement-windows-store-integrate-engagement.md).

##<a name="install-the-mobile-engagement-silverlight-sdk"></a>Installere Mobile aftale Silverlight SDK

Den Mobile aftale SDK for Windows Silverlight er tilgængelige som en Nuget pakke kaldet *MicrosoftAzure.MobileEngagement*. Du kan installere det fra Visual Studio Nuget Package Manager. 

##<a name="add-the-capabilities"></a>Tilføje funktionerne

Aftale SDK skal nogle funktioner i Windows Phone Silverlight SDK for at kunne fungere korrekt.

Åbn din `WMAppManifest.xml` filen, og Sørg for, at følgende funktioner er defineret i den `Capabilities` panel:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##<a name="initialize-the-engagement-sdk"></a>Initialiseret aftale SDK

### <a name="engagement-configuration"></a>Aftale konfiguration

Aftale konfigurationen er centraliserede i den `Resources\EngagementConfiguration.xml` fil af projektet.

Redigere denne fil til at angive:

-   Dit program forbindelsesstreng mellem mærker `<connectionString>` og `<\connectionString>`.

Hvis du vil angive den på kørselstidspunktet i stedet, kan du ringe til følgende metode før aftale agent initialisering:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

Forbindelsesstrengen for dit program vises på portalen Azure klassisk.

### <a name="engagement-initialization"></a>Aftale initialisering

Når du opretter et nyt projekt, en `App.xaml.cs` filen genereres. Denne klasse nedarver fra `Application` og indeholder mange vigtige metoder. Det bruges også initialiseret aftale SDK.

Ændre den `App.xaml.cs`:

-   Føje til din `using` sætninger:

        using Microsoft.Azure.Engagement;

-   Indsætte `EngagementAgent.Instance.Init` i den `Application_Launching` metode:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
          EngagementAgent.Instance.Init();
        }

-   Indsætte `EngagementAgent.Instance.OnActivated` i den `Application_Activated` metode:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
        }

> [AZURE.WARNING] Vi kraftigt ikke skal du for at tilføje aftale initialiseringen i et andet sted i dit program. Dog være opmærksom på, på `EngagementAgent.Instance.Init` metode kører på en dedikeret tråd og ikke på UI-tråd.

##<a name="basic-reporting"></a>Grundlæggende rapportering

### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Anbefalet metode: overbelastning din `PhoneApplicationPage` klasser

For at aktivere rapport over alle de logfiler, der kræves, før aftale til at beregne brugere, sessioner, aktiviteter, går ned og tekniske statistik, kan du blot gøre alle dine `PhoneApplicationPage` underordnede klasser nedarver fra den `EngagementPage` klasser.

Her er et eksempel på, hvordan du gør dette for en side i dit program. Du kan gøre det samme for alle sider på dit program.

#### <a name="c-source-file"></a>C#-kildefil

Rediger siden `.xaml.cs` fil:

-   Føje til din `using` sætninger:

        using Microsoft.Azure.Engagement;

-   Erstatte `PhoneApplicationPage` med `EngagementPage` :

**Uden at aftale:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Med aftale:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.WARNING] Hvis siden nedarver fra den `OnNavigatedTo` metode, skal du huske at lade den `base.OnNavigatedTo(e)` opkald. Ellers rapporteres aktiviteten ikke. Faktisk på `EngagementPage` ringer `StartActivity` i den `OnNavigatedTo` metode.

#### <a name="xaml-file"></a>XAML-fil

Rediger siden `.xaml` fil:

-   Føje til din navneområder erklæringer:

        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Erstatte `phone:PhoneApplicationPage` med `engagement:EngagementPage` :

**Uden at aftale:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Med aftale:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
        
            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Tilsidesætte standardfunktionsmåden

Som standard rapporteres klassenavnet på siden som på aktivitetens navn med uden ekstra. Hvis klassen bruger suffikset "Side", kan aftale også fjerne den.

Hvis du vil tilsidesætte standardfunktionsmåden for navnet, skal du blot tilføje dette i din kode:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Hvis du vil rapportere nogle ekstra oplysninger med din aktivitet, kan du tilføje dette i din kode:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Disse metoder kaldes inde fra den `OnNavigatedTo` metode til siden.

### <a name="alternate-method-call-startactivity-manually"></a>Alternative metode: ringe `StartActivity()` manuelt

Hvis du ikke kan eller ikke vil overbelastning din `PhoneApplicationPage` klasser, kan du i stedet starte dine aktiviteter ved at ringe til `EngagementAgent` metoder direkte.

Vi anbefaler opkald `StartActivity` i din `OnNavigatedTo` metode til din PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [AZURE.IMPORTANT] Kontrollér, at du afslutte sessionen korrekt.
>
> SDK automatisk opkald på `EndActivity` metode, når programmet er lukket. Det er derfor **meget** anbefales til at ringe til den `StartActivity` metode, når aktiviteten på brugeren, ændre, og til **aldrig** kald den `EndActivity` metode. Denne metode sender en meddelelse til serveren aftale, den aktuelle bruger har forladt programmet, og dette påvirker alle programmet logfiler.

##<a name="advanced-reporting"></a>Avanceret rapportering

Du kan du få rapporten til bestemte programhændelser, fejl og job, gør du ved at bruge de andre metoder findes i den `EngagementAgent` klasse. Aftale API kan bruge alle aftales avancerede funktioner.

Se, [hvordan du bruger de avancerede Mobile aftale mærkning API i din Windows Phone Silverlight-app](mobile-engagement-windows-phone-use-engagement-api.md)for yderligere oplysninger.

##<a name="advanced-configuration"></a>Avanceret konfiguration

### <a name="disable-automatic-crash-reporting"></a>Deaktivere automatisk nedbrud rapportering

Du kan deaktivere den automatiske nedbrud rapportering funktion i aftale. Derefter, når der opstår en ikke-afviklet undtagelse, aftale ikke gøre noget.

> [AZURE.WARNING] Hvis du vil deaktivere denne funktion, være opmærksom på, når der sker ikke-afviklet nedbrud i din app, ikke sende aftale går ned **, og** den ikke lukkes session og sager.

Tilpas din konfiguration afhængigt af den måde, du har defineret den for at deaktivere automatisk nedbrud rapportering skal:

#### <a name="from-engagementconfigurationxml-file"></a>Fra `EngagementConfiguration.xml` fil

Angiv rapport nedbrud til `false` mellem `<reportCrash>` og `</reportCrash>` mærker.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Fra `EngagementConfiguration` objekt på kørselstidspunktet

Angiv rapport nedbrud til falsk ved hjælp af EngagementConfiguration objektet.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Burst-tilstand

Som standard gemmes aftale servicerapporter i realtid. Hvis dit program rapporterer logfiler meget ofte, er det bedre at bufferen logfiler og for at rapportere dem på én gang på almindelig gangen base (dette kaldes "burst-tilstand").

Kalde metoden for at gøre det:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argumentet er en værdi i **millisekunder**. Når som helst, hvis du vil genaktivere realtid logføring, lige kalde metoden uden en parameter eller med værdien 0.

Tilstanden burst lidt øge levetiden, men har indflydelse på den aftale skærm: alle sessioner og -job varighed afrundes til grænseværdi for burst (altså sessioner og job kortere end grænseværdi for burst ikke muligvis er synlige). Det anbefales at bruge en burst grænseværdi ikke længere end 30000 (30s). Du skal være opmærksom på, gemmes loggene er begrænset til 300 elementer. Hvis det er for lang tid at sende kan du miste nogle logfiler.

> [AZURE.WARNING] Grænseværdi for burst kan ikke konfigureres til en periode, der er mindre end et sekund. Hvis du forsøger at gøre dette, vises i SDK en sporing med fejl og automatisk nulstiller til standardværdien, det vil sige, nul sekunder. Dette udløser SDK for at rapportere loggene i realtid.
 

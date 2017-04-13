<properties 
    pageTitle="Windows Universal Apps aftale SDK Integration" 
    description="Hvordan du integrerer Azure Mobile aftale med Windows Universal Apps"                  
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
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="windows-universal-apps-engagement-sdk-integration"></a>Windows Universal Apps aftale SDK Integration

> [AZURE.SELECTOR] 
- [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Denne procedure beskriver den nemmeste måde at aktivere aftales analyser og overvågning funktioner i Windows Universal programmet.

Følgende trin er nok til at aktivere rapporten af logfiler, der skal bruges til at beregne alle statistiske oplysninger om brugere, sessioner, aktiviteter, går ned og Technicals. Rapport over logfiler, der skal bruges til at beregne andre statistikker som begivenheder, fejl og job skal udføres manuelt ved hjælp af aftale API (se [Sådan bruges de avancerede Mobile aftale mærkning API i din Windows Universal app](mobile-engagement-windows-store-use-engagement-api.md) , fordi disse statistikker er afhænger af programmet.

## <a name="supported-versions"></a>Understøttede versioner

De Mobile aftale SDK for Windows Universal-Apps kan kun integreres i Windows Runtime og Universal Windows-Platform programmer målretning af:

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (stationære og bærbare familier)

> [AZURE.NOTE] Hvis du målretter Windows Phone Silverlight, skal du henvise til [Windows Phone Silverlight integration procedure](mobile-engagement-windows-phone-integrate-engagement.md).

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Installere Mobile aftale Universal Apps SDK

### <a name="all-platforms"></a>Alle platforme

Den Mobile aftale SDK for Windows Universal App er tilgængelig som en Nuget pakke kaldet *MicrosoftAzure.MobileEngagement*. Du kan installere det fra Visual Studio Nuget Package Manager.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x og Windows Phone 8.1

NuGet automatisk installerer SDK ressourcer i den `Resources` mappe i roden af projektet programmet på computeren.

### <a name="windows-10-universal-windows-platform-applications"></a>Windows 10 Universal Windows-Platform programmer

NuGet implementerer ikke automatisk SDK ressourcer i UWP programmet endnu. Du behøver at gøre det manuelt, indtil ressourcer installation igen i NuGet:

1.  Åbn din Stifinder.
2.  Gå til følgende placering (**x.x.x** er versionen af aftale, du installerer): *% BRUGERPROFIL %\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3.  Træk og slip mappen **ressourcer** fra Stifinder i roden af dit projekt i Visual Studio.
4.  Vælg dit projekt i Visual Studio og aktivere ikonet **Vis alle filer** oven på **Solution Explorer**.
5.  Nogle filer er ikke inkluderet i projektet. Hvis du vil importere Højreklik dem på én gang på mappen **ressourcer, der** skal **udelukkes fra project** og derefter en anden Højreklik på mappen **ressourcer** skal **Medtag i project** igen medtage hele mappen. Alle filer fra mappen **ressourcer** er nu inkluderet i projektet.

## <a name="add-the-capabilities"></a>Tilføje funktionerne

Aftale SDK skal nogle funktioner i Windows SDK for at kunne fungere korrekt.

Åbn din `Package.appxmanifest` filen, og Sørg for, at følgende funktioner er erklæret:

-   `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Initialiseret aftale SDK

### <a name="engagement-configuration"></a>Aftale konfiguration

Aftale konfigurationen er centraliserede i den `Resources\EngagementConfiguration.xml` fil af projektet.

Redigere denne fil til at angive:

-   Dit program forbindelsesstreng mellem mærker `<connectionString>` og `<\connectionString>`.

Hvis du vil angive den på kørselstidspunktet i stedet, kan du ringe til følgende metode før aftale agent initialisering:
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

Forbindelsesstrengen for dit program vises på portalen Azure klassisk.

### <a name="engagement-initialization"></a>Aftale initialisering

Når du opretter et nyt projekt, en `App.xaml.cs` filen genereres. Denne klasse nedarver fra `Application` og indeholder mange vigtige metoder. Det bruges også initialiseret aftale SDK.

Ændre den `App.xaml.cs`:

-   Føje til din `using` sætninger:

        using Microsoft.Azure.Engagement;

-   Definere en metode til at dele aftale initialiseringen én gang for alle opkald:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
        
          // or
        
          EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Ringe til `InitEngagement` i den `OnLaunched` metode:

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
          InitEngagement(e);
        }

-   Når dit program er startet ved hjælp af et brugerdefineret farveskema, et andet program eller kommandolinjen og klik derefter på `OnActivated` metode kaldes. Du skal også starte aftale SDK, når din app er aktiveret. Gør du ved at tilsidesætte `OnActivated` metode:

        protected override void OnActivated(IActivatedEventArgs args)
        {
          InitEngagement(args);
        }

> [AZURE.IMPORTANT] Vi kraftigt ikke skal du for at tilføje aftale initialiseringen i et andet sted i dit program.

## <a name="basic-reporting"></a>Grundlæggende rapportering

### <a name="recommended-method-overload-your-page-classes"></a>Anbefalet metode: overbelastning din `Page` klasser

For at aktivere rapport over alle de logfiler, der kræves, før aftale til at beregne brugere, sessioner, aktiviteter, går ned og tekniske statistik, kan du blot gøre alle dine `Page` underordnede klasser nedarver fra den `EngagementPage` klasser.

Her er et eksempel på, hvordan du gør dette for en side i dit program. Du kan gøre det samme for alle sider på dit program.

#### <a name="c-source-file"></a>C#-kildefil

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

> [AZURE.IMPORTANT] Hvis siden tilsidesætter den `OnNavigatedTo` metode, skal du sørge for at ringe `base.OnNavigatedTo(e)`. Ellers aktiviteten ikke rapporteres (den `EngagementPage` opkald `StartActivity` i dets `OnNavigatedTo` metode).

#### <a name="xaml-file"></a>XAML-fil

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

#### <a name="override-the-default-behaviour"></a>Tilsidesætte standard adfærd

Som standard rapporteres klassenavnet på siden som på aktivitetens navn med uden ekstra. Hvis klassen bruger suffikset "Side", kan aftale også fjerne den.

Hvis du vil tilsidesætte standard adfærd for navnet, skal du blot tilføje dette i din kode:

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

Hvis du ikke kan eller ikke vil overbelastning din `Page` klasser, kan du i stedet starte dine aktiviteter ved at ringe til `EngagementAgent` metoder direkte.

Vi anbefaler, at ringe til `StartActivity` i din `OnNavigatedTo` metode til siden.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Kontrollér, at du afslutte sessionen korrekt.
> 
> Windows Universal SDK automatisk opkald på `EndActivity` metode, når programmet er lukket. Det er derfor **meget** anbefales til at ringe til den `StartActivity` metode, når aktiviteten på brugeren, ændre, og til **aldrig** Ring til den `EndActivity` metode, denne metode sender til aftale server aktuelle bruger har forlade programmet, er påvirker alle programmet logfiler.

## <a name="advanced-reporting"></a>Avanceret rapportering

Du kan du få rapporten til bestemte programhændelser, fejl og job, gør du ved at bruge de andre metoder findes i den `EngagementAgent` klasse. Aftale API kan bruge alle aftales avancerede funktioner.

Se, [hvordan du bruger de avancerede Mobile aftale mærkning API i din Windows Universal app](mobile-engagement-windows-store-use-engagement-api.md)for yderligere oplysninger.

##<a name="advanced-configuration"></a>Avanceret konfiguration

### <a name="disable-automatic-crash-reporting"></a>Deaktivere automatisk nedbrud rapportering

Du kan deaktivere den automatiske nedbrud rapportering funktion i aftale. Derefter, når der opstår en ikke-afviklet undtagelse, aftale ikke gøre noget.

> [AZURE.WARNING] Hvis du vil deaktivere denne funktion, være opmærksom på, når der sker ikke-afviklet nedbrud i din app, aftale ikke vil sende nedbrud **og** ikke lukker session og sager.

Tilpas din konfiguration afhængigt af den måde, du har defineret den for at deaktivere automatisk nedbrud rapportering skal:

#### <a name="from-engagementconfigurationxml-file"></a>Fra `EngagementConfiguration.xml` fil

Angiv rapport nedbrud til `false` mellem `<reportCrash>` og `</reportCrash>` mærker.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Fra `EngagementConfiguration` objekt på kørselstidspunktet

Angiv rapport nedbrud til falsk ved hjælp af EngagementConfiguration objektet.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Burst-tilstand

Som standard gemmes aftale servicerapporter i realtid. Hvis dit program rapporterer logfiler meget ofte, er det bedre at bufferen logfiler og for at rapportere dem på én gang på almindelig gangen base (dette kaldes "burst-tilstand").

Kalde metoden for at gøre det:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argumentet er en værdi i **millisekunder**. Når som helst, hvis du vil genaktivere realtid logføring, lige kalde metoden uden en parameter eller med værdien 0.

Tilstanden burst lidt øge levetiden, men har indflydelse på den aftale skærm: alle sessioner og -job varighed afrundes til grænseværdi for burst (altså sessioner og job kortere end grænseværdi for burst ikke muligvis er synlige). Det anbefales at bruge en burst grænseværdi ikke længere end 30000 (30s). Du skal være opmærksom på, gemmes loggene er begrænset til 300 elementer. Hvis det er for lang tid at sende kan du miste nogle logfiler.

> [AZURE.WARNING] Grænseværdi for burst kan ikke konfigureres til en mindre end 1s periode. Hvis du forsøger at gøre det, i SDK vises en sporing med fejl og nulstiller automatisk til standardværdien, det vil sige 0s. Dette udløser SDK for at rapportere loggene i realtid.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
 

<properties 
    pageTitle="Universal Apps til Windows SDK opgradere procedurer" 
    description="Universel Apps til Windows SDK opgraderes procedurer til Azure Mobile aftale"                     
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

#<a name="windows-universal-apps-sdk-upgrade-procedures"></a>Universal Apps til Windows SDK opgradere procedurer

Hvis du har allerede integreret en ældre version af aftale i dit program, har du overveje følgende punkter, når du opgraderer SDK.

Du kan være nødvendigt at følge flere procedurer, hvis du ikke har set flere versioner af SDK. Eksempelvis hvis du overfører fra 0.10.1 til 0.11.0, du skal først følge fremgangsmåden "fra 0.9.0 til 0.10.1" derefter fremgangsmåden "fra 0.10.1 til 0.11.0".

##<a name="from-330-to-340"></a>Fra 3,3,0 til 3.4.0

### <a name="test-logs"></a>Test logfiler

Console logfiler, der er oprettet med SDK kan nu være aktiveret/deaktiveret/filtreret. Hvis du vil tilpasse, skal du opdatere egenskaben `EngagementAgent.Instance.TestLogEnabled` til en af den værdi, der er tilgængelige fra den `EngagementTestLogLevel` optælling, for eksempel:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Ressourcer

Rækkevidde overlejring er blevet forbedret. Det er en del af SDK NuGet pakke ressourcer.

Mens opgradering til den nye version af SDK, du kan vælge, om du vil beholde dine eksisterende filer fra mappen overlejring af dine ressourcer eller ej:

* Hvis den forrige overlejring arbejde for dig, eller du integrerer den `WebView` elementer manuelt derefter du kan vælge at beholde dine eksisterende filer, den fungerer stadig. 
* Hvis du vil opdatere til den nye overlejring, skal du bare erstatte hele `overlay` mappe fra dine ressourcer med et nyt fra SDK-pakken (UWP apps: efter opgraderingen, kan du få den nye mappe overlejring % BRUGERPROFIL %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] Ved hjælp af den nye overlejring overskriver alle tilpasninger, der er foretaget i den tidligere version.

##<a name="from-320-to-330"></a>Fra 3.2.0 til 3,3,0

### <a name="resources"></a>Ressourcer
Dette trin vedrører tilpassede ressourcer. Hvis du har tilpasset de ressourcer, der leveres af SDK (html, billeder, overlejring), derefter det nødvendigt at sikkerhedskopiere dem før du opgraderer og genanvende dine tilpasninger på opgraderede ressourcer.

##<a name="from-310-to-320"></a>Fra 3.1.0 til 3.2.0

### <a name="resources"></a>Ressourcer
Dette trin vedrører tilpassede ressourcer. Hvis du har tilpasset de ressourcer, der leveres af SDK (html, billeder, overlejring), derefter det nødvendigt at sikkerhedskopiere dem før du opgraderer og genanvende dine tilpasninger på opgraderede ressourcer.

### <a name="webview-integration"></a>Integration af webvisning
Forbedringer til svarer til anden enhed formular faktorer blev introduceret i denne version. Sørg for, at din integration af webvisning passer med følgende:

I din XAML side ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

Og i tilknyttede .cs filen:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
            
              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }
              
              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif
            
              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }
            
              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

##<a name="from-200-to-300"></a>Fra 2.0.0 til 3.0.0

### <a name="resources"></a>Ressourcer
Dette trin vedrører tilpassede ressourcer. Hvis du har tilpasset de ressourcer, der leveres af SDK (html, billeder, overlejring), derefter det nødvendigt at sikkerhedskopiere dem før du opgraderer og genanvende dine tilpasninger på opgraderede ressourcer.

##<a name="from-111-to-200"></a>Fra 1.1.1 til 2.0.0

Følgende fremgangsmåde beskriver, hvordan til at overføre en SDK-integration fra tjenesten Capptain, som Capptain SAS til en app drevet af Azure Mobile aftale. 

> [Azure.IMPORTANT] Capptain og Mobile aftale er ikke de samme tjenester, og den fremgangsmåde, der er angivet under fremhæves kun Sådan overføres klient-app. Overføre SDK i appen overføres ikke dine data fra Capptain servere til Mobile aftale servere

Hvis du overfører fra en tidligere version, skal du se webstedet Capptain for at overføre til 1.1.1 først og derefter anvende følgende procedure

### <a name="nuget-package"></a>Nuget pakke

Udskift **Capptain.WindowsPhone** ved **MicrosoftAzure.MobileEngagement** Nuget pakke.

### <a name="applying-mobile-engagement"></a>Anvende Mobile aftale

SDK bruges termen `Engagement`. Du vil opdatere dit projekt, så det svarer til denne ændring.

Du skal fjerne din aktuelle Capptain nuget pakke. Overvej, fjernes alle ændringerne i Capptain ressourcer mappe. Hvis du vil beholde filerne, og derefter oprette en kopi af dem.

Installere den nye Microsoft Azure aftale nuget pakke herefter på projektet. Du kan finde den direkte på [nuget websted]. eller her indeks. Denne handling erstatter alle ressourcefiler, der bruges af aftale og tilføjer nye aftale DLL-filen til dit projekt-referencer.

Du skal rydde projekt-referencer ved at slette Capptain DLL referencer. Hvis du ikke gør det, versionen af Capptain er i konflikt, og der sker fejl.

Hvis du har tilpasset Capptain ressourcer, kopiere din gamle filer indhold, og sæt dem ind i de nye aftale-filer. Vær opmærksom på, at både xaml og cs filer skal opdateres.

Når du har udført disse trin er kun nødvendigt at erstatte gamle Capptain refereres til ved de nye aftale referencer.

1. Alle navneområder Capptain skal opdateres.

    Før overførsel:
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Efter overflytningen:
    
        using Microsoft.Azure.Engagement;

2. Alle Capptain klasser, der indeholder "Capptain" skal indeholde "Aftale".

    Før overførsel:
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Efter overflytningen:
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. For xaml-filerne ændre Capptain navneområde og attributter også.

    Før overførsel:
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Efter overflytningen:
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Overlappende tegningssidens
    > [AZURE.IMPORTANT] Markeringsoverlejringer også ændringer. Den nye navneområde er `Microsoft.Azure.Engagement.Overlay`. Det skal bruges i både xaml og cs filer. Desuden `CapptainGrid` skal navngives `EngagementGrid`, `capptain_notification_content` og `capptain_announcement_content` navngives `engagement_notification_content` og `engagement_announcement_content`.
    
    For overlejring:
    
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
    
    Det bliver:
    
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>

5. For de andre ressourcer som Capptain billeder og HTML-filer, Vær opmærksom på, at de også er blevet omdøbt for at bruge "Aftale".

### <a name="project-declaration"></a>Project-erklæring

På Package.appxmanifest `File Type Associations` er blevet opdateret fra:

 -   capptain\_når\_indhold for at aftale\_når\_indhold
 -   capptain\_log\_fil for at aftale\_log\_fil

### <a name="application-id--sdk-key"></a>Program-ID / SDK-tasten

Aftale anvender en forbindelsesstreng. Du ikke behøver at angive et program-ID og en SDK nøgle med Mobile aftale, skal du kun angive en forbindelsesstreng. Du kan konfigurere det på EngagementConfiguration filen.

Aftale konfigurationen kan angives din `Resources\EngagementConfiguration.xml` fil af projektet.

Redigere denne fil til at angive:

-   Dit program forbindelsesstreng mellem mærker `<connectionString>` og `<\connectionString>`.

Hvis du vil angive den på kørselstidspunktet i stedet, kan du ringe til følgende metode før aftale agent initialisering:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

Forbindelsesstrengen for dit program vises på portalen Azure klassisk.

### <a name="items-name-change"></a>Elementer Navneændring

Alle elementer med navnet *capptain* er blevet udnævnt *aftale*. På samme måde for *Capptain* til *aftale*.

Eksempler på ofte anvendte Capptain elementer:

-   CapptainConfiguration hedder nu EngagementConfiguration
-   CapptainAgent hedder nu EngagementAgent
-   CapptainReach hedder nu EngagementReach
-   CapptainHttpConfig hedder nu EngagementHttpConfig
-   GetCapptainPageName hedder nu GetEngagementPageName

Bemærk, at omdøbe også påvirker tilsidesat metoder.

 

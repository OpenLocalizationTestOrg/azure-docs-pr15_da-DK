<properties 
    pageTitle="Når Universal Apps til Windows SDK-Integration" 
    description="Hvordan du integrerer Azure Mobile aftale rækkevidde med Windows Universal Apps"
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

# <a name="windows-universal-apps-reach-sdk-integration"></a>Når Universal Apps til Windows SDK-Integration

Du skal følge integration fremgangsmåden i [Windows Universal aftale SDK Integration](mobile-engagement-windows-store-integrate-engagement.md) før følge denne vejledning.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Integrere aftale når SDK i projektet Universal til Windows

Du har ikke noget, der skal tilføje. `EngagementReach`referencer og ressourcer, der allerede findes i dit projekt.

> [AZURE.TIP] Du kan tilpasse billeder, der er placeret i den `Resources` mappe med dit projekt, især brand-ikon (standard til ikonet aftale). På Universal Apps du kan også flytte den `Resources` mappe på din delt projekt til at dele dens indhold mellem apps, men du nødt til at holde det `Resources\EngagementConfiguration.xml` filen på standardplaceringen, som det er afhængige af platforme.

## <a name="enable-the-windows-notification-service"></a>Aktivere tjenesten Windows meddelelse

### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x og Windows Phone 8.1 kun

For at bruge **Windows Beskedtjeneste** (kaldes WNS) i din `Package.appxmanifest` filen på `Application UI` Klik på `All Image Assets` i feltet venstre bot. Til højre for feltet i `Notifications`, ændre `toast capable` fra `(not set)` til `(Yes)`.

### <a name="all-platforms"></a>Alle platforme

Du har brug at synkronisere din app til din Microsoft-konto og til den aftale platform. I dette skal du oprette en konto eller logger på [windows-udviklercenter](https://dev.windows.com). Oprette et nyt program efter, og find SID og hemmeligt nøgle. Gå på din app indstillingen i på front end aftale `native push` og indsætte dine legitimationsoplysninger. Herefter højreklikke på dit projekt, Vælg `store` og `Associate App with the Store...`. Du skal blot vælge det program, du har oprettet før synkronisere den.

## <a name="initialize-the-engagement-reach-sdk"></a>Initialiseret aftale rækkevidde SDK

Ændre den `App.xaml.cs`:

-   Indsætte `EngagementReach.Instance.Init` lige efter `EngagementAgent.Instance.Init` i din `InitEngagement` metode:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
          EngagementReach.Instance.Init(e);
        }

    Den `EngagementReach.Instance.Init` kører i en dedikeret tråd. Du behøver ikke at gøre det selv.

> [AZURE.NOTE] Hvis du bruger pushmeddelelser et andet sted i dit program har du [dele din opslagsnål kanal](#push-channel-sharing) med aftale når.

## <a name="integration"></a>Integration

Aftale er der to metoder til at tilføje rækkevidde i app'en bannere og intramuskulært visninger for meddelelser og afstemninger i dit program: overlejring integrationen og manuel integration web visninger. Du skal ikke kombinere begge metode på samme side.

Valget mellem to integrationen kan opsummeres på denne måde:

-   Du kan vælge overlejring integrationen, hvis dine sider allerede nedarver fra en Agent `EngagementPage`, er det kun et spørgsmål om at erstatte `EngagementPage` ved `EngagementPageOverlay` og `xmlns:engagement="using:Microsoft.Azure.Engagement"` ved `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` i dine sider.
-   Du kan vælge visningerne web manuel integration, hvis du vil placere nøjagtigt når Brugergrænsefladen i dine sider, eller hvis du ikke vil føje en anden nedarvning af niveau til dine sider. 

### <a name="overlay-integration"></a>Overlappende integration

Aftale overlejring tilføjer dynamisk brugergrænsefladeelementer, der bruges til at vise rækkevidde kampagner på siden. Hvis overlejringen ikke passer til dit layout bør du overveje visningerne web manuel integration i stedet.

I din .xaml filændring `EngagementPage` henviser til`EngagementPageOverlay`

-   Føje til din navneområder erklæringer:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Erstatte `engagement:EngagementPage` med `engagement:EngagementPageOverlay`:

**Med EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
        
            <!-- Your layout -->
        </engagement:EngagementPage>

**Med EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
        
            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Mærk derefter siden i i filen .cs `EngagementPageOverlay` i stedet for `EngagementPage` og importere `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

-   Erstatte `EngagementPage` med `EngagementPageOverlay`:

**Med EngagementPage:**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Med EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Aftale overlejring tilføjer en `Grid` element øverst på siden bestående af layoutet og to `WebView` elementer én for banneret og den anden computer for visningen intramuskulært.

Du kan tilpasse overlejring elementer direkte i den `EngagementPageOverlay.cs` fil.

### <a name="web-views-manual-integration"></a>Web visninger manuel integration

Rækkevidde vil søge dine sider for to `WebView` elementer, der er ansvarlig for at vise banneret og visningen intramuskulært. Det eneste, du skal gøre er at tilføje disse to `WebView` elementer et sted i dine sider her er et eksempel:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


I dette eksempel på `WebView` elementer strækkes, så det passer til deres beholder, der automatisk størrelse dem igen i tilfælde af skærmen rotation eller vinduet størrelse ændring.

> [AZURE.WARNING] Det er vigtigt at beholde den samme naming `engagement_notification_content` og `engagement_announcement_content` for den `WebView` elementer. Rækkevidde identificerer dem efter deres navn. 

## <a name="handle-datapush-optional"></a>Håndtere datapush (valgfrit)

Hvis du vil dit program kunne modtage rækkevidde data skubber, har du implementere to hændelser på klassen, EngagementReach:

Tilføj i App.xaml.cs i App() parametre:

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };
            
            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Du kan se, at tilbagekald for hver enkelt metode returnerer en boolesk værdi. Aftale sender feedback til dens back end-efter afsendelse data opslagsnål. Hvis tilbagekaldet returnerer falsk, den `exit` feedback bliver send. Ellers skal det vil være `action`. Hvis ingen tilbagekald er indstillet til hændelserne, den `drop` feedback kommer tilbage til aftale.

> [AZURE.WARNING] Aftale er ikke kunne modtage multipla feedback for et enkelt tryk data. Hvis du vil angive flere programmer på en begivenhed, skal du være opmærksom på, at feedbacken svarer til den sidste sendt en. I dette tilfælde, anbefaler vi altid returnerer samme værdi til at undgå forvirring feedback på front-end-program.

## <a name="customize-ui-optional"></a>Tilpasse UI (valgfrit)

### <a name="first-step"></a>Første trin

Vi giver dig mulighed at tilpasse rækkevidde brugergrænseflade.

For at gøre det, du skal oprette en underklasse af den `EngagementReachHandler` klasse.

**Eksempel på kode:**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Derefter skal du indstille indholdet af den `EngagementReach.Instance.Handler` med brugerdefinerede objektet i din `App.xaml.cs` klasse inden for den `App()` metode.

**Eksempel på kode:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [AZURE.NOTE]Som standard bruger aftale implementeringen af `EngagementReachHandler`.
> Du behøver ikke at oprette din egen, og hvis du gør det, du behøver ikke at tilsidesætte alle metode. Standardfunktionen er at vælge objektet aftale base.

### <a name="web-view"></a>Webvisning

Som standard bruger rækkevidde de ressourcer, der er integreret i DLL-filen til at vise meddelelser og sider.

Hvis du vil angive en komplet tilpasning muligheder bruge vi kun webvisning. Hvis du vil tilpasse layout, skal tilsidesætte direkte ressourcer filerne `EngagementAnnouncement.html` og `EngagementNotification.html`. Aftale skal al kode i `<body></body>` til at køre korrekt. Men du kan tilføje mærke ydre `engagement_webview_area`.

Du kan dog beslutter at bruge dit eget ressourcer.

Du kan tilsidesætte `EngagementReachHandler` metoder i din underklasse til at fortælle aftale bruge dine layout, men Sørg for at integreret aftale ordning:

**Eksempel på kode:**
            
            // In your subclass of EngagementReachHandler
            
            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


AnnouncementHTML er som standard `ms-appx-web:///Resources/EngagementAnnouncement.html`. Repræsenterer den html-fil, designe indholdet af en opslagsnål meddelelse (tekst om Familieforøgelse, Web anoucement og afstemning meddelelse). AnnouncementName er `engagement_announcement_content`. Det er navnet på webvisning designet i xaml-siden.

NotfificationHTML er `ms-appx-web:///Resources/EngagementNotification.html`. Repræsenterer den html-fil, designe besked om en opslagsnål meddelelse. NotfificationName er `engagement_notification_content`. Det er navnet på webvisning designet i xaml-siden.

### <a name="customization"></a>Tilpasning

Du kan tilpasse meddelelsen og om Familieforøgelse webvisning har du ønsker det, hvis du bevare aftale objekt. Tage omtanke, webvisning objekt er beskrevet tre gange - første gang i din xaml, anden gang i filen .cs i metoden "setwebview()" og tredje gang i HTML-filen.

-   I din xaml beskrive du den aktuelle grafiske layout webvisning komponent.
-   Du kan definere "setwebview()", hvor du angive dimensionen af de to webvisning (besked om, om Familieforøgelse) i filen .cs. Det er meget effektive, når programmet ændre størrelsen på.
-   I den aftale HTML-fil beskriver vi webvisning indholdet, design og elementer placeringer mellem hinanden.

### <a name="launch-message"></a>Start meddelelse

Når en bruger klikker på en systemmeddelelse (toast), aftale starter programmet, indlæse indholdet af push-meddelelser, og få vist siden til det tilsvarende campaign.

Der er en forsinkelse mellem lanceringen af programmet og visning af siden (afhængigt af hastigheden på netværket).

For at angive til brugeren, noget ved indlæsning, bør du give en visuel oplysninger, som en statuslinje eller en statusindikator. Aftale kan ikke håndtere, selve, men indeholder nogle programmer for dig.

Tilføj for at implementere på tilbagekald, i App.xaml.cs i "Offentlige App() {}":

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
            
            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
            
            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Du kan angive tilbagekaldet i din "Offentlige App() {}" metode til din `App.xaml.cs` fil, helst før den `EngagementReach.Instance.Init()` opkald.

> [AZURE.TIP] Hver handler kaldes af Brugergrænsefladen tråd. Du behøver ikke at bekymre dig, når du bruger en meddelelsesboks eller noget brugergrænseflade-relaterede.

##<a id="push-channel-sharing"></a>Push-kanal Deling

Hvis du bruger pushmeddelelser til andre formål i dit program derefter skal du bruge funktionen af aftale SDK deling opslagsnål kanalen. Dette er at undgå mistede opslagsnål.

- Du kan angive dine egne opslagsnål kanal at aftale når initialiseringen. SDK bruge den i stedet for at anmode om en ny.

Opdatere initialiseringen aftale når med din opslagsnål kanal i den `InitEngagement` metode fra den `App.xaml.cs` fil:
    
    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

- Hvis du blot ønsker at bruge opslagsnål kanalen, når rækkevidde initialiseringen, og du kan angive et tilbagekald på aftale kommer til at få salgskanalen opslagsnål én gang er det også oprettet af SDK.

Angiv din tilbagekald på en hvilken som helst sted **efter** rækkevidde initialisering:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Tip til brugerdefineret farveskema

Vi giver dig brugerdefineret farveskema brug. Du kan sende forskellige typer URI fra front end aftale skal bruges i programmet på aftale. Standard farveskema som `http, ftp, ...` er administrere af Windows, et vindue er Spørg Hvis de ikke er nogen standardprogram, der er installeret på enheden. Du kan også oprette dine egne brugerdefinerede farveskema for dit program.

Den enkle måde til at angive et brugerdefineret farveskema i dit program er at åbne din `Package.appxmanifest` gå i `Declarations` panel. Vælg `Protocol` Rul felt i de tilgængelige erklæringer og tilføje den. Redigere den `Name` felt med din nye protokol beskedteksten navn.

Rediger nu for at bruge denne protokol, din `App.xaml.cs` med den `OnActivated` metode, og Husk også initialiseret aftale her:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);
            
              //TODO design action to do when app is launch
            
              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
            
                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion
 

<properties 
    pageTitle="Windows Phone Silverlight rækkevidde SDK Integration" 
    description="Hvordan du integrerer Azure Mobile aftale rækkevidde med Windows Phone Silverlight Apps"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article"
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone Silverlight rækkevidde SDK Integration

Du skal følge integration fremgangsmåden i [Windows Phone Silverlight aftale SDK Integration](mobile-engagement-windows-phone-integrate-engagement.md) før følge denne vejledning.

##<a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Integrere aftale når SDK i din Windows Phone Silverlight-projekt

Du har ikke noget, der skal tilføje. `EngagementReach`referencer og ressourcer, der allerede findes i dit projekt.

> [AZURE.TIP]  Du kan tilpasse billeder, der er placeret i den `Resources` mappe med dit projekt, især brand-ikon (standard til ikonet aftale).

##<a name="add-the-capabilities"></a>Tilføje funktionerne

Aftale når SDK skal nogle yderligere egenskaber.

Åbn din `WMAppManifest.xml` filen, og Sørg for, at følgende funktioner er erklæret:

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

Den første opgave bruges af tjenesten MPNS til at tillade visningen af toastbeskeden. Den anden opgave bruges til at integrere en browser-opgave i SDK.

Redigere den `WMAppManifest.xml` filen og tilføje i den `<Capabilities />` mærke:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##<a name="enable-the-microsoft-push-notification-service"></a>Aktivere Microsoft Pushmeddelelsestjeneste

For at kunne bruge **Microsoft Push Beskedtjeneste** (kaldes MPNS) din `WMAppManifest.xml` filen skal have en `<App />` mærke med en `Publisher` attributten er indstillet til navnet på projektet.

##<a name="initialize-the-engagement-reach-sdk"></a>Initialiseret aftale rækkevidde SDK

### <a name="engagement-configuration"></a>Aftale konfiguration

Aftale konfigurationen er centraliserede i den `Resources\EngagementConfiguration.xml` fil af projektet.

Redigere denne fil for at angive rækkevidde konfiguration:

-   *Valgfrit*, angive, om den oprindelige opslagsnål (MPNS) er aktiveret eller ikke mellem `<enableNativePush>` og `</enableNativePush>` -koder (`true` som standard).
-   *Valgfrit*, angive navnet på opslagsnål kanalen mellem `<channelName>` og `</channelName>` mærker, giver det samme, at dit program kan i øjeblikket bruger eller lade feltet være tomt.

Hvis du vil angive den på kørselstidspunktet i stedet, kan du ringe til følgende metode før aftale agent initialisering:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */
    
    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] Du kan angive navnet på MPNS opslagsnål kanal i dit program. Som standard opretter aftale et navn, der er baseret på program-id'et. Du behøver ikke at angive navnet dig selv, undtagen hvis du planlægger at bruge salgskanalen opslagsnål uden for aftale.

### <a name="engagement-initialization"></a>Aftale initialisering

Ændre den `App.xaml.cs`:

-   Føje til din `using` sætninger:

        using Microsoft.Azure.Engagement;

-   Indsætte `EngagementReach.Instance.Init` lige efter `EngagementAgent.Instance.Init` i `Application_Launching` :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

-   Indsætte `EngagementReach.Instance.OnActivated` i den `Application_Activated` metode:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

> [AZURE.IMPORTANT] Den `EngagementReach.Instance.Init` kører i en dedikeret tråd. Du behøver ikke at gøre det selv.

##<a name="app-store-submission-considerations"></a>App store bidrag overvejelser

Microsoft samt nogle regler, når du bruger pushbeskeder:

Afsnit 2.9 fra dokumentation Microsofts [Programpolitikker] :

1) Du skal Bed brugeren om at acceptere for at hente pushmeddelelser. Tilføje en metode til at deaktivere pushbeskeder i dine indstillinger.

Objektet EngagementReach indeholder to metoder til at administrere tilmelding ind/tilmelding ud, `EnableNativePush()` og `DisableNativePush()`. Du kan, for eksempel oprette en indstilling i indstillingerne med et til/fra for at deaktivere eller aktivere MPNS.

Du kan også vælge at deaktivere MPNS gennem konfigurationen af aftale\<windows phone-sdk-rækkevidde-konfiguration\>.

> 2.9.1) programmet skal først Beskriv meddelelser der skal gives og **hente brugerens udtrykkelig tilladelse (vælge i)**og **skal med en funktion, som brugeren kan fravælger modtager pushmeddelelser**. Alle meddelelser, der er angivet ved hjælp af Microsoft-tjeneste Push meddelelse skal stemme overens med den beskrivelse, der er angivet til brugeren og skal opfylde alle relevante [Programmet politikker]  [ Content Policies] og [Yderligere krav til bestemte typer af programmet].

2) Du skal ikke bruge for mange pushmeddelelser. Aftale håndterer meddelelser for dig.

> 2.9.2) programmet og brugen af Microsoft-Pushmeddelelsestjeneste skal ikke stærkt bruger netværkskapacitet eller båndbredde af Microsoft-Pushmeddelelsestjeneste eller ellers uberettiget belaster en Windows Phone eller andre Microsoft-enhed eller tjeneste med for meget pushmeddelelser, som fastsat af Microsoft begrundet skøn, og skal ikke skade eller forstyrre nogen Microsoft-netværk eller servere eller tredjepartsservere eller netværk, der har forbindelse til Microsoft-Pushmeddelelsestjeneste.

3) Ikke stole på MPNS til at sende criticals oplysninger. Aftale bruges MPNS, så denne regel gælder også for kampagner, der er oprettet i aftale front end.

> 2.9.3) the Microsoft Push Beskedtjeneste muligvis ikke bruges til at sende meddelelser, der er kommunikations kritiske eller på anden måde, kan det påvirke spørgsmål af liv eller dør, herunder uden begrænsning vigtige beskeder relateret til en læge enhed eller betingelse. MICROSOFT FRASKRIVER UDTRYKKELIGT SIG EN HVILKEN SOM HELST ANSVAR, BRUG AF MICROSOFT OPSLAGSNÅL BESKEDTJENESTE ELLER LEVERINGEN AF MICROSOFT BESKED OM TJENESTEN PUSHMEDDELELSER BLIVER UAFBRUDT, FEJL GRATIS ELLER PÅ ANDEN MÅDE GARANTI FOR, AT OPSTÅ PÅ GRUNDLAG I REALTID.

**Vi kan ikke garantere, at dit program overfører valideringsprocessen, hvis du ikke overholder disse anbefalinger.**

##<a name="handle-data-push-optional"></a>Håndtere data opslagsnål (valgfrit)

Hvis du vil dit program kunne modtage rækkevidde data skubber, har du implementere to hændelser på klassen, EngagementReach:

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

##<a name="customize-ui-optional"></a>Tilpasse UI (valgfrit)

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

Derefter skal du indstille indholdet af den `EngagementReach.Instance.Handler` med brugerdefinerede objektet i din `App.xaml.cs` klasse inden for den `Application_Launching` metode.

**Eksempel på kode:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [AZURE.NOTE] Som standard bruger aftale implementeringen af `EngagementReachHandler`. Du behøver ikke at oprette din egen, og hvis du gør det, du behøver ikke at tilsidesætte alle metode. Standardfunktionen er at vælge objektet aftale base.

### <a name="layouts"></a>Layout

Som standard bruger rækkevidde de ressourcer, der er integreret i DLL-filen til at vise meddelelser og sider.

Du kan dog beslutter at bruge dit eget ressourcer til at afspejle dit brand i disse komponenter.

Du kan tilsidesætte `EngagementReachHandler` metoder i din underklasse til at fortælle aftale til at bruge din layout:

**Eksempel på kode:**

    // In your subclass of EngagementReachHandler
    
    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetPollUri()
    {
       // return the path of your own xaml
    }
    
    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [AZURE.TIP] Den `CreateNotification` metoden kan returnere null. Meddelelsen vises ikke, og rækkevidde kampagnens fjernes.

For at forenkle implementeringen layout, giver vi også vores egen xaml, kan fungere som udgangspunkt for din kode. De er placeret i det aftale SDK arkiv (/ src/rækkevidde /).

> [AZURE.WARNING] De kilder, giver vi er nøjagtige samme dem, som vi bruger. Så hvis du vil ændre dem direkte, Glem ikke at ændre navneområdet og navnet.

### <a name="notification-position"></a>Meddelelse om placering

Som standard vises en appmeddelelse nederst til venstre side af programmet. Du kan ændre dette problem ved at tilsidesætte de `GetNotificationPosition` metode til at den `EngagementReachHandler` objekt.

    // In your subclass of EngagementReachHandler
    
    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

I øjeblikket, kan du vælge mellem de `BOTTOM` (standard) og `TOP` placeringer.

### <a name="launch-message"></a>Start meddelelse

Når en bruger klikker på en systemmeddelelse (toast), aftale starter appen, indlæse indholdet af push-meddelelser, og få vist siden til det tilsvarende campaign.

Der er en forsinkelse mellem lanceringen af programmet og visning af siden (afhængigt af hastigheden på netværket).

For at angive til brugeren, noget ved indlæsning, bør du give en visuel oplysninger, som en statuslinje eller en statusindikator. Aftale kan ikke håndtere, selve, men indeholder nogle programmer for dig.

Gør følgende for at implementere på tilbagekald:

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

Du kan angive tilbagekaldet i din `Application_Launching` metode til din `App.xaml.cs` fil, helst før den `EngagementReach.Instance.Init()` opkald.

> [AZURE.TIP] Hver handler kaldes af Brugergrænsefladen tråd. Du behøver ikke at bekymre dig, når du bruger en meddelelsesboks eller noget brugergrænseflade-relaterede.

[Programmet politikker]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Ekstra krav for programmet på bestemte typer]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 

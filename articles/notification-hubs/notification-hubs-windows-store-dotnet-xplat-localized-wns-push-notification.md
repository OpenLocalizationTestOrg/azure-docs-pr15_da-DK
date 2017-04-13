<properties
    pageTitle="Meddelelse om Hubs lokaliseret seneste nyheder selvstudium"
    description="Lær at bruge Azure meddelelse Hubs for at sende oversatte seneste nyheder meddelelser."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>Brug meddelelse Hubs til at sende oversatte seneste nyheder

> [AZURE.SELECTOR]
- [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

##<a name="overview"></a>Oversigt

Dette emne beskrives, hvordan du bruger funktionen **skabelon** i Azure meddelelse Hubs til at transmittere seneste nyheder meddelelser, der er blevet oversat ved sprog og enhed. I dette selvstudium starter du med Windows Store app oprettet i [Brug meddelelse Hubs sende seneste nyheder]. Når du er færdig, vil du kunne registrere for kategorier, du er interesseret i, skal du angive et sprog, som skal modtage meddelelserne, og modtage kun pushmeddelelser for de valgte kategorier på det pågældende sprog.


Der er to dele dette scenarie:

- Windows Store-app kan klienten enheder til at angive et sprog, og at abonnere på forskellige seneste nyhedskategorier.

- back end-udsender meddelelser, ved hjælp af **skabelonen** og **mærke** feautres af Azure meddelelse Hubs.



##<a name="prerequisites"></a>Forudsætninger

Du skal allerede har fuldført selvstudiet [Brug meddelelse Hubs sende seneste nyheder] og har den kode, der er tilgængelige, fordi dette selvstudium bygger direkte på denne kode.

Du skal også Visual Studio 2012 eller nyere.


##<a name="template-concepts"></a>Skabelon begreber

I [Brug meddelelse Hubs sende seneste nyheder] indbygget du en app, bruges **mærker** til at abonnere på meddelelser for forskellige nyhedskategorier.
Mange apps, men målrette flere markeder og kræver lokalisering. Det betyder, at indholdet af meddelelserne selv skal være lokaliseret og leveret til det korrekte sæt enheder.
I dette emne får du vist Sådan bruges funktionen **skabelon** i meddelelse Hubs til at levere nemt oversatte seneste nyheder beskeder.

Bemærk: en metode til at sende oversatte meddelelser er at oprette flere versioner af hver kode. For eksempel for at understøtte engelsk, fransk og Mandarin, vi har brug for tre forskellige mærker for verden nyheder: "world_en", "world_fr" og "world_ch". Vi herefter har til at sende en oversat version af internationale nyheder til hver af disse koder. Vi bruger skabeloner til at undgå udbredelsen af mærker og kravet om at sende flere meddelelser i dette emne.

Skabeloner er en metode til at angive, hvordan en bestemt enhed skal modtage en besked på et højt niveau. Skabelonen angiver formatet nøjagtige data ved at referere til egenskaber, der er en del af den meddelelse, som din app back end. I dette tilfælde har vi sender en landestandard agnostic meddelelse, der indeholder alle understøttede sprog:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Derefter sikrer vi, at registrere enheder med en skabelon, der refererer til den korrekte egenskab. For eksempel vil en Windows Store-app, som ønsker at modtage en simpel toast meddelelse tilmelde følgende skabelon med en hvilken som helst tilsvarende mærker:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



Skabeloner er en meget effektiv funktion, du kan læse mere om i vores [skabeloner](notification-hubs-templates-cross-platform-push-messages.md) artikel. 


##<a name="the-app-user-interface"></a>App-brugergrænseflade

Vi vil nu ændre appen Bryd nyheder, du oprettede i emnet [Brug meddelelse Hubs sende seneste nyheder] til at sende oversatte seneste nyheder ved hjælp af skabeloner.

I Windows Store-app:

Ændre din MainPage.xaml for at medtage en landestandard kombinationsboks:

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

##<a name="building-the-windows-store-client-app"></a>Opbygning af klienten Windows Store-app

1. Føje en landestandard-parameter til din *StoreCategoriesAndSubscribe* og *SubscribeToCateories* metoder i klasse beskeder.

        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }

        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }

    Bemærk, at i stedet for kalder metoden *RegisterNativeAsync* vi kalder *RegisterTemplateAsync*: Vi registrerer for en bestemt meddelelse format, hvor skabelonen er afhængig af den valgte landestandard. Vi giver også et navn til skabelonen ("localizedWNSTemplateExample"), fordi vi måske til at registrere mere end én skabelon (for eksempel en til toast meddelelser) og en felter og vi brug for at give dem et navn for at kunne opdatere eller slette dem.

    Bemærk, at hvis en enhed registrerer flere skabeloner med det samme mærke, en indgående meddelelse målretning, mærke, der medfører flere meddelelser leveres til enheden (en for hver skabelon). Denne funktionalitet er nyttig, når den samme logiske meddelelse har medføre flere visuelle meddelelser, for eksempel viser både en badge og en hilsen i et Windows Store-program.

2. Tilføj følgende metode for at hente den lagrede landestandard:

        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. Din knappen Opdater i din MainPage.xaml.cs, klik på handler ved hentning af den aktuelle værdi af kombinationsboksen landestandard og indsende til opkaldet til klassen meddelelser, som vist:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;

            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);

            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }


4. Til sidst i filen App.xaml.cs Sørg for at opdatere din `InitNotificationsAsync` metode til at hente den valgte landestandard og bruge den, når du abonnerer:

        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


##<a name="send-localized-notifications-from-your-back-end"></a>Sende oversatte meddelelser fra din back end-

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]






<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Brug meddelelse Hubs til at sende seneste nyheder]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx

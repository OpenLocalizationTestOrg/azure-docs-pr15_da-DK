<properties 
    pageTitle="Udjævne Streaming Windows Store App selvstudium | Microsoft Azure" 
    description="Lær, hvordan du bruger Azure Media Services til at oprette et C# Windows Store-program med et XML-MediaElement kontrolelement til afspilning udglattede Stream indhold." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>



#<a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Sådan oprettes en blød Streaming Windows Store-program

Problemfri Streaming klient SDK for Windows 8 kan udviklere bygge Windows Store-programmer, der kan afspille efter behov og direkte udglattede Streaming indhold. Ud over grundlæggende afspilning af udglattede Streaming indhold, SDK også indeholder mange funktioner som Microsoft PlayReady beskyttelse, niveau begrænsning, Live DVR lydkvalitet streame skifte, lytter efter statusopdateringer (såsom kvalitet niveau ændringer) og arrangementer, fejl osv. Se mere af de understøttede funktioner, [Produktbemærkninger](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Du kan finde flere oplysninger [Player Framework for Windows 8](http://playerframework.codeplex.com/). 

Dette selvstudium indeholder fire lektioner:

1. Oprette et grundlæggende udglattede Streaming Store tjenesteprogram
2. Tilføje en skyder for at styre medier status
3. Vælg udglattede Streaming Streams
4. Vælg udglattede Streaming numre

##<a name="prerequisites"></a>Forudsætninger

- Windows 8 32-bit eller 64-bit. Du kan få [Windows 8 Enterprise evaluering](http://msdn.microsoft.com/evalcenter/jj554510.aspx) fra MSDN.
- Visual Studio 2012 eller Visual Studio Express 2012 (eller en nyere version). Du kan få prøveversionen fra [her](http://www.microsoft.com/visualstudio/11/downloads).
- [Microsoft jævne Streaming klient-SDK til Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).


Den færdige løsning til hver lektion kan hentes fra MSDN udvikler kodeeksempler (kode Gallery): 

- [Lektion 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - en enkelt Windows 8 jævne Streaming Media Player 
- [Lektion 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - en enkelt Windows 8 udglattede Streaming Media Player med en skyder styre, 
- [Lektion 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - en Windows 8 jævne Streaming Media Player med valg af værdistrøm  
- [Lektion 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - en Windows 8 jævne Streaming Media Player med Spor markering.

##<a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lektion 1: Oprette et grundlæggende udglattede Streaming Store tjenesteprogram

I denne lektion, skal du oprette et Windows Store-program med et MediaElement kontrolelement for at afspille udglattede Stream indhold.  Det aktive program ser sådan ud:

![Eksempel på jævne Streaming Windows Store-program][PlayerApplication]
 
Du kan finde flere oplysninger om udvikling af Windows Store-program, [udvikle gode Apps til Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). I denne lektion indeholder følgende procedurer:

1.  Oprette et Windows Store-projekt
2.  Designe brugergrænsefladen (XAML)
3.  Ændre koden bag fil
4.  Samle og test af programmet

**Oprette et Windows Store-projekt**

1.  Kør Visual Studio 2012 eller nyere.
2.  Klik på **Ny**i menuen **filer** , og klik derefter på **projekt**.
3.  Dialogboksen nyt projekt, Skriv eller Vælg følgende værdier:

Navn|Værdi
---|---
Skabelongruppe|Installeret/skabeloner/Visual C# / Windows Store
Skabelon|Tom App (XAML)
Navn|SSPlayer
Placering|C:\SSTutorials
Løsningsnavn|SSPlayer
Opret mappe til løsning|(valgt)

4.  Klik på **OK**.

**Tilføje en reference problemfri Streaming klient SDK**

1.  Højreklik på **SSPlayer**fra Solution Explorer, og klik derefter på **Tilføj Reference**.
2.  Skriv eller Vælg følgende værdier:

Navn|Værdi
---|---
Referencegruppe|Windows/filtypenavne
Reference|Vælg Microsoft jævne Streaming klient-SDK til Windows 8 og Microsoft Visual C++ Runtime-pakken
    
3.  Klik på **OK**. 

Når du har tilføjet referencerne, skal du vælge den målrettede platform (x64 eller x86), tilføje referencer fungerer ikke for alle CPU-platformskonfiguration.  I solution explorer skal se du tilføjes gul advarsel mærke for disse referencer.

**Til at designe player-brugergrænseflade**

1.  Fra Solution Explorer skal du dobbeltklikke på **MainPage.xaml** for at åbne den i designvisning.
2.  Find den ** &lt;gitter&gt; ** og ** &lt;/Grid&gt; ** koder XAML-filen, og Indsæt følgende kode mellem de to mærker:

        <Grid.RowDefinitions>
            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
        </Grid.RowDefinitions>
        
        <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
        </StackPanel>

        <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
        </StackPanel>

        <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
        </StackPanel>

    Kontrolelementet MediaElement bruges til afspilning af medier. Skyderen med navnet sliderProgress bruges til at styre medier status i næste lektion.

3.  Tryk på **CTRL + S for** at gemme filen.

Kontrolelementet MediaElement understøtter ikke udglattede Streaming indhold out-of-box. Hvis du vil aktivere understøttelse af udglattede Streaming, skal du registrere udglattede Streaming byte-stream handler ved filtypenavn og MIME-type.  For at registrere skal bruge du metoden MediaExtensionManager.RegisterByteStremHandler for navneområdet Windows.Media.

I denne XAML-fil er visse hændelseshandlere knyttet til kontrolelementerne.  Du skal definere disse hændelseshandlere.

**Ændre koden bag ved fil**

1.  Højreklik på **MainPage.xaml**fra Solution Explorer, og klik derefter på **Vis programkode**.
2.  Øverst i filen, skal du tilføje følgende ved hjælp af sætning:

        using Windows.Media;

3.  Tilføje datamedlemmet følgende i starten af klassen **MainPage** :

        private MediaExtensionManager extensions = new MediaExtensionManager();

4.  Tilføj følgende to linjer i slutningen af **MainPage** parametre:

        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
        
5.  I slutningen af klassen **MainPage** Indsæt følgende kode:

        #region UI Button Click Events
        private void btnPlay_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Play();
            txtStatus.Text = "MediaElement is playing ...";
        }
        
        private void btnPause_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Pause();
            txtStatus.Text = "MediaElement is paused";
        }
        
        private void btnSetSource_Click(object sender, RoutedEventArgs e)
        {
            sliderProgress.Value = 0;
            mediaElement.Source = new Uri(txtMediaSource.Text);
        
            if (chkAutoPlay.IsChecked == true)
            {
                txtStatus.Text = "MediaElement is playing ...";
            }
            else
            {
                txtStatus.Text = "Click the Play button to play the media source.";
            }
        }
        
        private void btnStop_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Stop();
            txtStatus.Text = "MediaElement is stopped";
        }
        
        private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            txtStatus.Text = "Seek to position " + sliderProgress.Value;
            mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
        }
        #endregion

    Hændelseshandler sliderProgress_PointerPressed er defineret her.  Der findes flere works gøre for at få det arbejde, som vil blive dækket i næste lektion af dette selvstudium.
6.  Tryk på **CTRL + S for** at gemme filen.

Den færdige koden bag ved fil skal se sådan ud:

![Codeview i Visual Studio af udglattede Streaming Windows Store-program][CodeViewPic]

**Til at samle og teste programmet**

1.  Klik på **Configuration Manager**fra menuen **BUILD** .
2.  Ændre **aktive løsning platform** , så det svarer til din udviklingsplatform.
3.  Tryk på **F6** for at samle projektet. 
4.  Tryk på **F5** for at køre programmet.
5.  Øverst i programmet, kan du bruge standardværdi udglattede Streaming URL-adresse eller skrive et andet navn. 
6.  Klik på **Angiv kilde**. Da **Automatisk afspilning** er aktiveret som standard, skal medier afspilles automatisk.  Du kan styre medier ved hjælp af **afspille**, knapperne **Afbryd** og **Stop** .  Du kan styre medier lydstyrken ved hjælp af skyderen lodret.  Men den vandrette skyder for at styre medier status er ikke fuldt implementeret endnu. 

Du har fuldført lesson1.  I denne lektion bruger du en MediaElement kontrolelement til afspilning udglattede Streaming indhold.  I næste lektion føjer du en skyderen for at kontrollere status for det udglattede Streaming indhold.


##<a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lektion 2: Tilføje en skyder for at styre medier status
I Lektion 1, du har oprettet et Windows Store-program med et MediaElement XAML kontrolelement til afspilning udglattede Streaming medieindhold.  Det bliver nogle grundlæggende medier funktioner som start, stop og pause.  I denne lektion føjer du en søjle-skyder til programmet.

I dette selvstudium bruger vi en timer for at opdatere skyderen placeringen baseret på den aktuelle placering af kontrolelementet MediaElement.  Skyderen starten og slutningen tid også skal opdateres i tilfælde af direkte indhold.  Dette kan bedre håndteres i hændelsen tilpasset kilde opdatering.

Mediekilder er objekter, der opretter medier data.  Kilde resolver tager en URL-adresse eller byte stream og opretter den relevante mediekilde til indholdet.  Kilde resolver er som standard til i programmer til at oprette mediekilder. 

I denne lektion indeholder følgende procedurer:

1.  Registrere den udglattede Streaming handler 
2.  Sådan tilføjes hændelseshandlere tilpasset kilde manager niveau
3.  Tilføje tilpasset kilde niveau hændelseshandlerne
4.  Tilføje MediaElement hændelseshandlere
5.  Tilføje skyderen relaterede stregkode
6.  Samle og test af programmet

**Du kan registrere den udglattede Streaming byte-stream handler og overfører propertyset**

1.  Højreklik på **MainPage.xaml**Solution Explorer, og klik derefter på **Vis programkode**.
2.  I starten af fil, skal du tilføje følgende ved hjælp af sætning:

        using Microsoft.Media.AdaptiveStreaming;

3.  Tilføj følgende datamedlemmer i starten af klassen MainPage:

        private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
        private IAdaptiveSourceManager adaptiveSourceManager;
    
4.  I parametre **MainPage** Tilføj følgende kode efter **. Initialiseret Components();** linje- og de registrering kode linjer, der er skrevet i den forrige lektion:
    
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
    
5.  Ændre de to RegisterByteStreamHandler metoder til at tilføje i parametre **MainPage** den tilbage parametre:

        // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
        // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
        // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
        propertySet);

6.  Tryk på **CTRL + S for** at gemme filen.

**Tilføje tilpasset kilde-manager niveau hændelseshandler**

1.  Højreklik på **MainPage.xaml**Solution Explorer, og klik derefter på **Vis programkode**.
2.  Tilføje datamedlemmet følgende i klassen **MainPage** :

        private AdaptiveSource adaptiveSource = null;

3.  Tilføj følgende hændelseshandler sidst i klassen **MainPage** :
    
        #region Adaptive Source Manager Level Events
        private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
        }
        #endregion Adaptive Source Manager Level Events

4.  I slutningen af **MainPage** parametre, du tilføje følgende linje abonnere på hændelsen tilpasset kilde Åbn:
    
    adaptiveSourceManager.AdaptiveSourceOpenedEvent += nye AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);

5.  Tryk på **CTRL + S for** at gemme filen.

**Tilføje tilpasset kilde niveau hændelseshandlere**

1.  Højreklik på **MainPage.xaml**Solution Explorer, og klik derefter på **Vis programkode**.
2.  Tilføje datamedlemmet følgende i klassen **MainPage** :
    
        private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
        private Manifest manifestObject;
    
3.  Tilføj følgende hændelseshandlerne sidst i klassen **MainPage** :

        #region Adaptive Source Level Events
        private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
        }
        
        private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
        {
            adaptiveSourceStatusUpdate = args;
        }
        
        private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
        {
            txtStatus.Text = "Error: " + args.HttpResponse;
        }
        #endregion Adaptive Source Level Events

4.  Føj følgende kode abonnere på hændelserne i slutningen af metoden **mediaElement AdaptiveSourceOpened** :
    
        adaptiveSource.ManifestReadyEvent +=
                    mediaElement_ManifestReady;
        adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
            mediaElement_AdaptiveSourceStatusUpdated;
        adaptiveSource.AdaptiveSourceFailedEvent += 
            mediaElement_AdaptiveSourceFailed;
    
5.  Tryk på **CTRL + S for** at gemme filen.

De samme hændelser er tilgængelige på tilpasset kilde Manager niveau samt, som kan bruges til håndtering af funktioner, der er fælles for alle medieelementer i appen. Hver AdaptiveSource indeholder sin egen begivenheder og alle AdaptiveSource hændelser skal være kaskadevise under AdaptiveSourceManager.

**Tilføje medieelement hændelseshandlere**

1.  Højreklik på **MainPage.xaml**Solution Explorer, og klik derefter på **Vis programkode**.
2.  Tilføj følgende hændelseshandlerne sidst i klassen **MainPage** :
    
        #region Media Element Event Handlers 
        private void MediaOpened(object sender, RoutedEventArgs e)
        {
            txtStatus.Text = "MediaElement opened";
        }
        
        private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
        {
            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
        }
        
        private void MediaEnded(object sender, RoutedEventArgs e)
        {
            txtStatus.Text ="MediaElement ended.";
        }
        #endregion Media Element Event Handlers

3.  Føj følgende kode i slutningen af **MainPage** parametre, med sænket skrift til hændelserne:
    
        mediaElement.MediaOpened += MediaOpened;
        mediaElement.MediaEnded += MediaEnded;
        mediaElement.MediaFailed += MediaFailed;

4.  Tryk på **CTRL + S for** at gemme filen.

**Tilføje skyderen relateret kode**

1.  Højreklik på **MainPage.xaml**Solution Explorer, og klik derefter på **Vis programkode**.
2.  I starten af fil, skal du tilføje følgende ved hjælp af sætning:
    
        using Windows.UI.Core;

3.  Tilføj følgende datamedlemmer i klassen **MainPage** :
    
        public static CoreDispatcher _dispatcher;
        private DispatcherTimer sliderPositionUpdateDispatcher;
    
4.  Føj følgende kode i slutningen af **MainPage** parametre:

        _dispatcher = Window.Current.Dispatcher;
        PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
        sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
    
5.  Føj følgende kode i slutningen af klassen **MainPage** :
    
        #region sliderMediaPlayer
        private double SliderFrequency(TimeSpan timevalue)
        {
            long absvalue = 0;
            double stepfrequency = -1;
        
            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }
        
            TimeSpan totalDVRDuration = new TimeSpan(absvalue);
        
            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }
        
            return stepfrequency;
        }
        
        void updateSliderPositionoNTicks(object sender, object e)
        {
            sliderProgress.Value = mediaElement.Position.TotalSeconds;
        }
        
        public void setupTimer()
        {
            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
        }

        public void startTimer()
        {
            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderStartTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderEndTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
        }
        #endregion sliderMediaPlayer

    **Note:** CoreDispatcher bruges til at foretage ændringer i brugergrænsefladetråd fra ikke UI-tråd. I tilfælde af flaskehals på afsenderen tråd kan udvikler vælge at bruge afsender, der leveres af brugergrænseflade-element han/hun har til hensigt at opdatere.  Eksempel:
    
        await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
          timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
        double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
          sliderProgress.Maximum = absvalue; }); 
        

6.  Føj følgende kode i slutningen af metoden **mediaElement_AdaptiveSourceStatusUpdated** :
    
        setSliderStartTime(args.StartTime);
        setSliderEndTime(args.EndTime);

7.  Føj følgende kode i slutningen af metoden **MediaOpened** :
    
    sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan); sliderProgress.Width = mediaElement.Width; setupTimer();

8.  Tryk på **CTRL + S for** at gemme filen.

**Til at samle og teste programmet**

1. Tryk på **F6** for at samle projektet. 
2.  Tryk på **F5** for at køre programmet.
3.  Øverst i programmet, kan du bruge standardværdi udglattede Streaming URL-adresse eller skrive et andet navn. 
4.  Klik på **Angiv kilde**. 
5.  Test af skyderen.

Du har fuldført lektion 2.  I denne lektion føjet du en skyder til programmet. 

##<a name="lesson-3-select-smooth-streaming-streams"></a>Lektion 3: Vælge udglattede Streaming Streams
Problemfri Streaming er i stand til at streame indhold med flere sprog lydspor, der kan vælges af fremvisere.  I denne lektion, vil du aktivere seere mulighed for at vælge streams. I denne lektion indeholder følgende procedurer:

1. Redigere filen XAML
2. Redigere filen kode behand
3. Samle og test af programmet


**Til at redigere filen XAML**

1. Højreklik på **MainPage.xaml**fra Solution Explorer, og klik derefter på **Vis Designer**.
2. Find &lt;Grid.RowDefinitions&gt;, og ændre RowDefinitions, så de ser sådan ud:

        <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
        </Grid.RowDefinitions>

3. I den &lt;gitter&gt;&lt;/Grid&gt; mærker, du tilføje følgende kode til at definere et kontrolelement af typen liste, så brugerne kan se en liste over tilgængelige streams, og vælg streams:

        <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
        </Grid>

4. Tryk på **CTRL + S for** at gemme ændringerne.


**Ændre koden bag ved fil**

1. Højreklik på **MainPage.xaml**fra Solution Explorer, og klik derefter på **Vis programkode**.
2. Tilføje en ny klasse i navneområde SSPlayer: #region klasse værdistrøm
    
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
    
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
    
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
    
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream

3. Tilføj følgende variable definitioner i starten af klassen MainPage:

        private List<Stream> availableStreams;
        private List<Stream> availableAudioStreams;
        private List<Stream> availableTextStreams;
        private List<Stream> availableVideoStreams;

4. Tilføj følgende område i klassen MainPage:

        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
        
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
        
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
        
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
        
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
        
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
        
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
        
            // Select the frist video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
        
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
        
            // Select the frist audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
        
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
        
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection

5. Find mediaElement_ManifestReady metoden skal du føje følgende kode i slutningen af funktionen:
    
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();

    Så når MediaElement manifest er klar, koden får liste over tilgængelige streams og udfylder listefeltet Brugergrænsefladen med på listen.

6. Find Brugergrænsefladen i klassen MainPage knapperne Klik på begivenheder område, og Tilføj derefter følgende funktionen definitionen:

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Til at samle og teste programmet**

1. Tryk på **F6** for at samle projektet. 
2.  Tryk på **F5** for at køre programmet.
3.  Øverst i programmet, kan du bruge standardværdi udglattede Streaming URL-adresse eller skrive et andet navn. 
4.  Klik på **Angiv kilde**. 
5.  Standardsproget er audio_eng. Prøv at skifte mellem audio_eng og audio_es. Formular, hver gang, du vælger en ny stream, skal du klikke på knappen Send.

Du har fuldført lektion 3.  I denne lektion skal tilføje du funktioner for at vælge streams.

##<a name="lesson-4-select-smooth-streaming-tracks"></a>Øvelse 4: Vælg udglattede Streaming numre
En jævn Streaming præsentation kan indeholde flere videofiler kodet med forskellige kvalitetsniveau (bit satser) og løsninger. I denne lektion, du giver brugerne mulighed at markere spor. I denne lektion indeholder følgende procedurer:

1. Redigere filen XAML
2. Redigere filen kode behand
3. Samle og test af programmet

**Til at redigere filen XAML**

1. Højreklik på **MainPage.xaml**fra Solution Explorer, og klik derefter på **Vis Designer**.
2. Find den &lt;gitter&gt; mærke med navnet **gridStreamAndBitrateSelection**, Føj følgende kode i slutningen af mærket:

        <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
        </StackPanel>

3. Tryk på **CTRL + S** for at gemme he ændringer


**Ændre koden bag ved fil**

1. Højreklik på **MainPage.xaml**fra Solution Explorer, og klik derefter på **Vis programkode**.
2. Tilføje en ny klasse i navneområde SSPlayer:
    
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
    
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
    
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
    
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track

3. Tilføj følgende variable definitioner i starten af klassen MainPage:
    
        private List<Track> availableTracks;

4. Tilføj følgende område i klassen MainPage:
    
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>

        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();

            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;

                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }

        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }

        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }

        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }

        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection

5. Find mediaElement_ManifestReady metoden skal du føje følgende kode i slutningen af funktionen:

        getTracks(manifestObject);
        refreshAvailableTracksListBoxItemSource();

6. Find Brugergrænsefladen i klassen MainPage knapperne Klik på begivenheder område, og Tilføj derefter følgende funktionen definitionen:

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Til at samle og teste programmet**

1. Tryk på **F6** for at samle projektet. 
2.  Tryk på **F5** for at køre programmet.
3.  Øverst i programmet, kan du bruge standardværdi udglattede Streaming URL-adresse eller skrive et andet navn. 
4.  Klik på **Angiv kilde**. 
5.  Alle numrene af video strømmen er valgt som standard. For at eksperimentere bit satsændringer, kan du vælge den laveste bit sats tilgængelige og derefter vælge den højeste bit-rente, der er tilgængelige. Du skal klikke på Send efter hver enkelt ændring.  Du kan se ændringerne videokvalitet.

Du har fuldført lektion 4.  I denne lektion skal tilføje du funktioner for at vælge numre.


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="other-resources"></a>Andre ressourcer:
- [Sådan oprettes en jævn Streaming Windows 8 JavaScript-program med avancerede funktioner](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
- [Problemfri Streaming teknisk oversigt](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
 

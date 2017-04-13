<properties
    pageTitle="Sådan bruger du Blob-lager fra Xamarin | Microsoft Azure"
    description="Azure-lager klientbibliotek til Xamarin gør det muligt for udviklere til at oprette iOS, Android og Windows Store-apps med deres oprindelige brugergrænseflader. Dette selvstudium viser, hvordan du bruger Xamarin til at oprette et program, der bruger Azure Blob-lager."
    services="storage"
    documentationCenter="xamarin"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-xamarin"></a>Sådan bruger du Blob-lager fra Xamarin

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Oversigt

Xamarin gør det muligt for udviklere at bruge en delt C# codebase for at oprette iOS, Android og Windows Store-apps med deres oprindelige brugergrænseflader. Dette selvstudium viser, hvordan du bruger Azure Blob-lager med et Xamarin program. Hvis du vil have mere at vide om Azure-lager, før dykning i koden, kan du se [Introduktion til Microsoft Azure-lager](storage-introduction.md).

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Oprette et nyt Xamarin program

For denne introduktion, skal vi oprette en app, der er beregnet til Android-, iOS- og Windows. Denne app blot oprettes en objektbeholder, og overføre en blob til denne beholder. Vi bruger Visual Studio på Windows for første gang, men de samme learnings kan anvendes, når du opretter en ved hjælp af Xamarin Studio på Mac OS-app.

Følg disse trin for at oprette dit program:

1. Hvis du ikke allerede har gjort, downloade og installere [Xamarin til Visual Studio](https://www.xamarin.com/download).
2. Åbn Visual Studio, og Opret en tom App (Oprindelig delt): **fil > Ny > projekt > på tværs af platforme > tom App(Native Shared)**.
3. Højreklik på din løsning i ruden Solution Explorer, og vælg **Administrer NuGet pakker til løsning**. Søg efter **WindowsAzure.Storage** og installere den nyeste stabile version for alle projekter i din løsning.
4. Oprette og køre projektet.

Nu skal du have et program, der gør det muligt at klikke på en knap, der øger en tæller.

> [AZURE.NOTE] Azure-lager klientbibliotek til Xamarin understøtter aktuelt følgende projekttyper: oprindelige delt, Xamarin.Forms delt, Xamarin.Android og Xamarin.iOS.

## <a name="create-container-and-upload-blob"></a>Oprette beholder, og Overfør blob

Derefter skal du vil tilføje kode til den delte klasse `MyClass.cs` , der opretter en objektbeholder og overfører et blob til denne beholder. `MyClass.cs`skal se ud som følger:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;

    namespace XamarinApp
    {
        public class MyClass
        {
            public MyClass ()
            {
            }

            public static async Task createContainerAndUpload()
            {
                // Retrieve storage account from connection string.
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

                // Create the blob client.
                CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

                // Retrieve reference to a previously created container.
                CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

                // Create the container if it doesn't already exist.
                await container.CreateIfNotExistsAsync();

                // Retrieve reference to a blob named "myblob".
                CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

                // Create the "myblob" blob with the text "Hello, world!"
                await blockBlob.UploadTextAsync("Hello, world!");
            }
        }
    }

Sørg for at erstatte "your_account_name_here" og "your_account_key_here" med dit faktiske kontonavn og kontonøgle. Du kan derefter bruge klassen delt i din iOS-, Android, og Windows Phone-programmet på computeren. Kan du blot tilføje `MyClass.createContainerAndUpload()` til hvert projekt. Eksempel:

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

    using Android.App;
    using Android.Widget;
    using Android.OS;

    namespace XamarinApp.Droid
    {
        [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
        public class MainActivity : Activity
        {
            int count = 1;

            protected override async void OnCreate (Bundle bundle)
            {
                base.OnCreate (bundle);

                // Set our view from the "main" layout resource
                SetContentView (Resource.Layout.Main);

                // Get our button from the layout resource,
                // and attach an event to it
                Button button = FindViewById<Button> (Resource.Id.myButton);

                button.Click += delegate {
                    button.Text = string.Format ("{0} clicks!", count++);
                };

              await MyClass.createContainerAndUpload();
            }
        }
    }

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

    using System;
    using UIKit;

    namespace XamarinApp.iOS
    {
        public partial class ViewController : UIViewController
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.createContainerAndUpload();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Navigation;

    // The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

    namespace XamarinApp.WinPhone
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.createContainerAndUpload();
            }
        }
    }


## <a name="run-the-application"></a>Køre programmet

Nu kan du køre dette program i en Android- eller Windows Phone emulator. Du kan også køre dette program i en iOS-emulator, men dette kræver en Mac. Yderligere oplysninger om hvordan du gør dette, skal du læse i dokumentationen til at [oprette forbindelse Visual Studio til en Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Når du kører din app, oprettes der objektbeholderen `mycontainer` i kontoen lagerplads. Det skal indeholde blob `myblob`, som indeholder teksten, `Hello, world!`. Det kan du kontrollere ved hjælp af [Microsoft Azure lagerplads Explorer](http://storageexplorer.com/).

## <a name="next-steps"></a>Næste trin

I denne introduktion, du har lært hvordan du opretter et program i tværs af platforme i Xamarin, der bruger Azure-lager. I denne introduktion specifikt fokuseret på et scenarie i Blob-lager. Men du kan gøre meget mere med, ikke kun Blob-lager, men også med tabel, filer og kø lagerplads. Se følgende artikler for at få mere at vide:
- [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md)
- [Introduktion til Azure Table Storage ved hjælp af .NET](storage-dotnet-how-to-use-tables.md)
- [Introduktion til Azure kø lager ved hjælp af .NET](storage-dotnet-how-to-use-queues.md)
- [Introduktion til Azure fillagring i Windows](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

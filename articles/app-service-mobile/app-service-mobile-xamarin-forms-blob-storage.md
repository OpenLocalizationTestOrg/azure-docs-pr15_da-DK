<properties
    pageTitle="Oprette forbindelse til Azure lager i din Xamarin.Forms-app"
    description="Føje billeder til opgaveliste listen Xamarin.Forms mobilappen ved at oprette forbindelse til Azure blob-lager"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="connect-to-azure-storage-in-your-xamarinforms-app"></a>Oprette forbindelse til Azure lager i din Xamarin.Forms-app

## <a name="overview"></a>Oversigt

Azure Mobile-Apps klienten og serveren SDK understøtter offlinesynkronisering af strukturerede data med CRUD handlinger i forhold til /tables slutpunkt. Generelt disse data er gemt i en database eller en lignende store, og generelt disse data butikker ikke kan gemme store binære data effektivt. Nogle programmer har også relaterede data, som er gemt et andet sted (f.eks., blob-lager, filshares), og det er praktisk at kunne oprette tilknytninger mellem poster i /tables slutpunkt og andre data.

Dette emne beskrives, hvordan du føjer understøttelse af billeder til Mobile-Apps opgaveliste listen Hurtig start. Først skal du udføre selvstudiet [oprette en Xamarin.Forms app].

I dette selvstudium, du opretter en lagerplads konto og føje en forbindelsesstreng til din Mobile-App backend-version. Derefter skal du føje en ny nedarvning fra den nye Mobile-Apps type `StorageController<T>` til projektet server.

>[AZURE.TIP] Dette selvstudium har en [companion eksempel](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/) tilgængelige, som kan være installeret på din egen Azure-konto. 

## <a name="prerequisites"></a>Forudsætninger

* Udfør det [Opret en Xamarin.Forms app] -selvstudium, som viser andre forudsætninger. I denne artikel bruges færdige appen fra dette selvstudium.

>[AZURE.NOTE] Hvis du vil Introduktion til Azure App Service, før du tilmelder dig en Azure-konto, skal du gå til [Prøve App Service](https://tryappservice.azure.com/?appServiceName=mobile). Der, skal du straks kan oprette en forbigående starter mobilapp i App Service – ingen kreditkort er påkrævet, og ingen forpligtelser.

## <a name="create-a-storage-account"></a>Oprette en lagerplads-konto

1. Oprette en lagerplads konto ved at følge selvstudiet [opretter en Azure lagerplads konto]. 

2. Gå til kontoen nyoprettede lagerplads i portalen Azure, og klik på ikonet **taster** . Kopiér den **primære forbindelsesstreng**.

3. Gå til din mobilapp backend-version. Under **Indstillinger for alle** -> **Programindstillinger** -> **Forbindelsesstrenge**, oprette en ny nøgle med navnet `MS_AzureStorageAccountConnectionString` og bruge den værdi, der er kopieret fra kontoen lagerplads. Brug **brugerdefineret** vigtige filtype.

## <a name="add-a-storage-controller-to-the-server"></a>Tilføje en lagerplads controller på serveren

Du vil føje en ny controller til projektet server, der vil svare på anmodninger om et SAS token til Azure-lager, såvel som returnerer en liste over filer, der svarer til en post:

- [Føje et lager controller til projektet server](#add-controller-code)
- [Omdirigerer registreret af lagerplads controller](#routes-registered)
- [Klient- og kommunikation](#client-communication)

###<a name="add-controller-code"></a>Føje et lager controller til projektet server

1. I Visual Studio, skal du åbne projektet .NET server. Føje Nuget pakken [Microsoft.Azure.Mobile.Server.Files]. Sørg for at vælge **medtage foreløbig version**.

2. I Visual Studio, skal du åbne projektet .NET server. Højreklik på mappen **enheder** og vælg **Tilføj** -> **Controller** -> **Web API 2 Controller - Tøm**. Navngiv controlleren `TodoItemStorageController`.

3. Tilføj følgende ved hjælp af sætninger:

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. Ændre den grundlæggende klasse til `StorageController`:
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. Føje følgende metoder til klassen:

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. Opdatere konfigurationen Web API til at konfigurere attributten routing. Tilføj følgende linje til i **Startup.MobileApp.cs**, den `ConfigureMobileApp()` metode efter definitionen af den `config` variabel:

        config.MapHttpAttributeRoutes();

7. Publicere projektet server til din mobilapp backend-version.

###<a name="routes-registered"></a>Omdirigerer registreret af lagerplads controller

Den nye `TodoItemStorageController` Fremviser to underordnede ressourcer under den post, det administrerer:

- StorageToken

    + HTTP POST: Opretter en lagerplads token
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles

    + HTTP GET: Henter en liste over filer, der er knyttet til posten
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + HTTP-Slet: Sletter den fil, der er angivet i filen ressource-id
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###<a name="client-communication"></a>Klient- og kommunikation

Bemærk, at `TodoItemStorageController` fungerer *ikke* har en rute for at overføre eller hente en blob. Det skyldes, at en mobilklient skal arbejde sammen med blob storage *direkte* for at udføre disse handlinger, når du første få et SAS token (delt Access underskrift) sikker adgang til en bestemt blob eller beholder. Dette er et vigtigt arkitektonisk design, som vil være begrænset ellers adgang til lagring af skalerbarhed og tilgængelighed af den mobile backend-version. I stedet, kan den mobilklient ved at oprette forbindelse direkte til Azure-lager, drage fordel af de tilgængelige funktioner som automatisk partitionering og geografisk-fordeling.

En delt adgang signatur giver delegeret adgang til ressourcer i kontoen lagerplads. Det betyder, at du kan give en klient begrænsede tilladelser til objekter på din lagerplads konto i en angivet periode tid og med et bestemt sæt tilladelser, uden at dele din konto access-taster. Se [Forstå delt Access signaturer]for at få mere for at vide.

Diagrammet nedenfor viser klient- og interaktioner. Før du overfører en fil, klienten anmoder om et SAS token fra tjenesten. Tjenesten bruges forbindelsesstrengen lagerplads til at oprette en ny SAS, som den derefter returnerer til klienten. Sikkerhedstilknytningerne er begrænset tid og begrænser tilladelser til andet end en bestemt fil eller beholder. Den mobilklient bruger derefter denne SAS og Azure-lager klient-SDK til at overføre filen til blob-lager.

![Anmoder om et SAS token](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## <a name="update-your-client-app-to-add-image-support"></a>Opdatere din klient app for at tilføje understøttelse af billede

Åbn Xamarin.Forms Hurtig start projektet i Visual Studio eller Xamarin Studio. Du kan installere Nuget pakker og opdatere bærbare bibliotek projektet og iOS, Android og Windows klient projekter:

- [Tilføje Nuget-pakker](#add-nuget)
- [Tilføje IPlatform grænseflade](#add-iplatform)
- [Tilføje FileHelper klasse](#add-filehelper)
- [Tilføje en fil Synkroniser handler](#file-sync-handler)
- [Opdatere TodoItemManager](#update-todoitemmanager)
- [Tilføje en detaljeret visning](#add-details-view)
- [Opdatere den primære visning](#update-main-view)
- [Opdatere Android projektet](#update-android), [iOS project](#update-ios), [Windows project](#update-windows)

>[AZURE.NOTE] Dette selvstudium indeholder kun instruktioner til Android, iOS og Windows Store platforme, ikke Windows Phone.

###<a name="add-nuget"></a>Tilføje Nuget-pakker

Højreklik på løsningen, og vælg **Administrer Nuget pakker til løsning**. Tilføj følgende Nuget-pakker for **alle** projekter i løsningen. Sørg for at kontrollere **medtage foreløbig version**.

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

Dette eksempel bruger biblioteket [PCLStorage] fordel, men det er ikke påkrævet af Azure Mobile-Apps klienten SDK.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###<a name="add-iplatform"></a>Tilføje IPlatform grænseflade

Oprette en ny brugergrænseflade `IPlatform` i det primære portable bibliotek projekt. Dette følger [Xamarin.Forms DependencyService] mønster for at indlæse den højre platform-specifikke klasse på kørselstidspunktet. Du kan senere tilføje platform-specifikke installationer i hvert af klient projekterne.

1. Tilføj følgende ved hjælp af sætninger:

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. Erstat implementeringen med følgende:

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###<a name="add-filehelper"></a>Tilføje FileHelper klasse

1. Oprette en ny klasse `FileHelper` i det primære portable bibliotek projekt. Tilføj følgende ved hjælp af sætninger:

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. Tilføje klassedefinitionen:

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

###<a name="file-sync-handler"></a>Tilføje en fil Synkroniser handler

Oprette en ny klasse `TodoItemFileSyncHandler` i det primære portable bibliotek projekt. Denne klasse indeholder tilbagekald fra Azure SDK besked din kode, når en fil er tilføjet eller fjernet.

Azure Mobile Client SDK ikke reelt lagrer data fra en hvilken som helst fil: klient SDK åbner din implementering af `IFileSyncHandler` som også bestemmer, om og hvordan filer gemmes på den lokale enhed.

1. Tilføj følgende ved hjælp af sætninger:

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. Erstat klassedefinitionen med følgende: 

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###<a name="update-todoitemmanager"></a>Opdatere TodoItemManager

1. Fjern kommentar fra linjen i **TodoItemManager.cs** `#define OFFLINE_SYNC_ENABLED`.

2. Tilføj følgende i **TodoItemManager.cs**, ved hjælp af sætninger:

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. I konstruktøren `TodoItemManager`, skal du tilføje følgende efter opkaldet til `DefineTable()`:

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. Erstat teksten i parametre, opkaldet til `InitializeAsync` med følgende. Dette sikrer, at der er tilbagekald, når posterne er ændret i det lokale lager. Synkroniseringsfunktion fil bruger disse tilbagekald til at udløse din fil Synkroniser handler.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. I `SyncAsync()`, skal du tilføje følgende efter opkaldet til `PushAsync()`:

        await this.todoTable.PushFileChangesAsync();

6. Tilføj følgende metoder til `TodoItemManager`:

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###<a name="add-details-view"></a>Tilføje en detaljeret visning

I dette afsnit, skal føje du en ny detaljevisning for en opgaveliste element. Visningen oprettes, når brugeren markerer en opgaveliste element og det tillader nye billeder der skal føjes til et element.

1. Tilføje en ny klasse **TodoItemImage** til bærbare bibliotek projektet med følgende implementering:

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. Redigere **App.cs**. Erstatte initialiseringen af `MainPage` med følgende:
    
        MainPage = new NavigationPage(new TodoList());

3. Tilføj følgende egenskab i **App.cs**:

        public static object UIContext { get; set; }

4. Højreklik på bærbare bibliotek projektet og vælg **Tilføj** -> **Nyt element** -> **på tværs af platforme** -> **Formularer Xaml-siden**. Navngive visningen `TodoItemDetailsView`.

5. Åbn **TodoItemDetailsView.xaml** , og Erstat brødteksten i ContentPage med følgende:

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. Redigere **TodoItemDetailsView.xaml.cs** og tilføje følgende ved hjælp af sætninger:

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. Erstatte implementeringen af `TodoItemDetailsView` med følgende:

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###<a name="update-main-view"></a>Opdatere den primære visning 

Opdatere den primære visning for at åbne visningen detaljer, når en opgaveliste element er markeret.

Erstat teksten i **TodoList.xaml.cs**implementeringen af `OnSelected` med følgende:

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###<a name="update-android"></a>Opdatere Android projektet

Føj platform-specifikke kode til Android projektet, herunder kode for at hente en fil og bruger kameraet til at registrere et nyt billede. 

Denne kode bruger Xamarin.Forms [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) til at indlæse den højre platform-specifikke klasse på kørselstidspunktet.

1. Føje komponenten **Xamarin.Mobile** til Android projektet.

2. Tilføje en ny klasse `DroidPlatform` med følgende implementering. Erstat "YourNamespace" med det primære navneområdet for projektet.

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Redigere **MainActivity.cs**. I `OnCreate`, tilføje følgende, før opkaldet til `LoadApplication()`:

        App.UIContext = this;

###<a name="update-ios"></a>Opdatere iOS projektet

Føje platform-specifikke kode til iOS-projekt.

1. Føje komponenten **Xamarin.Mobile** til iOS-projekt.

2. Tilføje en ny klasse `TouchPlatform` med følgende implementering. Erstat "YourNamespace" med det primære navneområdet for projektet.

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Redigere **AppDelegate.cs** og fjern kommentar fra opkaldet til `SQLitePCL.CurrentPlatform.Init()`.

###<a name="update-windows"></a>Opdatering af Windows-projekt

1. Installer Visual Studio-typenavn [SQLite til Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). Du kan finde yderligere oplysninger finder selvstudiet [aktivere offlinesynkronisering for din Windows-app](app-service-mobile-windows-store-dotnet-get-started-offline-data.md). 

2. Redigere **Package.appxmanifest** , og Markér muligheden for **Webcam** .

3. Tilføje en ny klasse `WindowsStorePlatform` med følgende implementering. Erstat "YourNamespace" med det primære navneområdet for projektet.

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##<a name="summary"></a>Oversigt

I denne artikel beskrives, hvordan du bruger den nye filunderstøttelse i Azure Mobile klienten og serveren SDK til at arbejde med Azure-lager. 

- Oprette en lagerplads-konto, og Føj forbindelsesstrengen til din mobilapp backend-version. Kun back-end har nøgle til Azure-lager: mobile klienten anmoder om et SAS token (delt Access underskrift), når det skal have adgang til Azure-lager. Hvis du vil vide mere om SAS tokens i Azure-lager, skal du se [Om delt Access signaturer].

- Oprette en controller, underklasser `StorageController` for at håndtere SAS token anmodningerne og for at få de filer, der er knyttet til en post. Som standard er filer knyttet til en post ved hjælp af post-ID'ET som en del af objektbeholdernavnet; funktionsmåden kan tilpasses ved at angive en implementering af `IContainerNameResolver`. Politikken SAS token kan også tilpasses.

- Azure Mobile Client SDK gemmer ikke faktisk gemme alle data fra en fil. I stedet klienten SDK aktiverer din `IFileSyncHandler`, som derefter bestemmer hvordan (og hvis) filer gemmes på den lokale enhed. Synkroniser handler er registreret som følger:

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + `IFileSyncHandler.GetDataSource`kaldes når Azure Mobile Client SDK skal filens data (f.eks., som en del af overførslen). Det giver dig mulighed for administrere hvordan (og hvis) filer gemmes på den lokale enhed og returnere disse oplysninger, når det er nødvendigt.

      + `IFileSyncHandler.ProcessFileSynchronizationAction`startes som en del af filen synkronisering af strømmen. En reference til filen og en FileSynchronizationAction optællingsværdi leveres, så du kan beslutte, hvordan dit program skal håndtere hændelsen (fx automatisk hentning af en fil når det oprettes eller opdateres, sletning af en fil fra lokale enheden, når denne fil slettes på serveren).

- A `MobileServiceFile` kan være enten i online eller offline-tilstand, anvendes ved hjælp af en `IMobileServiceTable` eller `IMobileServiceSyncTable`henholdsvis. I scenariet offline overførslen sker, når appen ringer `PushFileChangesAsync`. Derved offline handlingen køen skal behandles; for hver filhandling Azure Mobile-klient vises SDK kalde den `GetDataSource` metode på den `IFileSyncHandler` forekomst til at hente filindholdet til overførslen.

- For at hente filer til et element, skal du ringe på "GetFilesAsync` method on the  `IMobileServiceTable<T> ` or IMobileServiceSyncTable<T>` forekomst. Denne metode returnerer en liste over filer, der er knyttet til dataelementet angivet. (Bemærk: Dette er en *lokal* handling og vil returnere de filer, der er baseret på tilstanden for objektet, når det sidst blev synkroniseret. For at få en opdateret liste over filer fra serveren, skal du starte en synkroniseringshandling af først.)

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- Synkroniseringsfunktion fil bruger ændre post beskeder på det lokale lager til at hente de poster, som kunden har modtaget som en del af en Skub eller træk handling. Dette opnås ved at gøre lokale og server meddelelser i den Synkroniser kontekst ved hjælp af den `StoreTrackingOptions` parameter. 

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + Andre store sporingsindstillinger er tilgængelige, som kun lokalt eller kun server beskeder. Du kan tilføje eller ejer brugerdefinerede tilbagekald ved hjælp af den `EventManager` egenskab for `IMobileServiceClient`:

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- Det er muligt at tilføje eller fjerne filer fra en post ved at ændre blob-lager direkte, da tilknytningen opnås gennem en navngivningskonvention. I dette tilfælde skal du imidlertid altid **opdatere post tidsstemplet for, hvornår de tilknyttede BLOB er ændret**. Klient-Azure Mobile SDK opdaterer altid en post ved at tilføje eller fjerne en fil. 

    Årsagen til dette krav er, at nogle mobilklienter allerede har posten i lokale lager. Når disse klienter udføre en trinvis hente, returneres denne post ikke og klienten beder ikke til de nye tilknyttede filer. Det anbefales, at du opdaterer post tidsstemplet, når du udfører ændringer blob-lager, der ikke bruger Azure Mobile-klient SDK for at undgå dette problem.

- Klientprojektet bruger [Xamarin.Forms DependencyService] mønster til at indlæse klassen højre platform-specifikke på kørselstidspunktet. I dette eksempel vi definerede en grænseflade `IPlatform` med installationer i hvert af de platform-specifikke projekter.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Oprette en Xamarin.Forms-app]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Introduktion til delte Access signaturer]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Oprette en Azure-lager-konto]:  ../storage/storage-create-storage-account.md#create-a-storage-account

<properties
    pageTitle="Sådan bruger du Azure Blob-lager fra iOS | Microsoft Azure"
    description="Gemme ustrukturerede data i skyen med Azure Blob-lager (objekt storage)."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-ios"></a>Sådan bruger du Blob-lager fra iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Oversigt

I denne artikel viser dig, hvordan til at udføre almindelige scenarier ved hjælp af Microsoft Azure Blob-lager. Eksemplerne er skrevet i mål-C og bruge [Azure lagerplads klientbibliotek til iOS](https://github.com/Azure/azure-storage-ios). De scenarier, der er dækket omfatter **overførsel af** **listen over**, **hente**og **slette** BLOB. Du kan finde flere oplysninger om blob, afsnittet [Næste trin](#next-steps) . Du kan også hente [eksempel app](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) hurtigt få vist brugen af Azure-lager i et iOS-program.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importere biblioteket Azure-lager iOS til dit program

Du kan importere biblioteket Azure-lager iOS til dit program ved hjælp af [Azure lagerplads CocoaPod](https://cocoapods.org/pods/AZSClient) eller ved at importere filen **Framework** .

## <a name="cocoapod"></a>CocoaPod

1. Hvis du allerede har gjort det, [Installere CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) på din computer ved at åbne et terminal vindue og køre følgende kommando

        sudo gem install cocoapods

2. Næste i projektmappen (den mappe, der indeholder dine `.xcodeproj` fil), oprette en ny fil kaldet `Podfile`(uden filtypenavn). Tilføj følgende for at `Podfile` og gemme

        pod 'AZSClient'

3. I vinduet terminal skal du gå til projektmappen og køre følgende kommando

        pod install

4. Hvis din `.xcodeproj` er åbne i Xcode skal du lukke den. Åbn den nyoprettede projektfil som vil have i din project-mappe på `.xcworkspace` filtypenavn. Dette er den fil, du vil arbejde fra for nu på.

## <a name="framework"></a>Framework
For at kunne bruge Azure-lager iOS bibliotek, skal du først oprette framework filen.

1. Først skal du hente eller klone [azure-lager-ios repo](https://github.com/azure/azure-storage-ios).

2. Gå til *azure-lager-ios* -> *bibliotek* -> *Azure lagerplads klientbibliotek*, og Åbn `AZSClient.xcodeproj` i Xcode.

3. Ændre den aktive farveskema fra "Azure lagerplads klientbibliotek" til "Framework" på øverst til venstre på Xcode.

4. Opbygge projektet (⌘ + B). Dette vil oprette en `AZSClient.framework` filen på skrivebordet.

Du kan derefter importere filen framework til dit program ved at gøre følgende:

1. Oprette et nyt projekt eller åbne dit eksisterende projekt i Xcode.

2. Klik på dit projekt i navigationslinjen til venstre, og klik på fanen *Generelt* øverst i project-editor.

3. Klik på knappen Tilføj (+) under sektionen *sammenkædede rammer og biblioteker* .

4. Klik på *Tilføj andre …*. Gå til og tilføje den `AZSClient.framework` fil, du lige har oprettet.

5. Klik på knappen Tilføj (+) igen under sektionen *sammenkædede rammer og biblioteker* .

6. I listen over biblioteker, der er angivet, søge efter `libxml2.2.dylib` og tildele den til dit projekt.

7. Klik på fanen *Opbygge indstillinger* øverst i project-editor.

8. Dobbeltklik på *Framework søgestier* i afsnittet *Søgestier* og tilføje stien til din `AZSClient.framework` fil.

## <a name="import-statement"></a>Import-sætning
Skal du medtage følgende importsætning i filen, hvor du vil kalde API'EN Azure-lager.

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynkrone handlinger
> [AZURE.NOTE] Alle metoder, som udfører en anmodning om mod tjenesten er asynkrone handlinger. I eksemplerne finder du, at disse metoder har en fuldførelse handler. Kode inden fuldførelse handler kører, **Når** anmodningen er fuldført. Kode efter fuldførelse handler kører **mens** anmodningen foretages.

## <a name="create-a-container"></a>Oprette en objektbeholder
Alle blob i Azure-lager skal placeres i en beholder. I følgende eksempel viser, hvordan du opretter en objektbeholder, kaldet *newcontainer*i kontoen lagerplads, hvis den ikke allerede findes. Når du vælger et navn til din objektbeholder, Vær opmærksom på de naming regler, der er nævnt ovenfor.

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

      // Create container in your Storage account if the container doesn't already exist
      [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
         if (error){
             NSLog(@"Error in creating container.");
         }
      }];
    }

Du kan bekræfte, at dette virker ved at kigge på [Microsoft Azure lagerplads Explorer](http://storageexplorer.com) og ved at bekræfte *newcontainer* er på listen over beholdere for kontoen lagerplads.

## <a name="set-container-permissions"></a>Angive tilladelser til objektbeholderen
En objektbeholder tilladelser er konfigureret til **Private** adgang som standard. Omvendt indeholder beholdere et par forskellige metoder til objektbeholder adgang:

- **Privat**: objektbeholder og blob data kan læses af kontoejeren.

- **BLOB**: Blob-data i denne objektbeholder kan læses via anonyme anmodningen, men objektbeholder data er ikke tilgængelig. Klienter kan ikke optælles BLOB inde i beholderen via anonyme anmodninger.

- **Beholder**: beholder og blob data kan læses via anonyme anmodninger. Klienter kan optælle BLOB inde i beholderen via anonyme anmodningen, men kan ikke optælles objektbeholdere i kontoen lagerplads.

I følgende eksempel viser, hvordan du opretter en objektbeholder med **objektbeholderen** adgangstilladelser som gør det muligt offentlige, skrivebeskyttet adgang for alle brugere på internettet:

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## <a name="upload-a-blob-into-a-container"></a>Overføre en blob til en objektbeholder
Som nævnt i sektionen [Blob service begreber](#blob-service-concepts) , Blob-lager indeholder tre forskellige typer BLOB: blokere blob, tilføje BLOB og siden BLOB. I øjeblikket understøtter biblioteket Azure-lager iOS kun Bloker BLOB. I fleste tilfælde skal er Bloker blob typen anbefales at bruge.

I følgende eksempel viser, hvordan du overfører en blok blob fra en NSString. Hvis en blob med samme navn findes allerede i denne beholder, overskrives indholdet af denne blob.

    -(void)uploadBlobToContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
             else{
                 // Create a local blob object
                 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                 // Upload blob to Storage
                 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                     if (error){
                         NSLog(@"Error in creating blob.");
                     }
                 }];
             }
         }];
    }

Du kan bekræfte, at det virker ved at kigge på [Microsoft Azure lagerplads Explorer](http://storageexplorer.com) og ved at bekræfte, at objektbeholderen, *containerpublic*, indeholder blob, *sampleblob*. I dette eksempel brugte vi en offentlige objektbeholder, så du kan også kontrollere, at det lykkedes ved at gå til BLOB URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Ud over overføres en blok blob fra en NSString, findes lignende metoder til NSData, NSInputStream eller en lokal fil.

## <a name="list-the-blobs-in-a-container"></a>Liste over BLOB i en objektbeholder
I følgende eksempel viser, hvordan til at få vist alle BLOB i en beholder. Når du udfører denne handling, Vær opmærksom på følgende parametre:     

- **continuationToken** - fortsættelse tokenet repræsenterer hvor handlingen Oversigt skal starte. Hvis ingen token ikke er angivet, vises den BLOB fra begyndelsen. Et vilkårligt antal BLOB kan være angivet fra nul op til et sæt maksimale. Selvom denne metode returnerer nul resultater, hvis `results.continuationToken` er ikke tom, der kan være flere BLOB på tjenesten, som ikke er angivet.
- **præfiks** - kan du angive præfikset, du vil bruge til blob oversigt. Kun blob, der starter med præfikset vises.
- **useFlatBlobListing** - som nævnt i sektionen [Naming og refererer til sig beholdere og BLOB](#naming-and-referencing-containers-and-blobs) , selvom tjenesten Blob er en flad lagerplads farveskema, kan du oprette et virtuelt hierarki ved at navngive BLOB med oplysninger om printersti. Dog understøttes ikke flad listen ikke i øjeblikket; Dette kommer snart. Nu skal skal denne værdi være`YES`
- **blobListingDetails** - du kan angive, hvilke elementer skal medtages, når listen over BLOB
    - `AZSBlobListingDetailsNone`: Listen kun anvendt blob, og returnerer ikke blob metadata.
    - `AZSBlobListingDetailsSnapshots`: Listen anvendt BLOB og blob snapshots.
    - `AZSBlobListingDetailsMetadata`: Der returneres Hent blob metadataene for hver blob i på listen.
    - `AZSBlobListingDetailsUncommittedBlobs`: Liste over anvendt og tilbageskrivningshandlinger BLOB.
    - `AZSBlobListingDetailsCopy`: Medtage kopi egenskaber i på listen.
    - `AZSBlobListingDetailsAll`: Vis alle tilgængelige anvendt blob, tilbageskrivningshandlinger BLOB og snapshots og returnere alle metadata og Kopiér status for disse BLOB.
- **maxResults** - det maksimale antal resultater for at vende tilbage til denne handling. Brug -1 at ikke angive en udløbsdato.
- **completionHandler** - blokering af kode for at udføre med resultatet af handlingen oversigt.

I dette eksempel, der bruges til at en hjælper metode gælder opkald på listen BLOB metode hver gang en fortsættelse token returneres.

    -(void)listBlobsInContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        //List all blobs in container
        [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
            if (error != nil){
                NSLog(@"Error in creating container.");
            }
        }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
        [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
            if (error)
            {
                completionHandler(error);
            }
            else
            {
                for (int i = 0; i < results.blobs.count; i++) {
                    NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
                }
                if (results.continuationToken)
                {
                    [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
                }
                else
                {
                    completionHandler(nil);
                }
            }
        }];
    }


## <a name="download-a-blob"></a>Hente en blob

I følgende eksempel viser, hvordan til at hente en blob til et NSString objekt.

    -(void)downloadBlobToString{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
            else{
                NSLog(@"%@",text);
            }
        }];
    }

## <a name="delete-a-blob"></a>Slette en blob

I følgende eksempel viser, hvordan du sletter en blob.

    -(void)deleteBlob{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
            if (error) {
                NSLog(@"Error in deleting blob.");
            }
        }];
    }

## <a name="delete-a-blob-container"></a>Slette en blob objektbeholder

I følgende eksempel viser, hvordan du sletter en objektbeholder.

    -(void)deleteContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      // Delete container
      [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
         if(error){
             NSLog(@"Error in deleting container");
         }
      }];
    }

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært, hvordan du bruger Blob-lager fra iOS, skal du følge disse links for at få flere oplysninger om biblioteket iOS og tjenesten lagerplads.

- [Azure-lager klient-bibliotek til iOS](https://github.com/azure/azure-storage-ios)
- [Azure lagerplads iOS referencedokumentation](http://azure.github.io/azure-storage-ios/)
- [Azure-lager Services REST-API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage)

Hvis du har spørgsmål om dette bibliotek Velkommen til at sende indlæg til vores [MSDN Azure-forummet](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) eller [Stakoverløb](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Hvis du har funktion forslag til Azure-lager, skal du skrive indlæg til [Azure lagerplads Feedback](https://feedback.azure.com/forums/217298-storage/).

<properties
    pageTitle="Lokale program med blob-lager (Java) | Microsoft Azure"
    description="Få mere at vide, hvordan du opretter en console-program, der overfører et billede til Azure, og derefter viser billedet i din browser. Kodeeksempler i Java."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="on-premises-application-with-blob-storage"></a>Lokalt program med blob-lager

## <a name="overview"></a>Oversigt

I følgende eksempel viser, hvordan du kan bruge Azure-lager til lagring af billeder i Azure. Koden i denne artikel er til et console-program, der overfører et billede til Azure, og derefter opretter en HTML-fil, der viser billedet i din browser.

## <a name="prerequisites"></a>Forudsætninger

- En Java udvikler Kit (JDK), version 1,6 eller nyere, er installeret.
- Azure SDK er installeret.
- GLAS for Azure-biblioteker til Java, og eventuelle relevante afhængighed krukker, der er installeret og er i build stien, der anvendes af din Java-compileren. Oplysninger om installation af Azure-biblioteker til Java, kan du se [hente Azure SDK til Java](java-download-azure-sdk.md).
- Er der angivet en Azure-lager-konto. Kontonavnet og kontonøgle for kontoen lagerplads bruges af koden i denne artikel. Se, [hvordan du oprette en lagerplads konto](storage-create-storage-account.md#create-a-storage-account) oplysninger om oprettelse af en lagerplads konto, og [Vis, og Kopiér lagerplads access-taster](storage-create-storage-account.md#view-and-copy-storage-access-keys) for at få oplysninger om at hente tasten konto.

- Du har oprettet en lokal billedfil med navnet gemt på stien c:\\myimages\\image1.jpg. Du kan også ændre   **FileInputStream** parametre i eksemplet med at bruge et andet billede sti og navn.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>Bruge Azure blob-lager til at overføre en fil

En trinvis præsenteres her. Hvis du vil gå videre, vises hele koden senere i denne artikel.

Start koden ved at medtage importerer til Azure core lagerplads klasser, Azure blob-klienten klasser, Java EY klasser og klassen **URISyntaxException** .

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

Erklære en klasse, kaldet **StorageSample**, og Medtag et åbent slutparentes **{**.

    public class StorageSample {

Erklære en strengvariabel, der skal indeholde standard slutpunkt protokol, dit kontonavn lager og din lagerplads adgangsnøgle, som angivet i kontoen Azure-lager i klassen **StorageSample** . Erstatte pladsholder værdierne **din\_konto\_navnet** og **din\_konto\_nøgle** med dine egne konto navn og konto nøgle, henholdsvis.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

Tilføje i dit definition for **primære**, omfatter en **prøve** blok og medtage det er nødvendigt åbne parenteserne, **{**.

    public static void main(String[] args)
    {
        try
        {

Erklære variabler af typen følgende (beskrivelserne er for hvordan de bruges i dette eksempel):

-   **CloudStorageAccount**: initialiseret objektet konto med dit kontonavn til Azure lager og nøgle, og der oprettes objektet blob-klienten.
-   **CloudBlobClient**: bruges til at få adgang til tjenesten blob.
-   **CloudBlobContainer**: bruges til at oprette en blob objektbeholder, listen BLOB i beholderen, og Slet objektbeholderen.
-   **CloudBlockBlob**: bruges til at overføre en lokal billedfil til objektbeholderen.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Tildele en værdi til variablen **konto** .

    account = CloudStorageAccount.parse(storageConnectionString);

Tildele en værdi til variablen **serviceClient** .

    serviceClient = account.createCloudBlobClient();

Tildele en værdi til variablen **objektbeholder** . Vi får vist en reference til en objektbeholder med navnet **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Oprette objektbeholderen. Denne metode opretter objektbeholderen, hvis den ikke findes (og returnere **Sand**). Hvis objektbeholderen findes, returneres **Falsk**. Et alternativ til **createIfNotExists** er metoden **oprette** (som vil returnere en fejl, hvis der allerede findes i beholderen).

    container.createIfNotExists();

Konfigurere anonym adgang for objektbeholderen.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Få en reference til Bloker blob, som repræsenterer blob i Azure-lager.

    blob = container.getBlockBlobReference("image1.jpg");

Brug den **fil** til at få en reference til den lokalt oprettede fil, du vil overføre. Kontrollér, at du har oprettet denne fil før du kører koden.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Overfør den lokale fil via et opkald til metoden **CloudBlockBlob.upload** . Den første parameter til metoden **CloudBlockBlob.upload** er et **FileInputStream** objekt, der repræsenterer den lokale fil, der skal overføres til Azure-lager. Den anden parameter er størrelsen i byte i filen.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Ringe til en hjælper funktion med navnet **MakeHTMLPage**, for at sikre en grundlæggende HTML-side, der indeholder en ** &lt;billede&gt; ** elementet til kilden, der er angivet til blob, der er nu i kontoen Azure-lager. Koden for **MakeHTMLPage** beskrives senere i denne artikel.

    MakeHTMLPage(container);

Udskrive en statusmeddelelse samt oplysninger om den oprettede HTML-side.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Luk blokering **prøve** ved at indsætte en Luk højreparentes: **}**

Håndtere følgende undtagelser:

-   **FileNotFoundException**: kan være udløst af **FileInputStream** eller **FileOutputStream** konstruktører.
-   **StorageException**: kan være udløst af biblioteket Azure klient lagerplads.
-   **URISyntaxException**: kan være udløst af metoden **ListBlobItem.getUri** .
-   **Undtagelse**: håndtering af generisk undtagelse.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Luk **primære** ved at indsætte en Luk højreparentes: **}**

Oprette en metode med navnet **MakeHTMLPage** til at oprette en grundlæggende HTML-side. Denne metode har en parameter af typen **CloudBlobContainer**, som bruges til at navigere gennem listen over overførte BLOB. Denne metode kan udløse undtagelser af typen **FileNotFoundException**, som kan være udløst **FileOutputStream** parametre, og **URISyntaxException**, kan der være udløst af metoden **ListBlobItem.getUri** . Medtage en venstreparentes, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Oprette en lokal fil med navnet **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Skrive til den lokale fil, tilføje i den ** &lt;html&gt;**, ** &lt;sidehoved&gt;**, og ** &lt;brødtekst&gt; ** elementer.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Navigere gennem listen over overførte BLOB. For hver blob HTML på siden Opret en ** &lt;img&gt; ** element, der har dens **src** -attribut, der er sendt til blob URI, som den findes i kontoen Azure-lager.
Selvom du har tilføjet kun ét billede i dette eksempel, hvis du har tilføjet flere, vil denne kode forbedrer dem alle.

I dette eksempel forudsættes for enkel, hver overførte blob er et billede. Hvis du har opdateret BLOB end billeder eller siden BLOB i stedet for at blokere blob, skal du justere koden, efter behov.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Luk den ** &lt;brødtekst&gt; ** element og ** &lt;html&gt; ** element.

    stream.println("</body>");
    stream.println("</html>");

Luk den lokale fil.

    stream.close();

Luk **MakeHTMLPage** ved at indsætte en Luk højreparentes: **}**

Luk **StorageSample** ved at indsætte en Luk højreparentes: **}**

Følgende er fuldført koden for dette eksempel. Husk at ændre værdierne pladsholder **din\_konto\_navnet** og **din\_konto\_nøgle** at bruge dit kontonavn og kontonøgle, henholdsvis.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

Ud over at overføre billedfilen lokale til Azure-lager, opretter eksempelkoden namedindex.html en lokal fil, som du kan åbne i browseren for at se dine overførte billede.

Da koden indeholder dit kontonavn og kontonøgle, sørge for, at din kildekode er sikre.

## <a name="to-delete-a-container"></a>Slette en objektbeholder

Fordi du betaler for lagerplads, du vil slette objektbeholderen **gettingstarted** , når du er færdig med at eksperimentere med dette eksempel. Hvis du vil slette en objektbeholder, skal du bruge metoden **CloudBlobContainer.delete** .

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Hvis du vil ringe metoden **CloudBlobContainer.delete** , er initialiseringen **CloudStorageAccount**, **ClodBlobClient**og **CloudBlobContainer** objekter den samme som det er vist for metoden **createIfNotExist** . Følgende er en komplet eksempel, der sletter beholderen **gettingstarted**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Se, [hvordan du bruger Blob-lager fra Java](storage-java-how-to-use-blob-storage.md)for en oversigt over andre blob storage klasser og metoder.

## <a name="next-steps"></a>Næste trin

Følg disse links for at få flere oplysninger om mere komplekse lagerplads opgaver.

- [Azure-lager SDK til Java](https://github.com/azure/azure-storage-java)
- [Azure-lager klient SDK Reference](http://dl.windowsazure.com/storage/javadoc/)
- [Azure-lager Services REST-API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/)

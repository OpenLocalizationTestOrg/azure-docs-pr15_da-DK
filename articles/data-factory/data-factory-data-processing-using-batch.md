<properties
    pageTitle="Behandle store datasæt ved hjælp af Data Factory og batchen | Microsoft Azure"
    description="Beskriver, hvordan du behandle store mængder data i en Azure Data Factory pipeline ved hjælp af muligheden for parallel behandling af Azure Batch."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="spelluru"/>

# <a name="process-large-scale-datasets-using-data-factory-and-batch"></a>Procesfarver store datasæt ved hjælp af Data Factory og batchen
I denne artikel beskrives en arkitektur af en stikprøve løsning, der flytter og behandler store datasæt i en automatisk og planlagte måde. Den indeholder også en til slut gennemgang for at implementere løsningen ved hjælp af Azure Data Factory og Azure batchen. 

I denne artikel er længere end vores typisk artikel, fordi den indeholder en gennemgang af en hel eksempel løsning. Hvis du er ny bruger af batchen og Data Factory, du kan få mere at vide om disse tjenester, og hvordan de fungerer sammen. Hvis du vide noget om tjenesterne og design/planlagde arkitektur en løsning, kan du fokusere kun på den [arkitektur sektion](#architecture-of-sample-solution) i denne artikel, og hvis du udvikler en model eller en løsning, kan du også vil prøve en trinvis vejledning i denne [gennemgang](#implementation-of-sample-solution). Vi invitere dine kommentarer til indholdet, og hvordan du bruger den.

Først skal Lad os se på, hvordan Data Factory og batchen tjenester kan hjælpe med behandling af store datasæt i skyen.     

## <a name="why-azure-batch"></a>Hvorfor Azure batchen?
Azure batchen gør det muligt at køre store parallelle og high-performance computing (HPC) programmer effektivt i skyen. Det er en platform-tjeneste, der planlægger Beregn-intensivt arbejde køres en administreret samling af virtuelle maskiner, og kan automatisk skala beregne ressourcer for at imødekomme dine sager.

Du definerer Azure Beregn ressourcer for at udføre dine programmer parallelt og skaleres til brug med tjenesten batchen. Du kan køre efter behov eller planlagte job, og du behøver ikke at manuelt oprette, konfigurere og administrere en HPC-klynge, individuelle virtuelle maskiner, virtuelle netværk eller et kompleks job og opgave planlægning infrastruktur.

Se følgende artikler, hvis du ikke er fortrolig med Azure batchen, som hjælper med at forstå arkitektur/implementeringen af den løsning, der er beskrevet i denne artikel.   

- [Grundlæggende om Azure batchen](../batch/batch-technical-overview.md)
- [Batchen funktionsoversigt](../batch/batch-api-basics.md)

(valgfrit) Hvis du vil vide mere om Azure batchen, skal du se [læringssti til Azure batchen](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Hvorfor Azure Data Factory?
Data Factory er en skybaseret data integration tjeneste, som orchestrates og automatisere flytning og transformation af data. Brug af Data Factory-tjenesten, kan du oprette administrerede data rørledninger, flytte data fra lokale og skyen data butikker til et centralt datalager (for eksempel: Azure Blob-lager), og processen/Transformer data ved hjælp af tjenester som Azure HDInsight og Azure maskine læring. Du kan også planlægge data rørledninger til at køre i et planlagt måde (time, dagligt, ugentligt, osv.) og en skærm og administrere dem hurtigt at identificere problemer med, og benyt den fremgangsmåde. 

Se følgende artikler, hvis du ikke er fortrolig med Azure Data Factory, som hjælper med at forstå arkitektur/implementeringen af den løsning, der er beskrevet i denne artikel.  

- [Introduktion af Azure Data Factory](data-factory-introduction.md)
- [Oprette din første data pipeline](data-factory-build-your-first-pipeline.md)   

(valgfrit) Hvis du vil vide mere om Azure Data Factory, kan du se [læringssti til Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Data Factory og batchen sammen
Data Factory omfatter indbyggede aktiviteter som kopi aktivitet til at kopiere/flytte data fra en kilde datalager til en destination datalager og Hive aktivitet til proces data ved hjælp af Hadoop klynger (HDInsight) på Azure. Du kan se [Data Transformation aktiviteter](data-factory-data-transformation-activities.md) til en liste over understøttede transformation aktiviteter. 

Det gør det også muligt at oprette brugerdefinerede .NET aktiviteter for at flytte eller behandle data med din egen logik og køre disse aktiviteter på en Azure HDInsight klynge eller på en Azure batchen puljen af FOS. Når du bruger Azure batchen, kan du konfigurere gruppen til automatisk skalering (tilføje eller fjerne FOS baseret på arbejdsbelastningen) baseret på en formel, du angiver.     

## <a name="architecture-of-sample-solution"></a>Arkitekturen i eksempel løsning
Selvom den arkitektur, der er beskrevet i denne artikel er en enkel løsning, er det relevant for komplekse scenarier som risikoen modeling ved finansielle tjenesteydelser, billedbehandling og gengivelse og genom analyse. 

Diagrammet illustrerer 1) hvordan Data Factory orchestrates flytning af data og behandling og 2) hvordan Azure batchen behandler data i en parallelle måde. Downloade og udskrive diagrammet til senere brug (11 x 17 i. eller A3 størrelse): [HPC og data organisering ved hjælp af Azure batchen og Data Factory](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Store databehandling diagram](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

Følgende liste indeholder de grundlæggende trin i processen. Løsningen indeholder kode og forklaringer til at oprette den til en komplet løsning.

1.  **Konfigurere Azure batchen med en gruppe af Beregn knuder (FOS)**. Du kan angive antallet af knuder og størrelsen af hver node.

2.  **Opret en Azure Data Factory-forekomst** , der er konfigureret med enheder, der repræsenterer Azure blob-lager, Azure batchen Beregn service, input/output data og en arbejdsproces rørledning med aktiviteter, flytte og transformere data.

3.   **Oprette en brugerdefineret .NET aktivitet i Data Factory pipeline**. Aktiviteten er dit brugerkode, der kører på Azure batchen puljen.

4.  **Store store datamængder input som BLOB i Azure-lager**. Data er inddelt i logiske udsnit (som regel ved tid).

5.  **Data Factory kopierer data, der er behandlet parallelt** til den sekundære placering.

6.  **Data Factory kører den tilpassede aktiviteter ved hjælp af den gruppe, der er tildelt af batchen**. Data Factory kan køre aktiviteter samtidigt. Hver aktivitet behandler et udsnit af data. Resultaterne er gemt i Azure-lager.

7.  **Data Factory flytter det endelige resultat til en tredje placering**, enten til distribution via en app eller til videre behandling af andre værktøjer.

## <a name="implementation-of-sample-solution"></a>Implementering af eksempel løsning
Eksempel-løsning er bevidst enkle og viser, hvordan du bruger Data Factory og batchen sammen til at behandle datasæt. Løsningen blot tæller antallet af forekomster af et søgeord ("Microsoft") i input filer, der er organiseret i en tidsserie. Den udskriver antallet til outputfiler.

**Tid**: Hvis du allerede kender, grundlæggende Azure, Data Factory og batchen og har fuldført de forudsætninger, der vises nedenfor, vi anslå denne løsning tager 1-2 timer at fuldføre.

### <a name="prerequisites"></a>Forudsætninger

#### <a name="azure-subscription"></a>Azure-abonnement
Hvis du ikke har et Azure-abonnement, kan du oprette en gratis prøveversion konto på blot et par minutter. Se [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure-lager-konto
Du bruger en konto til Azure-lager til lagring af data i dette selvstudium. Hvis du ikke har en Azure-lager-konto, kan du se [oprette en lagerplads konto](../storage/storage-create-storage-account.md#create-a-storage-account). Eksempel løsningen bruger blob-lager.

#### <a name="azure-batch-account"></a>Azure batchen-konto
Opret en Azure batchen konto ved hjælp af [Azure-portalen](http://manage.windowsazure.com/). Se [oprette og administrere en Azure batchen konto](../batch/batch-account-create-portal.md). Bemærk Azure batchen konto navn og konto-tasten. Du kan også bruge [Ny AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) cmdlet til at oprette en Azure batchen konto. Se [Introduktion til Azure batchen PowerShell-cmdlet'er](../batch/batch-powershell-cmdlets-get-started.md) detaljerede oplysninger om brug af denne cmdlet.

Eksempel løsningen bruger Azure Batch (indirekte via en Azure Data Factory pipeline) til at behandle data i en parallelle måde på en samling af Beregn noder (en administreret samling af virtuelle maskiner).

#### <a name="azure-batch-pool-of-virtual-machines-vms"></a>Azure batchen puljen af virtuelle maskiner (FOS)
Oprette en **Azure batchen puljen** med mindst 2 Beregn noder.

1.  Klik på **Gennemse** i menuen til venstre på [Azure-portalen](https://portal.azure.com), og klik på **Batchen konti**. 
2. Vælg din Azure batchen konto for at åbne bladet **Batchen konto** . 
3. Klik på **grupper** felt.
4. Klik på knappen Tilføj på værktøjslinjen for at tilføje en samling i bladet **grupper** .
    1. Angiv et ID for puljen (**Pulje-ID**). Note **-ID'ET for puljen**. Du kan bruge den, når du opretter Data Factory-løsning. 
    2. Angiv **Windows Server 2012 R2** indstillingen operativsystem familie.
    3. Vælg en **node priser niveau**.
    4. Angive **2** som værdi for **Destination dedikeret** indstillingen.
    5. Angive **2** som værdi for indstillingen **Maks opgaver per node** .
    6. Klik på **OK** for at oprette puljen. 
    
#### <a name="azure-storage-explorer"></a>Azure-lager Explorer   
[Azure lagerplads Explorer 6 (værktøj)](https://azurestorageexplorer.codeplex.com/) eller [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (fra ClumsyLeaf Software). Du kan bruge disse værktøjer til undersøge og ændre dataene i din Azure-lager projekter, herunder logfiler over dine skyen hostet programmer.

1.  Oprette en beholder med navnet **mycontainer** med privat adgang (ingen anonym adgang)

2.  Hvis du bruger **CloudXplorer**, kan du oprette mapper og undermapper med følgende struktur:

    ![](./media/data-factory-data-processing-using-batch/image3.png)

    **Inputfolder** og **outputfolder** er mapper på øverste niveau i **mycontainer,** og **inputfolder** indeholder undermapper med dato / klokkeslæt stempler (åååå-MM-DD-HH).

    Hvis du bruger **Azure Storage Explorer**, i næste trin, du har brug at overføre filer med navne: inputfolder/2015-11-16-00/file.txt, inputfolder/2015-11-16-01/file.txt osv. Dette trin opretter automatisk mapperne.

3.  Oprette en tekst fil **fil.txt** på din computer med indhold, der indeholder nøgleordet **Microsoft**. For eksempel: "teste tilpassede aktiviteter Microsoft test tilpassede aktiviteter Microsoft".

4.  Overføre filen til følgende input mapper i Azure blob-lager.

    ![](./media/data-factory-data-processing-using-batch/image4.png)

    Hvis du bruger **Azure Storage Explorer**skal du overføre filer **fil.txt** til **mycontainer**. Klik på **Kopiér** på værktøjslinjen for at oprette en kopi af blob. Ændre **destinationen blob navn** til i dialogboksen **Kopiér Blob** **inputfolder/2015-11-16-00/file.txt.** Gentag dette trin for at oprette inputfolder/2015-11-16-01/file.txt, inputfolder/2015-11-16-02/file.txt, inputfolder/2015-11-16-03/file.txt, inputfolder/2015-11-16-04/file.txt og så videre. Denne handling opretter automatisk mapperne.

3.  Oprette en anden beholderen: **customactivitycontainer**. Du kan overføre tilpassede aktiviteter zip-filen til denne beholder.

#### <a name="visual-studio"></a>Visual Studio
Installer Microsoft Visual Studio 2012 eller nyere for at oprette den brugerdefinerede Batch aktivitet der skal bruges i Data Factory-løsning.

### <a name="high-level-steps-to-create-the-solution"></a>Overordnede trin til at oprette løsningen

1.  Oprette en brugerdefineret aktivitet, der indeholder databehandling logik.
2.  Oprette en Azure data factory, der bruger den tilpassede aktiviteter:

### <a name="create-the-custom-activity"></a>Oprette tilpassede aktiviteter

Den brugerdefinerede Data Factory-aktivitet er centrale denne eksempel-løsning. Eksempel løsningen bruger Azure batchen til at køre den tilpassede aktiviteter. Se [bruge brugerdefinerede aktiviteter i en Azure Data Factory pipeline](data-factory-use-custom-activities.md) til de grundlæggende oplysninger til at udvikle brugerdefinerede aktiviteter og bruge dem i Azure Data Factory rørledninger.

Hvis du vil oprette en brugerdefineret .NET-aktivitet, du kan bruge i en Azure Data Factory pipeline, skal du oprette et **.NET klassebibliotek** projekt med en klasse, der implementerer **IDotNetActivity** -grænsefladen. Denne grænseflade har kun en enkelt metode: **Execute**. Her er signaturen for metoden:

    public IDictionary<string, string> Execute(
                IEnumerable<LinkedService> linkedServices,
                IEnumerable<Dataset> datasets,
                Activity activity,
                IActivityLogger logger)

Metoden har nogle vigtige komponenter, du har brug for at forstå.

-   Metoden kræver fire parametre:

    1.  **linkedServices**. En ikke kan optælles liste over sammenkædede tjenester, der sammenkæder input/output datakilder (for eksempel: Azure Blob-lager) til data factory. I dette eksempel er der kun en sammenkædet tjeneste af typen Azure-lager, der bruges til både input og output.

    2.  **datasæt**. Dette er en ikke kan optælles liste over datasæt. Du kan bruge denne parameter til at få de placeringer og skemaer, der er defineret af input- og outputområder datasæt.

    3.  **aktivitet**. Denne parameter repræsenterer den aktuelle Beregn enhed – i dette tilfælde en Azure batchen tjeneste.

    4.  **logføring**. Logføring af kan du skrive fejlfinding kommentarer, grundflade som "Brugere" logfilen for pipeline.

-   Metoden returnerer en ordbog, der kan bruges til at sammenkæde brugerdefinerede aktiviteter i fremtiden. Denne funktion er ikke implementeret endnu, så returnere en tom ordbog fra metoden. 

#### <a name="procedure-create-the-custom-activity"></a>Fremgangsmåde: Oprette den brugerdefinerede aktivitet

1.  Oprette et .NET klassebibliotek projekt i Visual Studio.

    1.  Start **Visual Studio 2012**/**2013-2015**.

    2.  Klik på **filer**, peg på **Ny**, og klik på **projekt**.

    3.  Udvid **skabeloner**, og vælg **visuelle C\#**. I denne gennemgang skal du bruge C\#, men du kan bruge en hvilken som helst .NET sprog til at udvikle tilpassede aktiviteter.

    4.  Vælg **Klassebibliotek** på listen over projekttyper i højre side.

    5.  Angiv **MyDotNetActivity** for **navn**.

    6.  Vælg **C:\\ADF** for **placering**. Oprette mappen **ADF** , hvis den ikke findes.

    7.  Klik på **OK** for at oprette projektet.

2.  Klik på **Funktioner**, peg **NuGet Package Manager**, og klik på **Pakke Manager-konsollen**.

3.  Udfør følgende kommando for at importere **Microsoft.Azure.Management.DataFactories**i **Pakke Manager-konsollen**.

            Install-Package Microsoft.Azure.Management.DataFactories

4.  Importere pakken NuGet **Azure-lager** i projektet. Du skal bruge denne pakke, fordi du bruger Blob storage API i dette eksempel.

        Install-Package Azure.Storage

5.  Tilføj følgende **ved hjælp af** direktiver til kildefilen i projektet.

        using System.IO;
        using System.Globalization;
        using System.Diagnostics;
        using System.Linq;

        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Runtime;

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

6.  Ændre navnet på det **navneområde** til **MyDotNetActivityNS**.

        namespace MyDotNetActivityNS

7.  Ændre navnet på klassen til **MyDotNetActivity** , og som stammer fra grænsefladen **IDotNetActivity** som vist nedenfor.

        public class MyDotNetActivity : IDotNetActivity

8.  Implementere (Tilføj) **Execute** metode til at **IDotNetActivity** grænsefladen til klassen **MyDotNetActivity** , og Kopiér følgende eksempelkode til metoden. I afsnittet [Udfør metode](#execute-method) forklaring for den logik, der bruges i denne metode.

        /// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement.
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
        /// </summary>
        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {

            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same
            // Azure Storage linked service for input and output.
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);

                // Calculate method returns the number of occurrences of
                // the search term (“Microsoft”) in each blob associated
                // with the data slice.
                //
                // definition of the method is shown in the next step.
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file.
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

            // The dictionary can be used to chain custom activities together in the future.
            // This feature is not implemented yet, so just return an empty dictionary.
            return new Dictionary<string, string>();
        }


9.  Tilføj følgende hjælper metoder til klassen. Disse metoder aktiveres af metoden **Execute** . Metoden **Calculate** isolerer det vigtigste er, den kode, der gentager listen over hver blob.

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.
        /// </summary>
        private static string GetFolderPath(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }

        /// <summary>
        /// Gets the fileName value from the input/output dataset.
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
        /// and prepares the output text that is written to the output blob.
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }


    Metoden **GetFolderPath** returnerer stien til den mappe, der datasættet peger på og metoden **GetFileName** returnerer navnet på den blob /-fil, der datasættet peger på.

        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "file.txt",
                "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",


    Metoden **Calculate** beregner antallet af forekomster af nøgleord **Microsoft** i dialogboksen input filer (BLOB i mappen). Søgeordet ("Microsoft") er hårdt kodet i koden.

10.  Samle projektet. Klik på **Opret** i menuen og **Opbygge løsning**.

11.  Start **Windows Stifinder**, og gå til **placering\\fejlfinding** eller **placering\\slip** mappe afhængigt af typen build.

12.  Oprette en zip-fil **MyDotNetActivity.zip** , der indeholder alle de binære filer i den ** \\placering\\fejlfinding** mappe. Du vil medtage i MyDotNetActivity. **pdb** -fil opstår, så du få yderligere oplysninger som linjenummer i koden, der forårsagede problemet, når en fejl.

    ![](./media/data-factory-data-processing-using-batch/image5.png)

13.  Overføre **MyDotNetActivity.zip** som en blob til objektbeholderen blob: **customactivitycontainer** i den Azure blob-lager, **StorageLinkedService** sammenkædet-tjenesten i **ADFTutorialDataFactory** bruger. Oprette blob objektbeholder **customactivitycontainer** , hvis den ikke allerede findes.

#### <a name="execute-method"></a>Udføre metode

Dette afsnit indeholder flere detaljer og noter om koden i metoden Execute.

1.  Medlemmerne for gentagelse gennem samlingen input findes i navneområdet [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) . Gentagelse gennem samlingen blob kræver brug af klassen **BlobContinuationToken** . Egentlig, skal du bruge en-mens løkke med token som metode til at forlade løkken. Se, [hvordan du bruger Blob-lager fra .NET](../storage/storage-dotnet-how-to-use-blobs.md)kan finde flere oplysninger. En grundlæggende løkke er vist her:

        // Initialize the continuation token.
        BlobContinuationToken continuationToken = null;
        do
        {
        // Get the list of input blobs from the input storage client object.
        BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                true,
                                          BlobListingDetails.Metadata,
                                          null,
                                          continuationToken,
                                          null,
                                          null);
        // Return a string derived from parsing each blob.
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

    Se i dokumentationen til metoden [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) for detaljer.

2.  Kode til at arbejde via sættet af BLOB logisk stå i afkrydsningsfeltet-mens løkke. I metoden **Execute** afkrydsningsfeltet-mens løkke overfører på listen over BLOB til en metode med navnet **Calculate**. Metoden returnerer en strengvariabel med navnet **output** , der er resultatet af har nævnt gennem alle BLOB i målgruppen.

    Den returnerer antallet af forekomster af søgeordet (**Microsoft**) BLOB sendes til funktionen **Calculate** .

        output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.  Når metoden **Calculate** har gjort arbejdet, skal den være skrevet til en ny blob. Så en ny blob kan skrives med resultaterne for hvert sæt af BLOB behandles. Du kan skrive til en ny blob ved først at finde output datasættet.

        // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

4.  Koden også kalder en hjælper metode: **GetFolderPath** til at hente stien til mappen (lagerplads objektbeholder navn).

        folderPath = GetFolderPath(outputDataset);

    **GetFolderPath** konverterer en AzureBlobDataSet, som har en egenskab med navnet mappesti datasæt objektet.

        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

        return blobDataset.FolderPath;

5.  Koden kalder metoden **GetFileName** for at hente filnavnet (blob navn). Koden er lig med koden ovenfor for at få stien til mappen.

        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

        return blobDataset.FileName;

6.  Navnet på filen skrives ved at oprette et URI-objekt. URI-parametre bruges egenskaben **BlobEndpoint** til at returnere objektbeholder navn. Stien og navnet på mappen føjes til at opbygge output blob URI.  

        // Write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.  Navnet på fil, der er skrevet, og du kan nu skrive Outputstrengen fra metoden **Calculate** til en ny blob:

        // Create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);


### <a name="create-the-data-factory"></a>Oprette data factory

Du har oprettet en brugerdefineret aktivitet og overføres zip-filen med binære filer og PDB-fil til en Azure blob-objektbeholder i afsnittet [oprette tilpassede aktiviteter](#create-the-custom-activity) . I dette afsnit, skal oprette du en Azure **data factory** med en **rørledning** , der bruger den **tilpassede aktiviteter**.

Indtast datasættet til den brugerdefinerede aktivitet repræsenterer BLOB (filer) i mappen input (mycontainer\\inputfolder) i blob-lager. Output datasæt for aktiviteten repræsenterer output BLOB i outputmappen (mycontainer\\outputfolder) i blob-lager.

Slip en eller flere filer i mapperne input:

    mycontainer -\> inputfolder
        2015-11-16-00
        2015-11-16-01
        2015-11-16-02
        2015-11-16-03
        2015-11-16-04

For eksempel slippe én fil (fil.txt) med følgende indhold på hver af mapperne.

    test custom activity Microsoft test custom activity Microsoft

Hver input mappe svarer til et udsnit i Azure Data Factory, også selvom mappen indeholder 2 eller flere filer. Når hvert udsnit er behandlet af pipeline, gentager den tilpassede aktiviteter listen over alle BLOB i mappen input til udsnittet.

Du ser fem outputfiler med det samme indhold. For eksempel har outputfilen fra behandling af filen i mappen 2015-11-16-00 følgende indhold:

    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

Hvis du slipper flere filer (fil.txt, fil2.txt, file3.txt) med det samme indhold til mappen input, ser du følgende indhold i outputfilen. Hver mappe (2015-11-16-00, osv.) svarer til et udsnit i dette eksempel, selvom mappen, hvor flere filer er input.

    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.


Outputfilen indeholder tre linjer nu en for hver input fil (blob) i den mappe, der er knyttet til udsnit (2015-11-16-00).

Der oprettes en opgave for hver aktivitet køre. I dette eksempel er der kun én aktivitet i pipeline. Når et udsnit er behandlet af pipeline, kører den brugerdefinerede aktivitet på Azure batchen til at behandle udsnittet. Fordi der er fem udsnit (hvert udsnit kan have flere BLOB eller fil), der er fem opgaver, der er oprettet i Azure batchen. Når en opgave kører på batchen, er det faktisk tilpassede aktiviteter, der kører.

Følgende gennemgang indeholder flere oplysninger.

#### <a name="step-1-create-the-data-factory"></a>Trin 1: Oprette data factory

1.  Benyt følgende fremgangsmåde, når du logger på [Azure-portalen](https://portal.azure.com/):

    1.  Klik på **Ny** i menuen til venstre.

    2.  Klik på **Data + Analytics** i bladet **Ny** .

    3.  Klik på **Data Factory** i bladet **Data analyser** .

2.  Angiv **CustomActivityFactory** for navnet i bladet **nye data factory** . Navnet på Azure data factory skal være globalt entydige. Hvis du får fejl: **Data factory navnet "CustomActivityFactory" er ikke tilgængelig**, ændre navnet på data factory (for eksempel **yournameCustomActivityFactory**) og prøve at oprette igen.

3.  Klik på **RESSOURCEN GRUPPENAVN**, og vælg en eksisterende ressourcegruppe, eller Opret en ressourcegruppe.

4.  Kontrollér, at du bruger den korrekte abonnement og område, hvor du vil data factory skal oprettes.

5.  Klik på **Opret** på bladet **nye data factory** .

6.  Du kan se data fabriksindstillingerne der oprettes i **Dashboard** i portalen Azure.

7.  Når data factory er blevet oprettet, kan du se siden fabrik, som viser indholdet af data factory.

 ![](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Trin 2: Oprette sammenkædede tjenester

Sammenkædede services sammenkæde data butikker eller beregne tjenester til en Azure data factory. I dette trin kan sammenkæde du din **Azure-lager** og **Azure batchen** kontoen til dine data factory.

#### <a name="create-azure-storage-linked-service"></a>Oprette Azure-lager, der er sammenkædet service

1.  Klik på den **forfatter og installere** flisen på bladet **DATA FACTORY** for **CustomActivityFactory**. Du kan se Data Factory Editor.

2.  Klik på **nye data gemmes** på kommandolinjen og vælg **Azure-lager.** Du bør se JSON scriptet til at oprette en sammenkædet Azure-lager tjeneste i editoren.

    ![](./media/data-factory-data-processing-using-batch/image7.png)

3.  Erstat **kontonavn** med navnet på din konto Azure-lager og **kontonøgle** hurtigtast konto, Azure-lager. Få [vist, Kopiér og Regenerer lagerplads access-taster](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)for at lære at få din lagerplads hurtigtast.

4.  Klik på **Implementer** på kommandolinjen til at udrulle sammenkædede tjenesten.

    ![](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-azure-batch-linked-service"></a>Oprette Azure batchen sammenkædet service

I dette trin skal oprette du en sammenkædet tjeneste for kontoen **Azure batchen** , der bruges til at køre den brugerdefinerede Data Factory-aktivitet.

1.  Klik på **Ny beregne** på kommandolinjen og vælg **Azure batchen.** Du bør se JSON scriptet til at oprette en Azure batchen sammenkædet tjeneste i editoren.

2.  I JSON-script:

    1.  Erstat **kontonavn** med navnet på din konto, Azure batchen.

    2.  Erstat **hurtigtast** hurtigtast Azure batchen konto.

    3.  Angiv et ID for puljen for egenskaben **poolName** **.** For denne egenskab, kan du angive enten puljen navn eller ressourcepuljen ID.

    4.  Angiv batchen URI for egenskaben **batchUri** JSON. 
    
        > [AZURE.IMPORTANT] **URL-adressen** fra **Azure batchen konto blade** er i følgende format: \<kontonavn\>. \<område\>. batch.azure.com. I egenskaben **batchUri** i JSON skal du **fjerne "kontonavn."** fra URL-adressen. Eksempel: `"batchUri": "https://eastus.batch.azure.com"`.

        ![](./media/data-factory-data-processing-using-batch/image9.png)

        Du kan også angive puljen i stedet for navnet på puljen ID for egenskaben **poolName** .

        > [AZURE.NOTE] Tjenesten Data Factory understøtter ikke en indstilling efter behov for Azure batchen, på samme måde som for HDInsight. Du kan kun bruge din egen Azure batchen puljen i en Azure data factory.

    5.  Angiv egenskaben **linkedServiceName** **StorageLinkedService** . Du har oprettet denne sammenkædede tjeneste i det forrige trin. Denne lagerplads bruges som en midlertidige for logfiler og filer.

3.  Klik på **Implementer** på kommandolinjen til at udrulle sammenkædede tjenesten.

#### <a name="step-3-create-datasets"></a>Trin 3: Opret datasæt

I dette trin skal oprette du datasæt for at repræsentere input- og outputområder data.

#### <a name="create-input-dataset"></a>Oprette input datasæt

1.  Klik på **nye datasæt** knap på værktøjslinjen i **Editor** til fabriksindstillingerne Data, og vælg **Azure Blob-lager** i rullemenuen.

2.  Erstat JSON i højre rude med følgende JSON-kodestykket:

        {
            "name": "InputDataset",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService",
                "typeProperties": {
                    "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
                    "format": {
                        "type": "TextFormat"
                    },
                    "partitionedBy": [
                        {
                            "name": "Year",
                            "value": {
                                "type": "DateTime",
                                "date": "SliceStart",
                                "format": "yyyy"
                            }
                        },
                        {
                            "name": "Month",
                            "value": {
                                "type": "DateTime",
                                "date": "SliceStart",
                                "format": "MM"
                            }
                        },
                        {
                            "name": "Day",
                            "value": {
                                "type": "DateTime",
                                "date": "SliceStart",
                                "format": "dd"
                            }
                        },
                        {
                            "name": "Hour",
                            "value": {
                                "type": "DateTime",
                                "date": "SliceStart",
                                "format": "HH"
                            }
                        }
                    ]
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "external": true,
                "policy": {}
            }
        }


     Du opretter en rørledning senere i denne gennemgang med starttidspunkt: 2015-11-16T00:00:00Z-og sluttidspunkt: 2015-11-16T05:00:00Z. Det er planlagt til at producere data **hver time**, så der er 5 input/output udsnit (mellem **00**: 00:00 -\> **05**: 00:00).

     **Frekvens** og **interval** for input datasættet er indstillet til **time** og **1**, hvilket betyder, at det input udsnit er tilgængelige hver time.

     Her er starttidspunkter for hvert udsnit, der er repræsenteret af **SliceStart** systemvariabel i ovenstående JSON kodestykket.

  	| **Udsnit** | **Starttidspunktet**          |
  	|-----------|-------------------------|
  	| 1         | 2015-11-16T**00**: 00:00 |
  	| 2         | 2015-11-16T**01**: 00:00 |
  	| 3         | 2015-11-16T**02**: 00:00 |
  	| 4         | 2015-11-16T**03**: 00:00 |
  	| 5         | 2015-11-16T**04**: 00:00 |

     **Mappesti** beregnes ved hjælp af den år, måned, dag og time del af udsnit starttidspunktet (**SliceStart**). Her er derfor hvordan en input mappe er knyttet til et udsnit.

  	| **Udsnit** | **Starttidspunktet**          | **Input-mappe**  |
  	|-----------|-------------------------|-------------------|
  	| 1         | 2015-11-16T**00**: 00:00 | 2015-11-16 -**00** |
  	| 2         | 2015-11-16T**01**: 00:00 | 2015-11-16 -**01** |
  	| 3         | 2015-11-16T**02**: 00:00 | 2015-11-16 -**02** |
  	| 4         | 2015-11-16T**03**: 00:00 | 2015-11-16 -**03** |
  	| 5         | 2015-11-16T**04**: 00:00 | 2015-11-16 -**04** |

3.  Klik på **Implementer** på værktøjslinjen for at oprette og installere tabellen **InputDataset** . 

#### <a name="create-output-dataset"></a>Oprette output datasæt

I dette trin skal oprette du et andet dataset af typen AzureBlob til at repræsentere outputdata.

1.  Klik på **nye datasæt** knap på værktøjslinjen i **Editor** til fabriksindstillingerne Data, og vælg **Azure Blob-lager** i rullemenuen.

2.  Erstat JSON i højre rude med følgende JSON-kodestykket:

        {
            "name": "OutputDataset",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService",
                "typeProperties": {
                    "fileName": "{slice}.txt",
                    "folderPath": "mycontainer/outputfolder",
                    "partitionedBy": [
                        {
                            "name": "slice",
                            "value": {
                                "type": "DateTime",
                                "date": "SliceStart",
                                "format": "yyyy-MM-dd-HH"
                            }
                        }
                    ]
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }


    Der oprettes en blob/outputfil for hvert input udsnit. Her er, hvordan en outputfil har navnet for hvert udsnit. Alle outputfilerne er oprettet i én outputmappe: **mycontainer\\outputfolder**.


  	| **Udsnit** | **Starttidspunktet**          | **Outputfil**       |
  	|-----------|-------------------------|-----------------------|
  	| 1         | 2015-11-16T**00**: 00:00 | 2015-11-16 -**00. txt** |
  	| 2         | 2015-11-16T**01**: 00:00 | 2015-11-16 -**01. txt** |
  	| 3         | 2015-11-16T**02**: 00:00 | 2015-11-16 -**02. txt** |
  	| 4         | 2015-11-16T**03**: 00:00 | 2015-11-16 -**03. txt** |
  	| 5         | 2015-11-16T**04**: 00:00 | 2015-11-16 -**04. txt** |

     Husk, at alle filerne i en input mappe (for eksempel: 2015-11-16-00) er en del af et udsnit med starttidspunktet: 2015-11-16-00. Når dette udsnit behandles, tilpassede aktiviteter scanner gennem hver fil og giver en linje i outputfilen med antallet af forekomster af søgeord ("Microsoft"). Hvis der er tre filer i mappen 2015-11-16-00, der er tre linjer i outputfilen: 2015-11-16-00.txt.

3.  Klik på **Implementer** på værktøjslinjen for at oprette og installere **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-custom-activity"></a>Trin 4: Oprette og køre rørledningen med tilpassede aktiviteter

I dette trin skal oprette du en rørledning med en aktivitet, den tilpassede aktiviteter, du oprettede tidligere.

> [AZURE.IMPORTANT] Hvis du ikke har overført **fil.txt** for at skrive mapper i objektbeholderen blob, skal du gøre det før du opretter rørledningen. Egenskaben **isPaused** er indstillet til falsk i pipeline JSON, så pipeline kører med det samme som **startdatoen** er passeret.

1.  Klik på **ny pipeline** på kommandolinjen i Data Factory Editor. Hvis du ikke kan se kommandoen, skal du klikke på **... (Ellipse)** for at se den.

2.  Erstat JSON i højre rude med følgende JSON-script:

        {
            "name": "PipelineCustom",
            "properties": {
                "description": "Use custom activity",
                "activities": [
                    {
                        "type": "DotNetActivity",
                        "typeProperties": {
                            "assemblyName": "MyDotNetActivity.dll",
                            "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "packageLinkedService": "AzureStorageLinkedService",
                            "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                        },
                        "inputs": [
                            {
                                "name": "InputDataset"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "OutputDataset"
                            }
                        ],
                        "policy": {
                            "timeout": "00:30:00",
                            "concurrency": 5,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MyDotNetActivity",
                        "linkedServiceName": "AzureBatchLinkedService"
                    }
                ],
                "start": "2015-11-16T00:00:00Z",
                "end": "2015-11-16T05:00:00Z",
                "isPaused": false
           }
        }

    Bemærk følgende punkter:

    -   Der er kun en aktivitet i pipeline og det er af typen: **DotNetActivity**.

    -   **AssemblyName** er indstillet til navnet på DLL: **MyDotNetActivity.dll**.

    -   **Indgangspunkt** er angivet til **MyDotNetActivityNS.MyDotNetActivity**. Det er grundlæggende \<navneområde\>. \<klassenavn\> i din kode.

    -   **PackageLinkedService** er angivet til **StorageLinkedService** , der peger på den blob-lager, der indeholder den tilpassede aktiviteter zip-fil. Hvis du bruger forskellige Azure-lager konti til input/output filer og den tilpassede aktiviteter zip-fil, har du oprette en anden Azure-lager, der er sammenkædet tjeneste. Denne artikel forudsætter, at du bruger den samme konto Azure-lager.

    -   **PackageFile** er angivet til **customactivitycontainer/MyDotNetActivity.zip**. Det er i formatet: \<containerforthezip\>/\<nameofthezip.zip\>.

    -   Tilpassede aktiviteter tager **InputDataset** som input og **OutputDataset** som output.

    -   Egenskaben **linkedServiceName** for den brugerdefinerede aktivitet peger på **AzureBatchLinkedService**, der fortæller Azure Data Factory, som den tilpassede aktiviteter skal køre på Azure batchen.

    -   Indstillingen **på dokumentsammenfald** er vigtigt. Hvis du bruger standardværdien, som er 1, selvom du har 2 eller mere beregne knuder på Azure batchen puljen, udsnittene behandles efter hinanden. Du derfor ikke at drage fordel af muligheden for parallel behandling af Azure Batch. Hvis du angiver **på dokumentsammenfald** til en højere værdi, sig 2, betyder det, at to udsnit (svarer til to opgaver i Azure batchen) kan behandles på samme tid, i hvilket tilfælde begge FOS i Azure batchen puljen anvendes. Derfor indstille egenskaben på dokumentsammenfald korrekt.

    -   Kun én opgave (udsnit), der udføres på en VM når som helst som standard. Det skyldes, at som standard **maksimalt opgaver per VM** er angivet til 1 for en Azure batchen puljen. Som en del af forudsætninger, du har oprettet en gruppe med denne egenskab, der er angivet til 2, så kan køre to Data Factory udsnit på en VM på samme tid.


    -   **isPaused** egenskab er som standard angivet til falsk. Pipeline kører med det samme i dette eksempel, fordi udsnittene starter i fortiden. Du kan angive denne egenskab til Sand for at afbryde pipeline og angive den tilbage til false for at genstarte.

    -   **Starttidspunkt** og **Sluttidspunkt** er fem timer fra hinanden og udsnit er produceret hver time, så fem udsnit er oprettet af pipeline.

3.  Klik på **Implementer** på kommandolinjen til at udrulle pipeline.

#### <a name="step-5-test-the-pipeline"></a>Trin 5: Test pipeline

I dette trin skal teste du pipeline ved at slippe filer i mapperne input. Lad os starte med at teste rørledningen med én fil i én input mappe.

1.  Klik på **Diagram**i bladet Data Factory i portalen Azure.

    ![](./media/data-factory-data-processing-using-batch/image10.png)

2.  Dobbeltklik på input datasæt i diagramvisning,: **InputDataset**.

    ![](./media/data-factory-data-processing-using-batch/image11.png)

3.  Du bør se bladet **InputDataset** med alle fem udsnit, der er klar. Bemærk **UDSNIT STARTTIDSPUNKT** og **SLUTTIDSPUNKT for UDSNIT** for hvert udsnit.

    ![](./media/data-factory-data-processing-using-batch/image12.png)

4.  Klik nu på **OutputDataset**i **Diagramvisning**.

5.  Du bør se, at fem output udsnittene er i tilstanden klar, hvis de allerede er produceret.

    ![](./media/data-factory-data-processing-using-batch/image13.png)

6.  Bruge Azure portal til at få vist de **opgaver** , der er knyttet til **udsnit** og se, hvad hvert udsnit kørte på VM. Se [Data Factory og batchen integration](#data-factory-and-batch-integration) afsnittet Yderligere oplysninger. 

7.  Du bør se outputfilerne i **outputfolder** af **mycontainer** i dit Azure blob-lager.

    ![](./media/data-factory-data-processing-using-batch/image15.png)

    Du bør se fem outputfiler, én for hvert input udsnit. Hver af outputfilen bør have indhold, der ligner følgende output:

        2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

    I følgende diagram vises, hvordan Data Factory udsnittene knyttes til opgaver i Azure batchen. I dette eksempel har et udsnit kun én Kør.

    ![](./media/data-factory-data-processing-using-batch/image16.png)

8.  Nu, Lad os prøve med flere filer i en mappe. Oprette filer: **fil2.txt**, **file3.txt**, **file4.txt**og **file5.txt** med det samme indhold som fil.txt i mappen: **2015-11-06-01**.

9.  I outputmappen skal **slette** outputfilen: **2015-11-16-01.txt**.

10. Nu, højreklik på udsnittet med **UDSNIT STARTTIDSPUNKT** , der er angivet til i bladet **OutputDataset** **11-16-2015 01:00:00 AM**, og klik på **Kør** til Kør/genudførsel-transparente udsnittet. Nu har udsnittet fem filer i stedet for én fil.

    ![](./media/data-factory-data-processing-using-batch/image17.png)

11. Når udsnittet kører og dens status er **klar**, skal du kontrollere indholdet i outputfilen for denne udsnit (**2015-11-16-01.txt**) i **outputfolder** af **mycontainer** i dit blob-lager. Der skal være en linje for hver fil på udsnittet.

        2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
        2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
        2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
        2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
        2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.


> [AZURE.NOTE] Hvis du ikke har slettet output fil 2015-11-16-01.txt før du prøver med fem input filer, kan du se én linje fra den forrige udsnit Kør og fem linjer fra den aktuelle udsnit Kør. Som standard føjes indholdet for at outputfil, hvis den findes allerede.

#### <a name="data-factory-and-batch-integration"></a>Data Factory og batchen integration
Tjenesten Data Factory opretter en sag i Azure batchen med navnet: **adf-poolname:job-xxx**. 

![Azure Data Factory - kørslen.](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Der oprettes en opgave i jobbet for hver aktivitet kørsel af et udsnit. Hvis der er klar til at blive behandlet 10 udsnit, oprettes 10 opgaver i jobbet. Du kan have mere end ét udsnit kører parallelt, hvis du har flere Beregn noder i puljen. Hvis de maksimale opgaver per beregningsnode er indstillet til > 1, kan der være mere end ét udsnit, der kører på den samme Beregn.

I dette eksempel er der fem udsnit, så fem opgaver i Azure Batch. Med den **på dokumentsammenfald** , der er angivet til **5** i pipeline angive JSON Azure Data Factory og **Maksimum opgaver per VM** til **2** i Azure batchen puljen med **2** FOS, den opgaver kører hurtigt (se start- og sluttidspunkter for opgaver).

Brug portalen til at få vist kørslen og dens opgaver, der er knyttet til **udsnit** og se, hvilke VM hvert udsnit kørte på. 

![Azure Data Factory - batchen arbejdsopgaver](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Fejlfinding af pipeline

Fejlfinding består af nogle grundlæggende teknikker:

1.  Hvis det input udsnit ikke er angivet til **klar**, kan du bekræfte, input mappestrukturen er korrekt og fil.txt findes i mapperne input.

    ![](./media/data-factory-data-processing-using-batch/image3.png)

2.  Bruge objektet **IActivityLogger** til at logge oplysninger, der hjælper dig med at løse problemer i metoden **Execute** af din tilpassede aktiviteter. De gemte meddelelser vises i brugeren\_0. logfil.

    Klik på udsnittet for at få vist bladet **DATA UDSNIT** for udsnittet i bladet **OutputDataset** . Du kan se **aktivitet kører** for udsnittet. Du bør se én aktivitet køre til udsnittet. Hvis du klikker på **Kør** i kommandolinjen, kan du starte en anden aktivitet køre til det samme udsnit.

    Når du klikker på Kør aktivitet, kan du se bladet **Aktivitet køre detaljer** med en liste over logfiler. Du kan finde logget meddelelser i den **bruger\_0. log** fil. Når der opstår en fejl, kan du se tre aktivitet kører, fordi antal forsøg er indstillet til 3 i pipeline/aktiviteten JSON. Når du klikker på Kør aktivitet, kan du se de logfiler, du kan gennemse for at foretage fejlfinding af fejlen.

    ![](./media/data-factory-data-processing-using-batch/image18.png)

    Klik på **bruger-0.log**på listen over logfiler. Er resultatet af ved hjælp af metoden **IActivityLogger.Write** i højre panel.

    ![](./media/data-factory-data-processing-using-batch/image19.png)

    Se system-0.log på en hvilken som helst system fejlmeddelelser og undtagelser.

        Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

        Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

        Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

        Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully

3.  Medtage filen **PDB** i zip-filen, så oplysninger om fejlen har oplysninger som **stak af kald** , når der opstår en fejl.

4.  Alle filerne i zip-filen til den brugerdefinerede aktivitet skal være på **øverste niveau** med ingen undermapper.

    ![](./media/data-factory-data-processing-using-batch/image20.png)

5.  Sørg for, at den **assemblyName** (MyDotNetActivity.dll), **indgangspunkt** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) og **packageLinkedService** (skal pege på Azure blob-lager, der indeholder zip-fil) er angivet til korrekte værdier.

6.  Hvis du fast en fejl og vil igen udsnittet, skal du højreklikke på udsnittet i bladet **OutputDataset** , og klik på **Kør**.

    ![](./media/data-factory-data-processing-using-batch/image21.png)

    > [AZURE.NOTE] 
    > Du får vist en **objektbeholder** i dit Azure Blob-lager, med navnet: **adfjobs**. Denne beholder slettes ikke automatisk, men du kan slette den på en sikker måde, når du er færdig med test af løsningen. På samme måde, Data Factory løsningen opretter en Azure **job** med navnet: **adf -\<ressourcepuljen ID/navn\>: jobbet 0000000001**. Når du har testet løsningen, hvis du ønsker, kan du slette dette job.
7. Den brugerdefinerede aktivitet bruger ikke **app.config** -filen fra din pakke. Derfor, hvis din kode læser alle strenge fra konfigurationsfilen, virker det ikke på kørselstidspunktet. Den bedste fremgangsmåde, når ved hjælp af Azure batchen er ved at holde en hvilken som helst hemmeligheder i en **Azure KeyVault**, bruge et certifikat-baseret tjeneste hovedstolen til at beskytte keyvault og distribuere certifikat til Azure batchen puljen. Den .NET tilpassede aktiviteter kan derefter åbne hemmeligheder fra KeyVault på kørselstidspunktet. Denne løsning er en generisk og kan skalere til alle typer hemmeligt, ikke kun forbindelsesstreng.

    Der er en nemmere løsning (men ikke en bedste fremgangsmåde): Du kan oprette en **sammenkædet SQL Azure service** med forbindelse streng indstillinger, oprette et datasæt, der bruger tjenesten sammenkædede og sammenkæde datasættet som et dummy input datasæt på den brugerdefinerede .NET aktivitet. Du kan derefter få adgang til tjenesten sammenkædede forbindelsesstrengen i koden tilpassede aktiviteter og det skal fungere korrekt på kørselstidspunktet.  

#### <a name="extend-the-sample"></a>Udvide stikprøvernes

Du kan udvide dette eksempel mere at vide om Azure Data Factory og Azure batchen funktioner. Eksempelvis for at behandle udsnit i et andet tidsrum, udføre følgende trin:

1.  Tilføj følgende undermapper i **inputfolder**: 2015-11-16-05 2015-11-16-06 201-11-16-07, 2011-11-16-08, 2015-11-16-09 og placere input filer i disse mapper. Ændre sluttidspunktet for rørledning fra `2015-11-16T05:00:00Z` til `2015-11-16T10:00:00Z`. Dobbeltklik på **InputDataset**i **Diagramvisning**, og Bekræft, at de input udsnit er klar. Dobbeltklik på **OuptutDataset** for at få vist status for output udsnit. Hvis de er i klar tilstand, kan du se outputfolder for outputfiler.

2.  Øge eller mindske indstillingen **på dokumentsammenfald** at forstå, hvordan det påvirker ydeevnen for din løsning, især behandling, der optræder på Azure batchen. (Se trin 4: oprette og køre rørledningen mere at vide om indstillingen **på dokumentsammenfald** .)

3.  Oprette en gruppe med højere/nederste **Maksimum opgaver per VM**. For at bruge den nye gruppe, du har oprettet, skal du opdatere tjenesten Azure batchen sammenkædet i Data Factory-løsning. (Se trin 4: oprette og køre rørledningen mere at vide om indstillingen **Maksimum opgaver per VM** .)

4.  Oprette en Azure batchen gruppe med **Autoskalering** funktion. Automatisk skalering Beregn knuder på en Azure batchen puljen er dynamiske justering af behandling power bruges af programmet. For eksempel kan du oprette en azure batchen puljen med 0 dedikeret FOS og en Autoskalering formel, der er baseret på antallet ventende opgaver:
 
    Én VM per afventer opgave ad gangen (for eksempel: Giv ventende opgaver -> fem FOS):

        pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
        $TargetDedicated = max(pendingTaskSampleVector);

    Maksimalt antal én VM ad gangen uanset antallet af ventende opgaver:

        pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
        $TargetDedicated = (max(pendingTaskSampleVector)>0)?1:0;

    Se [Automatisk skala beregne knuder på en Azure batchen puljen](../batch/batch-automatic-scaling.md) få mere at vide. 

    Hvis puljen bruger standard [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), kan tjenesten batchen tage 15-30 minutter at forberede VM før du kører den tilpassede aktiviteter.  Hvis puljen bruger en anden autoScaleEvaluationInterval, kan tjenesten batchen tage autoScaleEvaluationInterval + 10 minutter. 
     
5. I eksempel-løsningen aktiverer metoden **Execute** metoden **Calculate** , der behandler et inputdataene udsnit for at frembringe et output data udsnit. Du kan skrive din egen metode til at behandle inputdataene og erstatte Calculate metode opkald i metoden Execute med et opkald til din metode.

 


### <a name="next-steps-consume-the-data"></a>Næste trin: bruge dataene

Når du behandler data, kan du bruge det med onlineværktøjer som **Microsoft Power BI**. Her er links, der kan hjælpe dig med at forstå Power BI og hvornår det skal bruges i Azure:

-   [Undersøge et datasæt i Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-data/)

-   [Introduktion til Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/)

-   [Opdatere data i Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-refresh-data/)

-   [Azure og Power BI - grundlæggende oversigt](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referencer

-   [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

    -   [Introduktion til Azure Data Factory-tjenesten](data-factory-introduction.md)

    -   [Introduktion til Azure Data Factory](data-factory-build-your-first-pipeline.md)

    -   [Bruge brugerdefinerede aktiviteter i en Azure Data Factory pipeline](data-factory-use-custom-activities.md)

-   [Azure batchen](https://azure.microsoft.com/documentation/services/batch/)

    -   [Grundlæggende om Azure batchen](../batch/batch-technical-overview.md)

    -   [Oversigt over Azure batchen funktioner](../batch/batch-api-basics.md)

    -   [Oprette og administrere Azure batchen konto i portalen Azure](../batch/batch-account-create-portal.md)

    -   [Introduktion til Azure batchen bibliotek .NET](../batch/batch-dotnet-get-started.md)


[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx


<properties
    pageTitle="Generere anbefalinger ved hjælp af Mahout og WIndows-baseret HDInsight | Microsoft Azure"
    description="Lær at bruge Apache Mahout maskinen læ bibliotek til at generere film anbefalinger med Windows-baseret HDInsight (Hadoop)."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>Generere anbefalinger film ved hjælp af Apache Mahout med Hadoop i HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Lær at bruge den [Apache Mahout](http://mahout.apache.org) maskine læ bibliotek med Azure HDInsight til at generere film anbefalinger.

> [AZURE.NOTE] Trinnene i dette dokument kræver en Windows-klient og en Windows-baseret HDInsight klynge. Oplysninger om brug af Mahout fra en Linux, OS X eller Unix-klient med en Linux-baserede HDInsight klynge, se [Generer film anbefalinger ved hjælp af Apache Mahout med Linux-baserede Hadoop i HDInsight](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>Hvad du lærer

Mahout er en [maskine læ] [ ml] bibliotek til Apache Hadoop. Mahout indeholder algoritmer til at behandle data, som filtrerer, klassificering, og klynge. I denne artikel kan bruge du en anbefaling program til at generere film anbefalinger, der er baseret på dine venner har set-film. Du kan også Lær at udføre klassificeringer med en beslutning-område. Dette lærer du følgende:

* Sådan køres Mahout job ved hjælp af Windows PowerShell

* Sådan køres Mahout job fra kommandolinjen Hadoop

* Hvordan du installerer Mahout på HDInsight 3.0 og HDInsight 2.0 klynger

    > [AZURE.NOTE] Mahout leveres med HDInsight 3.1 versionen klynger. Hvis du bruger en tidligere version af HDInsight, kan du se [Installere Mahout](#install) , før du fortsætter.

##<a name="prerequisites"></a>forudsætninger

- **En Windows-baseret Hadoop klynge i HDInsight**. Oplysninger om oprettelse af en finder du i [gang med at bruge Hadoop i HDInsight][getstarted]
- **En arbejdsstation med Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a name="recommendations"></a>Generere anbefalinger ved hjælp af Windows PowerShell

> [AZURE.NOTE] Selvom jobbet bruges i dette afsnit fungerer ved hjælp af Windows PowerShell, mange af de klasser, der følger med Mahout fungerer ikke i øjeblikket sammen med Windows PowerShell og de skal køres ved hjælp af Hadoop-kommandolinjen. I afsnittet [fejlfinding i forbindelse med](#troubleshooting) en liste over klasser, der ikke fungerer sammen med Windows PowerShell.
>
> Du kan finde et eksempel på en Hadoop kommandolinje til at køre Mahout job, [klassificeringen data ved hjælp af Hadoop-kommandolinjen](#classify).

En af de funktioner, som leveres af Mahout er en anbefaling program. Dette program accepterer data i formatet af `userID`, `itemId`, og `prefValue` (indstillingen brugere for elementet). Mahout kan derefter udføre samtidig forekomst analyse til at bestemme: _brugere, der har en indstilling for et element har også en indstilling for disse andre elementer_. Mahout bestemmer derefter brugere med indstillinger i synes godt om-element, som kan bruges til at foretage anbefalinger.

Følgende er en meget simpel eksempel, der bruger film:

* __Samtidig forekomst__: Joe, Bent og Bob trykket synes godt om _stjerne krige_, _Tilbage slår ned til feltet imperium_og _returnere af Jedi_. Mahout bestemmer, at brugere der synes godt om et af disse film også som de andre to.

* __Samtidig forekomst__: Bob og Bent også synes godt om _Feltet Fantombillede truslen fra rummet_, _angreb af både_og _hævn af Sith_. Mahout bestemmer, at brugere, der synes godt om de forrige tre film også som disse tre.

* __Lighed anbefaling__: fordi Joe synes godt om de tre første film, Mahout ser på film, som andre med samme indstillinger synes godt om, men Joe ikke har set (synes godt om/klassificeret). I dette tilfælde anbefaler Mahout _Feltet Fantombillede truslen fra rummet_, _angreb af både_og _hævn af Sith_.

###<a name="understanding-the-data"></a>Forstå dataene

Når jeg [GroupLens opslag] [ movielens] giver bedømmelse data til film i et format, der er kompatibelt med Mahout. Disse data er tilgængelige på din klynge standard lagerplads på `/HdiSamples/MahoutMovieData`.

Der findes to filer `moviedb.txt` (oplysninger om filmene,) og `user-ratings.txt`. Bruger ratings.txt filen og bruges under analyse, mens moviedb.txt bruges til at give brugervenlig tekst oplysninger, når viser resultaterne af analysen.

Data i bruger-ratings.txt har en struktur af `userID`, `movieID`, `userRating`, og `timestamp`, som oplyser, hvor meget hver bruger bedømt en film. Her er et eksempel på dataene:


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

###<a name="run-the-job"></a>Kørsel

Brug følgende Windows PowerShell-script til at køre et job, der bruger Mahout anbefaling program med filmdataene:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
            
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
    # NOTE: The version number in the file path
    # may change in future versions of HDInsight.
    $jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
                    "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                    "--output", "wasbs:///example/out",
                    "--tempDir", "wasbs:///example/temp"

    # Create the job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
    # Write out any error information
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE] Mahout job fjerner ikke midlertidige data, der oprettes under behandling af jobbet. Den `--tempDir` parameter er angivet i eksempel jobbet til at isolere de midlertidige filer i et bestemt bibliotek.

Kørslen Mahout returnerer ikke output til STDOUT. I stedet, gemmes det i den angivne outputmappe som __en del-r-00000__. Scriptet henter denne fil til __output.txt__ i den aktuelle mappe på computeren.

Følgende er et eksempel på indholdet af denne fil:

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Den første kolonne er den `userID`. De værdier, der er indeholdt i ' [' og ']' er `movieId`:`recommendationScore`.

###<a name="view-the-output"></a>Få vist output

Selvom genererede output kan være OK til brug i et program, er det ikke meget læsbare. Den `moviedb.txt` fra serveren kan bruges til at løse det `movieId` til en film navn, men du skal først hente den og filen bedømmelser fra serveren ved hjælp af følgende script:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

Når du har hentet filerne, kan du bruge følgende PowerShell-script til at vise anbefalinger med film navne:

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "user-ratings.txt"
            -movieFile "moviedb.txt"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

Følgende er et eksempel på at køre scriptet:

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

Output skal se ud som følger:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Klassificere data ved hjælp af kommandolinjen Hadoop

En af de tilgængelige med Mahout klassifikation metoder er at opbygge en [tilfældigt skov][forest]. Dette er en proces i flere trin, der involverer ved hjælp af kursus data til at generere beslutningstræer, som derefter bruges til at klassificere data. Dette bruger klassen __org.apache.mahout.classifier.df.tools.Describe__ leveres af Mahout. Den skal aktuelt køres ved hjælp af Hadoop-kommandolinjen.

###<a name="load-the-data"></a>Indlæs data

1. Du kan hente følgende filer fra [feltet NSL KDD datasæt](http://nsl.cs.unb.ca/NSL-KDD/).

  * [KDDTrain +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): filen kursus

  * [KDDTest +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): testdata

2. Åbne hver fil, og fjerne stregerne øverst, der starter med '@', og Gem filerne. Hvis dette ikke er fjernet, modtager du fejlmeddelelser, når du bruger disse data med Mahout.

2. Du kan overføre filerne til __eksempeldataene /__. Du kan gøre dette ved hjælp af følgende script. Erstat __CLUSTERNAME__ med navnet på HDInsight klyngen. Erstat FILNAVN med navnet fo filen overføres.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###<a name="run-the-job"></a>Kørsel

1. Dette job kræver Hadoop-kommandolinjen. Aktivere Fjernskrivebord for HDInsight-klyngen, og derefter oprette forbindelse til den ved at følge vejledningen på [Opret forbindelse til HDInsight klynger ved hjælp af RDP](hdinsight-administer-use-management-portal.md#rdp).

3. Når du har forbindelse, skal du bruge ikonet __Hadoop kommandolinjen__ til at åbne kommandolinjen Hadoop:

    ![hadoop cli][hadoopcli]

3. Brug følgende kommando til at oprette en fil beskrivelse (__KDDTrain + .info__), som bruger Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    Den `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` beskriver attributterne for dataene i filen. For eksempel angiver L en etiket.

4. Opbygge et område af beslutningstræer ved hjælp af følgende kommando:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    Output fra denne handling er gemt i mappen __nsl-område__ , der er placeret i lagerplads på din HDInsight klynge på __ wasbs://user/&lt;brugernavn > / nsl-skov/nsl-forest.seq. Den &lt;brugernavn > er det brugernavn, du har brugt til Fjernskrivebord-sessionen. Denne fil kan ikke læses af mennesker.

5. Test området ved at klassificere __KDDTest + .arff__ datasættet. Brug følgende kommando:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    Denne kommando returnerer oversigtsoplysninger om klassifikation processen ligner følgende:

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

  Dette job giver også en fil er placeret på __wasbs:///example/data/predictions/KDDTest+.arff.out__. Denne fil er dog ikke kan læses af mennesker.

> [AZURE.NOTE] Mahout job overskriver ikke filer. Hvis du vil køre disse job igen, skal du slette de filer, der er oprettet af forrige job.

##<a name="troubleshooting"></a>Fejlfinding i forbindelse med

###<a name="install"></a>Installere Mahout

Mahout er installeret på HDInsight 3.1 klynger, og det kan være installeret manuelt på HDInsight 3.0 eller HDInsight 2.1 klynger ved at benytte følgende fremgangsmåde:

1. Versionen af Mahout bruge, afhænger af HDInsight versionen af din klynge. Du kan finde den klynge version ved at få vist egenskaberne for klyngen i portalen Azure.

  * __For HDInsight 2.1__, kan du hente en Java arkiv (glas)-fil, der indeholder [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * __Til HDInsight 3.0__, skal du [oprette Mahout fra kilden] [ build] og angive den Hadoop-version, der leveres af HDInsight. Installere de forudsætninger, der er angivet på siden Opret, hente kilden og derefter bruge følgende kommando til at oprette Mahout glas filer:

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [AZURE.NOTE] Når Mahout 1.0 frigives, bør du kunne bruge de indbyggede pakker med HDInsight 3.0.

2. Overføre filen glas til __eksempel/krukker__ i standard-lagerplads på din klynge. Erstat CLUSTERNAME i følgende script med navnet på din HDInsight klynge, og Erstat FILNAVN med stien til filen __mahout-coure-0.9-job.jar__ ..

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###<a name="cannot-overwrite-files"></a>Kan ikke overskrive filer

Mahout job gør oprydning midlertidige filer, der blev oprettet under behandling. Desuden overskriver job ikke en eksisterende outputfil.

For at undgå fejl, når du kører Mahout job, slette midlertidige- og outputområder filer mellem kører, eller brug entydige midlertidige- og outputområder mappenavne.

###<a name="cannot-find-the-jar-file"></a>Kan ikke finde filen glas

HDInsight 3.1 klynger omfatter Mahout. Stien til og filnavnet omfatter versionsnummeret for Mahout, der er installeret på klyngen. Windows PowerShell eksempel scriptet i dette selvstudium bruger en sti, som er gyldige og November 2015, men versionsnummeret ændres i fremtidige opdateringer til HDInsight. Brug følgende Windows PowerShell-kommando for at bestemme den aktuelle sti til filen Mahout JAR for din klynge skal, og derefter redigere scriptet for at henvise stien til filen, der returneres:

    Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>Klasser, der ikke fungerer sammen med Windows PowerShell

Mahout job, der bruger følgende klasser returnere en række forskellige fejlmeddelelser, hvis de anvendes fra Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Oprette forbindelse til HDInsight klyngen for at køre job, der bruger disse klasser, og Kør job ved hjælp af Hadoop-kommandolinjen. Du kan finde et eksempel i [klassificeringen data ved hjælp af Hadoop-kommandolinjen](#classify) .

##<a name="next-steps"></a>Næste trin

Nu hvor du har lært, hvordan du bruger Mahout, opdage andre måder at arbejde med data på HDInsight:

* [Hive med HDInsight](hdinsight-use-hive.md)
* [Gris med HDInsight](hdinsight-use-pig.md)
* [MapReduce med HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

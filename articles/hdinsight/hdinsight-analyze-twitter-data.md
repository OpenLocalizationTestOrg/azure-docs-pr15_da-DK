<properties
    pageTitle="Analysere Twitter data med Hadoop i HDInsight | Microsoft Azure"
    description="Lær, hvordan du bruger Hive til at analysere Twitter data på Hadoop i HDInsight til at finde brugen hyppigheden for et bestemt ord."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analysere Twitter data ved hjælp af Hive i HDInsight

Sociale websteder er en af de vigtigste fører styrker for stor data indføring. Offentlige API'er, der leveres af Twitter-websteder er en praktisk datakilde til at analysere og forstå populære tendenser. I dette selvstudium, kan du få tweets ved hjælp af en Twitter streaming API, og brug derefter Apache Hive på Azure HDInsight til at få en liste over Twitter-brugere, der sendes på de fleste tweets, der indeholdt et bestemt ord.

> [AZURE.NOTE] Trinnene i dette dokument kræver en Windows-baseret HDInsight klynge. Få en vejledning bestemt til en Linux-baserede klynge i [Twitter-analysere data ved hjælp af Hive i HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).


##<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En arbejdsstation** med Azure PowerShell installeret og konfigureret. 

    Hvis du vil udføre Windows PowerShell-scripts, skal du køre Azure PowerShell som administrator og indstiller politikken udførelse af til *RemoteSigned*. Se [køre Windows PowerShell-scripts][powershell-script].

    Før du kører Windows PowerShell-scripts, du kontrollere, at du har forbindelse til abonnementet Azure ved hjælp af følgende cmdlet:

        Login-AzureRmAccount

    Hvis du har flere Azure abonnementer, kan du bruge følgende cmdlet til at angive det aktuelle abonnement:

        Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **En Azure HDInsight klynge**. Finde instruktioner til klargøring af klynge, se [Introduktion til brug af HDInsight] [ hdinsight-get-started] eller [klargøring HDInsight grupper] [hdinsight-provision]. Du skal klyngenavnet senere i selvstudiet.

I følgende tabel vises de filer, der bruges i dette selvstudium:

Filer|Beskrivelse
---|---
/tutorials/Twitter/data/tweets.txt|Kildedataene for Hive jobbet.
/tutorials/Twitter/output|Outputmappen til Hive jobbet. Hive job output standardfilnavnet er **000000_0**.
tutorials/Twitter/Twitter.hql|Scriptfil HiveQL.
/tutorials/Twitter/jobstatus|Jobstatus Hadoop.


##<a name="get-twitter-feed"></a>Hent Twitter feed

I dette selvstudium, du vil bruge på [Twitter-streaming API'er][twitter-streaming-api]. Specifikke Twitter-streaming API, du vil bruge er [statusser/filter][twitter-statuses-filter].

>[AZURE.NOTE] En fil, der indeholder 10.000 tweets og Hive-script-fil (dækket i næste afsnit), der er blevet overført i en offentlig Blob beholder. Hvis du vil bruge de overførte filer, kan du springe dette afsnit.

[Tweets data](https://dev.twitter.com/docs/platform-objects/tweets) er gemt i formatet JavaScript Object Notation (JSON), der indeholder en kompleks indlejrede struktur. I stedet for at skrive mange kodelinjer ved hjælp af et traditionelt programmeringssprog, kan du transformere denne indlejrede struktur til en Hive-tabel, så den kan forespørges ved en SQL Structured Query Language ()-som sprog, der hedder HiveQL.

Twitter bruger OAuth til at give autoriserede adgang til dens API. OAuth er en godkendelsesprotokollen, der gør det muligt for brugerne at godkende programmer til at handle på deres vegne uden at dele deres adgangskode. Du kan finde flere oplysninger kan findes på [oauth.net](http://oauth.net/) eller i fremragende [begyndervejledning til OAuth](http://hueniverse.com/oauth/) fra Hueniverse.

Det første trin til at bruge OAuth er at oprette et nyt program på webstedet Twitter-udvikler.

**Sådan oprettes et Twitter-program**

1. Log på [https://apps.twitter.com/](https://apps.twitter.com/). Klik på linket **Tilmeld dig nu** , hvis du ikke har en Twitter-konto.
2. Klik på **Opret ny App**.
3. Angiv **navn**, **Beskrivelse**, **websted**. Du kan udgør en URL-adresse til feltet **websted** . I følgende tabel vises nogle eksempelværdier bruge:

    Felt|Værdi
    ---|---
    Navn|MyHDInsightApp
    Beskrivelse|MyHDInsightApp
    Websted|http://www.myhdinsightapp.com

4. Markér **Ja, jeg accepterer**, og klik derefter på **Opret Twitter-program**.
5. Klik på fanen **tilladelser** . Standardtilladelsen til er **skrivebeskyttet**. Dette er tilstrækkelige til dette selvstudium.
6. Klik på fanen **nøgler og Access Tokens** .
7. Klik på **Opret min adgangstoken**.
8. Klik på **Test OAuth** i øverste højre hjørne på siden.
9. Notér **consumer nøgle**, **Consumer hemmeligt**, **adgangstoken**og **Access token hemmeligt**. Du skal værdierne senere i selvstudiet.

Du vil bruge Windows PowerShell til at foretage opkald webtjenesten i dette selvstudium. For en .NET C# prøve og se [analysér realtid Twitter synspunkt med HBase i HDInsight][hdinsight-hbase-twitter-sentiment]. Andre populære værktøjet til at foretage web serviceopkald er [*Curl*][curl]. Krøllet kan hentes fra [her][curl-download].

>[AZURE.NOTE] Når du bruger kommandoen krøllet i Windows, kan du bruge dobbelte anførselstegn i stedet for enkelte anførselstegn til værdierne.

**At få tweets**

1. Åbn Windows PowerShell integreret Scripting-miljø (ISE). (På startskærmen i Windows 8, Skriv **PowerShell_ISE** og klik derefter på **Windows PowerShell ISE**. Se [Start Windows PowerShell i Windows 8 og Windows][powershell-start].)

2. Kopier følgende script i ruden script:

        #region - variables and constants
        $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

        # Enter the OAuth information for your Twitter application
        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

        $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
        #endregion

        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Login-AzureRmAccount
        #endregion

        #region - Create a block blob object for writing tweets into Blob storage
        Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $containerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
        Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
        #end region

        # region - Format OAuth strings
        Write-Host "Format oauth strings ..." -ForegroundColor Green
        $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
        $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
        $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

        $signature = "POST&";
        $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
        $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
        $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
        $signature += [System.Uri]::EscapeDataString("track=" + $track);

        $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

        $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
        $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
        $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

        $oauth_authorization = 'OAuth ';
        $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
        $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
        $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
        $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
        $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
        $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
        $oauth_authorization += 'oauth_version="1.0"';

        $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
        #endregion

        #region - Read tweets
        Write-Host "Create HTTP web request ..." -ForegroundColor Green
        [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
        $request.Method = "POST";
        $request.Headers.Add("Authorization", $oauth_authorization);
        $request.ContentType = "application/x-www-form-urlencoded";
        $body = $request.GetRequestStream();

        $body.write($post_body, 0, $post_body.length);
        $body.flush();
        $body.close();
        $response = $request.GetResponse() ;

        Write-Host "Start stream reading ..." -ForegroundColor Green

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }
        #endregion

        #region - Write tweets to Blob storage
        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream)

        $sReader.close()
        #endregion

        Write-Host "Completed!" -ForegroundColor Green

3. Angive først fem til otte variabler i scriptet:


    Variabel|Beskrivelse
    ---|---
    $clusterName|Dette er navnet på den HDInsight klynge, hvor du vil køre programmet.
    $oauth_consumer_key|Dette er Twitter-program **consumer nøgle** du skrev ned tidligere da du oprettede Twitter-program.
    $oauth_consumer_secret|Dette er Twitter-program **consumer hemmeligt** du skrev ned tidligere.
    $oauth_token|Dette er det program til Twitter **adgangstoken** , du skrev ned tidligere.
    $oauth_token_secret|Dette er Twitter-program **access token hemmeligt** du skrev ned tidligere.
    $destBlobName|Dette er navnet på output blob. Standardværdien er **tutorials/twitter/data/tweets.txt**. Hvis du ændrer standardværdien, skal du opdatere Windows PowerShell-scripts i overensstemmelse hermed.
    $trackString|Webtjenesten returnerer tweets, der er relateret til disse nøgleord. Standardværdien er **Azure, skyen, HDInsight**. Hvis du ændrer standardværdien, opdaterer du Windows PowerShell-scripts i overensstemmelse hermed.
    $lineMax|Værdien, der bestemmer, hvor mange tweets scriptet læser. Det tager omkring tre minutter for at læse 100 tweets. Du kan angive et større tal, men det tager længere tid at indlæse.

5. Tryk på **F5** for at køre scriptet. Hvis du støder på problemer, som en løsning, Markér alle linjer, og derefter trykke på **F8**.
6. Du skal se "Udført!" i slutningen af output. Fejlmeddelelser vises med rødt.

Du kan kontrollere outputfilen, **/tutorials/twitter/data/tweets.txt**, som en valideringsprocedure på dit Azure Blob-lager ved hjælp af en Azure lagerplads explorer eller Azure PowerShell. Et eksempel Windows PowerShell-script til listen over filer, du kan finde [Brug Blob-lager med HDInsight][hdinsight-storage-powershell].



##<a name="create-hiveql-script"></a>Oprette HiveQL script

Ved hjælp af Azure PowerShell, kan du køre flere HiveQL sætninger én ad gangen eller pakke sætningen HiveQL til en scriptfil. Du skal oprette et script HiveQL i dette selvstudium. Script-filen skal overføres til Azure Blob-lager. I næste afsnit kører du script-fil ved hjælp af Azure PowerShell.

>[AZURE.NOTE] Hive script-fil og en fil, der indeholder 10.000 tweets er overført i en offentlig Blob beholder. Hvis du vil bruge de overførte filer, kan du springe dette afsnit.

Scriptet HiveQL skal udføre følgende:

1. **Slip tabellen tweets_raw** i tilfælde tabellen findes allerede.
2. **Oprette tweets_raw Hive-tabellen**. Denne midlertidige strukturerede Hive-tabellen indeholder dataene til yderligere Udpak, transformere, og Indlæs (ETL) behandling. Finde oplysninger om partitioner, se [Hive selvstudium][apache-hive-tutorial].  
3. **Indlæs data** fra kildemappen, /tutorials/twitter/data. Store tweets datasættet i indlejrede JSON-formatet er nu omdannet til en midlertidig Hive tabelstruktur.
3. **Slip tabellen tweets** i tilfælde tabellen findes allerede.
4. **Opret tabellen tweets**. Før du kan forespørge mod tweets datasættet ved hjælp af Hive, skal du køre en anden ETL proces. Denne proces ETL definerer et mere detaljeret tabelskema for de data, du har gemt i tabellen "twitter_raw".  
5. **Indsætte Overskriv tabel**. Dette komplekse Hive-script starte et sæt af lang MapReduce job ved Hadoop-klynge. Afhængigt af dit datasæt og størrelsen af din klynge, kan det tage omkring 10 minutter.
6. **Indsæt overskrive directory**. Køre en forespørgsel, og Medtag datasæt til en fil. Denne forespørgsel returnerer en liste over Twitter-brugere, der har sendt de fleste tweets, der indeholdt ordet "Azure".

**Oprette et Hive-script, og Overfør det til Azure**

1. Åbn Windows PowerShell ISE.
2. Kopier følgende script i ruden script:

        #region - variables and constants
        $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
        $subscriptionID = "<Azure Subscription ID>"
        
        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"
        $hqlScriptFile = "tutorials/twitter/twitter.hql"
        
        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        
        DROP TABLE tweets_raw;
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '$sourceDataPath';
        
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
        
        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;
        "@
        #endregion
        
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        
        Try{
            Get-AzureRmSubscription
        }
        Catch{
            Login-AzureRmAccount
        }
        
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
        
        #endregion
        
        #region - Create a block blob object for writing the Hive script file
        Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $defaultBlobContainerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
        
        Write-Host "Define the connection string ..." -ForegroundColor Green
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
        Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
        $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
        
        Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream
        $writeStream.Writeline($hqlStatements)
        #endregion
        
        #region - Write the Hive script file to Blob storage
        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)
        #endregion
        
        Write-Host "Completed!" -ForegroundColor Green

        

4. Angive de første to variabler i scriptet:

    Variabel|Beskrivelse
    ---|---
    $clusterName|Angiv HDInsight klyngenavn, hvor du vil køre programmet.
    $subscriptionID|Angiv dit Azure abonnement-ID.
    $sourceDataPath|Azure Blob storage placeringen hvor forespørgslerne Hive læser data fra. Du behøver ikke at ændre denne variabel.
    $outputPath|Azure Blob storage placeringen hvor forespørgslerne Hive vil output resultaterne. Du behøver ikke at ændre denne variabel.
    $hqlScriptFile|Placeringen og navnet på HiveQL script-fil. Du behøver ikke at ændre denne variabel.

5. Tryk på **F5** for at køre scriptet. Hvis du støder på problemer, som en løsning, Markér alle linjer, og derefter trykke på **F8**.
6. Du skal se "Udført!" i slutningen af output. Fejlmeddelelser vises med rødt.

Du kan kontrollere outputfilen, **/tutorials/twitter/twitter.hql**, som en valideringsprocedure på dit Azure Blob-lager ved hjælp af en Azure lagerplads explorer eller Azure PowerShell. Et eksempel Windows PowerShell-script til listen over filer, du kan finde [Brug Blob-lager med HDInsight][hdinsight-storage-powershell].  


##<a name="process-twitter-data-by-using-hive"></a>Behandle Twitter data ved hjælp af Hive

Du er færdig med alle forberedelser. Du kan nu kalde Hive scriptet og kontrollere resultaterne.

### <a name="submit-a-hive-job"></a>Sende et Hive-job

Brug følgende Windows PowerShell-script til at køre scriptet Hive. Du skal angive den første variabel.

>[AZURE.NOTE] For at bruge tweets og scriptet HiveQL, du har overført i de sidste to afsnit, skal du angive $hqlScriptFile til "/ tutorials/twitter/twitter.hql". Du kan bruge dem, der er overført til en offentlig blob for dig ved at angive $hqlScriptFile til "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    $httpUserName = "admin"
    $httpUserPassword = "<HDInsight Cluster HTTP User Password>"
    
    #use one of the following
    $hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    
    $statusFolder = "/tutorials/twitter/jobstatus"
    #endregion
    
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    
    #region - Invoke Hive
    Write-Host "Invoke Hive ... " -ForegroundColor Green
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential 
    $response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable
    
    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
    #endregion

### <a name="check-the-results"></a>Kontrollere resultaterne

Brug følgende Windows PowerShell-script til at kontrollere Hive job output. Du skal angive de første to variabler.

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
    #engregion
    
    #region - Create an Azure storage context object
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey  
    #endregion
    
    #region - Download blob and display blob
    Write-Host "Download the blob ..." -ForegroundColor Green
    cd $HOME
    Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "Display the output ..." -ForegroundColor Green
    Write-Host "==================================" -ForegroundColor Green
    cat "./$blob"
    Write-Host "==================================" -ForegroundColor Green
    #end region

> [AZURE.NOTE] Tabellen Hive bruger \001 som feltafgrænser. Afgrænseren, der er ikke synlig i outputtet.

Når analyseresultaterne er placeret i Azure Blob-lager, kan du eksportere dataene til en Azure SQL database/SQL server, eksportere dataene til Excel ved hjælp af Power-forespørgsel eller forbinde dit program til data ved hjælp af Hive ODBC-Driver. Kan finde flere oplysninger i [Brug Sqoop med HDInsight][hdinsight-use-sqoop], [analysér flight forsinkelse data ved hjælp af HDInsight][hdinsight-analyze-flight-delay-data], [Forbinder du Excel til HDInsight med Power-forespørgsel][hdinsight-power-query], og [Forbinder du Excel til HDInsight med Microsoft Hive ODBC-Driver][hdinsight-hive-odbc].

##<a name="next-steps"></a>Næste trin

Vi har set Sådan omdanner et ustrukturerede JSON-datasæt til en strukturerede Hive-tabel til forespørgslen, udforske og analysere data fra Twitter ved hjælp af HDInsight på Azure i dette selvstudium. Hvis du vil vide mere, skal du se:

- [Komme i gang med HDInsight][hdinsight-get-started]
- [Analysere realtid Twitter synspunkt med HBase i HDInsight][hdinsight-hbase-twitter-sentiment]
- [Analysere flight forsinkelse data ved hjælp af HDInsight][hdinsight-analyze-flight-delay-data]
- [Oprette forbindelse Excel til HDInsight med Power-forespørgsel][hdinsight-power-query]
- [Oprette forbindelse Excel til HDInsight med Microsoft Hive ODBC-Driver][hdinsight-hive-odbc]
- [Bruge Sqoop med HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

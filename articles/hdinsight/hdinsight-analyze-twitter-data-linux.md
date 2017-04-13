<properties
    pageTitle="Analysere Twitter data med Apache Hive på HDInsight | Microsoft Azure"
    description="Lær at bruge Python til at gemme Tweets, der indeholder bestemt nøgleord, og brug Hive og Hadoop på HDInsight for at transformere rækkedata TWitter til en søgbar Hive-tabel."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analysere Twitter data ved hjælp af Hive i HDInsight

I dette dokument, får du tweets ved hjælp af en Twitter streaming API og derefter bruge Apache Hive på en Linux-baserede HDInsight klynge for processen JSON formaterede data. Resultatet vil være en liste over Twitter-brugere, der har sendt i de fleste tweets, der indeholdt et bestemt ord.

> [AZURE.NOTE] Mens individuelle elementer i dette dokument kan bruges med Windows-baseret HDInsight klynger (Python for eksempel), mange trin er baseret på ved hjælp af en Linux-baserede HDInsight klynge. Få en vejledning bestemt til en Windows-baseret klynge i [Twitter-analysere data ved hjælp af Hive i HDInsight](hdinsight-analyze-twitter-data.md).

###<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- En __Linux-baserede Azure HDInsight klynge__. Oplysninger om oprettelse af en klynge, se [Komme i gang med Linux-baserede HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) trin til oprettelse af en klynge.

- En __SSH klient__. Du kan finde flere oplysninger om brug af SSH med Linux-baserede HDInsight, i følgende artikler:

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ og [pip](https://pypi.python.org/pypi/pip)

##<a name="get-a-twitter-feed"></a>Få en Twitter feed

Twitter gør det muligt at hente [data for hver tweet](https://dev.twitter.com/docs/platform-objects/tweets) som et JavaScript Object Notation (JSON) dokument via en REST-API. [OAuth](http://oauth.net) er påkrævet for godkendelse til API. Du skal også oprette en _Twitter-program_ , der indeholder de indstillinger, der bruges til at få adgang til API.

###<a name="create-a-twitter-application"></a>Oprette en Twitter-program

1. Log på [https://apps.twitter.com/](https://apps.twitter.com/)fra en webbrowser. Klik på linket **Tilmeld dig nu** , hvis du ikke har en Twitter-konto.
2. Klik på **Opret ny App**.
3. Angiv **navn**, **Beskrivelse**, **websted**. Du kan udgør en URL-adresse til feltet **websted** . I følgende tabel vises nogle eksempelværdier bruge:

  	| Felt | Værdi |
  	|:----- |:----- |
  	| Navn  | MyHDInsightApp |
  	| Beskrivelse | MyHDInsightApp |
  	| Websted | http://www.myhdinsightapp.com |
    
4. Markér **Ja, jeg accepterer**, og klik derefter på **Opret Twitter-program**.
5. Klik på fanen **tilladelser** . Standardtilladelsen til er **skrivebeskyttet**. Dette er tilstrækkelige til dette selvstudium.
6. Klik på fanen **nøgler og Access Tokens** .
7. Klik på **Opret min adgangstoken**.
8. Klik på **Test OAuth** i øverste højre hjørne på siden.
9. Notér **consumer nøgle**, **Consumer hemmeligt**, **adgangstoken**og **Access token hemmeligt**. Du skal bruge værdierne senere.

>[AZURE.NOTE] Når du bruger kommandoen krøllet i Windows, kan du bruge dobbelte anførselstegn i stedet for enkelte anførselstegn til værdierne.

###<a name="download-tweets"></a>Hente tweets

Følgende kode i Python vil hente 10.000 tweets fra Twitter og gemme dem i en fil med navnet __tweets.txt__.

> [AZURE.NOTE] Følgende trin udføres på klynge HDInsight, da Python er allerede installeret.

1. Oprette forbindelse til den HDInsight klynge, ved hjælp af SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Hvis du brugte en adgangskode til at sikre brugerkontoen SSH, bliver du bedt om at angive det. Hvis du har brugt en offentlig nøgle, kan du skal bruge den `-i` parameter for at angive den tilsvarende privat nøgle. For eksempel `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
        
    Du kan finde flere oplysninger om brug af SSH med Linux-baserede HDInsight, i følgende artikler:
    
    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows)
    
2. Værktøjet __pip__ er som standard ikke installeret på den HDInsight hovedsæde node. Bruge følgende for at installere, og derefter opdatere dette værktøj:

        sudo apt-get install python-pip
        sudo pip install --upgrade pip

3. I koden for at hente tweets er afhængig af [Tweepy](http://www.tweepy.org/) og [Progressbar](https://pypi.python.org/pypi/progressbar/2.2). For at installere disse skal du bruge følgende kommando:

        sudo apt-get install python-dev libffi-dev libssl-dev
        sudo apt-get remove python-openssl
        sudo pip install tweepy progressbar pyOpenSSL requests[security]
        
    > [AZURE.NOTE] Bit om at fjerne python-openssl, installere python Udviklingscenter, libffi-Udviklingscenter, libssl-Udviklingscenter, pyOpenSSL og anmodninger [sikkerhed] er at undgå en InsecurePlatform advarsel, når du opretter forbindelse til Twitter via SSL fra Python.
    >
    > Tweepy v3.2.0 bruges til at undgå [en fejl](https://github.com/tweepy/tweepy/issues/576) , der kan opstå, når behandling af tweets.

4. Brug følgende kommando til at oprette en ny fil med navnet __gettweets.py__:

        nano gettweets.py

5. Du kan bruge følgende som indholdet af filen __gettweets.py__ . Erstatte pladsholder oplysningerne om __consumer\_hemmeligt__, __consumer\_nøgle__, __access /\_token__, og __access\_token\_hemmeligt__ med oplysninger fra din Twitter-program.

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. Brug __Ctrl + X__og __Y__ til at gemme filen.

7. Brug følgende kommando til at køre filen og hente tweets:

        python gettweets.py

    En statusindikator skal vises, og tælle op til 100%, som er hentet og gemt filen på tweets.

    > [AZURE.NOTE] Hvis det tager lang tid for statuslinjen til at fremføre, skal du ændre filteret til at registrere populære emner Når der er en masse tweets om det emne, du filtrerer på, kan du hurtigt få de 10000 tweets det er nødvendigt.

###<a name="upload-the-data"></a>Overføre dataene

For at overføre data til WASB (den distribueret filsystem bruges af HDInsight), skal du bruge følgende kommandoer:

    hdfs dfs -mkdir -p /tutorials/twitter/data
    hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

Dataene gemmes i en placering, der kan få adgang til alle noder i klyngen.

##<a name="run-the-hiveql-job"></a>Kørslen HiveQL


1. Brug følgende kommando til at oprette en fil, der indeholder HiveQL sætninger:

        nano twitter.hql
    
    Du kan bruge følgende som indholdet af filen:

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
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
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
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
        
        
3. Tryk på __Ctrl + X__, og derefter trykke på __j__ for at gemme filen.

4. Brug følgende kommando til at køre den HiveQL, der er indeholdt i filen:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
        
    Dette vil indlæse Hive shell, køre HiveQL i filen __twitter.hql__ og derefter returnere en `jdbc:hive2//localhost:10001/>` prompt.
    
5. Fra beeline bliver spurgt, skal du bruge følgende til at kontrollere, at du kan vælge data fra tabellen __tweets__ oprettet af HiveQL i filen __twitter.hql__ :
        
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;

    Dette vil returnere maksimalt 10 tweets, der indeholder ordet __Azure__ i meddelelsesteksten.

##<a name="next-steps"></a>Næste trin

Vi har set hvordan du transformere en ustrukturerede JSON-datasæt i en struktureret Hive-tabel til at forespørge, udforske og analysere data fra Twitter ved hjælp af HDInsight på Azure i dette selvstudium. Hvis du vil vide mere, skal du se:

- [Komme i gang med HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Analysere flight forsinkelse data ved hjælp af HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

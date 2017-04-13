<properties
 pageTitle="Udvikle skoldning MapReduce sager med Maven | Microsoft Azure"
 description="Lær, hvordan du bruger Maven til at oprette en skoldning MapReduce sag, og derefter installere og køre jobbet på et Hadoop på HDInsight klynge."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>
<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="10/18/2016"
 ms.author="larryfr"/>

# <a name="develop-scalding-mapreduce-jobs-with-apache-hadoop-on-hdinsight"></a>Udvikle skoldning MapReduce sager med Apache Hadoop på HDInsight

Skoldning er et Scala bibliotek, der gør det nemt at oprette Hadoop MapReduce job. Det giver adgang til en præcis syntaksen samt tæt integration med Scala.

Lær, hvordan du bruger Maven til at oprette et grundlæggende word Tæl MapReduce job, der er skrevet i Scalding i dette dokument. Du kan derefter Lær at installere og køre dette job på en HDInsight klynge.

## <a name="prerequisites"></a>Forudsætninger

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **En Windows eller Linux baseret Hadoop på HDInsight klynge**. Du kan se [klargøring Linux-baserede Hadoop på HDInsight](hdinsight-hadoop-provision-linux-clusters.md) eller [klargøring Windows-baseret Hadoop på HDInsight](hdinsight-provision-clusters.md) kan finde flere oplysninger.

* **[Maven](http://maven.apache.org/)**

* **[Java platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 eller nyere**

## <a name="create-and-build-the-project"></a>Oprette og opbygge projektet

1. Brug følgende kommando til at oprette et nyt Maven projekt:

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    Denne kommando skal oprette en ny mappe med navnet **scaldingwordcount**, og oprette stilladser for et Scala program.

2. Åbn filen **pom.xml** i mappen **scaldingwordcount** og erstatte indholdet med følgende:

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
                    <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    Denne fil i denne artikel beskrives det projekt, afhængigheder og plug-ins. Her er de vigtige poster:

    * **maven.compiler.Source** og **maven.compiler.target**: Angiver Java versionen for dette projekt

    * **typer lagre**: de typer lagre, der indeholder de afhængighed-filer, der bruges af dette projekt

    * **skoldning core_2.11** og **hadoop-core**: dette projekt afhænger af både Scalding og Hadoop core-pakker

    * **maven-scala-plug-in'en**: plug-in til at samle scala programmer

    * **maven-skygge-plug-in'en**: plug-in til at oprette nedtonet (fat) krukker. Denne plug-in gælder filtre og transformationer; specificially:

        * **filtre**: filtrene anvendt ændre metakoden oplysningerne følger med i filen glas. For at forhindre signering undtagelser på kørselstidspunktet, dette udelukker forskellige signaturfiler, der eventuelt følger med afhængigheder.

        * **udførelser**: fase udførelse af konfigurationen af angiver klassen **com.twitter.scalding.Tool** som den primære klasse til pakken. Uden at det, du har brug at angive com.twitter.scalding.Tool, samt den klasse, der indeholder logik programmet på computeren, når du kører jobbet med kommandoen hadoop.

3. Slet mappen **src/test** , som du ikke oprette test med dette eksempel.

4. Åbn filen **src/main/scala/com/microsoft/example/App.scala** , og Erstat indholdet med følgende:

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    Dette implementerer et grundlæggende word Tæl job.

5. Gem og Luk filerne.

6. Brug følgende kommando fra mappen **scaldingwordcount** til at opbygge og pakke programmet:

        mvn package

    Når denne jobbet er fuldført, kan den pakke, der indeholder programmet WordCount findes på **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## <a name="run-the-job-on-a-linux-based-cluster"></a>Køre jobbet på en Linux-baserede klynge

> [AZURE.NOTE] I følgende trin bruges SSH og kommandoen Hadoop. Du kan finde andre metoder til at køre MapReduce job [Brug MapReduce i Hadoop på HDInsight](hdinsight-use-mapreduce.md).

1. Brug følgende kommando for at overføre pakken til din HDInsight klynge:

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    Derved kopieres filerne fra det lokale system til noden hoved.

    > [AZURE.NOTE] Hvis du brugte en adgangskode til at sikre kontoen SSH, bliver du bedt om adgangskoden. Hvis du har brugt en SSH nøgle, kan du skal bruge den `-i` parameter og stien til den private nøgle. For eksempel`scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Brug følgende kommando til at oprette forbindelse til Klyngenoden hoved:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Hvis du brugte en adgangskode til at sikre kontoen SSH, bliver du bedt om adgangskoden. Hvis du har brugt en SSH nøgle, kan du skal bruge den `-i` parameter og stien til den private nøgle. For eksempel`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Når tilsluttet noden hoved, kan du bruge følgende kommando til at køre word Tæl jobbet

        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout

    Klassen WordCount, du tidligere har implementeret køres. `--hdfs`får jobbet bruge HDFS. `--input`Angiver fil, Skriv tekst, mens `--output` angiver en outputplacering.

4. Når jobbet er fuldført, skal du bruge følgende for at få vist output.

        hdfs dfs -text wasbs:///example/wordcountout/*

    Derved vises oplysninger i stil med følgende:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="run-the-job-on-a-windows-based-cluster"></a>Køre jobbet på en Windows-baseret klynge

I følgende trin bruges Windows PowerShell. Du kan finde andre metoder til at køre MapReduce job [Brug MapReduce i Hadoop på HDInsight](hdinsight-use-mapreduce.md).

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

2. Start Azure PowerShell og Log på din Azure-konto. Når du har indtastet dine legitimationsoplysninger, returnerer kommandoen oplysninger om din konto.

        Add-AzureRMAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. Hvis du har flere abonnementer, skal du give det abonnement-id, du vil bruge til installation.

        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Du kan bruge `Get-AzureRMSubscription` til at få en liste over alle abonnementer, der er knyttet til din konto, som omfatter abonnementets Id for hver enkelt.

4. Brug følgende script til at overføre og køre WordCount jobbet. Erstatte `CLUSTERNAME` med navnet på din HDInsight klynge, og Sørg for, at `$fileToUpload` er den korrekte sti til filen __scaldingwordcount-1.0-SNAPSHOT.jar__ .

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = Read-Host -Prompt "Enter the HDInsight cluster name"
        $fileToUpload = Read-Host -Prompt "Enter the path to the scaldingwordcount-1.0-SNAPSHOT.jar file"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential -Message "Enter the login credentials for the cluster"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                        "--input", `
                        "wasbs:///example/data/gutenberg/davinci.txt", `
                        "--output", `
                        "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     Når du kører scriptet, bliver du bedt om angive administrator brugernavnet og adgangskoden til din HDInsight klynge. Eventuelle fejl, der opstår, mens jobbet kører vil blive logført til konsollen.
     
6. Når jobbet er fuldført, hentes output til tekstfilen __output.txt__ i den aktuelle mappe. Brug følgende kommando til at få vist resultaterne.

        cat output.txt

    Filen skal indeholde værdier, der ligner følgende:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært, hvordan du bruger Scalding til at oprette MapReduce job til HDInsight, kan du bruge følgende links for at undersøge andre måder at arbejde med Azure HDInsight.

* [Bruge Hive med HDInsight](hdinsight-use-hive.md)

* [Brug gris med HDInsight](hdinsight-use-pig.md)

* [Brug MapReduce sager med HDInsight](hdinsight-use-mapreduce.md)

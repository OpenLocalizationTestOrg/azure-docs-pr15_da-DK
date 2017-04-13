<properties
    pageTitle="Udvikle Java MapReduce programmer til Linux-baserede HDInsight | Microsoft Azure"
    description="Lær at udvikle Java MapReduce programmer og anvende dem til Linux-baserede HDInsight."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Udvikle Java MapReduce programmer til Hadoop på HDInsight Linux

Denne dokumenter vejleder dig gennem bruge Apache Maven til at oprette et MapReduce program, og derefter installere og køre den på en Linux-baserede Hadoop på HDInsight klynge.

##<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 eller nyere (eller en tilsvarende, såsom OpenJDK)

- [Apache Maven](http://maven.apache.org/)

- **Et Azure-abonnement**

- **Azure CLI**

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="configure-environment-variables"></a>Konfigurere miljøvariabler

Følgende miljøvariabler kan angives, når du installerer Java og JDK. Skal du kontrollere, at de findes, og at de indeholder de korrekte værdier for systemet.

* **JAVA_HOME** - skal pege på den mappe, hvor Java runtime-miljø (JRE) er installeret. For eksempel i OS X, Unix eller Linux system, den skal have en værdi, der ligner `/usr/lib/jvm/java-7-oracle`. I Windows, skal den indeholde en værdi, der ligner`c:\Program Files (x86)\Java\jre1.7`

* **Sti** - skal indeholde følgende stier:

    * **JAVA_HOME** (eller tilsvarende stien)

    * **JAVA_HOME\bin** (eller tilsvarende stien)

    * Den mappe, hvor Maven er installeret

##<a name="create-a-new-maven-project"></a>Oprette et nyt Maven projekt

1. Fra en terminal session, eller kommandolinjen i dit udviklingsmiljø, skal du ændre kataloger til den placering, du vil gemme dette projekt.

3. Bruge kommandoen __mvn__ , som er installeret med Maven, til at generere stilladser for projektet.

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Dette vil oprette en ny mappe i den aktuelle mappe med det navn, der er angivet af parameteren __artifactID__ (**wordcountjava** i dette eksempel.) Denne mappe kan indeholde følgende elementer:

    * __pom.xml__ - [Projekt (POM Object Model)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) , der indeholder oplysninger og konfiguration af de oplysninger, der bruges til at opbygge projektet.

    * __src__ - den mappe, hvor mappen __hoved/java/org/apache/hadoop/eksempler__ , hvor du vil redigere programmet.

3. Slet filen __src/test/java/org/apache/hadoop/examples/apptest.java__ , som det ikke vil blive brugt i dette eksempel.

##<a name="add-dependencies"></a>Tilføje afhængigheder

1. Redigere filen __pom.xml__ og tilføje følgende i den `<dependencies>` afsnit:

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>

    Dette fortæller Maven, projektet kræver bibliotekerne (angivet i &lt;artifactId\>) med en bestemt version (angivet i &lt;version\>). Dette der, hentes fra standard Maven lager under kompilering. Du kan bruge [Maven lager Søg](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) til at få vist mere.

    Den `<scope>provided</scope>` fortæller Maven, at disse afhængigheder ikke være pakket med programmet, som de leveres af HDInsight klynge på kørselstidspunktet.

2. Tilføj følgende filen __pom.xml__ . Det skal være i den `<project>...</project>` mærker i filen. for eksempel mellem `</dependencies>` og `</project>`.

        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                </execution>
              </executions>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>

    Første plug-in'et konfigurerer [Maven skygge plug-in](http://maven.apache.org/plugins/maven-shade-plugin/), som bruges til at opbygge en uberjar (også kaldet en fatjar), som indeholder afhængigheder, der kræves af programmet. Det forhindrer også kopiering af licenser i pakken glas, hvilket kan medføre problemer på visse systemer.

    Anden plug-in'et konfigurerer Maven compileren, som bruges til at angive versionen af Java, der kræves af dette program til den version, der bruges på HDInsight klyngen.

3. Gem filen __pom.xml__ .

##<a name="create-the-mapreduce-application"></a>Oprette programmet MapReduce

1. Gå til mappen __wordcountjava/src/main/java/org/apache/hadoop/eksempler__ og Omdøb filen __App.java__ til __WordCount.java__.

2. Åbn filen __WordCount.java__ i et tekstredigeringsprogram, og Erstat indholdet med følgende:

        package org.apache.hadoop.examples;

        import java.io.IOException;
        import java.util.StringTokenizer;
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.fs.Path;
        import org.apache.hadoop.io.IntWritable;
        import org.apache.hadoop.io.Text;
        import org.apache.hadoop.mapreduce.Job;
        import org.apache.hadoop.mapreduce.Mapper;
        import org.apache.hadoop.mapreduce.Reducer;
        import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
        import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class WordCount {

          public static class TokenizerMapper
               extends Mapper<Object, Text, Text, IntWritable>{

            private final static IntWritable one = new IntWritable(1);
            private Text word = new Text();

            public void map(Object key, Text value, Context context
                            ) throws IOException, InterruptedException {
              StringTokenizer itr = new StringTokenizer(value.toString());
              while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
              }
            }
          }

          public static class IntSumReducer
               extends Reducer<Text,IntWritable,Text,IntWritable> {
            private IntWritable result = new IntWritable();

            public void reduce(Text key, Iterable<IntWritable> values,
                               Context context
                               ) throws IOException, InterruptedException {
              int sum = 0;
              for (IntWritable val : values) {
                sum += val.get();
              }
              result.set(sum);
              context.write(key, result);
            }
          }

          public static void main(String[] args) throws Exception {
            Configuration conf = new Configuration();
            String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
            if (otherArgs.length != 2) {
              System.err.println("Usage: wordcount <in> <out>");
              System.exit(2);
            }
            Job job = new Job(conf, "word count");
            job.setJarByClass(WordCount.class);
            job.setMapperClass(TokenizerMapper.class);
            job.setCombinerClass(IntSumReducer.class);
            job.setReducerClass(IntSumReducer.class);
            job.setOutputKeyClass(Text.class);
            job.setOutputValueClass(IntWritable.class);
            FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
            FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
            System.exit(job.waitForCompletion(true) ? 0 : 1);
          }
        }

    Bemærk navnet på pakken er **org.apache.hadoop.examples** og klassenavnet er **WordCount**. Du skal bruge disse navne, når du sender MapReduce jobbet.

3. Gem filen.

##<a name="build-the-application"></a>Opbygge programmet

1. Skift til mappen __wordcountjava__ , hvis du ikke allerede er der.

2. Brug følgende kommando til at opbygge en glas-fil, der indeholder programmet:

        mvn clean package

    Dette vil rydde alle tidligere build elementer, hente afhængigheder, der ikke har allerede er installeret, og klik derefter opbygge og pakke programmet.

3. Når kommandoen afsluttes, skal indeholde ____ wordcountjava/destinationsmappen en fil med navnet __wordcountjava-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] __wordcountjava-1.0-SNAPSHOT.jar__ filen er en uberjar, som indeholder ikke kun den WordCount job, men også afhængigheder, der kræver jobbet på kørselstidspunktet.


##<a id="upload"></a>Overføre krukken

Brug følgende kommando for at overføre filen glas til HDInsight headnode:

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Derved kopieres filerne fra det lokale system til noden hoved.

> [AZURE.NOTE] Hvis du brugte en adgangskode til at sikre kontoen SSH, bliver du bedt om adgangskoden. Hvis du har brugt en SSH nøgle, kan du skal bruge den `-i` parameter og stien til den private nøgle. For eksempel `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Kørslen MapReduce

1. Oprette forbindelse til HDInsight ved hjælp af SSH, som beskrevet i følgende artikler:

    - [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Brug følgende kommando til at køre programmet MapReduce fra session SSH:

        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout

    Dette vil bruge programmet WordCount MapReduce til at tælle antal ord i filen davinci.txt og gemme resultater i __wasbs: / / / eksempel/data/wordcountout__. Både input fil- og output er gemt til standard-lager for-klyngen.

3. Når jobbet er fuldført, skal du bruge følgende for at få vist resultaterne:

        hdfs dfs -cat wasbs:///example/data/wordcountout/*

    Du skal modtage en liste over ord og tæller med værdier, der ligner følgende:

        zeal    1
        zelus   1
        zenith  2

##<a id="nextsteps"></a>Næste trin

I dette dokument, har du lært at udvikle et Java MapReduce job. Se de følgende dokumenter til andre måder at arbejde med HDInsight.

- [Bruge Hive med HDInsight][hdinsight-use-hive]
- [Brug gris med HDInsight][hdinsight-use-pig]
- [Bruge MapReduce med HDInsight](hdinsight-use-mapreduce.md)

Du kan finde flere oplysninger, se også [Java Developer Center](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx


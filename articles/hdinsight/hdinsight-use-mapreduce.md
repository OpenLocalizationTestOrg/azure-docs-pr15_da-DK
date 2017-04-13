<properties
   pageTitle="MapReduce med Hadoop på HDInsight | Microsoft Azure"
   description="Lær at køre MapReduce job på Hadoop i HDInsight klynger. Du kører en grundlæggende word Tæl handling, der er implementeret som et Java MapReduce job."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Brug MapReduce i Hadoop på HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

I denne artikel kan lære du, hvordan du køre MapReduce job på Hadoop i HDInsight klynger. Vi udføre en grundlæggende word Tæl implementeret som et Java MapReduce job.

##<a id="whatis"></a>Hvad er MapReduce?

Hadoop MapReduce er en software struktur til at skrive job, der behandler store datamængder. Indtastede data er opdelt i uafhængige dele, som behandles derefter parallelt på tværs af knuderne i din klynge. Et MapReduce job består af to funktioner:

* **Mapper**: forbruger inputdataene, analyserer (som regel med filter og sortering) og udsender tupler (nøgle-værdi-par)
* **Reduktionsrør**: forbruger tupler fra mapperen og udfører en oversigt over handling, der opretter en mindre, kombinerede resultatet fra dataene, Mapper

Eksempel på jobbet en grundlæggende word count MapReduce illustreret i følgende diagram:

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

Output fra dette job er et antal hvor mange gange hvert ord, der er opstået i den tekst, der blev analysere.

* Mapperen tager hver linje fra den input tekst som input og opdeler i ord. Det udsender en nøgleværdi par hver gang et ord forekommer af ordet efterfølges af en 1. Output sorteres før det sendes til reduktionsrør.

* Reduktionsrør summerer disse individuelle tæller for hvert ord og udsender et enkelt nøgle/værdi-par, der indeholder ordet efterfulgt af summen af dens forekomster.

MapReduce kan være integreret i en række forskellige sprog. Java er den mest almindelige implementering og bruges til demonstration i dette dokument.

### <a name="hadoop-streaming"></a>Hadoop Streaming

Sprog eller strukturer, der er baseret på Java og Java Virtual Machine (for eksempel Scalding eller kaskadevis,) kan være kørte direkte som en MapReduce sag, der ligner en Java-program. Andre, som C# eller Python eller enkeltstående eksekverbare filer, skal du bruge Hadoop streaming.

Hadoop streaming kommunikerer med mapper og reduktionsrør over STDIN og STDOUT - mapper og reduktionsrør læse data en linje ad gangen fra STDIN og skrive output til STDOUT. Hver linje få eller udsendes af mapper og reduktionsrør skal være i formatet for et tasten/værdi-par, adskilt af et fanen charaacter:

    [key]/t[value]

Se [Hadoop Streaming](http://hadoop.apache.org/docs/r1.2.1/streaming.html)kan finde flere oplysninger.

Eksempler på brug af Hadoop streaming med HDInsight, se følgende:

* [Udvikle Python MapReduce job](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>Om eksempeldataene

I dette eksempel skal til eksempeldata, du bruge de notesbøger Leonardo Da Vinci, som leveres som et tekstdokument med i din HDInsight klynge.

Eksempeldataene er gemt i Azure Blob-lager, som HDInsight bruger som standard-filsystemet til Hadoop klynger. HDInsight kan få adgang til filer ved hjælp af præfikset **wasb** har gemt i Blob-lager. For at få adgang til sample.log-filen, skal du bruge følgende syntaks:

    wasbs:///example/data/gutenberg/davinci.txt

Da Azure Blob-lager er standard lagerplads for HDInsight, kan du også adgang til filen ved hjælp af **/example/data/gutenberg/davinci.txt**.

> [AZURE.NOTE] I den forrige syntaks **wasbs: / / /** bruges til at få adgang til filer, der er gemt i objektbeholderen til lagring af standard for din HDInsight klynge. Hvis du har angivet ekstra lagerplads konti, når du har klargjort din klynge, og du vil have adgang til filer, der er gemt på disse konti, kan du få adgang til dataene ved at angive objektbeholder navn og lager konto adressen. For eksempel **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>Om eksempel MapReduce

Kørslen MapReduce, der bruges i dette eksempel er placeret i **wasbs://example/jars/hadoop-mapreduce-examples.jar**, og den leveres med din HDInsight klynge. Dette viser et eksempel på word count, som du vil køre mod **davinci.txt**.

> [AZURE.NOTE] Placeringen af er på HDInsight 2.1 klynger **wasbs:///example/jars/hadoop-examples.jar**.

Til reference er følgende Java-kode for word Tæl MapReduce sagen:

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

Du kan finde instruktioner til at skrive MapReduce tingene, [udvikle Java MapReduce programmer til HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md).

##<a id="run"></a>Køre MapReduce

HDInsight kan køre HiveQL job ved hjælp af en række forskellige metoder. Brug tabellen nedenfor til at afgøre, hvilken metode der passer til dine og derefter følge linket for en gennemgang.

| **Brug dette**...                                                    | **.. .at gør dette**                                       | .. .with **klynge operativsystem** | .. .from **klientens operativsystem** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md)                       | Brug kommandoen Hadoop gennem **SSH**                  | Linux                                     | Linux, Unix, Mac OS X eller Windows        |
| [Krøllet](hdinsight-hadoop-use-mapreduce-curl.md)                     | Sende jobbet fra en fjernplacering ved hjælp af **RESTEN**               | Linux eller Windows                          | Linux, Unix, Mac OS X eller Windows        |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | Sende jobbet fra en fjernplacering ved hjælp af **Windows PowerShell** | Linux eller Windows                          | Windows                                  |
| [Fjernskrivebord](hdinsight-hadoop-use-mapreduce-remote-desktop)    | Brug kommandoen Hadoop gennem **Fjernskrivebord**       | Windows                                   | Windows                                  |

##<a id="nextsteps"></a>Næste trin

Selvom MapReduce giver effektive diagnosticering muligheder, kan det være en udfordring at master bit. Der findes flere Java-baserede strukturer, som gør det nemmere at definere MapReduce programmer samt teknologier som gris og Hive, som giver en nemmere måde at arbejde med data i HDInsight. Hvis du vil vide mere, skal du se følgende artikler:

* [Udvikle Java MapReduce programmer til HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Udvikle Python streaming MapReduce programmer til HDInsight](hdinsight-hadoop-streaming-python.md)

* [Udvikle skoldning MapReduce sager med Apache Hadoop på HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Bruge Hive med HDInsight][hdinsight-use-hive]

* [Brug gris med HDInsight][hdinsight-use-pig]

* [Køre HDInsight-eksempler][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif

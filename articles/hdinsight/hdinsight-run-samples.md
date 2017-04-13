<properties
    pageTitle="Køre Hadoop eksemplerne i HDInsight | Microsoft Azure"
    description="Kom i gang ved hjælp af tjenesten Azure HDInsight med de eksempler, der. Bruge PowerShell-scripts, der kører MapReduce programmer på dataklynger."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>

#<a name="run-hadoop-mapreduce-samples-in-windows-based-hdinsight"></a>Køre Hadoop MapReduce eksempler i Windows-baseret HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

En række eksempler er lettere at få Introduktion, der kører MapReduce job på Hadoop klynger ved hjælp af Azure HDInsight. Disse eksempler er gjort tilgængelig på hver af de HDInsight administrerede klynger, som du opretter. Kører disse eksempler til at lære bruger Azure PowerShell-cmdlet'er til at køre job på Hadoop klynger.

- [**Word Tæl**][hdinsight-sample-wordcount]: tæller word forekomster i en tekstfil.
- [**C#-streaming Ordoptælling**][hdinsight-sample-csharp-streaming]: tæller word forekomster i en tekstfil, brug af streaming Hadoop-grænsefladen.
- [**Pi estimator**][hdinsight-sample-pi-estimator]: anvender en statistisk (kvasi Monte nu) metode til at anslå værdien af pi.
- [**10 GB Graysort**][hdinsight-sample-10gb-graysort]: Kør et generelt formål GraySort på en fil til 10 GB ved hjælp af HDInsight. Der er tre opgaver skal køre: Teragen til at generere dataene, Terasort til at sortere dataene, og Teravalidate for at bekræfte, at dataene er sorteret korrekt.

>[AZURE.NOTE] Koden kan findes i tillæg. 

Meget ekstra dokumentation findes på internettet til Hadoop-relaterede teknologier, som Java-baserede MapReduce programmering og streaming samt dokumentation om de cmdlet'er, der bruges i Windows PowerShell scripting. Du kan finde flere oplysninger om disse ressourcer:

- [Udvikle Java MapReduce programmer til Hadoop i HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
- [Sende Hadoop-job i HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Introduktion til Azure HDInsight][hdinsight-introduction]

I dag, Vælg mange personer Hive og gris over MapReduce.  Du kan finde flere oplysninger i:

- [Brug Hive i HDInsight](hdinsight-use-hive.md)
- [Bruge gris i HDInsight](hdinsight-use-pig.md)
 
**Forudsætninger**:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **en HDInsight klynge**. Du kan finde oplysninger om de forskellige måder, hvor disse klynger kan oprettes, [oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md).
- **En arbejdsstation med Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="hdinsight-sample-wordcount"></a>Tæl - Java i Word 

Hvis du vil sende et MapReduce projekt, skal oprette du først en MapReduce jobdefinition. I definitionen af job angiver du MapReduce glas programfil og placeringen af filen glas, som er **wasbs:///example/jars/hadoop-mapreduce-examples.jar**, klassenavnet og argumenterne.  Programmet wordcount MapReduce tager to argumenter: kildefilen, der skal bruges til at tælle ord og en placering til output.

Koden kan findes i [tillæg A](#apendix-a---the-word-count-MapReduce-program-in-java).

Under proceduren for udvikling af et Java MapReduce program skal du se - [udvikle Java MapReduce programmer til Hadoop i HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
 
**Sende et word Tæl MapReduce job**

1. Åbn **Windows PowerShell ISE**. Flere oplysninger under [installere og konfigurere Azure PowerShell][powershell-install-configure].
2. Indsæt følgende PowerShell-script:

        $subscriptionName = "<Azure Subscription Name>"
        $resourceGroupName = "<Resource Group Name>"
        $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
        
        Select-AzureRmSubscription -SubscriptionName $subscriptionName
        
        # Define the MapReduce job
        $mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                    -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                    -ClassName "wordcount" `
                                    -Arguments "wasbs:///example/data/gutenberg/davinci.txt", "wasbs:///example/data/WordCountOutput1"
        
        # Submit the job and wait for job completion
        $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
        $mrJob = Start-AzureRmHDInsightJob `
                            -ResourceGroupName $resourceGroupName `
                            -ClusterName $clusterName `
                            -HttpCredential $cred `
                            -JobDefinition $mrJobDefinition 
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $clusterName `
            -HttpCredential $cred `
            -JobId $mrJob.JobId 
        
        # Get the job output
        $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
        $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
        $defaultStorageContainer = $cluster.DefaultStorageContainer
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $clusterName `
            -HttpCredential $cred `
            -DefaultStorageAccountName $defaultStorageAccount `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -DefaultContainer $defaultStorageContainer  `
            -JobId $mrJob.JobId `
            -DisplayOutputType StandardError

        # Download the job output to the workstation
        $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
        Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
        
        # Display the output file
        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    Kørslen MapReduce giver en fil med navnet *del-r-00000*, som indeholder ord og antallet af poster. Scriptet bruger kommandoen **findstr** til at få vist alle de ord, der indeholder *"der"*.

3. Angive de første 3 variabler og køre scriptet.

## <a name="hdinsight-sample-csharp-streaming"></a>Tæl - C#-streaming i Word

Hadoop leverer en streaming API til MapReduce, som gør det muligt at skrive kort og reducere funktioner på andre sprog end Java.

> [AZURE.NOTE] Trinnene i dette selvstudium gælder kun for Windows-baseret HDInsight klynger. Du kan finde et eksempel på streaming til Linux-baserede HDInsight klynger, [udvikle Python streaming programmer til HDInsight](hdinsight-hadoop-streaming-python.md).

I eksemplet med mapperen og reduktionsrør kan udføres, og Læs input fra [stdin] [ stdin-stdout-stderr] (linje for linje) og udsender output til [stdout][stdin-stdout-stderr]. Programmet tæller alle ord i teksten.

Når en eksekverbar fil er angivet for **mappers**, starter hver mapper opgave den eksekverbare fil som en separat proces, når mapperen er initialiseret. Som mapper opgaven kører, den konverterer dens input til linjer, og feeds linjerne til [stdin] [ stdin-stdout-stderr] over processen.

I mellemtiden indsamler mapperen linje rundt output fra stdout over processen. Den konverterer hver linje i et nøgle/værdi-par, som indsamles som output fra mapperen. Præfikset af en linje op til det første tegn i fanen er tasten som standard, og resten af linjen (undtagen tabulatortegnet) er værdien. Hvis der er ingen tabulatortegn i linjen, hele linjen betragtes som nøglen, og værdien er null.

Når en eksekverbar fil er angivet for **reducers**, åbner hver reduktionsrør opgave den eksekverbare fil som en separat proces, når reduktionsrør startes. Mens reduktionsrør opgaven kører, den konverterer dens input nøgleværdier par til linjer, og den feeds linjerne til [stdin] [ stdin-stdout-stderr] over processen.

I mellemtiden reduktionsrør indsamler linje rundt output fra [stdout] [ stdin-stdout-stderr] over processen. Den konverterer hver linje til et tasten/værdi-par, som indsamles som output fra reduktionsrør. Præfikset af en linje op til det første tegn i fanen er tasten som standard, og resten af linjen (undtagen tabulatortegnet) er værdien.

Finde flere oplysninger om grænsefladen Hadoop Streaming, [Hadoop Streaming]-[hadoop-streaming].

**Sende en C# streaming word antal job**

- Følg fremgangsmåden i [Ordoptælling - Java](#word-count-java), og Erstat definitionen af job med følgende:

        $mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
                                -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                                -Mapper "cat.exe" `
                                -Reducer "wc.exe" `
                                -InputPath "/example/data/gutenberg/davinci.txt" `
                                -OutputPath "/example/data/StreamingOutput/wc.txt"  


    Outputfilen skal være:
    
        example/data/StreamingOutput/wc.txt/part-00000      
                                
## <a name="hdinsight-sample-pi-estimator"></a>PI estimator

Pi estimator bruger en statistisk (kvasi Monte nu) metode til at anslå værdien af pi. Punkter, der er placeret inde i en enhed tilfældigt firkantede også falder inden for en cirkel, der er anført i denne firkant med en sandsynlighed, der er lig med området af cirklen, pi/4. Værdien af pi kan beregnede fra værdien af 4R, hvor R er forholdet mellem antallet point, der er inde i en cirkel til det samlede antal point, der er i kvadratet. Jo større eksempel på punkter, desto bedre estimatet er.

Script, der er knyttet til dette eksempel sender et Hadoop glas job og er indstillet til at køre med en værdi 16 kort, hver især kræves til at beregne 10 millioner eksempel punkter af parameterværdierne. Disse parameterværdier kan ændres for at forbedre anslåede værdien af pi. Til reference er de første 10 decimaler af pi 3.1415926535.

**Sende et pi estimator job**

- Følg fremgangsmåden i [Ordoptælling - Java](#word-count-java), og Erstat definitionen af job med følgende:

        $mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                    -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                    -ClassName "pi" `
                                    -Arguments "16", "10000000"

## <a name="hdinsight-sample-10gb-graysort"></a>10 GB Graysort

Dette eksempel bruger en mindre 10GB af data, så den kan køres relativt hurtigt. Den anvender de MapReduce programmer, der er udviklet af Owen O'Malley og Arun Murthy, der har vundet årlige generelle ("daytona") terabyte Sortér benchmark i 2009 med en hastighed på 0.578 TB/min (100 TB i 173 minutter). Du kan finde flere oplysninger om dette og andre sortering benchmarktests webstedet [Sortbenchmark](http://sortbenchmark.org/) .

Dette eksempel bruger tre typer MapReduce programmer:

1. **TeraGen** er et MapReduce program, du kan bruge til at generere rækkerne af data til at sortere.
2. **TeraSort** eksempler på indtastede data og bruger MapReduce til at sortere dataene i en samlet rækkefølge. TeraSort er en standard sortering af MapReduce funktioner, med undtagelse af en brugerdefineret partitioner, der bruger en sorteret liste over N-1 prøver fra taster, der definerer det vigtigste område for hver reducere. Især alle nøgler sådanne, som eksempel [i-1] < = nøgle < eksempel [i] sendes til reducere i. Dette garantier, output af reducere i er alle, der er mindre end output fra reducere i + 1.
3. **TeraValidate** er et MapReduce program, der valideret, output globalt er sorteret. Det opretter en tilknytning per fil i outputmappen, og hver tilknytning sikrer, at hver nøgle er mindre end eller lig med den forrige opgave. Funktionen kort genererer også poster af de første og sidste taster for hver fil, og funktionen reducere sikrer, at den første tast af fil i er større end den sidste nøgle i filen i-1. Problemer rapporteres som output fra reducere med de pågældende taster, der er defekt.

Input- og outputområder formatet, bruges af alle tre programmer, læser og skriver tekstfiler i det ønskede format. Output fra reducere har gentagelse, der er angivet til 1, i stedet for standard 3, da benchmark-konkurrencen ikke kræver, at outputdataene replikeres til flere noder.

Tre opgaver er påkrævet ved at prøve og hver svarer til en af de MapReduce programmer, der er beskrevet i introduktionen:

1. Oprette dataene, til at sortere ved at køre **TeraGen** MapReduce jobbet.
2. Sortere data ved at køre **TeraSort** MapReduce jobbet.
3. Bekræft, at dataene er sorteret korrekt ved at køre **TeraValidate** MapReduce jobbet.

**Sende job**

- Følg fremgangsmåden i [Ordoptælling - Java](#word-count-java), og brug følgende jobdefinitioner:

    $teragen = ny AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - klassenavn "teragen" '-argumenter "-Dmapred.map.tasks=50", "100000000", "/ eksempel / / 10 GB-Sortér-datainput"
    
    $terasort = ny AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - klassenavn "terasort" '-argumenter "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/ eksempel / / 10 GB-Sortér-datainput", "/ eksempel/data/10 GB-Sortér-output"
    
    $teravalidate = ny AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - klassenavn "teravalidate" '-argumenter "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/ eksempel/data/10 GB-Sortér-output", "/ eksempel/data/10 GB-Sortér-validere"


##<a name="next-steps"></a>Næste trin 

I denne artikel og artiklerne i hver af prøverne, du har lært at køre eksemplerne inkluderet med HDInsight klynger ved hjælp af PowerShell Azure. Du kan finde selvstudier om at bruge gris, Hive og MapReduce med HDInsight, i følgende emner:

* [Introduktion til brug af Hadoop med Hive i HDInsight til at analysere mobile håndsæt brug][hdinsight-get-started]
* [Brug gris med Hadoop på HDInsight][hdinsight-use-pig]
* [Bruge Hive med Hadoop på HDInsight][hdinsight-use-hive]
* [Sende Hadoop-job i HDInsight] [hdinsight-submit-jobs]
* [Azure HDInsight SDK dokumentation][hdinsight-sdk-documentation]
* [Fejlfinding af Hadoop i HDInsight: fejlmeddelelser] [hdinsight-errors]


## <a name="appendix-a---the-word-count-source-code"></a>Tillæg A - Word Tæl-kildekode

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


## <a name="appendix-b---the-word-count-streaming-source-code"></a>Tillæg B – antallet af ord streaming kildekode

Programmet MapReduce bruger programmet cat.exe som en tilknytning grænseflade til at streame teksten i konsollen og programmet wc.exe som grænsefladen reducere til at tælle antallet af ord, der er streamet fra et dokument. Både mapper og reduktionsrør læse tegn linje for linje fra standard input strømmen (stdin) og skrive til standard output strømmen (stdout).

    // The source code for the cat.exe (Mapper).

    using System;
    using System.IO;

    namespace cat
    {
        class cat
        {
            static void Main(string[] args)
            {
                if (args.Length > 0)
                {
                    Console.SetIn(new StreamReader(args[0]));
                }

                string line;
                while ((line = Console.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
    }



Mapper koden i filen cat.cs bruger en [TextWriter] [ streamreader] objekt til at læse tegnene for den indgående stream til konsollen, som derefter skriver strømmen til standard output strømmen med statisk [Console.Writeline] [ console-writeline] metode.


    // The source code for wc.exe (Reducer) is:

    using System;
    using System.IO;
    using System.Linq;

    namespace wc
    {
        class wc
        {
            static void Main(string[] args)
            {
                string line;
                var count = 0;

                if (args.Length > 0){
                    Console.SetIn(new StreamReader(args[0]));
                }

                while ((line = Console.ReadLine()) != null) {
                    count += line.Count(cr => (cr == ' ' || cr == '\n'));
                }
                Console.WriteLine(count);
            }
        }
    }


Reduktionsrør koden i filen wc.cs bruger en [TextWriter] [ streamreader] objekt til at læse tegn fra standard input strømmen, der er resultatet af cat.exe mapperen. Når den læser tegn med [Console.Writeline] [ console-writeline] metode, den tæller ord ved at tælle mellemrum og slut på linje tegn i slutningen af hvert ord. Det derefter skriver totalen til standard output strømmen med [Console.Writeline] [ console-writeline] metode.





## <a name="appendix-c---the-pi-estimator-source-code"></a>Tillæg C - Pi estimator kildekode

Pi estimator Java-kode, der indeholder mapper og reduktionsrør funktionerne er tilgængelige for inspektion nedenfor. Programmet mapper genererer et angivet antal point, der er placeret tilfældigt inde i en firkant enhed og derefter tæller antallet af de punkter, der er inde i cirklen. Programmet reduktionsrør indsamles der tælles ved mappers punkter og derefter beregner et skøn over værdien af pi fra formel 4R, hvor R er forholdet mellem antallet point tælles inde i en cirkel til det samlede antal point, der er i kvadratet.

    /**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements. See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership. The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License. You may obtain a copy of the License at
    *
    * http://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or   implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.math.BigDecimal;
    import java.util.Iterator;

    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.BooleanWritable;
    import org.apache.hadoop.io.LongWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Writable;
    import org.apache.hadoop.io.WritableComparable;
    import org.apache.hadoop.io.SequenceFile.CompressionType;
    import org.apache.hadoop.mapred.FileInputFormat;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.MapReduceBase;
    import org.apache.hadoop.mapred.Mapper;
    import org.apache.hadoop.mapred.OutputCollector;
    import org.apache.hadoop.mapred.Reducer;
    import org.apache.hadoop.mapred.Reporter;
    import org.apache.hadoop.mapred.SequenceFileInputFormat;
    import org.apache.hadoop.mapred.SequenceFileOutputFormat;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;


    //A Map-reduce program to estimate the value of Pi
    //using quasi-Monte Carlo method.
    //
    //Mapper:
    //Generate points in a unit square
    //and then count points inside/outside of the inscribed circle of the square.
    //
    //Reducer:
    //Accumulate points inside/outside results from the mappers.
    //Let numTotal = numInside + numOutside.
    //The fraction numInside/numTotal is a rational approximation of
    //the value (Area of the circle)/(Area of the square),
    //where the area of the inscribed circle is Pi/4
    //and the area of unit square is 1.
    //Then, Pi is estimated value to be 4(numInside/numTotal).
    //

    public class PiEstimator extends Configured implements Tool {
    //tmp directory for input/output
    static private final Path TMP_DIR = new Path(
    PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

    //2-dimensional Halton sequence {H(i)},
    //where H(i) is a 2-dimensional point and i >= 1 is the index.
    //Halton sequence is used to generate sample points for Pi estimation.
    private static class HaltonSequence {
    // Bases
    static final int[] P = {2, 3};
    //Maximum number of digits allowed
    static final int[] K = {63, 40};

    private long index;
    private double[] x;
    private double[][] q;
    private int[][] d;

    //Initialize to H(startindex),
    //so the sequence begins with H(startindex+1).
    HaltonSequence(long startindex) {
    index = startindex;
    x = new double[K.length];
    q = new double[K.length][];
    d = new int[K.length][];
    for(int i = 0; i < K.length; i++) {
    q[i] = new double[K[i]];
    d[i] = new int[K[i]];
    }

    for(int i = 0; i < K.length; i++) {
    long k = index;
    x[i] = 0;

    for(int j = 0; j < K[i]; j++) {
    q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
    d[i][j] = (int)(k % P[i]);
    k = (k - d[i][j])/P[i];
    x[i] += d[i][j] * q[i][j];
    }
    }
    }

    //Compute next point.
    //Assume the current point is H(index).
    //Compute H(index+1).
    //@return a 2-dimensional point with coordinates in [0,1)^2
    double[] nextPoint() {
    index++;
    for(int i = 0; i < K.length; i++) {
    for(int j = 0; j < K[i]; j++) {
    d[i][j]++;
    x[i] += q[i][j];
    if (d[i][j] < P[i]) {
    break;
    }
    d[i][j] = 0;
    x[i] -= (j == 0? 1.0: q[i][j-1]);
    }
    }
    return x;
    }
    }

    //Mapper class for Pi estimation.
    //Generate points in a unit square and then
    //count points inside/outside of the inscribed circle of the square.
    public static class PiMapper extends MapReduceBase
    implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

    //Map method.
    //@param offset samples starting from the (offset+1)th sample.
    //@param size the number of samples for this map
    //@param out output {ture->numInside, false->numOutside}
    //@param reporter
    public void map(LongWritable offset,
    LongWritable size,
    OutputCollector<BooleanWritable, LongWritable> out,
    Reporter reporter) throws IOException {

    final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
    long numInside = 0L;
    long numOutside = 0L;

    for(long i = 0; i < size.get(); ) {
    //generate points in a unit square
    final double[] point = haltonsequence.nextPoint();

    //count points inside/outside of the inscribed circle of the square
    final double x = point[0] - 0.5;
    final double y = point[1] - 0.5;
    if (x*x + y*y > 0.25) {
    numOutside++;
    } else {
    numInside++;
    }

    //report status
    i++;
    if (i % 1000 == 0) {
    reporter.setStatus("Generated " + i + " samples.");
    }
    }

    //output map results
    out.collect(new BooleanWritable(true), new LongWritable(numInside));
    out.collect(new BooleanWritable(false), new LongWritable(numOutside));
    }
    }


    //Reducer class for Pi estimation.
    //Accumulate points inside/outside results from the mappers.
    public static class PiReducer extends MapReduceBase
    implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

    private long numInside = 0;
    private long numOutside = 0;
    private JobConf conf; //configuration for accessing the file system

    //Store job configuration.
    @Override
    public void configure(JobConf job) {
    conf = job;
    }


    // Accumulate number of points inside/outside results from the mappers.
    // @param isInside Is the points inside?
    // @param values An iterator to a list of point counts
    // @param output dummy, not used here.
    // @param reporter

    public void reduce(BooleanWritable isInside,
    Iterator<LongWritable> values,
    OutputCollector<WritableComparable<?>, Writable> output,
    Reporter reporter) throws IOException {
    if (isInside.get()) {
    for(; values.hasNext(); numInside += values.next().get());
    } else {
    for(; values.hasNext(); numOutside += values.next().get());
    }
    }

    //Reduce task done, write output to a file.
    @Override
    public void close() throws IOException {
    //write output to a file
    Path outDir = new Path(TMP_DIR, "out");
    Path outFile = new Path(outDir, "reduce-out");
    FileSystem fileSys = FileSystem.get(conf);
    SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
    outFile, LongWritable.class, LongWritable.class,
    CompressionType.NONE);
    writer.append(new LongWritable(numInside), new LongWritable(numOutside));
    writer.close();
    }
    }

    //Run a map/reduce job for estimating Pi.
    //@return the estimated value of Pi.
    public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
    )
    throws IOException {
    //setup job conf
    jobConf.setJobName(PiEstimator.class.getSimpleName());

    jobConf.setInputFormat(SequenceFileInputFormat.class);

    jobConf.setOutputKeyClass(BooleanWritable.class);
    jobConf.setOutputValueClass(LongWritable.class);
    jobConf.setOutputFormat(SequenceFileOutputFormat.class);

    jobConf.setMapperClass(PiMapper.class);
    jobConf.setNumMapTasks(numMaps);

    jobConf.setReducerClass(PiReducer.class);
    jobConf.setNumReduceTasks(1);

    // turn off speculative execution, because DFS doesn't handle
    // multiple writers to the same file.
    jobConf.setSpeculativeExecution(false);

    //setup input/output directories
    final Path inDir = new Path(TMP_DIR, "in");
    final Path outDir = new Path(TMP_DIR, "out");
    FileInputFormat.setInputPaths(jobConf, inDir);
    FileOutputFormat.setOutputPath(jobConf, outDir);

    final FileSystem fs = FileSystem.get(jobConf);
    if (fs.exists(TMP_DIR)) {
     throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
     + " already exists. Please remove it first.");
     }
     if (!fs.mkdirs(inDir)) {
     throw new IOException("Cannot create input directory " + inDir);
     }

     //generate an input file for each map task
     try {
     for(int i=0; i < numMaps; ++i) {
     final Path file = new Path(inDir, "part"+i);
     final LongWritable offset = new LongWritable(i * numPoints);
     final LongWritable size = new LongWritable(numPoints);
     final SequenceFile.Writer writer = SequenceFile.createWriter(
     fs, jobConf, file,
     LongWritable.class, LongWritable.class, CompressionType.NONE);
     try {
     writer.append(offset, size);
     } finally {
     writer.close();
     }
     System.out.println("Wrote input for Map #"+i);
     }

     //start a map/reduce job
     System.out.println("Starting Job");
     final long startTime = System.currentTimeMillis();
     JobClient.runJob(jobConf);
     final double duration = (System.currentTimeMillis() - startTime)/1000.0;
     System.out.println("Job Finished in " + duration + " seconds");

     //read outputs
     Path inFile = new Path(outDir, "reduce-out");
     LongWritable numInside = new LongWritable();
     LongWritable numOutside = new LongWritable();
     SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
     try {
     reader.next(numInside, numOutside);
     } finally {
     reader.close();
     }

     //compute estimated value
     return BigDecimal.valueOf(4).setScale(20)
     .multiply(BigDecimal.valueOf(numInside.get()))
     .divide(BigDecimal.valueOf(numMaps))
     .divide(BigDecimal.valueOf(numPoints));
     } finally {
     fs.delete(TMP_DIR, true);
     }
     }

    //Parse arguments and then runs a map/reduce job.
    //Print output in standard out.
    //@return a non-zero if there is an error. Otherwise, return 0.
     public int run(String[] args) throws Exception {
     if (args.length != 2) {
     System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
     ToolRunner.printGenericCommandUsage(System.err);
     return -1;
     }

     final int nMaps = Integer.parseInt(args[0]);
     final long nSamples = Long.parseLong(args[1]);

     System.out.println("Number of Maps = " + nMaps);
     System.out.println("Samples per Map = " + nSamples);

     final JobConf jobConf = new JobConf(getConf(), getClass());
     System.out.println("Estimated value of Pi is "
     + estimate(nMaps, nSamples, jobConf));
     return 0;
     }

     //main method for running it as a stand alone command.
     public static void main(String[] argv) throws Exception {
     System.exit(ToolRunner.run(null, new PiEstimator(), argv));
     }
     }
     
## <a name="appendix-d---the-10gb-graysort-source-code"></a>Tillæg D - 10gb graysort kildekode

Koden for programmet TeraSort MapReduce præsenteres til inspektion i dette afsnit.


    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     *     http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

    package org.apache.hadoop.examples.terasort;

    import java.io.IOException;
    import java.io.PrintStream;
    import java.net.URI;
    import java.util.ArrayList;
    import java.util.List;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.filecache.DistributedCache;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.NullWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.Partitioner;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;

    /**
     * Generates the sampled split points, launches the job,
     * and waits for it to finish.
     * <p>
     * To run the program:
     * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
     */

    public class TeraSort extends Configured implements Tool {
      private static final Log LOG = LogFactory.getLog(TeraSort.class);

      /**
       * A partitioner that splits text keys into roughly equal
       * partitions in a global sorted order.
       */

      static class TotalOrderPartitioner implements Partitioner<Text,Text>{
        private TrieNode trie;
        private Text[] splitPoints;

        /**
         * A generic trie node
         */
        static abstract class TrieNode {
          private int level;
          TrieNode(int level) {
            this.level = level;
          }
          abstract int findPartition(Text key);
          abstract void print(PrintStream strm) throws IOException;
          int getLevel() {
            return level;
          }
        }

        /**
         * An inner trie node that contains 256 children based on the next
         * character.
         */
        static class InnerTrieNode extends TrieNode {
          private TrieNode[] child = new TrieNode[256];

          InnerTrieNode(int level) {
            super(level);
          }
          int findPartition(Text key) {
            int level = getLevel();
            if (key.getLength() <= level) {
              return child[0].findPartition(key);
            }
            return child[key.getBytes()[level]].findPartition(key);
          }
          void setChild(int idx, TrieNode child) {
            this.child[idx] = child;
          }
          void print(PrintStream strm) throws IOException {
            for(int ch=0; ch < 255; ++ch) {
              for(int i = 0; i < 2*getLevel(); ++i) {
                strm.print(' ');
              }
              strm.print(ch);
              strm.println(" ->");
              if (child[ch] != null) {
                child[ch].print(strm);
              }
            }
          }
        }

        /**
         * A leaf trie node that does string compares to figure out where the given
         * key belongs between lower..upper.
         */
        static class LeafTrieNode extends TrieNode {
          int lower;
          int upper;
          Text[] splitPoints;
          LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
            super(level);
            this.splitPoints = splitPoints;
            this.lower = lower;
            this.upper = upper;
          }
          int findPartition(Text key) {
            for(int i=lower; i<upper; ++i) {
              if (splitPoints[i].compareTo(key) >= 0) {
                return i;
              }
            }
            return upper;
          }
          void print(PrintStream strm) throws IOException {
            for(int i = 0; i < 2*getLevel(); ++i) {
              strm.print(' ');
            }
            strm.print(lower);
            strm.print(", ");
            strm.println(upper);
          }
        }


        /**
         * Read the cut points from the given sequence file.
         * @param fs the file system
         * @param p the path to read
         * @param job the job config
         * @return the strings to split the partitions on
         * @throws IOException
         */
        private static Text[] readPartitions(FileSystem fs, Path p,
                                             JobConf job) throws IOException {
          SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
          List<Text> parts = new ArrayList<Text>();
          Text key = new Text();
          NullWritable value = NullWritable.get();
          while (reader.next(key, value)) {
            parts.add(key);
            key = new Text();
          }
          reader.close();
          return parts.toArray(new Text[parts.size()]);  
        }

        /**
         * Given a sorted set of cut points, build a trie that will find the correct
         * partition quickly.
         * @param splits the list of cut points
         * @param lower the lower bound of partitions 0..numPartitions-1
         * @param upper the upper bound of partitions 0..numPartitions-1
         * @param prefix the prefix that we have already checked against
         * @param maxDepth the maximum depth we will build a trie for
         * @return the trie node that will divide the splits correctly
         */
        private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                          Text prefix, int maxDepth) {
          int depth = prefix.getLength();
          if (depth >= maxDepth || lower == upper) {
            return new LeafTrieNode(depth, splits, lower, upper);
          }
          InnerTrieNode result = new InnerTrieNode(depth);
          Text trial = new Text(prefix);
          // append an extra byte on to the prefix
          trial.append(new byte[1], 0, 1);
          int currentBound = lower;
          for(int ch = 0; ch < 255; ++ch) {
            trial.getBytes()[depth] = (byte) (ch + 1);
            lower = currentBound;
            while (currentBound < upper) {
              if (splits[currentBound].compareTo(trial) >= 0) {
                break;
              }
              currentBound += 1;
            }
            trial.getBytes()[depth] = (byte) ch;
            result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                         maxDepth);
          }
          // pick up the rest
          trial.getBytes()[depth] = 127;
          result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                        maxDepth);
          return result;
        }

        public void configure(JobConf job) {
          try {
            FileSystem fs = FileSystem.getLocal(job);
            Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
            splitPoints = readPartitions(fs, partFile, job);
            trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
          } catch (IOException ie) {
            throw new IllegalArgumentException("can't read paritions file", ie);
          }
        }

        public TotalOrderPartitioner() {
        }

        public int getPartition(Text key, Text value, int numPartitions) {
          return trie.findPartition(key);
        }

      }

      public int run(String[] args) throws Exception {
        LOG.info("starting");
        JobConf job = (JobConf) getConf();
        Path inputDir = new Path(args[0]);
        inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
        Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
        URI partitionUri = new URI(partitionFile.toString() +
                                   "#" + TeraInputFormat.PARTITION_FILENAME);
        TeraInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        job.setJobName("TeraSort");
        job.setJarByClass(TeraSort.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        job.setInputFormat(TeraInputFormat.class);
        job.setOutputFormat(TeraOutputFormat.class);
        job.setPartitionerClass(TotalOrderPartitioner.class);
        TeraInputFormat.writePartitionFile(job, partitionFile);
        DistributedCache.addCacheFile(partitionUri, job);
        DistributedCache.createSymlink(job);
        job.setInt("dfs.replication", 1);
        TeraOutputFormat.setFinalSync(job, true);
        JobClient.runJob(job);
        LOG.info("done");
        return 0;
      }

      /**
       * @param args
       */

      public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
        System.exit(res);
      }
    }














 




[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../powershell-install-configure.md

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx

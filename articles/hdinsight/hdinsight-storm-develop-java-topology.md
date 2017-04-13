<properties
   pageTitle="Udvikle Java-baserede topologier for Apache Storm | Microsoft Azure"
   description="Lær at oprette Storm topologier i Java ved at oprette en simpel word Tæl topologi."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/14/2016"
   ms.author="larryfr"/>

#<a name="develop-java-based-topologies-for-a-basic-word-count-application-with-apache-storm-and-maven-on-hdinsight"></a>Udvikle Java-baserede topologier til et grundlæggende Ordoptælling program med Apache Storm og Maven på HDInsight

Få mere at vide, hvordan du opretter en Java-baserede topologi for Apache Storm på HDInsight ved hjælp af Maven. Du fører gennem processen med at oprette et grundlæggende Ordoptælling program ved hjælp af Maven og Java, hvor topologien er defineret i Java. Derefter skal lære du, hvordan du definerer topologien ved hjælp af frigivelse framework.

> [AZURE.NOTE] Den frigivelse, som er tilgængelig i Storm 0.10.0 eller nyere. Storm 0.10.0 leveres med HDInsight 3.3 og 3.4.

Når du har fuldført trinnene i dette dokument, har du en grundlæggende topologi, som du kan installere til Apache Storm på HDInsight.

> [AZURE.NOTE] En endelig version af topologier oprettet i dette dokument er tilgængelig på [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

##<a name="prerequisites"></a>Forudsætninger

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Java udvikler Kit (JDK) version 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a>: Maven er et projekt build system til Java projekter.

* En teksteditor, <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, <a href="http://www.sublimetext.com/" target="_blank">Sublime tekst</a>, <a href="https://atom.io/" target="_blank">Atom.io</a>, <a href="http://brackets.io/" target="_blank">Brackets.io</a>. Eller du kan bruge et integreret udviklingsmiljø (IDE) som <a href="https://eclipse.org/" target="_blank">Eklipse</a> (version Luna eller nyere).

    > [AZURE.NOTE] Din redaktør eller IDE muligvis bestemte funktioner til at arbejde med Maven, der ikke behandles i dette dokument. Du kan finde oplysninger om funktionerne for redigering miljøet for det produkt, du bruger.

##<a name="configure-environment-variables"></a>Konfigurere miljøvariabler

Følgende miljøvariabler kan angives, når du installerer Java og JDK. Skal du kontrollere, at de findes, og at de indeholder de korrekte værdier for systemet.

* **JAVA_HOME** - skal pege på den mappe, hvor Java runtime-miljø (JRE) er installeret. For eksempel i en Unix eller Linux fordeling, den skal have en værdi, der ligner `/usr/lib/jvm/java-7-oracle`. I Windows, skal den indeholde en værdi, der ligner`c:\Program Files (x86)\Java\jre1.7`

* **Sti** - skal indeholde følgende stier:

    * **JAVA_HOME** (eller tilsvarende stien)

    * **JAVA_HOME\bin** (eller tilsvarende stien)

    * Den mappe, hvor Maven er installeret

##<a name="create-a-new-maven-project"></a>Oprette et nyt Maven projekt

Brug følgende kode til at oprette et projekt med Maven med navnet **WordCount**fra kommandolinjen:

    mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

Dette vil oprette en ny mappe med navnet **WordCount** på den aktuelle placering, som indeholder et grundlæggende Maven projekt.

Mappen **WordCount** skal indeholde følgende elementer:

* **pom.XML**: indeholder indstillinger for Maven projektet.

* **src\main\java\com\microsoft\example**: indeholder din programkode.

* **src\test\java\com\microsoft\example**: indeholder test for dit program. I dette eksempel skal vil vi ikke oprette test.

###<a name="remove-the-example-code"></a>Fjerne eksempelkoden

Da vi opretter vores program, slette testen genererede og programfiler:

*  **src\test\java\com\microsoft\example\AppTest.Java**

*  **src\main\java\com\microsoft\example\App.Java**

##<a name="add-properties"></a>Tilføj egenskaber

Maven gør det muligt at definere projektniveau værdier, der kaldes egenskaber. Tilføj følgende efter den `<url>http://maven.apache.org</url>` linje:

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!--
        Storm 0.10.0 is for HDInsight 3.3 and 3.4.
        To find the version information for earlier HDInsight cluster
        versions, see https://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/
        -->
        <storm.version>0.10.0</storm.version>
    </properties>

Vi kan nu bruge disse værdier i andre sektioner. Når du angiver versionen af Storm komponenter, kan vi for eksempel bruge `${storm.version}` i stedet for hårde kodningssprog en værdi.

##<a name="add-dependencies"></a>Tilføje afhængigheder

Da dette er en Storm topologi, skal du tilføje en afhængighed for Storm komponenter. Åbn filen **pom.xml** og tilføje følgende kode i den ** &lt;afhængigheder >** afsnit:

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-core</artifactId>
      <version>${storm.version}</version>
      <!-- keep storm out of the jar-with-dependencies -->
      <scope>provided</scope>
    </dependency>

Under kompilering bruger Maven disse oplysninger til at slå op **storm core** i Maven lager. Det første ser ud i lager på din lokale computer. Hvis filerne, der ikke er der, den kan du hente dem fra det offentlige Maven lager og gemme dem i det lokale lager.

> [AZURE.NOTE] Meddelelse om den `<scope>provided</scope>` linje i afsnittet vi tilføjet. Det fortæller Maven udelukke **storm core** fra glas filer vi opretter, fordi det leveres af systemet. Dette giver mulighed pakkerne du opretter for at være en smule mindre, og det sikrer, at de skal bruge de **storm core -** bit, der er inkluderet i Storm på HDInsight klynge.

##<a name="build-configuration"></a>Opbygge konfiguration

Maven plug-ins gør det muligt at tilpasse build faserne af projektet, som hvordan projektet er kompileret eller Sådan pakke til en glas-fil. Åbn filen **pom.xml** og tilføje følgende kode direkte over den `</project>` linje.

    <build>
      <plugins>
      </plugins>
      <resources>
      </resources>
    </build>

Dette afsnit bruges til at tilføje plug-ins, ressourcer og andre build-indstillinger. Du kan finde en fuld reference af filen __pom.xml__ [http://maven.apache.org/pom.html](http://maven.apache.org/pom.html).

###<a name="add-plug-ins"></a>Tilføje plug-ins

For Storm topologier er <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">Eksekverbar Maven plug-in'en</a> er nyttigt, fordi det giver dig mulighed at køre nemt topologien lokalt i dit udviklingsmiljø. Tilføj følgende til den `<plugins>` afsnit i filen **pom.xml** medtage eksekverbar Maven plug-in'et:

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.4.0</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

> [AZURE.NOTE] Bemærk, at den `<mainClass>` post bruger `${storm.topology}`. Vi definere ikke det tidligere i sektionen egenskaber (men vi kan have). I stedet vil vi Angiv denne værdi fra kommandolinjen, når der benyttes topologien dit udviklingsmiljø på et senere tidspunkt.

En anden nyttige plug-in'en er <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Apache Maven compileren plug-in</a>, som bruges til at ændre indstillingerne for målgruppen. Den primære årsag vi brug for dette løses ved at ændre den Java-version, Maven anvender til kilde- og destinationswebsteder for dit program. Vi vil version 1.7.

Tilføj følgende i den `<plugins>` afsnit i filen **pom.xml** medtage Apache Maven compileren plug-in'et og angive de kilde- og destinationswebsteder versioner til 1.7.

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

###<a name="configure-resources"></a>Konfigurere ressourcer

Afsnittet ressourcer kan du medtage ikke-koderessourcer som konfigurationsfiler, der bruges til komponenter i topologien. I dette eksempel skal du tilføje følgende i den `<resources>` afsnit i filen **pom.xml** .

    <resource>
        <directory>${basedir}/resources</directory>
        <filtering>false</filtering>
        <includes>
          <include>log4j2.xml</include>
        </includes>
    </resource>

Dette tilføjer mappen ressourcer i roden af projektet (`${basedir}`) som en placering, der indeholder ressourcer, og indeholder filen med navnet __log4j2.xml__. Denne fil bruges til at konfigurere, hvilke oplysninger logføres topologien.

##<a name="create-the-topology"></a>Oprette topologien

En Java-baserede Storm topologi består af tre komponenter, der skal du oprette (eller reference) som en afhængighed.

* **Spouts**: læser data fra eksterne datakilder og udsender streams af data i topologien.

* **Bolte**: udfører behandling på streams udsendes af spouts eller andre bolte og udsender en eller flere indholdsstreams.

* **Topologi**: definerer, hvordan spouts og bolte er arrangeret og giver indgangspunktet for topologien.

###<a name="create-the-spout"></a>Oprette tud

Hvis du vil reducere krav til konfiguration af eksterne datakilder, udsender den følgende tud blot tilfældige sætninger. Det er en ændret version af en tud, der følger med [Storm Starter eksempler](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [AZURE.NOTE] Et eksempel på en tud, der læses fra en ekstern datakilde, kan du se en af følgende eksempler:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): en eksempel-tud, der læses fra Twitter
>
> * [Storm Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): en tud, der læses fra Kafka

Oprette en ny fil med navnet **RandomSentenceSpout.java** i mappen **src\main\java\com\microsoft\example** tud, og bruge følgende som indholdet:

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

Tage et øjeblik at læse gennem kodekommentarer at forstå, hvordan denne tud fungerer.

> [AZURE.NOTE] Selvom denne topologi bruger kun én tud, kan andre har flere, feed data fra forskellige kilder i topologien.

###<a name="create-the-bolts"></a>Oprette boltene

Bolte håndtere databehandlingen. Til denne topologi har vi to bolte:

* **SplitSentence**: opdeles sætningerne udsendes af **RandomSentenceSpout** i enkeltord.

* **WordCount**: tæller, hvor mange gange hvert ord, der er opstået.

> [AZURE.NOTE] Bolte kan gøre direkte noget, for eksempel beregning, brugerdata eller taler med eksterne komponenter.

Oprette to nye filer, **SplitSentence.java** og **WordCount.Java** i mappen **src\main\java\com\microsoft\example** . Du kan bruge følgende som indholdet efter filerne:

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;
    import java.util.Iterator;

    import backtype.storm.Constants;
    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;
    import backtype.storm.Config;

    // For logging
    import org.apache.logging.log4j.Logger;
    import org.apache.logging.log4j.LogManager;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
        //Create logger for this class
        private static final Logger logger = LogManager.getLogger(WordCount.class);
        //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();
        //How often we emit a count of words
        private Integer emitFrequency;

        // Default constructor
        public WordCount() {
            emitFrequency=5; // Default to 60 seconds
        }

        // Constructor that sets emit frequency
        public WordCount(Integer frequency) {
            emitFrequency=frequency;
        }

        //Configure frequency of tick tuples for this bolt
        //This delivers a 'tick' tuple on a specific interval,
        //which is used to trigger certain actions
        @Override
        public Map<String, Object> getComponentConfiguration() {
            Config conf = new Config();
            conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
            return conf;
        }

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
            //If it's a tick tuple, emit all words and counts
            if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
                    && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
                for(String word : counts.keySet()) {
                    Integer count = counts.get(word);
                    collector.emit(new Values(word, count));
                    logger.info("Emitting a count of " + count + " for word " + word);
                }
            } else {
                //Get the word contents from the tuple
                String word = tuple.getString(0);
                //Have we counted any already?
                Integer count = counts.get(word);
                if (count == null)
                    count = 0;
                //Increment the count and store it
                count++;
                counts.put(word, count);
            }
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            declarer.declare(new Fields("word", "count"));
        }
    }

Tage et øjeblik at læse gennem kodekommentarer at forstå, hvordan hver bolt fungerer.

###<a name="define-the-topology"></a>Definere topologien

Topologien binder på spouts og bolte sammen i et diagram, som definerer, hvordan data flyder mellem komponenterne. Den indeholder også parallelitet tip, der bruger Storm, når du opretter forekomster af komponenterne i klyngen.

Følgende er et grundlæggende diagram over graf over komponenter til denne topologi.

![diagram, der viser det spouts og bolte arrangement](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Oprette en ny fil med navnet **WordCountTopology.java** i mappen **src\main\java\com\microsoft\example** for at implementere topologien. Du kan bruge følgende som indholdet til filen:

    package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        //Set to false to disable debug information
        // when running in production mode.
        conf.setDebug(false);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

Tage et øjeblik at læse gennem kodekommentarer at forstå, hvordan topologien defineres og sendes derefter til klyngen.

###<a name="configure-logging"></a>Konfigurere logføring

Storm bruger Apache Log4j til at logge oplysninger. Hvis du ikke konfigurerer logføring, udsender topologien en masse diagnostiske oplysninger, som kan være svært at læse. Du kan styre, hvad logføres ved at oprette en fil med navnet __log4j2.xml__ i mappen __ressourcer__ . Du kan bruge følgende som indholdet af filerne.

    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
    </Configuration>

Dette konfigurerer en ny logføring for klassen __com.microsoft.example__ , som indeholder komponenterne i dette eksempel topologi. Niveauet er angivet til sporing for denne logføring, som vil registrere logføringsoplysninger fra komponenter i denne topologi. Hvis du ser tilbage gennem koden for dette projekt, vil du bemærke, at filen WordCount.java implementerer logføring. antallet af hvert ord registreres.

Den `<Root level="error">` sektion konfigurerer rodniveau på logføring (alt ikke i __com.microsoft.example__,) for at logge kun oplysninger om fejlen.

> [AZURE.IMPORTANT] Dette gør det meget reducerer de oplysninger, der er logget, når du tester en topologi i dit udviklingsmiljø, men det fjerner ikke fejlfindingsoplysninger, der er oprettet, når du kører på en klynge i fremstilling. For at reducere disse oplysninger, skal du også angive fejlfinding til falsk i sektionen konfiguration, der er sendt til klyngen. Se WordCountTopology.java koden i dette dokument for et eksempel. 

Du kan finde flere oplysninger om konfiguration af logføring til Log4j [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [AZURE.NOTE] Storm version 0.10.0 bruger Log4j 2.x. Ældre versioner af storm bruges Log4j 1.x, der bruges et andet format til konfigurationen af log. Du kan finde oplysninger på ældre konfigurationen [http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

##<a name="test-the-topology-locally"></a>Teste topologien lokalt

Når du gemmer filerne, kan du bruge følgende kommando til at teste topologien lokalt.

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

Når der køres, vises topologien startoplysninger. Vælg derefter den begynder at få vist linjer, som svarer til følgende som sætninger udledes fra tud og behandles af boltene.

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Ved at se på listen logføring udsendes af WordCount bolt, vi kan se, at ' og ' er blevet udledes 113 gange. Antallet fortsætter med at gå op, så længe topologien kører, da tud løbende udsender de samme sætninger.

Der vil også være et 5 anden interval mellem emission af ord og optællinger. Dette sker, fordi komponenten __WordCount__ er konfigureret til at udsende kun oplysninger, når en tupel aksemærker ankommer, og der anmodes om, at disse tupler kun leveres hvert 5 som standard.

## <a name="convert-the-topology-to-flux"></a>Konvertere topologien til frigivelse

Som er en ny struktur, der er tilgængelige med Storm 0.10.0, som du kan bruge til at adskille konfiguration fra implementering. Dine komponenter (Bolte og spouts,) stadig er defineret i Java, men topologien defineres ved hjælp af en YAML fil.

Filen YAML definerer komponenterne, der bruges til topologien, hvordan data flyder mellem dem, og hvilke værdier du skal bruge, når initialisering af komponenterne. Du kan medtage en YAML fil som en del af glas filen med dit projekt, når du installerer det, eller du kan bruge en ekstern YAML-fil, når du starter topologien.

1. Flytte filen __WordCountTopology.java__ af projektet. Tidligere dette defineret topologien, men vi kan ikke bruge det til frigivelse.

2. Oprette en ny fil med navnet __topology.yaml__i mappen __ressourcer__ . Du kan bruge følgende som indholdet af denne fil.

        # topology definition

        # name to be used when submitting. This is what shows up...
        # in the Storm UI/storm command-line tool as the topology name
        # when submitted to Storm
        name: "wordcount"

        # Topology configuration
        config:
        # Hint for the number of workers to create
        topology.workers: 1

        # Spout definitions
        spouts:
        - id: "sentence-spout"
            className: "com.microsoft.example.RandomSentenceSpout"
            # parallelism hint
            parallelism: 1

        # Bolt definitions
        bolts:
        - id: "splitter-bolt"
            className: "com.microsoft.example.SplitSentence"
            parallelism: 1

        - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 10
            parallelism: 1

        # Stream definitions
        streams:
        - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
            # The stream emitter
            from: "sentence-spout"
            # The stream consumer
            to: "splitter-bolt"
            # Grouping type
            grouping:
            type: SHUFFLE

        - name: "Splitter -> Counter"
            from: "splitter-bolt"
            to: "counter-bolt"
            grouping:
            type: FIELDS
            # field(s) to group on
            args: ["word"]

    Tage et øjeblik på at læse og forstå, hvad hver enkelt sektion betyder, og hvordan den relaterer til Java-baserede definitionen i filen __WordCountTopology.java__ .

3. Foretage følgende ændringer til filen __pom.xml__ .

    * Tilføj følgende nye afhængigheden i den `<dependencies>` afsnit:

            <!-- Add a dependency on the Flux framework -->
            <dependency>
                <groupId>org.apache.storm</groupId>
                <artifactId>flux-core</artifactId>
                <version>${storm.version}</version>
            </dependency>

    * Tilføj følgende plug-in'et til den `<plugins>` sektion. Denne plug-in håndterer oprettelse af en pakke (glas fil) for projektet og gælder nogle bestemte transformationer for frigivelse, når du opretter pakken.

            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                        <!-- Keep us from getting a "can't overwrite file error" -->
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                        <!-- We're using Flux, so refer to it as main -->
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>org.apache.storm.flux.Flux</mainClass>
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
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
                    </execution>
                </executions>
            </plugin>

    * I __eksekverbar-maven-plug-in__ `<configuration>` skal du ændre værdien for `<mainClass>` til `org.apache.storm.flux.Flux`. Dette giver mulighed for frigivelse til at håndtere kører topologien, når vi Kør det lokalt er under udvikling.

    * I den `<resources>` skal du tilføje følgende for at den `<includes>`. Dette omfatter YAML filen, der definerer topologien som en del af projektet.
    
            <include>topology.yaml</include>

## <a name="test-the-flux-topology-locally"></a>Teste frigivelse topologien lokalt

1. Brug følgende til at samle og udføre frigivelse topologien ved hjælp af Maven.

        mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    
    Hvis du bruger PowerShell, kan du bruge følgende:
    
        mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"

    Hvis du er på et Unix-Linux/OS X-system, og du har [installeret Storm i dit udviklingsmiljø](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), kan du bruge følgende kommandoer i stedet:

        mvn compile package
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml

    Den `--local` parameter kører topologien i lokal tilstand på dit udviklingsmiljø. Den `-R /topology.yaml` parameteren bruger den `topology.yaml` fil ressource fra filen glas til at definere topologien.

    Når der køres, vises topologien startoplysninger. Vælg derefter den begynder at få vist linjer, som svarer til følgende som sætninger udledes fra tud og behandles af boltene.

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    
    Der vil være en 10 anden forsinkelse mellem batches med registrerede oplysninger, som den `topology.yaml` fil overfører en værdi på `10` oprettelsen af komponenten WordCount. Dette angiver forsinkelse intervallet for aksemærker tuplen til 10 sekunder.

2.  Oprette en kopi af den `topology.yaml` fil fra projektet. Kald den noget i retning af `newtopology.yaml`. Fil, find følgende afsnit og Skift værdien for `10` til `5`. Dette ændrer intervallet mellem udsendelse batches med Ordtælling i 10 sekunder til 5.

          - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 5
            parallelism: 1

3. For at køre topologien skal du bruge følgende kommando:

        mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"

    Eller hvis du har Storm på din Linux/Unix/OS X udviklingsmiljø:

        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml

    Ændre den `/path/to/newtopology.yaml` til stien til filen newtopology.yaml, du har oprettet i ovenstående trin. Denne kommando anvender newtopology.yaml som topologi definitionen. Da vi ikke indeholder den `compile` parameter, Maven vil genbruge versionen af det projekt, der er oprettet i ovenstående trin.

    Når topologien starter, skal du bør Bemærk, at tid mellem emitterede batches har ændret afspejler værdien i newtopology.yaml. Så du kan se, at du kan ændre din konfiguration via en YAML-fil uden at skulle genkompilere topologien.

Der findes flere andre funktioner, som indeholder, der ikke er beskrevet her, som erstatning af variabler i filen YAML baseret på parametre sendes på kørselstidspunktet eller fra miljøvariabler. Du kan finde flere oplysninger om disse og andre funktioner i frigivelse framework, [frigivelse (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

##<a name="trident"></a>Trident

Trident er en overordnet fremstilling, som leveres af Storm. Det understøtter med høj sikkerhed behandling. Den primære fordel ved Trident er, at det kan garanterer, at alle meddelelser, som indsætter topologien behandles kun én gang. Dette er svært at opnå i en rå Java topologi, som sikrers, meddelelser vil blive behandlet mindst én gang. Der er også andre forskelle, som indbyggede komponenter, der kan bruges i stedet for at oprette bolte. Bolte erstattes faktisk helt med mindre-generisk komponenter, som filtre, prognoser og funktioner.

Trident programmer kan oprettes ved hjælp af Maven projekter. Du kan bruge de samme grundlæggende trin, som præsenteres tidligere i denne artikel – kun koden er forskellige. Trident kan også (i øjeblikket) ikke bruges med frigivelse framework.

Du kan finde flere oplysninger om Trident, <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Trident API oversigt</a>.

Et eksempel på et Trident til computeren, kan du se [Twitter-cirkulerer emner med Apache Storm på HDInsight](hdinsight-storm-twitter-trending.md).

##<a name="next-steps"></a>Næste trin

Du har lært, hvordan du opretter en Storm topologi ved hjælp af Java. Nu se, hvordan du:

* [Installere og administrere Apache Storm topologier på HDInsight](hdinsight-storm-deploy-monitor-topology.md)

* [Udvikle C# topologier for Apache Storm på HDInsight ved hjælp af Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Du kan finde flere eksempel Storm topologier ved at besøge [eksempel topologier for Storm på HDInsight](hdinsight-storm-example-topology.md).

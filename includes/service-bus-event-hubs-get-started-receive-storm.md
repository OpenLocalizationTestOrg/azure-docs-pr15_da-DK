## <a name="receive-messages-with-apache-storm"></a>Modtage meddelelser med Apache Storm

[**Apache Storm**](https://storm.incubator.apache.org) er et fordelt realtid beregning system, der forenkler stabil behandling af uafhængige streams af data. Dette afsnit viser, hvordan du bruger en begivenhed Hubs Storm tud til at modtage hændelser fra begivenhed Hubs. Brug af Apache Storm, kan du opdele begivenheder på tværs af flere processer, der er placeret i forskellige noder. Begivenhed Hubs integration med Storm forenkler begivenhed forbrug ved transparent checkpointing dens status ved hjælp af Storm's Zookeeper installation, administration af fast kontrolpunkter og parallelt modtager fra begivenhed Hubs.

Du kan finde flere oplysninger om begivenhed Hubs modtage mønstre, skal du se [begivenhed Hubs oversigt][].

Dette selvstudium bruger en [HDInsight Storm][] installation, som følger med den begivenhed Hubs tud allerede er tilgængelige.

1. Følg fremgangsmåden [HDInsight Storm - komme i gang](../articles/hdinsight/hdinsight-storm-overview.md) for at oprette en ny HDInsight klynge og oprette forbindelse til den via Fjernskrivebord.

2. Kopiér den `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` filer til din lokale udviklingsmiljø. Dette indeholder begivenheder-storm-tud.

3. Brug følgende kommando for at installere pakken i det lokale lager Maven. Dette gør det muligt at tilføje den som en reference i Storm projektet på et senere tidspunkt.

        mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

4. Oprette et nyt projekt Maven i Eklipse, (Klik på **filer**, derefter **Ny**og derefter **Project**).

    ![][12]

5. Vælg **Brug standard arbejdsområde placering**, og klik derefter på **Næste**

6. Vælg **maven-archetype-Hurtig start** archetype, og klik derefter på **Næste**

7. Indsætte en **gruppe-id** og **ArtifactId**, og klik derefter på **Udfør**

8. Tilføj følgende afhængigheder i i **pom.xml**, den `<dependency>` node.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-core</artifactId>
            <version>0.9.2-incubating</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.microsoft.eventhubs</groupId>
            <artifactId>eventhubs-storm-spout</artifactId>
            <version>0.9</version>
        </dependency>
        <dependency>
            <groupId>com.netflix.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>1.3.3</version>
            <exclusions>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
            <scope>provided</scope>
        </dependency>

9. Opret en fil kaldet **Config.properties** i mappen **src** og kopiere følgende indhold, erstatning med følgende værdier:

        eventhubspout.username = ReceiveRule

        eventhubspout.password = {receive rule key}

        eventhubspout.namespace = ioteventhub-ns

        eventhubspout.entitypath = {event hub name}

        eventhubspout.partitions.count = 16

        # if not provided, will use storm's zookeeper settings
        # zookeeper.connectionstring=localhost:2181

        eventhubspout.checkpoint.interval = 10

        eventhub.receiver.credits = 10

    Værdien for **eventhub.receiver.credits** bestemmer, hvor mange begivenheder er batches før du slipper dem til Storm rørledning. Af hensyn til enkel angives i dette eksempel denne værdi til 10. Fremstilling, det som regel skal du vælge højere værdier. for eksempel 1024.

10. Oprette en ny klasse, kaldet **LoggerBolt** med følgende kode:

        import java.util.Map;
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import backtype.storm.task.OutputCollector;
        import backtype.storm.task.TopologyContext;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.topology.base.BaseRichBolt;
        import backtype.storm.tuple.Tuple;

        public class LoggerBolt extends BaseRichBolt {
            private OutputCollector collector;
            private static final Logger logger = LoggerFactory
                      .getLogger(LoggerBolt.class);

            @Override
            public void execute(Tuple tuple) {
                String value = tuple.getString(0);
                logger.info("Tuple value: " + value);

                collector.ack(tuple);
            }

            @Override
            public void prepare(Map map, TopologyContext context, OutputCollector collector) {
                this.collector = collector;
                this.count = 0;
            }

            @Override
            public void declareOutputFields(OutputFieldsDeclarer declarer) {
                // no output fields
            }

        }

    Denne Storm bolt logger indholdet af de hændelser, der er modtaget. Dette kan nemt udvides for at gemme tupler i en lagringstjeneste. [HDInsight føler analyse selvstudium] bruger denne samme metode til at gemme data i HBase.

11. Oprette en klasse, kaldet **LogTopology** med følgende kode:

        import java.io.FileReader;
        import java.util.Properties;
        import backtype.storm.Config;
        import backtype.storm.LocalCluster;
        import backtype.storm.StormSubmitter;
        import backtype.storm.generated.StormTopology;
        import backtype.storm.topology.TopologyBuilder;
        import com.microsoft.eventhubs.samples.EventCount;
        import com.microsoft.eventhubs.spout.EventHubSpout;
        import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

        public class LogTopology {
            protected EventHubSpoutConfig spoutConfig;
            protected int numWorkers;

            protected void readEHConfig(String[] args) throws Exception {
                Properties properties = new Properties();
                if (args.length > 1) {
                    properties.load(new FileReader(args[1]));
                } else {
                    properties.load(EventCount.class.getClassLoader()
                            .getResourceAsStream("Config.properties"));
                }

                String username = properties.getProperty("eventhubspout.username");
                String password = properties.getProperty("eventhubspout.password");
                String namespaceName = properties
                        .getProperty("eventhubspout.namespace");
                String entityPath = properties.getProperty("eventhubspout.entitypath");
                String zkEndpointAddress = properties
                        .getProperty("zookeeper.connectionstring"); // opt
                int partitionCount = Integer.parseInt(properties
                        .getProperty("eventhubspout.partitions.count"));
                int checkpointIntervalInSeconds = Integer.parseInt(properties
                        .getProperty("eventhubspout.checkpoint.interval"));
                int receiverCredits = Integer.parseInt(properties
                        .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                    // (opt)
                System.out.println("Eventhub spout config: ");
                System.out.println("  partition count: " + partitionCount);
                System.out.println("  checkpoint interval: "
                        + checkpointIntervalInSeconds);
                System.out.println("  receiver credits: " + receiverCredits);

                spoutConfig = new EventHubSpoutConfig(username, password,
                        namespaceName, entityPath, partitionCount, zkEndpointAddress,
                        checkpointIntervalInSeconds, receiverCredits);

                // set the number of workers to be the same as partition number.
                // the idea is to have a spout and a logger bolt co-exist in one
                // worker to avoid shuffling messages across workers in storm cluster.
                numWorkers = spoutConfig.getPartitionCount();

                if (args.length > 0) {
                    // set topology name so that sample Trident topology can use it as
                    // stream name.
                    spoutConfig.setTopologyName(args[0]);
                }
            }

            protected StormTopology buildTopology() {
                TopologyBuilder topologyBuilder = new TopologyBuilder();

                EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
                topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                        spoutConfig.getPartitionCount()).setNumTasks(
                        spoutConfig.getPartitionCount());
                topologyBuilder
                        .setBolt("LoggerBolt", new LoggerBolt(),
                                spoutConfig.getPartitionCount())
                        .localOrShuffleGrouping("EventHubsSpout")
                        .setNumTasks(spoutConfig.getPartitionCount());
                return topologyBuilder.createTopology();
            }

            protected void runScenario(String[] args) throws Exception {
                boolean runLocal = true;
                readEHConfig(args);
                StormTopology topology = buildTopology();
                Config config = new Config();
                config.setDebug(false);

                if (runLocal) {
                    config.setMaxTaskParallelism(2);
                    LocalCluster localCluster = new LocalCluster();
                    localCluster.submitTopology("test", config, topology);
                    Thread.sleep(5000000);
                    localCluster.shutdown();
                } else {
                    config.setNumWorkers(numWorkers);
                    StormSubmitter.submitTopology(args[0], config, topology);
                }
            }

            public static void main(String[] args) throws Exception {
                LogTopology topology = new LogTopology();
                topology.runScenario(args);
            }
        }


    Denne klasse opretter en ny begivenhed Hubs tud, ved hjælp af egenskaber i konfigurationen Arkivér til instatiate den. Det er vigtigt at Bemærk, at dette eksempel opretter så mange spouts opgaver som antallet af partitioner i hubben begivenhed for at bruge den maksimale parallelitet tillader det pågældende begivenhed Hub.

<!-- Links -->
[Oversigt over Hubs begivenhed]: ../articles/event-hubs/event-hubs-overview.md
[HDInsight Storm]: ../articles/hdinsight/hdinsight-storm-overview.md
[HDInsight føler analyse selvstudium]: ../articles/hdinsight/hdinsight-storm-sensor-data-analysis.md

<!-- Images -->

[12]: ./media/service-bus-event-hubs-get-started-receive-storm/create-storm1.png
<properties
    pageTitle="Opbygge et HBase program ved hjælp af Maven og Java og derefter installere til Linux-baserede HDInsight | Microsoft Azure"
    description="Lær, hvordan du bruger Apache Maven til at opbygge et Java-baserede Apache HBase til computeren og derefter installere det i Linux-baserede HDInsight i Azure skyen."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-linux-based-hdinsight-hadoop"></a>Bruge Maven til at oprette Java-programmer, der bruger HBase med Linux-baserede HDInsight (Hadoop)

Få mere at vide, hvordan du opretter og opbygge et [Apache HBase](http://hbase.apache.org/) program i Java ved hjælp af Apache Maven. Brug derefter programmet med en Linux-baserede HDInsight klynge.

[Maven](http://maven.apache.org/) er en software projektstyring og forståelse værktøj, der gør det muligt at opbygge software, dokumentation og rapporter til Java projekter. I denne artikel kan du lære, hvordan du bruger den for at oprette en grundlæggende Java-program, som opretter, forespørgsler, og sletter en HBase tabel på en Linux-baserede HDInsight klynge.

> [AZURE.NOTE] Trinnene i dette dokument forudsætter, at du bruger en Linux-baserede HDInsight klynge. Oplysninger om brug af en Windows-baseret HDInsight klynge, se [Bruge Maven til at oprette Java-programmer, der bruger HBase med Windows-baseret HDInsight](hdinsight-hbase-build-java-maven.md)

##<a name="requirements"></a>Krav

* [Java platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 eller nyere

* [Maven](http://maven.apache.org/)

* [En Linux-baserede Azure HDInsight klynge med HBase](../hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] Trinnene i dette dokument er blevet testet med HDInsight klynge versioner 3.2, 3.3 og 3.4. Fremgår eksempler standardværdierne for en HDInsight 3.4 klynge.

* **Kendskab til SSH og SCP**. Du kan finde flere oplysninger om brug af SSH og SCP med HDInsight, se følgende:

    * **Linux, Unix eller OS X-klienter**: se [Brug SSH med Linux-baserede Hadoop på HDInsight fra Linux, OS X eller Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Windows-klienter**: se [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="create-the-project"></a>Oprette projektet

1. Fra kommandolinjen i dit udviklingsmiljø, skifte til den placering, hvor du vil oprette i projektet, for eksempel `cd code/hdinsight`.

2. Bruge kommandoen __mvn__ , som er installeret med Maven, til at generere stilladser for projektet.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Dette opretter en ny mappe i den aktuelle mappe med det navn, der er angivet af parameteren __artifactID__ (**hbaseapp** i dette eksempel.) Denne mappe kan indeholde følgende elementer:

    * __pom.XML__: feltet Project-objektmodellen ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) indeholder oplysninger og konfiguration af oplysninger, der bruges til at opbygge projektet.

    * __src__: den mappe, hvor mappen __hoved/java/com/microsoft/eksempler__ , hvor du vil redigere programmet.

3. Slet filen __src/test/java/com/microsoft/examples/apptest.java__ , fordi den ikke bruges i dette eksempel.

##<a name="update-the-project-object-model"></a>Opdatere Project-objektmodellen

1. Redigere filen __pom.xml__ og tilføje følgende kode i den `<dependencies>` afsnit:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Det fortæller Maven, projektet kræver __hbase-klient__ version __1.1.2__. Dette der, hentes fra standard Maven lager under kompilering. Du kan bruge [Maven centralt lager søgning](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) mere for at vide om denne afhængighed.

    > [AZURE.IMPORTANT] Versionsnummeret skal stemme overens med versionen af HBase, der følger med din HDInsight klynge. Brug tabellen nedenfor til at finde det korrekte versionsnummeret.

  	| HDInsight klynge version | HBase version du vil bruge |
  	| ----- | ----- |
  	| 3.2 | 0.98.4-hadoop2 |
  	| 3.3 og 3.4 | 1.1.2 |

    Du kan finde flere oplysninger om HDInsight versioner og komponenter, kan du se [Hvad er de forskellige Hadoop-komponenter, der er tilgængelige med HDInsight](hdinsight-component-versioning.md).

2. Hvis du bruger en HDInsight 3.3 eller 3,4 klynge, skal du også tilføje følgende for at den `<dependencies>` afsnit:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Der indlæses København core-komponenter, der er nødvendige med Hbase version 1.1.x.

2. Tilføj følgende kode til filen __pom.xml__ . Det skal være i den `<project>...</project>` mærker i filen, for eksempel mellem `</dependencies>` og `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
            <resource>
              <directory>${basedir}/conf</directory>
              <filtering>false</filtering>
              <includes>
                <include>hbase-site.xml</include>
              </includes>
            </resource>
          </resources>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
                        <version>3.3</version>
              <configuration>
                <source>1.7</source>
                <target>1.7</target>
              </configuration>
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
          </plugins>
        </build>

    Dette konfigurerer en ressource (__conf/hbase-site.xml__,), der indeholder konfigurationsoplysninger for HBase.

    > [AZURE.NOTE] Du kan også angive konfigurationsværdier via kode. Få vist kommentarer i eksemplet __CreateTable__ , der følger efter til, hvordan du gør dette.

    Dette konfigurerer også [Maven compileren plug-in](http://maven.apache.org/plugins/maven-compiler-plugin/) og [Maven skygge plug-in'en](http://maven.apache.org/plugins/maven-shade-plugin/). Plug-in'en compileren bruges til at samle topologien. Plug-in'en skyggen bruges for at undgå licens dubletter i pakken glas, der er oprettet ved Maven. Denne funktion bruges årsagen er, at de dublerede licensfiler medfører en fejl på kørselstidspunktet på HDInsight klynge. Brug af maven-skygge-plug-in med den `ApacheLicenseResourceTransformer` implementering forhindrer denne fejl.

    Maven-skygge-plug-in'et giver også en uber glas (eller fat glas), der indeholder alle afhængigheder, der kræves af programmet.

3. Gem filen __pom.xml__ .

4. Oprette en ny mappe med navnet __conf__ i mappen __hbaseapp__ . Det bruges til at holde konfigurationsoplysninger til at oprette forbindelse til HBase.

5. Brug følgende kommando til at kopiere HBase konfiguration fra HDInsight serveren til mappen __telefonmøde med manuelt opkald__ . Erstatte **brugernavn** på navnet på dit SSH logon. Erstat **CLUSTERNAME** med dit HDInsight klyngenavn:

        scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

    > [AZURE.NOTE] Hvis du brugte en adgangskode for kontoen SSH, bliver du bedt om at angive adgangskoden. Hvis du har brugt en SSH nøgle med kontoen, du skal bruge den `-i` parameter for at angive stien til filen nøgle. I følgende eksempel indlæses privat nøgle fra `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##<a name="create-the-application"></a>Oprette programmet

1. Gå til mappen __hbaseapp/src/main/java/com/microsoft/eksempler__ og Omdøb filen app.java til __CreateTable.java__.

2. Åbn filen __CreateTable.java__ og erstatte de eksisterende indholdsfortegnelse med følgende:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Dette er den klasse, __CreateTable__ , som opretter en tabel med navnet __personer__ og udfylde den med nogle foruddefinerede brugere.

3. Gem filen __CreateTable.java__ .

4. Oprette en ny fil med navnet __SearchByEmail.java__i mappen __hbaseapp/src/main/java/com/microsoft/eksempler__ . Du kan bruge følgende som indholdet af denne fil:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    Klassen __SearchByEmail__ kan bruges til forespørgsel til rækker på mailadressen. Fordi den bruger filteret regulære udtryk, kan du angive enten en streng eller et regulære udtryk, når du bruger klassen.

5. Gem filen __SearchByEmail.java__ .

6. Oprette en ny fil med navnet __DeleteTable.java__i mappen __hbaseapp/src/hoved/hava/com/microsoft/eksempler__ . Du kan bruge følgende som indholdet af denne fil:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Denne klasse er for at rydde op i dette eksempel ved at deaktivere og slippe den tabel, der er oprettet af klassen __CreateTable__ .

7. Gem filen __DeleteTable.java__ .

##<a name="build-and-package-the-application"></a>Opbygge og pakke programmet

2. Brug følgende kommando til at opbygge en glas-fil, der indeholder programmet fra mappen __hbaseapp__ :

        mvn clean package

    Dette rydder eventuelle tidligere build elementer, henter afhængigheder, der ikke har allerede er installeret, derefter opbygger og pakker programmet.

3. Når kommandoen er udført, indeholder ____ hbaseapp/destinationsmappen en fil med navnet __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] __hbaseapp-1.0-SNAPSHOT.jar__ filen er en uber glas (også kaldet en fat glas), som indeholder alle de afhængigheder, der er nødvendige for at køre programmet.

##<a name="upload-the-jar-file-and-run-jobs"></a>Overføre filen glas og køre job

1. Brug følgende for at overføre glas til HDInsight klynge. Erstatte **brugernavn** på navnet på dit SSH logon. Erstat **CLUSTERNAME** med dit HDInsight klyngenavn:

        scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Dette vil overføre filen til mappen privat for brugerkontoen SSH.

    > [AZURE.NOTE] Hvis du brugte en adgangskode for kontoen SSH, bliver du bedt om at angive adgangskoden. Hvis du har brugt en SSH nøgle med kontoen, du skal bruge den `-i` parameter for at angive stien til filen nøgle. I følgende eksempel indlæses privat nøgle fra `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. Brug SSH til at oprette forbindelse til HDInsight klyngen. Erstatte **brugernavn** på navnet på dit SSH logon. Erstat **CLUSTERNAME** med dit HDInsight klyngenavn:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Hvis du brugte en adgangskode for kontoen SSH, bliver du bedt om at angive adgangskoden. Hvis du har brugt en SSH nøgle med kontoen, du skal bruge den `-i` parameter for at angive stien til filen nøgle. I følgende eksempel indlæses privat nøgle fra `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Når forbindelse, kan du bruge følgende til at oprette en ny HBase tabel ved hjælp af programmet Java:

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

    Dette vil oprette en ny HBase tabel med navnet __personer__og udfylde den med data.

4. Derefter skal du bruge følgende til at søge efter mailadresser, der er gemt i tabellen:

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

    Du skal modtage følgende resultater:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

##<a name="delete-the-table"></a>Slet tabellen

Når du er færdig med eksemplet, skal du bruge følgende kommando fra Azure PowerShell-session til at slette tabellen __personer__ i dette eksempel bruges:

    hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable


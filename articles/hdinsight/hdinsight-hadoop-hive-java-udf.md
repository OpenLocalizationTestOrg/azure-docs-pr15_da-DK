<properties
pageTitle="Bruge en Java brugerdefineret funktion (UDF) med Hive i HDInsight | Microsoft Azure"
description="Lær, hvordan du opretter og bruger en Java brugerdefineret funktion (UDF) fra Hive i HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/27/2016"
ms.author="larryfr"/>

#<a name="use-a-java-udf-with-hive-in-hdinsight"></a>Bruge en Java UDF med Hive i HDInsight

Hive er velegnet til at arbejde med data i HDInsight, men nogle gange kan du skal bruge et mere generelt formål sprog. Hive gør det muligt at oprette brugerdefinerede funktioner (UDF) ved hjælp af en række programmeringssprog. I dette dokument lærer du, hvordan du bruger en Java UDF fra Hive.

## <a name="requirements"></a>Krav

* Et Azure-abonnement

* En HDInsight klynge (Windows eller Linux-baseret)

    > [AZURE.NOTE] De fleste trinnene i dette dokument skal arbejde på begge typer klynge; de trin, der bruges til at overføre kompileret udf-filen til klyngen og køre den er dog specifikt for Linux-baserede klynger. Der findes links til oplysninger, der kan bruges med Windows-baserede klynger.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 eller nyere (eller en tilsvarende, såsom OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* En teksteditor eller Java IDE

    > [AZURE.IMPORTANT] Hvis du bruger en Linux-baserede HDInsight-server, men opretter Python filerne på en Windows-klient, skal du bruge en editor bruger, der LF som en linje slut. Hvis du ikke er sikker på, om din editor bruger LF eller CRLF, kan du se afsnittet [fejlfinding](#troubleshooting) for trinnene i fjerne tegnet CR ved hjælp af funktioner på HDInsight klynge.

## <a name="create-an-example-udf"></a>Oprette et eksempel UDF

1. Bruge følgende for at oprette et nyt Maven projekt fra en kommandolinje:

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    > [AZURE.NOTE] Hvis du bruger PowerShell, skal du sætte anførselstegn omkring parametrene. For eksempel `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Dette vil oprette en ny mappe med navnet __exampleudf__, som skal indeholde Maven projektet.

2. Når projektet er blevet oprettet, kan du slette mappen __exampleudf/src/test__ , der er oprettet som en del af projektet. det bruges ikke i dette eksempel.

3. Åbn __exampleudf/pom.xml__, og erstatte den nuværende `<dependencies>` post med følgende:

        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

    Disse poster angiver versionen af Hadoop og Hive inkluderet med HDInsight 3.3 og 3,4 klynger. Du kan finde oplysninger om versionerne af Hadoop og Hive forsynet med HDInsight fra [HDInsight komponent versionsstyring](hdinsight-component-versioning.md) dokumentet.

    Tilføje en `<build>` afsnit før den `</project>` linje i slutningen af filen. Dette afsnit skal indeholde følgende:

        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
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
            </plugins>
        </build>
    
    Disse poster definere, hvordan du opretter projektet. Nærmere betegnet kan version af Java, der bruger projektet, og hvordan du opretter en uberjar til installation til klyngen.

    Gem filen, når der er foretaget ændringerne.

4. Omdøb __exampleudf/src/main/java/com/microsoft/examples/App.java__ til __ExampleUDF.java__, og Åbn derefter filen i din editor.

5. Erstat indholdet af filen __ExampleUDF.java__ med følgende, derefter gemme filen.

        package com.microsoft.examples;

        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;

        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }

    Dette implementerer et UDF, der accepterer en strengværdi, og returnerer en små version af strengen.

## <a name="build-and-install-the-udf"></a>Oprette og installere UDF

1. Brug følgende kommando til at samle og pakke udf-filen:

        mvn compile package

    Dette vil opbygge og derefter pakke udf-filen til __exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar__.

2. Brug den `scp` kommando for at kopiere filen til HDInsight klyngen.

        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight

    Erstat __myuser__ med brugerkontoen SSH for din klynge. Erstat __mycluster__ med klyngenavnet. Hvis du brugte en adgangskode til at sikre kontoen SSH, bliver du bedt om at indtaste adgangskoden. Hvis du har brugt et certifikat, du skal bruge den `-i` parameter for at angive filen privat nøgle.

3. Oprette forbindelse til den klynge ved hjælp af SSH. 

        ssh myuser@mycluster-ssh.azurehdinsight.net

    Du kan finde flere oplysninger om brug af SSH med HDInsight, følgende dokumenter.

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

4. Kopiere filen glas til HDInsight lagerplads fra session SSH.

        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Brug UDF fra Hive

1. Brug følgende fremgangsmåde til at starte Beeline klienten fra SSH session.

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Denne kommando antages det, du brugte standard __for__ til logonkonto for din klynge.

2. Når du kommer til den `jdbc:hive2://localhost:10001/>` Spørg, skal du angive følgende for at føje udf-filen til Hive og vise det som en funktion.

        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';

3. Brug udf-filen til at konvertere værdier, der hentes fra en tabel til små bogstaver strenge.

        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;

    Dette vil Vælg enhed platformen (Android, Windows, iOS osv.) i tabellen, konvertere strengen til små bogstaver og derefter få dem vist. Output vises som følger.

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Næste trin

Andre måder at arbejde med Hive finde [Brug Hive med HDInsight](hdinsight-use-hive.md).

Yderligere oplysninger om Hive User-Defined funktioner, se [Hive operatorer og brugerdefinerede funktioner](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) afsnittet af Hive wikien på apache.org.

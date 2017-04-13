<properties
pageTitle="Opbygge et HBase program ved hjælp af Maven og anvende til Windows-baseret HDInsight | Microsoft Azure"
description="Lær, hvordan du bruger Apache Maven til at opbygge et Java-baserede Apache HBase til computeren og derefter installere det på en Windows-baseret Azure HDInsight klynge."
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
ms.date="10/03/2016"
ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Bruge Maven til at oprette Java-programmer, der bruger HBase med Windows-baseret HDInsight (Hadoop)

Få mere at vide, hvordan du opretter og opbygge et [Apache HBase](http://hbase.apache.org/) program i Java ved hjælp af Apache Maven. Brug derefter programmet med Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) er en software projektstyring og forståelse værktøj, der gør det muligt at opbygge software, dokumentation og rapporter til Java projekter. I denne artikel lærer du at bruge det til at oprette en grundlæggende Java-program, der, der opretter forespørgsler, og sletter en HBase tabel på en Azure HDInsight klynge.

> [AZURE.NOTE] Trinnene i dette dokument forudsætter, at du bruger en Windows-baseret HDInsight klynge. Oplysninger om brug af en Linux-baserede HDInsight klynge, se [Bruge Maven til at oprette Java-programmer, der bruger HBase med Linux-baserede HDInsight](hdinsight-hbase-build-java-maven-linux.md)

##<a name="requirements"></a>Krav

* [Java platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 eller nyere

* [Maven](http://maven.apache.org/)


* [En Windows-baseret HDInsight klynge med HBase](hdinsight-hbase-tutorial-get-started.md#create-hbase-cluster)


    > [AZURE.NOTE] Trinnene i dette dokument er blevet testet med HDInsight klynge versioner 3.2 og 3.3. Fremgår eksempler standardværdierne for en HDInsight 3.3 klynge.

##<a name="create-the-project"></a>Oprette projektet

1. Fra kommandolinjen i dit udviklingsmiljø, skifte til den placering, hvor du vil oprette i projektet, for eksempel `cd code\hdinsight`.

2. Bruge kommandoen __mvn__ , som er installeret med Maven, til at generere stilladser for projektet.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Denne kommando opretter en mappe i den aktuelle placering med det navn, der er angivet af parameteren __artifactID__ (**hbaseapp** i dette eksempel.) Denne mappe indeholder følgende elementer:

    * __pom.XML__: feltet Project-objektmodellen ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) indeholder oplysninger og konfiguration af oplysninger, der bruges til at opbygge projektet.

    * __src__: den mappe, hvor mappen __main\java\com\microsoft\examples__ , hvor du vil redigere programmet.

3. Slet filen __src\test\java\com\microsoft\examples\apptest.java__ , fordi den ikke bruges i dette eksempel.

##<a name="update-the-project-object-model"></a>Opdatere Project-objektmodellen

1. Rediger filen __pom.xml__ , og Tilføj følgende kode i den `<dependencies>` afsnit:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    I dette afsnit beskrives Maven, projektet kræver __hbase-klient__ version __1.1.2__. Under kompilering hentes denne afhængighed fra standard Maven lager. Du kan bruge [Maven centralt lager søgning](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) mere for at vide om denne afhængighed.

    > [AZURE.IMPORTANT] Versionsnummeret skal stemme overens med versionen af HBase, der følger med din HDInsight klynge. Brug tabellen nedenfor til at finde det korrekte versionsnummeret.

  	| HDInsight klynge version | HBase version du vil bruge |
  	| ----- | ----- |
  	| 3.2 | 0.98.4-hadoop2 |
  	| 3.3 | 1.1.2 |

    Du kan finde flere oplysninger om HDInsight versioner og komponenter, kan du se [Hvad er de forskellige Hadoop-komponenter, der er tilgængelige med HDInsight](hdinsight-component-versioning.md).

2. Hvis du bruger en HDInsight 3.3 klynge, skal du også tilføje følgende for at den `<dependencies>` afsnit:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Denne afhængighed indlæser København core-komponenter, der anvendes af Hbase version 1.1.x.

2. Tilføj følgende kode til filen __pom.xml__ . Dette afsnit skal være placeret i den `<project>...</project>` mærker i filen, for eksempel mellem `</dependencies>` og `</project>`.

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

    Den `<resources>` sektion konfigurerer en ressource (__conf\hbase site.xml__), der indeholder konfigurationsoplysninger for HBase.

    > [AZURE.NOTE] Du kan også angive konfigurationsværdier via kode. Få vist kommentarer i eksemplet __CreateTable__ , der følger efter til, hvordan du gør dette.

    Dette `<plugins>` sektion konfigurerer [Maven compileren plug-in](http://maven.apache.org/plugins/maven-compiler-plugin/) og [Maven skygge plug-in'en](http://maven.apache.org/plugins/maven-shade-plugin/). Plug-in'en compileren bruges til at samle topologien. Plug-in'en skyggen bruges for at undgå licens dubletter i pakken glas, der er oprettet ved Maven. Denne funktion bruges årsagen er, at de dublerede licensfiler medfører en fejl på kørselstidspunktet på HDInsight klynge. Brug af maven-skygge-plug-in med den `ApacheLicenseResourceTransformer` implementering forhindrer denne fejl.

    Maven-skygge-plug-in'et giver også en uber glas (eller fat glas), der indeholder alle afhængigheder, der kræves af programmet.

3. Gem filen __pom.xml__ .

4. Oprette en ny mappe med navnet __conf__ i mappen __hbaseapp__ . Oprette en fil med navnet __hbase site.xml__i mappen __conf__ . Du kan bruge følgende som indholdet af filen:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
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
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Denne fil bruges til at indlæse HBase konfigurationen for en HDInsight klynge.

    > [AZURE.NOTE] Dette er en minimale hbase-site.xml-fil, og den indeholder som minimum indstillingerne for HDInsight-klyngen.

3. Gem filen __hbase site.xml__ .

##<a name="create-the-application"></a>Oprette programmet

1. Gå til mappen __hbaseapp\src\main\java\com\microsoft\examples__ , og Omdøb filen app.java til __CreateTable.java__.

2. Åbn filen __CreateTable.java__ og erstatte de eksisterende indholdsfortegnelse med følgende kode:

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
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

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

4. Oprette en ny fil med navnet __SearchByEmail.java__i mappen __hbaseapp\src\main\java\com\microsoft\examples__ . Du kan bruge følgende kode som indholdet af denne fil:

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

    Klassen __SearchByEmail__ kan bruges til forespørgsel til rækker på mailadressen. Fordi den bruger filteret regulære udtryk, kan du angive enten en streng eller et søgemønster, når du bruger klassen.

5. Gem filen __SearchByEmail.java__ .

6. Oprette en ny fil med navnet __DeleteTable.java__i mappen __hbaseapp\src\main\hava\com\microsoft\examples__ . Du kan bruge følgende kode som indholdet af denne fil:

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

1. Åbn en kommandoprompt og skifte til mappen __hbaseapp__ .

2. Brug følgende kommando til at opbygge en glas-fil, der indeholder programmet:

        mvn clean package

    Dette rydder eventuelle tidligere build elementer, henter afhængigheder, der ikke har allerede er installeret, derefter opbygger og pakker programmet.

3. Når kommandoen er udført, indeholder mappen __hbaseapp\target__ en fil med navnet __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] __hbaseapp-1.0-SNAPSHOT.jar__ filen er en uber glas (også kaldet en fat glas), som indeholder alle de afhængigheder, der er nødvendige for at køre programmet.

##<a name="upload-the-jar-file-and-start-a-job"></a>Overføre filen glas og starte et job

Der findes mange måder til at overføre en fil til din HDInsight klynge, som beskrevet i [overføre data til Hadoop-job i HDInsight](hdinsight-upload-data.md). I følgende trin bruges Azure PowerShell.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


1. Oprette en ny fil med navnet __hbase runner.psm1__efter installation og konfiguration af Azure PowerShell. Du kan bruge følgende som indholdet af denne fil:

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasbs:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Denne fil indeholder to moduler:

    * __Tilføj HDInsightFile__ - bruges til at overføre filer til HDInsight

    * __Start-HBaseExample__ - bruges til at køre de oprettede tidligere klasser

2. Gem filen __hbase runner.psm1__ .

3. Åbn et nyt Azure PowerShell-vindue, skifte til mappen __hbaseapp__ , og derefter køre følgende kommando.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Ændre stien til placeringen i filen __hbase runner.psm1__ oprettede tidligere. Dette registrerer modulet Azure PowerShell for denne session.

2. Brug følgende kommando for at overføre __hbaseapp-1.0-SNAPSHOT.jar__ til din HDInsight klynge.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Erstat __hdinsightclustername__ med navnet på din HDInsight klynge. Kommandoen overfører __hbaseapp-1.0-SNAPSHOT.jar__ til __eksempel/krukker__ placering i den primære lagerplads for dit HDInsight klynge.

3. Når filerne er blevet overført, kan du bruge følgende kode til at oprette en tabel med __hbaseapp__:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Erstat __hdinsightclustername__ med navnet på din HDInsight klynge.

    Denne kommando opretter en ny tabel med navnet __personer__ i din HDInsight klynge. Denne kommando viser ikke noget output i vinduet console.

2. Hvis du vil søge efter poster i tabellen, skal du bruge følgende kommando:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Erstat __hdinsightclustername__ med navnet på din HDInsight klynge.

    Denne kommando bruger klassen **SearchByEmail** til at søge efter en række, hvor __contactinformation__ kolonne familie og kolonnen __mail__ indeholder streng __contoso.com__. Du skal modtage følgende resultater:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Brug af __fabrikam.com__ til den `-emailRegex` værdi, returneres de brugere, der har __fabrikam.com__ i feltet mail. Da denne søgning er implementeret ved hjælp af et almindelige udtryk-baserede filter, du kan også angive almindelige udtryk, såsom __^ r__, hvilke returnerer poster, hvor mailen begynder med bogstavet "r".

##<a name="delete-the-table"></a>Slet tabellen

Når du er færdig med eksemplet, skal du bruge følgende kommando fra Azure PowerShell-session til at slette tabellen __personer__ i dette eksempel bruges:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Erstat __hdinsightclustername__ med navnet på din HDInsight klynge.

##<a name="troubleshooting"></a>Fejlfinding i forbindelse med

###<a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Ingen resultater eller uventede resultater, når du bruger Start HBaseExample

Brug den `-showErr` parameter for at få vist standardafvigelsen (STDERR), der oprettes under kørsel af jobbet.

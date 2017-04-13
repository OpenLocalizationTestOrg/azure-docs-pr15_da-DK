<properties
    pageTitle="Sekventielt data med Microsoft Avro Library | Microsoft Azure"
    description="Få mere at vide, hvordan Azure HDInsight anvender Avro til at sekventielt stor data."
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
    ms.date="09/14/2016"
    ms.author="jgao"/>


# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Sekventielt data i Hadoop med Microsoft Avro Library

Dette emne viser, hvordan du bruger <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> ordne sekventielt objekter og andre datastrukturer til streams for at bevare dem til hukommelse, en database eller en fil, og Sådan deserialisere dem for at gendanne de oprindelige objekter.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

##<a name="apache-avro"></a>Apache Avro
<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> implementerer serialisering Apache Avro datasystem for Microsoft.NET-miljø. Apache Avro indeholder et kompakt binære data interchange format til serialisering. <a href="http://www.json.org" target="_blank">JSON</a> bruges til at definere et sprog agnostic skema, der dækker sprog interoperabilitet. Data serialiseres i ét sprog kan læses i en anden. Aktuelt understøttes C, C++, C#, Java, PHP, Python og fonetisk. Detaljerede oplysninger om formatet kan findes i <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro specifikation</a>. Bemærk, at den aktuelle version af Microsoft Avro Library ikke understøtter fjernprocedurekald opkald (RPC) del af denne specifikation.

Den fortløbende repræsentation af et objekt i Avro systemet består af to dele: skemaet og faktiske værdi. Avro skemaet beskrives sprog uafhængige datamodellen fortløbende data med JSON. Det er Præsenter side om side med en binær repræsentation af data. Har du skemaet adskilt fra den binære repræsentation tillader, at de enkelte objekter skal skrives med ikke-værdien omkostninger, gør serialisering hurtigt og repræsentationen small.

##<a name="the-hadoop-scenario"></a>Hadoop-scenario
Serialiseringsformatet Apache Avro der anvendes på Azure HDInsight og andre Apache Hadoop-miljøer. Avro er en praktisk metode til at repræsentere komplekse datastrukturer inden for et Hadoop MapReduce job. Formatet af Avro filer (Avro objekt objektbeholder fil) er udviklet til at understøtte distribuerede MapReduce programming modellen. Funktionen nøgle, der gør det muligt for fordelingen er, at filerne "kan opdeles" i mening, kan søge efter en hvilken som helst sted i en fil, og start læseruden fra en bestemt tekstblok.

##<a name="serialization-in-avro-library"></a>Serialisering i Avro bibliotek
Biblioteket .NET for Avro understøtter serialisering objekter på to måder:

- **refleksion** - feltet JSON skema for typerne bygger automatisk fra dataene kontrakt attributter af .NET-datatyper skal serialiseres.
- **generisk post** - A JSON skema eksplicit er angivet i en post, der er repræsenteret af klassen [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) , når ingen .NET-datatyper er til stede til at beskrive skemaet for dataene, der serialiseres.

Når dataskemaet kendes både writer og læser af strømmen, kan dataene, der sendes uden skemaet. Når der anvendes en Avro objekt objektbeholder-fil, gemmes i skemaet i tilfælde i filen. Du kan angive andre parametre, som bruges til datakomprimering codec. Disse scenarier er beskrevet mere detaljeret og illustreret i kode eksemplerne nedenfor.


## <a name="install-avro-library"></a>Installere Avro bibliotek

Følgende er påkrævet før du installerer biblioteket:

- <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 eller nyere)

Bemærk, at Newtonsoft.Json.dll afhængigheden hentes automatisk med installationen af Microsoft Avro Library. Fremgangsmåden for dette er angivet i følgende afsnit.


Microsoft Avro Library distribueres som en NuGet-pakke, som kan installeres fra Visual Studio via følgende fremgangsmåde:

1. Vælg fanen **projekt** -> **Administrer... NuGet pakker**
2. Søge efter "Microsoft.Hadoop.Avro" i feltet **Søg Online** .
3. Klik på knappen **Installer** ud for **Microsoft Azure HDInsight Avro bibliotek**.

Bemærk, at Newtonsoft.Json.dll (> = 6.0.4) afhængighed hentes også automatisk med Microsoft Avro Library.

Du overveje at besøge <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library startside</a> for at læse de aktuelle produktbemærkninger.


Microsoft Avro Library koden er aktiveret i <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library startsiden</a>.

##<a name="compile-schemas-using-avro-library"></a>Samle ved hjælp af skemaer ved hjælp af Avro bibliotek

Microsoft Avro Library indeholder en kode generering af funktion, som giver mulighed for oprettelse af C# typer automatisk på baggrund af den tidligere defineret JSON-skema. Generering af kode værktøjet distribueres ikke som en binær eksekverbare, men kan konfigureres nemt via følgende fremgangsmåde:

1. Download .zip-filen med den seneste version af HDInsight SDK kildekode fra <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK For Hadoop</a>. (Klik på ikonet **Download** , ikke **er hentet** fanen).

2. Udtrække HDInsight SDK til en mappe på computeren med .NET Framework 4 installeret og forbindelse til internettet til at hente det er nødvendigt afhængighed NuGet pakker. Nedenfor antager vi, at kildekoden udtrækkes til C:\SDK.

3. Gå til mappen C:\SDK\src\Microsoft.Hadoop.Avro.Tools og køre build.bat. (Filen ringer MSBuild fra 32-bit fordelingen af .NET Framework. Hvis du vil gerne bruge 64-bit-versionen, redigere build.bat, følge kommentarerne i filen.) Sørg for, at Opret er gået igennem. (På nogle systemer MSBuild kan give advarsler. Disse advarsler påvirker ikke værktøjet så længe der ikke er nogen buildfejl.)

4. Værktøjet kompileret er placeret i C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.


For at få kendskab til den syntaks skal du udføre følgende kommando fra den mappe, hvor værktøjet kode generering af er placeret:`Microsoft.Hadoop.Avro.Tools help /c:codegen`

Du kan generere C# klasser fra eksempel JSON skemafilen i koden for at teste værktøjet. Du kan udføre følgende kommando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Dette er meningen, for at producere to C#-filer i den aktuelle mappe: SensorData.cs og Location.cs.

Se filen GenerationVerification.feature findes i C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc for at forstå logik, der bruger funktionen kode generering af under konverteringen JSON skemaet til C#-typer.

Vær opmærksom på, at navneområder udtrækkes fra JSON skemaet, ved hjælp af den logik, der er beskrevet i den fil, der er nævnt i det forrige afsnit. Navneområder, der er hentet fra skemaet tilsidesætter uanset hvad der følger med parameteren /n i kommandolinjen utility. Hvis du vil tilsidesætte de navneområder, der er indeholdt i skemaet, skal du bruge parameteren /nf. For eksempel for at ændre alle navneområder fra SampleJSONSchema.avsc til my.own.nspace skal udføre følgende kommando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="samples"></a>Eksempler
Seks eksempler, der er angivet i dette emne viser forskellige scenarier, der understøttes af Microsoft Avro Library. Microsoft Avro Library er udviklet til at fungere sammen med en hvilken som helst stream. I disse eksempler, der arbejdes med data via hukommelse streams i stedet for fil-streams eller databaser for enkle og konsistens. Den fremgangsmåde, der er taget i et produktionsmiljø, afhænger af de nøjagtige scenarie krav, datakilde og lydstyrken, ydeevne begrænsninger og andre faktorer.

De første to eksempler viser, hvordan sekventielt og deserialisere data til hukommelse streambuffere ved hjælp af refleksion og generisk poster. Skema i begge tilfælde antages deles mellem læsere og forfattere out-of-band.

De tredje og fjerde eksempler viser, hvordan sekventielt og deserialisere data ved hjælp af Avro objekt objektbeholder filer. Når data er gemt i en Avro objektbeholder-fil, gemmes skemaet altid med den, fordi skemaet skal deles til fjernelse af serienummeret.

Eksemplet, der indeholder de første fire eksempler kan hentes fra <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">Azure kodeeksempler</a> -webstedet.

Femte eksempel viser hvordan du hvordan du bruger et brugerdefineret komprimering codec til Avro objekt objektbeholder filer. Et eksempel, der indeholder koden i dette eksempel kan hentes fra <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">Azure kodeeksempler</a> -webstedet.

Den sjette eksempel viser, hvordan du bruger Avro serialisering til at overføre data til Azure Blob-lager og derefter analysere dem ved hjælp af Hive med en HDInsight (Hadoop) klynge. Det kan hentes fra <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">Azure kodeeksempler</a> -webstedet.

Her er links til seks stikprøver, der er beskrevet i emnet:

 * <a href="#Scenario1">**Serialisering med refleksion**</a> - feltet JSON skema for typer skal serialiseres bygger automatisk fra dataene kontrakt attributter.
 * <a href="#Scenario2">**Serialisering med generisk post**</a> - feltet JSON skema eksplicit er angivet i en post, når der er ingen .NET type er tilgængelig for refleksion.
 * <a href="#Scenario3">**Serialisering ved hjælp af objekt objektbeholder filer med refleksion**</a> - feltet JSON skema er automatisk oprettet og deles sammen med de fortløbende data via en Avro objekt objektbeholder-fil.
 * <a href="#Scenario4">**Serialisering ved hjælp af objektet objektbeholder filer med generisk post**</a> - feltet JSON skema eksplicit er angivet før af serialisering og delt sammen med dataene via en Avro objekt objektbeholder-fil.
 * <a href="#Scenario5">**Serialisering ved hjælp af objektet objektbeholder filer med et brugerdefineret komprimering codec**</a> - eksemplet viser, hvordan du opretter en Avro objekt objektbeholder-fil med en tilpasset .NET implementering af Deflate data komprimering codec.
 * <a href="#Scenario6">**Ved hjælp af Avro til at overføre data til tjenesten Microsoft Azure HDInsight**</a> - eksemplet illustrerer, hvordan Avro serialisering arbejde sammen med tjenesten HDInsight. Et aktivt Azure abonnement og adgang til en Azure HDInsight klynge kræves for at køre dette eksempel.

###<a name="Scenario1"></a>Eksempel 1: Serialisering med refleksion

JSON skemaet for typerne kan automatisk konfigureres i Microsoft Avro Library via refleksion fra dataene kontrakt attributter C#-objekter skal serialiseres. Microsoft Avro Library opretter en [**IAvroSeralizer<T> **](http://msdn.microsoft.com/library/dn627341.aspx) til at identificere felterne, der serialiseres.

Objekter (en **SensorData** klasse med en medlem **placering** struktur) er serialiseres til en hukommelsesstream i dette eksempel, og denne strøm er også deserialiseres. Resultatet er derefter sammenlignet med den første forekomst for at bekræfte, at **SensorData** objektet gendannet, er identisk med oprindeligt.

Skema i dette eksempel antages deles læsere og forfattere, så Avro objekt objektbeholder formatet ikke er påkrævet. Du kan finde et eksempel på Sådan sekventielt og deserialisere data til hukommelsesbuffere ved hjælp af refleksion med objekt objektbeholder format, når skemaet skal deles med dataene, <a href="#Scenario3">serialisering ved hjælp af objekt objektbeholder filer med refleksion</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-2-serialization-with-a-generic-record"></a>Eksempel 2: Serialisering med en generisk post

Et JSON-skema kan kun eksplicit angives i en generisk post, når refleksion ikke kan bruges, fordi dataene, der ikke kan gengives via .NET klasser til en datakontrakt. Denne metode er generelt langsommere end ved hjælp af refleksion. I så fald kendes skemaet for dataene, der er måske også dynamisk, det vil sige ikke under kompilering. Data, der repræsenteres som kommaseparerede værdier (CSV) filer, hvis skema er ukendt, indtil den omdannes til formatet Avro på kørselstidspunktet er et eksempel på denne type dynamiske scenario.

I dette eksempel vises, hvordan du kan oprette og bruge en [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) udtrykkeligt at angive et JSON-skema, hvordan du kan udfylde den med dataene, og derefter Sådan sekventielt og deserialisere den. Resultatet er derefter sammenlignet med den første forekomst for at bekræfte, at den post, der er gendannet, er identisk med oprindeligt.

Skema i dette eksempel antages deles mellem læsere og forfattere, så Avro objekt objektbeholder formatet ikke er påkrævet. Du kan finde et eksempel på Sådan sekventielt og deserialisere data til hukommelsesbuffere ved hjælp af en generisk post med objekt objektbeholder format, når skemaet skal bruges sammen med fortløbende dataene, eksemplet <a href="#Scenario4">serialisering ved hjælp af objektet objektbeholder filer med generisk post</a> .


    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Eksempel 3: Serialisering ved hjælp af objekt objektbeholder filer og serialisering med refleksion

I dette eksempel er lig med dette scenario i <a href="#Scenario1">første eksempel</a>, hvor skemaet implicit er angivet med refleksion. Forskellen er det her, antages skemaet ikke at være kendt til læseren, deserializes den. **SensorData** objekter skal serialiseres og deres implicit angivne skema er gemt i en Avro objekt objektbeholder-fil, der er repræsenteret af klassen [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) .

Dataene er serienummereret i dette eksempel med [**SequentialWriter<SensorData> **](http://msdn.microsoft.com/library/dn627340.aspx) og deserialiserede med [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). Resultatet derefter sammenlignes med de indledende forekomster skal sikre identitet.

Dataene i objektet objektbeholder filen komprimeres via standard [**Deflate**] [ deflate-100] komprimering codec fra .NET Framework 4. Se <a href="#Scenario5">femte eksempel</a> i dette emne for at se, hvordan du bruger en nyere og mere overordnede versioner af [**Deflate**] [ deflate-110] komprimering codec tilgængelige i .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will be compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Eksempel 4: Serialisering ved hjælp af objekt objektbeholder filer og serialisering med generisk post

I dette eksempel er lig med dette scenario i det <a href="#Scenario2">andet eksempel</a>, hvor der eksplicit er angivet i skemaet med JSON. Forskellen er det her, antages skemaet ikke at være kendt til læseren, deserializes den.

Test datasættet indsamles i en liste over [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) objekter via en eksplicit defineret JSON-skema og derefter gemt i en objekt objektbeholder fil, der er repræsenteret af klassen [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) . Denne beholder fil opretter en writer, der bruges til sekventielt de data, ikke-komprimeret format, til en hukommelsesstream, der gemmes derefter til en fil. Parameteren [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) bruges til at oprette læseren angiver, at disse data ikke komprimeres.

Dataene er derefter læse fra filen og serialisere til en samling objekter. Denne samling sammenlignes med den første liste over Avro poster for at bekræfte, at de er identiske.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will not be compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




###<a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Eksempel 5: Serialisering ved hjælp af objekt objektbeholder filer med et brugerdefineret komprimering-codec

Femte eksempel viser hvordan du hvordan du bruger et brugerdefineret komprimering codec til Avro objekt objektbeholder filer. Et eksempel, der indeholder koden i dette eksempel kan hentes fra [Azure kodeeksempler](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111) -webstedet.

[Specifikation af Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) tillader brugen af en valgfri komprimering codec (ud over standarder for **Null-værdier** og **Deflate** ). I dette eksempel er ikke implementere en helt ny codec som Snappy (omtalt, som et understøttede valgfrit codec i [Avro specifikation](http://avro.apache.org/docs/current/spec.html#snappy)). Det viser, hvordan du bruger .NET Framework 4.5 implementeringen af [**Deflate**] [ deflate-110] codec, som giver en bedre komprimering algoritme baseret på biblioteket [zlib](http://zlib.net/) komprimering end .NET Framework 4 standardversionen.


    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It will catch the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it will rely on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

###<a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Eksempel 6: Brug af Avro til at overføre data til tjenesten Microsoft Azure HDInsight

Det sjette eksempel illustrerer nogle programming teknikker, der er relateret til arbejde med tjenesten Azure HDInsight. Et eksempel, der indeholder koden i dette eksempel kan hentes fra [Azure kodeeksempler](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3) -webstedet.

Eksemplet gør følgende:

* Opretter forbindelse til en eksisterende HDInsight tjenesten klynge.
* Serializes flere csv-filer og overfører resultatet til Azure Blob-lager. (CSV-filer er distribueret sammen med stikprøvernes og repræsenterer et uddrag AMEX aktie historiske data distribueret af [Infochimps](http://www.infochimps.com/) for perioden 1970-2010. Eksemplet læser data fra en CSV-fil, konverterer posterne til forekomster af det **aktie** klasse og serializes dem ved hjælp af refleksion. Aktiekurser typedefinition er oprettet ud fra et JSON skema via Microsoft Avro Library kode generering af utility.
* Opretter en ny ekstern tabel med navnet **aktier** i Hive og links det til dataene, der er overført i ovenstående trin.
* Udfører en forespørgsel ved hjælp af Hive over **aktier** tabel.

Desuden udfører stikprøvernes en oprydning procedure, før og efter udføre overordnede handlinger. Under oprydningen, fjernes alle de relaterede Azure Blob-data og mapper, og tabellen Hive udelades. Du kan også aktivere oprydning procedure fra kommandolinjen eksempel.

Stikprøvernes har følgende forudsætninger:

* Et aktivt Microsoft Azure-abonnement og dens abonnement-ID.
* Et management certifikat til abonnementet med den tilhørende private nøgle. Certifikatet, der skal installeres i den aktuelle bruger private lagerplads på den computer, der bruges til at køre eksemplet.
* En aktiv HDInsight klynge.
* En Azure-lager-konto, der er sammenkædet med HDInsight klynge fra den forrige påkrævede sammen med den tilsvarende primær eller sekundær hurtigtast.

Alle oplysninger fra forudsætningerne skal angives til eksempel konfigurationsfil, før eksemplet køres. Der er to mulige måder at gøre det:

* Redigere app.config-filen i eksempel rodmappe og derefter bygge stikprøvernes
* Først opbygge eksemplet, og rediger derefter AvroHDISample.exe.config i mappen build

I begge tilfælde skal alle redigeringer udføres i den **<appSettings>** sektionen Indstillinger. Følg kommentarer i filen.
Eksemplet køres fra kommandolinjen ved at udføre følgende kommando (hvor .zip-filen med stikprøvernes blev formodet for at udtrækkes til C:\AvroHDISample; Hvis Ellers anvender den relevante filsti):

    AvroHDISample run C:\AvroHDISample\Data

Hvis du vil rydde op i klyngen, skal du køre følgende kommando:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx

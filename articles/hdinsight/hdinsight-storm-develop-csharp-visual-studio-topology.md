<properties
   pageTitle="Apache Storm topologier med Visual Studio og C# | Microsoft Azure"
   description="Få mere at vide, hvordan du opretter Storm topologier i C# ved at oprette en simpel word Tæl topologi i Visual Studio ved hjælp af værktøjerne HDInsight til Visual Studio."
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
   ms.date="10/27/2016"
   ms.author="larryfr"/>

# <a name="develop-c-topologies-for-apache-storm-on-hdinsight-using-hadoop-tools-for-visual-studio"></a>Udvikle C# topologier for Apache Storm på HDInsight med Hadoop-værktøjer til Visual Studio

Få mere at vide, hvordan du opretter en C# Storm topologi ved hjælp af værktøjerne HDInsight til Visual Studio. Dette selvstudium fører gennem processen med at oprette et projekt med Storm i Visual Studio, tester den lokalt og installere den på en Apache Storm på HDInsight klynge.

Du kan også Lær at oprette hybrid topologier, der bruger C# og Java komponenter.

> [AZURE.IMPORTANT] Mens trinnene i dette dokument skal have et Windows udviklingsmiljø med Visual Studio, kan projektet kompileret sendes til en Linux eller Windows-baseret HDInsight klynge. Kun Linux-baserede klynger oprettet efter 28-10/2016 support SCP.NET topologier.
>
> Hvis du vil bruge en C#-topologi med en Linux-baserede klynge, skal du opdatere pakken Microsoft.SCP.Net.SDK NuGet bruges af dit projekt til version 0.10.0.6 eller nyere. Versionen af pakken skal også svare på overordnet version af Storm installeret på HDInsight. For eksempel Storm på HDInsight versioner 3.3 og 3.4 bruge Storm version 0.10.x, mens HDInsight 3.5 bruger Storm 1.0.x.
> 
> C# topologier på Linux-baserede klynger skal bruge .NET 4.5, og Brug sort/hvid til at køre på HDInsight klyngen. De fleste ting fungerer, men du skal kontrollere [Sort/hvide kompatibilitet](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentet til potentielle inkompatibilitet.

## <a name="prerequisites"></a>Forudsætninger

- En af følgende versioner af Visual Studio

    - Visual Studio 2012 med [opdatere 4](http://www.microsoft.com/download/details.aspx?id=39305)

    - Visual Studio 2013 med [opdatere 4](http://www.microsoft.com/download/details.aspx?id=44921) eller [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

    - Visual Studio 2015 eller [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

- Azure SDK 2.9.5 eller nyere

- HDInsight værktøjer til Visual Studio: se [Introduktion til brug af HDInsight Tools til Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) til at installere og konfigurere HDInsight værktøjerne til Visual Studio.

    > [AZURE.NOTE] HDInsight værktøjer til Visual Studio understøttes ikke i Visual Studio Express

-   Apache Storm på HDInsight klynge: se [Introduktion til Apache Storm på HDInsight](hdinsight-apache-storm-tutorial-get-started.md) til trin til at oprette en klynge.

## <a name="templates"></a>Skabeloner

HDInsight-værktøjer til Visual Studio giver følgende skabeloner::

| Projekttype | Viser |
| ------------ | ------------- |
| Storm program | Et tomt Storm topologi for projekt |
| Storm Azure SQL Writer eksempel | Sådan skrives til Azure SQL-Database |
| Storm DocumentDB læser eksempel | Hvordan du kan læse fra Azure DocumentDB |
| Storm DocumentDB Writer eksempel | Sådan skrives til Azure DocumentDB |
| Storm EventHub læser eksempel | Hvordan du kan læse fra Azure begivenhed Hubs |
| Storm EventHub Writer eksempel | Sådan skrives til Azure begivenhed hubber |
| Storm HBase læser eksempel | Hvordan du kan læse fra HBase på HDInsight klynger |
| Storm HBase Writer eksempel | Sådan skrives til HBase på HDInsight klynger |
| Storm Hybrid eksempel | Sådan bruger du en Java-komponent |
| Storm eksempel | En grundlæggende word Tæl topologi |

> [AZURE.NOTE] HBase læser og writer eksemplerne Brug HBase REST-API til at kommunikere med en HBase på HDInsight klynge, ikke HBase Java API.

I trin i dette dokument skal bruge du grundlæggende Storm programmet projekttype til at oprette en ny topologi.

## <a name="create-a-c-topology"></a>Oprette en C#-topologi

1. Hvis du ikke allerede har installeret den nyeste version af HDInsight værktøjerne til Visual Studio, kan du se [Introduktion til brug af HDInsight Tools til Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Åbne Visual Studio skal du vælge **filer** > **Ny**, og klik derefter **Project**.

3. Skærmbilledet **Nyt projekt** , og udvid **installerede** > **skabeloner**, og vælg **HDInsight**. Vælg **Storm program**på listen over skabeloner. Nederst på skærmen skal du angive **WordCount** som navnet på programmet.

    ![Billede af](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4. Når projektet er blevet oprettet, bør du have følgende filer:

    - **Program.CS**: Dette definerer topologi for projektet. Bemærk, at der oprettes en standard-topologi, der består af én tud og én bolt som standard.

    - **Spout.CS**: en eksempel-tud, udsender vilkårlige tal.

    - **Bolt.CS**: et eksempel bolt, der holder en optælling af tal, der udsendes af tud.

    Som en del af projektoprettelse der, hentes de nyeste [SCP.NET-pakker](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) fra NuGet.

    [AZURE.INCLUDE [scp.net version important](../../includes/hdinsight-storm-scpdotnet-version.md)]

I næste afsnit skal redigere du dette projekt i et grundlæggende WordCount-program.

### <a name="implement-the-spout"></a>Implementere tud

1. Åbn **Spout.cs**. Spouts bruges til at læse data i en topologi fra en ekstern kilde. De vigtigste komponenter til en tud er:

    - **NextTuple**: kaldet ved Storm, når tud har tilladelse til at udsende nye tupler.

    - **ACK** (kun transaktions topologi): håndterer godkendelser, som andre komponenter i topologien for tupler sendt fra denne tud er startet. Om, at en tupel kan tud vide, at det blev behandlet af efterfølgende komponenter.

    - **Mislykkes** (kun transaktions topologi): håndterer tupler, der fejl – behandling andre komponenter i topologien. Dette giver mulighed for at udlede en tupel igen, så den kan behandles igen.

2. Erstat indholdet af klassen **Spout** med følgende. Dette opretter en tud, tilfældigt udsender en sætning i topologien.

        private Context ctx;
        private Random r = new Random();
        string[] sentences = new string[] {
            "the cow jumped over the moon",
            "an apple a day keeps the doctor away",
            "four score and seven years ago",
            "snow white and the seven dwarfs",
            "i am at two with nature"
        };

        public Spout(Context ctx)
        {
            // Set the instance context
            this.ctx = ctx;

            Context.Logger.Info("Generator constructor called");

            // Declare Output schema
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // The schema for the default output stream is
            // a tuple that contains a string field
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        }

        // Get an instance of the spout
        public static Spout Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Spout(ctx);
        }

        public void NextTuple(Dictionary<string, Object> parms)
        {
            Context.Logger.Info("NextTuple enter");
            // The sentence to be emitted
            string sentence;

            // Get a random sentence
            sentence = sentences[r.Next(0, sentences.Length - 1)];
            Context.Logger.Info("Emit: {0}", sentence);
            // Emit it
            this.ctx.Emit(new Values(sentence));

            Context.Logger.Info("NextTuple exit");
        }

        public void Ack(long seqId, Dictionary<string, Object> parms)
        {
            // Only used for transactional topologies
        }

        public void Fail(long seqId, Dictionary<string, Object> parms)
        {
            // Only used for transactional topologies
        }
    
    Tage et øjeblik på at læse kommentarer at forstå, hvad betyder denne kode.

### <a name="implement-the-bolts"></a>Implementere boltene

1. Slet den eksisterende **Bolt.cs** -fil fra projektet.

2. Højreklik på projektet i **Solution Explorer**, og vælg **Tilføj** > **nyt element**. Vælg **Storm Bolt**, og angiv **Splitter.cs** som navnet på listen. Gentag dette for at oprette en anden bolt navngivne **Counter.cs**.

    - **Splitter.CS**: implementerer et bolt, der opdeler sætninger i enkeltord og udsender en ny stream af ord.

    - **Counter.CS**: implementerer et bolt, der tæller hvert ord og udsender en ny strøm af ord og antal for hvert ord.

    > [AZURE.NOTE] Disse bolte blot læse og skrive til streams, men du kan også bruge en bolt til at kommunikere med kilder som en database eller en tjeneste.

3. Åbn **Splitter.cs**. Bemærk, at det har kun én metode som standard: **Execute**. Dette kaldes Når bolten modtager en tupel for behandling. Her kan du læse og behandle indgående tupler og udsender udgående tupler.

4. Erstat indholdet af klassen **fordeler** med følgende kode:

        private Context ctx;

        // Constructor
        public Splitter(Context ctx)
        {
            Context.Logger.Info("Splitter constructor called");
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // Input contains a tuple with a string field (the sentence)
            inputSchema.Add("default", new List<Type>() { typeof(string) });
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // Outbound contains a tuple with a string field (the word)
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance of the bolt
        public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Splitter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the sentence from the tuple
            string sentence = tuple.GetString(0);
            // Split at space characters
            foreach (string word in sentence.Split(' '))
            {
                Context.Logger.Info("Emit: {0}", word);
                //Emit each word
                this.ctx.Emit(new Values(word));
            }

            Context.Logger.Info("Execute exit");
        }

    Tage et øjeblik på at læse kommentarer at forstå, hvad betyder denne kode.

5. Åbn **Counter.cs** , og Erstat klasse indholdet med følgende:

        private Context ctx;

        // Dictionary for holding words and counts
        private Dictionary<string, int> counts = new Dictionary<string, int>();

        // Constructor
        public Counter(Context ctx)
        {
            Context.Logger.Info("Counter constructor called");
            // Set instance context
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string field - the word
            inputSchema.Add("default", new List<Type>() { typeof(string) });

            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string and integer field - the word and the word count
            outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance
        public static Counter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Counter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the word from the tuple
            string word = tuple.GetString(0);
            // Do we already have an entry for the word in the dictionary?
            // If no, create one with a count of 0
            int count = counts.ContainsKey(word) ? counts[word] : 0;
            // Increment the count
            count++;
            // Update the count in the dictionary
            counts[word] = count;

            Context.Logger.Info("Emit: {0}, count: {1}", word, count);
            // Emit the word and count information
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
            Context.Logger.Info("Execute exit");
        }

    Tage et øjeblik på at læse kommentarer at forstå, hvad betyder denne kode.

### <a name="define-the-topology"></a>Definere topologien

Spouts og bolte er arrangeret i en graf, som definerer, hvordan data, der flyder mellem komponenter. Til denne topologi er grafen på følgende måde:

![Billede af hvordan komponenter er arrangeret](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Sætninger der udledes fra tud, som er fordelt på forekomster af fordeler bolt. Fordeler bolt opdeler sætningerne i ord, som er fordelt til tæller bolt.

Fordi Ordoptælling holdes lokalt på forekomsten tæller, vil vi sikre dig, at bestemte ord dataflow til den samme tæller bolt forekomst, så vi har kun én forekomst at holde styr på et bestemt ord. Men til bolt fordeler det virkelig er ligegyldigt, hvilken bolt modtager hvilke sætning, så vi blot ønsker at indlæse balance sætninger på tværs af disse forekomster.

Åbn **Program.cs**. Den vigtige metode er **GetTopologyBuilder**, som bruges til at definere topologien, der er sendt til Storm. Erstat indholdet af **GetTopologyBuilder** med følgende kode til at implementere topologien beskrevet tidligere:

        // Create a new topology named 'WordCount'
        TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

        // Add the spout to the topology.
        // Name the component 'sentences'
        // Name the field that is emitted as 'sentence'
        topologyBuilder.SetSpout(
            "sentences",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
            },
            1);
        // Add the splitter bolt to the topology.
        // Name the component 'splitter'
        // Name the field that is emitted 'word'
        // Use suffleGrouping to distribute incoming tuples
        //   from the 'sentences' spout across instances
        //   of the splitter
        topologyBuilder.SetBolt(
            "splitter",
            Splitter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
            },
            1).shuffleGrouping("sentences");

        // Add the counter bolt to the topology.
        // Name the component 'counter'
        // Name the fields that are emitted 'word' and 'count'
        // Use fieldsGrouping to ensure that tuples are routed
        //   to counter instances based on the contents of field
        //   position 0 (the word). This could also have been
        //   List<string>(){"word"}.
        //   This ensures that the word 'jumped', for example, will always
        //   go to the same instance
        topologyBuilder.SetBolt(
            "counter",
            Counter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
            },
            1).fieldsGrouping("splitter", new List<int>() { 0 });

        // Add topology config
        topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
        {
            {"topology.kryo.register","[\"[B\"]"}
        });

        return topologyBuilder;

Tage et øjeblik på at læse kommentarer at forstå, hvad betyder denne kode.

## <a name="submit-the-topology"></a>Sende topologien

1. I **Solution Explorer**, skal du højreklikke på projektet, og vælg **Send for at Storm på HDInsight**.

    > [AZURE.NOTE] Hvis du bliver bedt om det, kan du angive logonoplysninger for abonnementet Azure. Hvis du har mere end ét abonnement, log på den, der indeholder dine Storm på HDInsight klynge.

2. Vælg din Storm på HDInsight klynge fra rullelisten **Storm klynge** , og vælg derefter **Send**. Du kan overvåge Hvis indsendelse lykkes ved hjælp af vinduet **Output** .

3. Når topologien er blevet indsendt, skal **Storm topologier** for-klyngen vises. Vælg **WordCount** topologien på listen for at få vist oplysninger om den, der kørende topologi.

    > [AZURE.NOTE] Du kan også få vist **Storm topologier** fra **Server Explorer**: Udvid **Azure** > **HDInsight**, skal du højreklikke på en Storm på HDInsight klynge, og vælg derefter **Vis Storm topologier**.

    Bruge linkene for spouts eller bolte til at få vist oplysninger om disse komponenter. Der åbnes et nyt vindue for hvert enkelt markeret element.

4. Klik på **Afslut** for at stoppe topologien fra visningen **Topologi oversigt** .

    > [AZURE.NOTE] Storm topologier fortsætte med at køre, indtil de er deaktiveret, eller klyngen slettes.

## <a name="transactional-topology"></a>Transaktions topologi

Den forrige topologi er ej. Komponenterne i topologien implementerer ikke funktioner, der er for replaying meddelelser, hvis behandlingen mislykkes af en komponent i topologien. Oprette et nyt projekt til en transaktions topologi for eksempel, og vælg **Storm eksempel** som projekttype.

Transaktions topologier implementere følgende for at understøtte genafspilning af data:

- **Cachelagring af metadata**: tud skal lagre metadata om de data, der udsendes, så dataene kan være hentet og udledes igen, hvis der opstår fejl. Da de data, der udsendes af prøven er lille, er rækkedata for hver tupel gemt i en ordbog til genafspilning.

- **ACK**: kan ringe til hver bolt i topologien `this.ctx.Ack(tuple)` ack, der er blevet behandlet en tupel. Når alle bolte har registrerede tuplen, den `Ack` metode til at tud startes. Dette giver mulighed for tud til at fjerne cachelagrede data til genafspilning, fordi data, der blev behandlet helt.

- **Mislykkes**: kan ringe til hver bolt `this.ctx.Fail(tuple)` til at angive, at behandling mislykkedes for en tupel. Fejlen overfører til den `Fail` metode til at tud, hvor kan være afspilles igen tuplen ved hjælp af cachelagret metadata.

- **Række-ID**: Når udsendelse en tupel, der kan angives en sekvens-ID. Dette skal være en værdi, der identificerer tuplen til genafspilning (Ack og fejl) behandling. For eksempel bruger tud i projektet **Storm eksempel** følgende, når udsendelse data:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Dette udsender en ny tupel, der indeholder en sætning til stream standard med sekvensværdien-ID, der indeholdt i **lastSeqId**. I dette eksempel skal øges **lastSeqId** blot for hver tupel udledes.

Som vist i projektet **Storm eksempel** , kan om en komponent er transaktions være angivet på kørselstidspunktet, baseret på konfiguration.

## <a name="hybrid-topology"></a>Hybrid topologi

HDInsight værktøjer til Visual Studio kan også bruges til at oprette hybrid topologier, hvor nogle komponenter er C# og andre er Java.

Oprette et nyt projekt til en eksempel hybrid topologi, og vælg **Storm Hybrid eksempel**. Dette opretter en fuldt kommenterede eksempel, der indeholder flere topologier, der viser følgende:

- **Java spout** og **C# bolt**: defineret i **HybridTopology_javaSpout_csharpBolt**

    - En transaktions version er defineret i **HybridTopologyTx_javaSpout_csharpBolt**

- **C# spout** og **Java bolt**: defineret i **HybridTopology_csharpSpout_javaBolt**

    - En transaktions version er defineret i **HybridTopologyTx_csharpSpout_javaBolt**

        > [AZURE.NOTE] Denne version viser også, hvordan du bruger Clojure kode fra en tekstfil som en Java-komponent.

Hvis du vil skifte mellem topologien, der bruges, når projektet er sendt, skal du blot flytte den `[Active(true)]` erklæring om, at du vil bruge, før du sender den til klyngen topologien.

> [AZURE.NOTE] Alle de Java-filer, der kræves der leveres som en del af dette projekt i mappen **JavaDependency** .

Overvej følgende, når oprette og sende en hybrid topologi:

- **JavaComponentConstructor** skal bruges til at oprette en ny forekomst af klassen Java for en tud eller bolt.

- **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** skal bruges til at sekventielt data i eller af Java komponenter fra Java-objekter til JSON.

- Når du indsender topologien på serveren, skal du bruge indstillingen **konfigurationer for yderligere** for at angive **Java fil stier**. Den angivne sti skal være den mappe, der indeholder de filer, glas, der indeholder Java-klasser.

### <a name="azure-event-hubs"></a>Azure begivenhed Hubs

SCP.Net version 0.9.4.203 introducerer en ny klasse og metode specifikt til at arbejde med den begivenhed Hub Spout (en Java tud, der læses fra begivenhed Hub.) Når du opretter en topologi, der bruger denne tud, kan du bruge følgende metoder:

- **EventHubSpoutConfig** klasse: opretter et objekt, der indeholder konfigurationen for komponenten tud

- **TopologyBuilder.SetEventHubSpout** metode: Tilføjer komponenten begivenhed Hub Spout til topologien

> [AZURE.NOTE] Mens de gør det nemmere at arbejde med begivenhed Hub Spout end andre Java-komponenter, skal du stadig bruge CustomizedInteropJSONSerializer ordne sekventielt data, som tud.

## <a id="configurationmanager"></a>Brug af ConfigurationManager

Brug ikke ConfigurationManager til konfiguration af hentes værdier fra bolt og spout komponenter. Dette vil føre til en null markøren undtagelse. I stedet er konfigurationen for dit projekt givet til Storm topologien som et tasten/værdi-par i konteksten topologi. Hver komponent, der er afhænger af konfiguration af værdier skal hente dem fra konteksten under initialisering.

Følgende kode demonstrerer, hvordan du kan hente disse værdier:

    public class MyComponent : ISCPBolt
    {
        // To hold configuration information loaded from context
        Configuration configuration;
        ...
        public MyComponent(Context ctx, Dictionary<string, Object> parms)
        {
            // Save a copy of the context for this component instance
            this.ctx = ctx;
            // If it exists, load the configuration for the component
            if(parms.ContainsKey(Constants.USER_CONFIG))
            {
                this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
            }
            // Retrieve the value of "Foo" from configuration
            var foo = this.configuration.AppSettings.Settings["Foo"].Value;
        }
        ...
    }

Hvis du bruger en `Get` metode til at returnere en forekomst af din komponent, skal du sikre dig, at den passerer begge de `Context` og `Dictionary<string, Object>` parametrene til. I følgende eksempel er en basic `Get` afskrivningsmetode, som overfører korrekt disse værdier:

    public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new MyComponent(ctx, parms);
    }

## <a name="how-to-update-scpnet"></a>Sådan opdaterer du SCP.NET

Seneste versioner af SCP.NET understøtter pakke opgradering gennem NuGet. Når der findes en ny opdatering, modtager du besked via opgradering. Udføre disse trin for at søge efter en opgradering manuelt:

1. I **Solution Explorer**, skal du højreklikke på projektet, og vælg **Administrer NuGet pakker**.

2. Vælg **opdateringer**af package manager. Hvis der findes en opdatering, vil det være angivet. Klik på knappen **Opdater** at installere den pakken.

> [AZURE.IMPORTANT] Hvis dit projekt er blevet oprettet med en af de tidligere versioner af SCP.NET, der ikke har brugt NuGet efter pakke opdateringer, skal du udføre følgende trin for at opdatere til den nye version:
>
> 1. I **Solution Explorer**, skal du højreklikke på projektet, og vælg **Administrer NuGet pakker**.
> 2. Bruge feltet **Søg** , søge efter, og Tilføj derefter **Microsoft.SCP.Net.SDK** til projektet.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

### <a name="null-pointer-exceptions"></a>Null markøren undtagelser

Når du bruger en C#-topologi med en Linux-baserede HDInsight klynge, bolt og spout komponenter, der bruger ConfigurationManager til at læse konfigurationsindstillinger på kørselstidspunktet kan returnere null markøren undtagelser. Dette sker, fordi konfigurationen for det domæne, der indlæses ikke er fra samlingen, der indeholder dit projekt.

Konfigurationen af dit projekt, som et tasten/værdi-par i konteksten topologi der overføres til Storm topologien, og kan hentes fra objektet ordbog, der sendes til din komponenter, når de er initialiseret.

Følgende eksempel viser indlæser konfiguration værdierne fra topologi konteksten, skal du se afsnittet [ConfigurationManager](#configurationmanager) i dette dokument.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Når du bruger en C#-topologi med en Linux-baserede HDInsight klynge, kan du støde på følgende fejl:

    System.TypeLoadException: Failure has occurred while loading a type.

Dette normalt occurrs, når du bruger et binært tal, der er ikke kompatibel med versionen af .NET, der understøtter sort/hvide.

Til klynger med Linux-baserede HDInsight, skal du sikre dig, at projektet bruger binære filer til .NET 4.5-kompileret.


### <a name="test-a-topology-locally"></a>Teste en topologi lokalt

Selvom det er nemt at installere en topologi til en klynge i nogle tilfælde skal du teste en topologi lokalt. Brug følgende trin til at køre og teste eksempel topologien i dette selvstudium lokalt i dit udviklingsmiljø.

> [AZURE.WARNING] Test af lokale fungerer kun for grundlæggende, C# kun topologier. Du skal ikke bruge lokale test for hybride topologier eller topologier, der bruger flere streams, som du får fejl.

1. I **Solution Explorer**, skal du højreklikke på projektet, og vælg **Egenskaber**. Ændre **outputtype** **Console**-program i egenskaberne for projektet.

    ![outputtype](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

    > [AZURE.NOTE] Husk at ændre den **outputtype** tilbage til **Klassebibliotek** , inden du installerer topologien til en klynge.

2. I **Solution Explorer**, skal du højreklikke på projektet, og vælg derefter **Tilføj** > **Nyt element**. Vælg **klassen** , og angiv **LocalTest.cs** som klassenavnet. Til sidst skal du klikke på **Tilføj**.

3. Åbn **LocalTest.cs** og tilføje øverst, sætningen **ved hjælp af** følgende:

        using Microsoft.SCP;

4. Du kan bruge følgende som indholdet af klassen **LocalTest** :

        // Drives the topology components
        public void RunTestCase()
        {
            // An empty dictionary for use when creating components
            Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

            #region Test the spout
            {
                Console.WriteLine("Starting spout");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext spoutCtx = LocalContext.Get();
                // Get a new instance of the spout, using the local context
                Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

                // Emit 10 tuples
                for (int i = 0; i < 10; i++)
                {
                    sentences.NextTuple(emptyDictionary);
                }
                // Use LocalContext to persist the data stream to file
                spoutCtx.WriteMsgQueueToFile("sentences.txt");
                Console.WriteLine("Spout finished");
            }
            #endregion

            #region Test the splitter bolt
            {
                Console.WriteLine("Starting splitter bolt");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext splitterCtx = LocalContext.Get();
                // Get a new instance of the bolt
                Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);


                // Set the data stream to the data created by the spout
                splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
                // Get a batch of tuples from the stream
                List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
                // Process each tuple in the batch
                foreach (SCPTuple tuple in batch)
                {
                    splitter.Execute(tuple);
                }
                // Use LocalContext to persist the data stream to file
                splitterCtx.WriteMsgQueueToFile("splitter.txt");
                Console.WriteLine("Splitter bolt finished");
            }
            #endregion

            #region Test the counter bolt
            {
                Console.WriteLine("Starting counter bolt");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext counterCtx = LocalContext.Get();
                // Get a new instance of the bolt
                Counter counter = Counter.Get(counterCtx, emptyDictionary);

                // Set the data stream to the data created by splitter bolt
                counterCtx.ReadFromFileToMsgQueue("splitter.txt");
                // Get a batch of tuples from the stream
                List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
                // Process each tuple in the batch
                foreach (SCPTuple tuple in batch)
                {
                    counter.Execute(tuple);
                }
                // Use LocalContext to persist the data stream to file
                counterCtx.WriteMsgQueueToFile("counter.txt");
                Console.WriteLine("Counter bolt finished");
            }
            #endregion
        }

    Tage et øjeblik at læse gennem kodekommentarer. Denne kode bruger **LocalContext** til at køre komponenterne i udviklingsmiljøet, og det fortsætter datastrømmen mellem komponenter til tekstfiler på det lokale drev.

5. Åbn **Program.cs** , og Tilføj følgende metoden **Main** :

        Console.WriteLine("Starting tests");
        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
        // Initialize the runtime
        SCPRuntime.Initialize();

        //If we are not running under the local context, throw an error
        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
        }
        // Create test instance
        LocalTest tests = new LocalTest();
        // Run tests
        tests.RunTestCase();
        Console.WriteLine("Tests finished");
        Console.ReadKey();

6. Gemme ændringerne, og derefter klikke på **F5** , eller Vælg **fejlfinding af** > **Starte fejlfinding** til at starte projektet. Et console vindue skal vises, og log status som test status. Når **test færdig** vises, skal du trykke på en tast for at lukke vinduet.

7. Brug **Windows Stifinder** til at finde den mappe, der indeholder dit projekt, for eksempel **C:\Users\<your_user_name > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Åbn **placering**i denne mappe, og klik derefter på **fejlfinding**. Skal du se de filer, der er produceret, når testene, der har kørt: sentences.txt, counter.txt og splitter.txt. Åbn hver tekstfil, og Undersøg dataene.

    > [AZURE.NOTE] Strengdata bevares som en matrix med decimal værdierne i disse filer. For eksempel \[[97,103,111]] i **splitter.txt** filen er ordet 'og'.

Selvom test af en grundlæggende word tælle er programmet lokalt ret trivial, den faktiske værdi leveres, når du har en kompleks søgetopologien, der kommunikerer med eksterne datakilder eller udfører kompleks dataanalyse. Når du arbejder på det pågældende projekt, skal du muligvis angive pausepunkter og gennemgå koden i dine komponenter til at isolere problemer.

> [AZURE.NOTE] Sørg for at angive den **projekttype** tilbage til **Klassebibliotek** før du anvender for en Storm på HDInsight klynge.

### <a name="log-information"></a>Logoplysninger

Du kan nemt logge oplysninger fra din søgetopologikomponenter ved hjælp af `Context.Logger`. For eksempel opretter følgende en logfil med oplysninger post:

    Context.Logger.Info("Component started");

Registrerede oplysninger kan ses fra **Loggen for Hadoop-tjenesten**, som findes i **Server Explorer**. Udvid posten for dit Storm på HDInsight klynge, og derefter udvide **Loggen for Hadoop-tjenesten**. Markér logfilen for at få vist.

> [AZURE.NOTE] Logfiler gemmes i kontoen Azure-lager, der bruges af din klynge. Hvis dette er et andet abonnement end den, du er logget på med Visual Studio, skal du logge på det abonnement, der indeholder lagerplads konto for at få vist disse oplysninger.

### <a name="view-error-information"></a>Vis oplysninger om fejl

Følge nedenstående trin for at få vist fejl, der er opstået i en igangværende topologi:

1. Højreklik på Storm på HDInsight klynge fra **Server Explorer**, og vælg **Vis Storm topologier**.

2. **Spout** og **bolte**, vil kolonnen **Sidste fejl** have oplysninger om den sidste fejl, der er opstået.

3. Vælg det **Spout-Id** eller **Bolt-Id** for komponenten, der indeholder en fejl, der er angivet. På siden detaljer, der vises yderligere fejloplysninger er angivet i sektionen **fejl** i bunden af siden.

4. Vælg en **Port** sektionen **bobestyreren** på siden for at se Storm arbejder logfilen for de sidste minutter for at få flere oplysninger.

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært at udvikle og installere Storm topologier fra HDInsight tools til Visual Studio, lære, hvordan du kan [håndtere hændelser fra Azure begivenhed hubben med Storm på HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Du kan finde et eksempel på en C#-topologi, der opdeles streamdata i flere streams, [C# Storm eksempel](https://github.com/Blackmist/csharp-storm-example).

Du kan finde flere oplysninger om oprettelse af C# topologier, ved at besøge [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Flere måder at arbejde med HDInsight og flere Storm på HDinsight eksempler under følgende:

**Microsoft SCP.NET**

* [SCP programming vejledning](hdinsight-storm-scp-programming-guide.md)

**Apache Storm på HDInsight**

- [Installere og overvåge topologier med Apache Storm på HDInsight](hdinsight-storm-deploy-monitor-topology.md)

- [Eksempel topologier for Storm på HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop på HDInsight**

- [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

- [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

- [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase på HDInsight**

- [Introduktion til HBase på HDInsight](hdinsight-hbase-tutorial-get-started.md)

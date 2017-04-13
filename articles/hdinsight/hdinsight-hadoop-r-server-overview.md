<properties
    pageTitle="Hvad er R på HDInsight? Introduktion til R Server på HDInsight (preview) | Microsoft Azure"
    description="Hvad er R Server på HDInsight (preview) og hvordan du bruger R Server til oprettelse af programmer til stor dataanalyse."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/17/2016"
   ms.author="jeffstok"/>


# <a name="overview-of-r-server-on-hdinsight-preview"></a>Oversigt over R Server på HDInsight \(preview\)

Med Microsoft Azure HDInsight Premium, er Microsoft R Server er nu tilgængelig som en valgmulighed, når du opretter HDInsight klynger i Azure. Denne nye funktion indeholder data forskere, statistikere og R programmører med efter behov adgang til SVG, distribueret metoder til analytics på HDInsight.

Klynger kan tilpasset projekter og opgaver ved hånden og torn, når de er ikke længere bruges. Da de er en del af Azure HDInsight, følger disse klynger med enterprise-niveau 24/7 support, en SERVICENIVEAUAFTALE 99,9% oppetid og fleksibilitet du integrere systemet med andre komponenter i Azure økosystemet.

>[AZURE.NOTE] R Server fås kun med HDInsight Premium. HDInsight Premium er i øjeblikket kun tilgængelig for Hadoop og knallertmotor klynger. Så kan aktuelt du bruge R Server kun med Hadoop og knallertmotor klynger på HDInsight. Du kan finde flere oplysninger, se [Hvad er den anden tjeneste niveauer og Hadoop-komponenter, der følger med HDInsight?](hdinsight-component-versioning.md).

R Server på HDInsight indeholder de nyeste funktioner for R-baserede analyser i store datasæt, der er indlæst til Azure Blob-lager. Da R Server er bygget på Åbn kilde R, kan de R-baserede programmer, du opretter udnytte nogen af de 8000 + Åbn kilde R pakker samt rutiner i ScaleR, Microsofts stor data analytics-pakke, som følger med R-serveren.

Noden kant af Premium klynger er et godt sted at oprette forbindelse til klyngen og køre scripts R. Med en kantnode har du mulighed for at køre Scaler's parallelized fordelt funktioner på tværs af kerner på serveren, edge node. Har du også mulighed for at køre dem på tværs af noderne i klyngen ved hjælp af Scaler's Hadoop kort reducere eller knallertmotor beregne kontekster.

Modeller eller prognoser, som kan downloades resultatet fra analyser til brug i det lokale miljø. De kan også være opgivet et andet sted i Azure, sådan som gennem en [Azure Machine Learning Studio](http://studio.azureml.net) [webtjeneste](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md).

## <a name="get-started-with-r-on-hdinsight"></a>Komme i gang med R på HDInsight

Hvis du vil medtage R Server i en HDInsight klynge, skal du oprette et Hadoop eller knallertmotor klynge med indstillingen Premium, når du opretter en klynge ved hjælp af portalen Azure. Begge typer klynge bruge den samme konfiguration, som omfatter R Server på noderne data i en klynge og en kantnode som en landing zone for R serverbaserede analyser. Se [Introduktion til R Server på HDInsight](hdinsight-hadoop-r-server-get-started.md) til en detaljeret gennemgang om oprettelse af en klynge.

## <a name="learn-about-data-storage-options"></a>Få mere at vide om indstillinger for lagring af data

Standard lagerplads til HDInsight klynger er Blob-lager med filsystemet HDFS knyttet til en blob objektbeholder. Dette sikrer, at netop data, der er overført til klynge-lager eller skrevet til klynge lager i løbet af analyse foretages fast. Du kan bruge værktøjet [AzCopy](../storage/storage-use-azcopy.md) til at kopiere data til og fra blob.

Ud over at bruge Blob-lager, har du mulighed for at bruge [Azure datalagring sø](https://azure.microsoft.com/services/data-lake-store/) med din klynge. Hvis du bruger Data sø, kan du bruge både Blob-lager og Data sø for HDFS lagerplads.

Du kan også bruge [Azure filer](../storage/storage-how-to-use-files-linux.md) som et datalager til brug på noden kant. Azure filer gør det muligt at oprette forbindelse til et filshare, der er oprettet i Azure-lager til Linux-filsystemet. Du kan finde flere oplysninger om indstillinger for lagring af data for R Server på HDInsight klynge mappeindstillingerne [lagerplads til R Server på HDInsight klynger](hdinsight-hadoop-r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Access R Server på klyngen

Når du har oprettet en klynge med R-serveren, kan du oprette forbindelse til konsollen R på noden kant af klynge gennem SSH/trykfarver. Du kan også forbinde via en browser, hvis du vælger at installere valgfri RStudio Server IDE på noden kant. Du kan finde flere oplysninger om installation af RStudio Server, [Installere RStudio Server på HDInsight klynger](hdinsight-hadoop-r-server-install-r-studio.md).   

## <a name="develop-and-run-r-scripts"></a>Udvikle og køre scripts, som R

R-scripts, du opretter og kører kan bruge en af de 8000 + Åbn kilde R pakker ud over den parallelized og fordelt produktionsmæssige i biblioteket ScaleR. Script, der køres i R Server på noden kant kører Generelt i R fortolker på noden. Undtagelsen er disse trin, der kalder en ScaleR fungerer sammen med en Beregn kontekst det er indstillet til Hadoop kort reducere (RxHadoopMR) eller knallertmotor (RxSpark).

I disse tilfælde kører funktionen i en fordelt måde på tværs af disse data (opgave) noder klyngen, der er forbundet med de data, der refereres til. Finde flere oplysninger om de forskellige Beregn kontekst indstillinger, [beregne kontekst indstillinger for R Server på HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Giver muligheder for en model

Når din datamodellering er fuldført, kan du giver muligheder for at gøre prognoser til nye data, både i Azure og lokale modellen. Denne proces kaldes også pointmodel for kundeemner. Her er nogle eksempler.

### <a name="score-in-hdinsight"></a>Karakteren i HDInsight

Skriv en R-funktion, der ringer til din model for at gøre prognoser for en ny datafil, som du har indlæst din lagerplads konto for at få i HDInsight. Gem prognoser tilbage til kontoen lagerplads. Du kan køre den rutinemæssige efter behov på noden kant af din klynge eller ved hjælp af et planlagt job.  

### <a name="score-in-azure-machine-learning"></a>Karakteren i Azure Machine Learning

For at få ved hjælp af en Azure maskine Learning webtjeneste, kan du bruge Åbn kilde Azure maskine Learning R installationspakken kendt som [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) til at publicere din model som en Azure-webtjeneste. Fordel er denne pakke forudinstalleret på noden kant. Nu skal bruge faciliteter i Machine Learning til at oprette en brugergrænseflade til webtjenesten, og kald derefter webtjenesten efter behov for vundne.

Hvis du vælger denne indstilling, skal du konvertere en hvilken som helst ScaleR modelobjekter til objekter i tilsvarende open source-modeller til brug sammen med webtjenesten. Dette kan gøres ved hjælp af ScaleR tvang funktioner, såsom `as.randomForest()` for ensemble-baserede modeller.


### <a name="score-on-premises"></a>Lokale resultat

For at få lokalt efter oprettelse af din model, kan du sekventielt modellen i R, kan du hente det, sekventielt deaktivere den og derefter bruge den til vundne nye data. Du kan resultat nye data ved hjælp af den fremgangsmåde, der er beskrevet tidligere i [resultat i HDInsight](#scoring-in-hdinsight) eller ved hjælp af [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Bevare klyngen

### <a name="install-and-maintain-r-packages"></a>Installere og vedligeholde R-pakker

De fleste af de R-pakker, som du bruger skal på noden kant, da de fleste af dine R scripts kører der. For at installere yderligere R-pakker på noden kant, kan du bruge den sædvanlige `install.packages()` metode i R.

I de fleste tilfælde kan behøver du ikke at installere yderligere R-pakker på noderne data, hvis du kun bruger rutiner fra biblioteket ScaleR på tværs af klyngen. Du muligvis dog yderligere pakker til at understøtte brugen af **rxExec** eller udførelse af **RxDataStep** på noderne data.

I disse tilfælde skal være angivet yderligere pakker ved hjælp af handlingen script, når du har oprettet klyngen. Du kan finde flere oplysninger i [oprette en HDInsight klynge med R-serveren](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Ændre indstillingerne for Hadoop kort reducere hukommelse

Du kan ændre en klynge for at ændre mængden hukommelse, der er tilgængelige for R Server, når den kører et kort reducere job. Du kan ændre en klynge ved at bruge Apache Ambari Brugergrænsefladen, der er tilgængelige via bladet Azure til portalen for din klynge. Du kan finde oplysninger om, hvordan du kan få adgang til Ambari UI som din klynge, [administrere HDInsight klynger ved hjælp af Ambari Webbrugergrænsefladen](hdinsight-hadoop-manage-ambari.md).

Det er også muligt at ændre mængden hukommelse, der er tilgængelige for R Server ved hjælp af Hadoop parametre i opkaldet til **RxHadoopMR** på følgende måde:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Tilpasse din klynge

En eksisterende klynge kan skaleres op eller ned gennem på portalen. Du kan få den ekstra kapacitet, som du måske brug for større opgaver ved skalering, eller du kan skalere tilbage en klynge når den er inaktiv. Du kan finde oplysninger om, hvordan du kan tilpasse en klynge, [administrere HDInsight klynger](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Vedligeholde systemet

Vedligeholdelse udføres på den underliggende Linux FOS i en HDInsight klynge kontortiden anvende OS rettelser og andre opdateringer. Typisk sker vedligeholdelse på 3:30 AM (baseret på den lokale tid for VM) hver mandag og torsdag. Opdateringer udføres på en sådan måde, at de ikke kan påvirke mere end en fjerdedel af klyngen ad gangen.  

Da noderne hoved er overflødige og ikke alle data noder påvirkes, langsommere alle job, der kører i denne periode. De skal stadig køre til afslutning, men. Brugerdefineret software eller lokale data, du har bevares på tværs af disse vedligeholdelse hændelser, medmindre opstår nedbrud opstår, der kræver en klynge Genopbyg.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Få mere at vide om ide-indstillinger for R Server på en HDInsight klynge

Noden Linux kant af en HDInsight Premium klynge er zonen landing til R-baserede analyse. Når du har forbindelse til klyngen, kan du starte console grænsefladen til R Server ved at skrive **R** kommandoprompten Linux. Brug af grænsefladen console er udvidet, hvis du kører en teksteditor for R script udvikling i et andet vindue, og klippe og indsætte sektioner i dit script i konsollen R efter behov.

En mere avancerede værktøj til udvikling af scriptet R er den R-baserede IDE til brug på computeren, som Microsofts nyligt offentliggjorte [R Tools til Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS). Dette er en serie af skrivebord og server værktøjer fra [RStudio](https://www.rstudio.com/products/rstudio-server/). Du kan også bruge Walwares Eklipse-baseret [StatET](http://www.walware.de/goto/statet).

En anden mulighed er at installere en IDE på noden Linux kant sig selv.  Valgmuligheden populære er [RStudio Server](https://www.rstudio.com/products/rstudio-server/), som indeholder en browser-baseret IDE til brug af fjerne klienter. Installerer RStudio Server på noden kant af en HDInsight Premium klynge indeholder en fuld ide-oplevelse til udvikling og udførelse af R scripts med R Server på klyngen. Det kan være meget mere produktiv end R-konsollen.  Hvis du vil bruge RStudio Server, skal du se [Installere RStudio Server på HDInsight klynger](hdinsight-hadoop-r-server-install-r-studio.md).

## <a name="learn-about-pricing"></a>Få mere at vide om priser

De tid, der er knyttet til en HDInsight Premium klynge med R-serveren er struktureret på samme måde at gebyrer for de almindelige HDInsight klynger. De er baseret på tilpasningen af de underliggende FOS på tværs af det navn, data og kant noder, med tilføjelse af en core-timers hævning for Premium. Finde flere oplysninger om HDInsight Premium priser, herunder priser under Public Preview og tilgængeligheden af en 30-dages gratis prøveversion [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Næste trin

Følge linkene nedenfor for at få mere at vide om, hvordan du bruger R Server med HDInsight klynger.

- [Introduktion til R Server på HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Føje RStudio Server til HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Beregne kontekst indstillinger for R Server på HDInsight (preview)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure lagerplads indstillinger for R Server på HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

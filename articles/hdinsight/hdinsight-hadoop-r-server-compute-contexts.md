<properties
   pageTitle="Beregne kontekst indstillinger for R Server på HDInsight (preview) | Microsoft Azure"
   description="Få mere at vide om de forskellige Beregn kontekst indstillinger tilgængelige for brugere med R Server på HDInsight (preview)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="10/18/2016"
   ms.author="jeffstok"
/>

# <a name="compute-context-options-for-r-server-on-hdinsight-preview"></a>Beregne kontekst indstillinger for R Server på HDInsight (preview)

Microsoft R Server på Azure HDInsight (preview) indeholder de nyeste funktioner for R-baserede analyser. Det bruges data, der er gemt i HDFS i en objektbeholder i kontoen [Azure Blob](../storage/storage-introduction.md "Azure Blob-lager") lagerplads eller lokale Linux-filsystemet. Da R Server er bygget på Åbn kilde R, kan de R-baserede programmer, du opretter udnytte nogen af de 8000 + Åbn kilde R pakker. De kan også udnytte rutiner i [ScaleR]-(http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR"), Microsofts stor data analytics-pakke, som følger med R-serveren.  

Noden kant af en Premium klynge er et godt sted for at oprette forbindelse til klyngen og køre scripts R. Med en kantnode har du mulighed for at køre Scaler's parallelized fordelt funktioner på tværs af kerner på serveren, edge node. Har du også mulighed for at køre dem på tværs af noderne i klyngen ved hjælp af Scaler's Hadoop kort reducere eller knallertmotor beregne kontekster.

## <a name="compute-contexts-for-an-edge-node"></a>Beregne kontekster for en kantnode

Generelt, kører en R-script, der køres i R Server på noden kant i R fortolker på noden. Undtagelsen er disse trin, der kalder en ScaleR funktion. ScaleR opkald kører i et Beregn miljø, der afhænger af, hvordan du konfigurerer ScaleR Beregn kontekst.  Når du kører dit R script fra en kantnode, de mulige værdier af konteksten Beregn er lokale sekventielle ('lokal'), lokale parallelt (localpar), kort reducere og gnister.

Indstillingerne 'lokal' og 'localpar' variere kun i hvordan rxExec opkald udføres. De begge udføre andre modtagelse funktionskald i en parallelle måde på tværs af alle tilgængelige kerner medmindre andet er angivet ved hjælp af ScaleR numCoresToUse indstillingen, f.eks. rxOptions(numCoresToUse=6). Følgende opsummerer de forskellige Beregn kontekst indstillinger

| Beregne kontekst  | Sådan angives                      | Udførelse af kontekst                                                                     |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Lokal sekventielle | rxSetComputeContext('local')    | Parallelized udførelse på tværs af borekerner af edge node server, med undtagelse af rxExec opkald, der udføres serielt |
| Lokale parallelt   | rxSetComputeContext('localpar') | Parallelized udførelse på tværs af borekerner af node edge server                                 |
| Knallertmotor            | RxSpark()                       | Parallelized fordelt udførelse via knallertmotor på tværs af noderne af HDI klynge      |
| Reducere kort       | RxHadoopMR()                    | Parallelized fordelt udførelse via kort reducere på tværs af noderne af HDI klynge |


Hvis resultatet, du vil have parallelized udførelse af med henblik på ydeevne, er der tre indstillinger. Hvilken indstilling du vælger, afhænger af art dit analytics arbejde, og størrelsen og placeringen af dine data.

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Retningslinjer for bestemmer dig for en Beregn kontekst

Der findes i øjeblikket ingen formel, der fortæller, beregner kontekst til at bruge. Der er dog nogle ledende principper, der kan hjælpe dig med at gøre det rigtige valg, eller mindst hjælpe dig med at indskrænke dine valg, før du kører en benchmark. Disse vejledende principper omfatter:

1.  Lokale Linux-filsystemet er hurtigere end HDFS.
2.  Gentagne analyser er hurtigere, hvis dataene er lokale, og hvis det er i XDF.
3.  Det anbefales at streame mindre mængder data fra et tekst-datakilder. Hvis mængden data er større, kan du konvertere den til XDF før analyse.
4.  Spild af kopiering eller streaming dataene til noden kant til analyse bliver uhensigtsmæssig til meget store datamængder.
5.  Knallertmotor er hurtigere end kort reducere til analyse i Hadoop.

Hvis du har disse principper, er nogle generelle regler for rulleboks for at vælge en Beregn kontekst:

### <a name="local"></a>Lokale

- Hvis mængden data til at analysere er lille og kræver ikke gentagne analyser, derefter streame den direkte i analysis rutinemæssige og bruge 'lokal' eller 'localpar'.
- Hvis mængden data til at analysere er små eller mellemstore og kræver gentagne analyser, derefter kopiere den til det lokale filsystem, importere den til XDF og analysere dem via 'lokal' eller 'localpar'.

### <a name="hadoop-spark"></a>Hadoop knallertmotor

- Hvis mængden data til at analysere er stort, derefter importere den til XDF i HDFS (medmindre lagerplads er et problem), og analysere dem via 'Knallertmotor'.

### <a name="hadoop-map-reduce"></a>Reducere Hadoop-kort

- Brug kun, hvis du støder på et insurmountable problem med brug af konteksten knallertmotor Beregn, da det vil normalt være langsommere.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Indbygget hjælp på rxSetComputeContext

Flere oplysninger og eksempler på ScaleR Beregn kontekster, se indbygget hjælp i R til metoden rxSetComputeContext for eksempel:

    > ?rxSetComputeContext

Du kan også se til "ScaleR distribueret databehandling hjælpelinjen", der er tilgængelig fra [R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "R Server på MSDN") -biblioteket.


## <a name="next-steps"></a>Næste trin

I denne artikel, du har lært hvordan du opretter en ny HDInsight klynge, der indeholder R Server. Du har også lært grundlæggende om brug af konsollen R fra en SSH session. Du kan nu læse følgende artikler for at se andre måder at arbejde med R Server på HDInsight:

- [Oversigt over R Server til Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Introduktion til R server til Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Føje RStudio Server til HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Azure lagerplads indstillinger for R Server på HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

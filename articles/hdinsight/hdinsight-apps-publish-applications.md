<properties
    pageTitle="Udgive HDInsight programmer | Microsoft Azure"
    description="Lær at oprette og udgive HDInsight programmer."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/18/2016"
    ms.author="jgao"/>

# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Udgive HDInsight programmer til Azure Marketplace

Et HDInsight program er et program, der kan brugere installere på en Linux-baserede HDInsight klynge. Disse programmer kan udvikles af Microsoft, uafhængige softwareleverandører (ISV) eller dig selv. I denne artikel lærer du, hvordan du publicerer et HDInsight program til Azure Marketplace.  Se [publicere et tilbud til Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md)kan finde generelle oplysninger om publicering til Azure Marketplace.

HDInsight programmer bruger *Tage dine egne licens (BYOL)* modellen, hvor programmet udbyder er ansvarlig for licensering programmet til slutbrugere, og slutbrugere betaler kun Azure for de ressourcer, de opretter, som HDInsight klyngen og dens FOS/noder. På nuværende tidspunkt sker fakturering for selve programmet ikke via Azure.

Andre HDInsight programmer relateret artikel:

- [Installere HDInsight-programmer](hdinsight-apps-install-applications.md): Lær, hvordan du installerer et HDInsight program til din klynger.
- [Installere brugerdefinerede HDInsight-programmer](hdinsight-apps-install-custom-applications.md): Lær at installere og teste brugerdefinerede HDInsight-programmer.

 
## <a name="prerequisites"></a>Forudsætninger

For at sende dit brugerdefinerede program til marketplace, skal du har oprettet og testet din brugerdefineret program. Se følgende artikler:

- [Installere brugerdefinerede HDInsight-programmer](hdinsight-apps-install-custom-applications.md): Lær at installere og teste brugerdefinerede HDInsight-programmer.

Du skal også have registrere kontoen udvikler. Se [publicere et tilbud til Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) og [oprette en Microsoft Developer-konto](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-application"></a>Definere program

Der er to trin involveret til publicering af programmer til Azure Marketplace.  Først definerer du en **createUiDef.json** fil for at angive, hvilke klynger dit program er kompatibelt med; og derefter du udgive skabelonen fra Azure-portalen. Følgende er en eksempelfil createUiDef.json.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


|Felt  | Beskrivelse   | Mulige værdier|
|-------|---------------|----------------|
|datatyper  | De typer, klynge, der er kompatibelt med programmet.    |Hadoop, HBase, Storm, knallertmotor (eller en kombination af disse)|
|niveauer  | De klynge lag, der er kompatibelt med programmet.    |Standard, Premium (eller begge dele)|
|versioner|  De HDInsight klynge typer, der er kompatibelt med programmet.    |3.4|

## <a name="package-application"></a>Pakke program

Oprette en zip-fil, der indeholder alle nødvendige filer til installation af programmerne HDInsight. Du skal zip-filen i [programmet på Publicer](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Se et eksempel på [installere brugerdefinerede HDInsight-programmer](hdinsight-apps-install-custom-applications.md).

    >[AZURE.IMPORTANT] Navnet på programmet Installer script navnene skal være entydige for en bestemt klynge med formatet nedenfor. Desuden et installere og fjerne scripthandlinger skal være idempotent, hvilket betyder, at scriptene kan kaldes repeatly under fremstilling det samme resultat.
    
    >   navn":" [Sammenkæd (' farvetone-Installer-v0 ','-', uniquestring('applicationName')] "
        
    >Bemærk, at der er tre dele til scriptnavnet på:
        
    >   1. Et script navnepræfiks, som skal indeholde navnet på programmet eller et navn, der er relevante for programmet.
    >   2. A "-" for læsbarhed.
    >   3. En entydig strengfunktion med navnet på programmet som parameteren.

    >   Et eksempel er ovenfor enderne af blive: farvetone-Installer-v0-4wkahss55hlas på listen permanente script handling. Du kan finde et eksempel JSON dataene, [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Alle de nødvendige scripts.

> [AZURE.NOTE] De-programfiler (herunder webprogramfiler, hvis der ikke er nogen) kan være placeret på en hvilken som helst offentligt tilgængelige slutpunkt.

## <a name="publish-application"></a>Udgive program

Følg vejledningen nedenfor for at publicere et HDInsight program:

1. Log [Azure udgivelsesportal](https://publish.windowsazure.com/).
2. Klik på **løsning skabeloner** fra venstre for at oprette en ny løsning skabelon.
3. Angiv en titel, og klik derefter på **Opret en ny løsning skabelon**.
3. Klik på **Opret Udviklercenter konto og deltage i programmet Azure** for at registrere din virksomhed, hvis du ikke har gjort det.  Se [oprette en Microsoft Developer-konto](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Klik på **Definer nogle topologier for at komme i gang**. En løsning skabelon er en "overordnet" på alle dens topologier. Du kan angive flere topologier i én tilbud/løsning skabelon. Når et tilbud rykkes til midlertidige, rykkes den med alle dens topologier. 
4. Angiv et topologi navn, og klik derefter på plustegnet.
5. Angiv en ny version, og klik derefter på plustegnet.
6. Overføre zip-filen forberedt i [pakke programmet](#package-application).  
7. Klik på **Anmod om certificering**. Microsoft-certificering teamet Gennemse filerne, og bekræfte topologien.

## <a name="next-steps"></a>Næste trin

- [Installere HDInsight-programmer](hdinsight-apps-install-applications.md): Lær, hvordan du installerer et HDInsight program til din klynger.
- [Installere brugerdefinerede HDInsight-programmer](hdinsight-apps-install-custom-applications.md): Lær, hvordan du installerer en ophævelse udgivet HDInsight program til HDInsight.
- [Tilpasse Linux-baserede HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster-linux.md): Lær at bruge scripthandling til at installere flere programmer.
- [Oprette Linux-baserede Hadoop klynger i HDInsight ved hjælp af Azure ressourcestyring skabeloner](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lær, hvordan du ringe til Ressourcestyring skabeloner for at oprette HDInsight klynger.
- [Brug af tomme kant knuder på HDInsight](hdinsight-apps-use-edge-node.md): Lær at bruge en tom kantnode for adgang til HDInsight klynge, teste HDInsight programmer og vært HDInsight programmer.


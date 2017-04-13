<properties
pageTitle="Tilføje Hive biblioteker under oprettelse af HDInsight klynge | Azure"
description="Lær at føje Hive biblioteker (glas filer,) til en HDInsight klynge under oprettelse af klynge."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/20/2016"
ms.author="larryfr"/>

#<a name="add-hive-libraries-during-hdinsight-cluster-creation"></a>Tilføje Hive biblioteker under oprettelse af HDInsight klynge

Hvis du har biblioteker, som du bruger ofte med Hive på HDInsight, indeholder dette dokument oplysninger om brug af en Script-handling til at indlæse før bibliotekerne under oprettelse af klynge. Dette giver bibliotekerne globalt tilgængelig i Hive (ingen grund til at bruge [Tilføje JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) for at indlæse dem).

##<a name="how-it-works"></a>Sådan fungerer det

Du kan eventuelt angive en Script-handling, der kører et script på klyngenoderne, mens de oprettes, når du opretter en klynge. Scriptet i dette dokument accepterer en enkelt parameter, som er en WASB placering, der indeholder de biblioteker (gemt som glas filer), der skal allerede indlæses.

Under oprettelse af klynge, scriptet optælles filerne, kopierer dem til den `/usr/lib/customhivelibs/` mappe på hovedet og arbejder noder, føjer dem til den `hive.aux.jars.path` egenskab i den `core-site.xml` fil. På Linux-baserede klynger den også opdateres på `hive-env.sh` filen med placeringen af filerne.

> [AZURE.NOTE] Brug af scripthandlinger i denne artikel, er bibliotekerne tilgængelig i følgende scenarier:
>
> * __Linux-baserede HDInsight__ - når du bruger __Hive kommandolinjen__, __WebHCat__ (Templeton) og __HiveServer2__.
> * __Windows-baseret HDInsight__ - når du bruger __Hive kommandolinjen__ og __WebHCat__ (Templeton).

##<a name="the-script"></a>Scriptet

__Scriptplacering__

Til __Linux-baserede klynger__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Til __Windows-baserede klynger__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Krav__

* Scriptene, der skal anvendes på både __hoved noder__ og __arbejder noder__.

* De krukker, du vil installere skal være gemt i Azure Blob-lager i en __enkelt objektbeholder__. 

* Kontoen lagerplads, der indeholder biblioteket for glas filer __skal__ være knyttet til HDInsight klyngen under oprettelse. Dette kan gøres på en af to måder:

    * Ved at være en objektbeholder på lagerplads standardkontoen for-klyngen.
    
    * Ved at være en objektbeholder på en sammenkædet lagerplads beholder. Du kan for eksempel bruge __Valgfri konfiguration__, __sammenkædede lagerplads konti__ , for at tilføje yderligere lagerplads på portalen.

* WASB stien til objektbeholderen skal være angivet som en parameter til handlingen Script. Eksempelvis hvis krukker gemmes i en objektbeholder med navnet __libs__ på en lagerplads konto med navnet __mystorage__, parameteren ville være __wasbs://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] Dette dokument forudsætter, at du har allerede oprette en lagerplads konto, blob objektbeholder og filerne, der er overført til den. 
    >
    > Hvis du ikke har oprettet en lagerplads-konto, kan du gøre dette via [Azure-portalen](https://portal.azure.com). Derefter kan du bruge et værktøj som [Azure Storage Explorer](http://storageexplorer.com/) til at oprette en ny objektbeholder i kontoen og overføre filer til den.

##<a name="create-a-cluster-using-the-script"></a>Oprette en klynge ved hjælp af script

> [AZURE.NOTE] Følgende trin Opret en ny Linux-baserede HDInsight klynge. Vælg __Windows__ som klynge OS, når du opretter klyngen for at oprette en ny Windows-baseret klynge, og brug scriptet Windows (PowerShell) i stedet for scriptet fest.
> 
> Du kan også bruge Azure PowerShell eller HDInsight .NET SDK til at oprette en klynge ved hjælp af dette script. Du kan finde flere oplysninger om brug af disse metoder, [tilpasse HDInsight klynger med scripthandlinger](hdinsight-hadoop-customize-cluster-linux.md).

1. Start klargøring en klynge ved hjælp af trinnene i [klargøring HDInsight klynger på Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), men ikke fuldføre klargøring.

2. Vælg **Scripthandlinger**på bladet **Valgfri konfiguration** , og angiv oplysninger, som vist nedenfor:

    * __Navn__: Angiv et fuldt navn for handlingen script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __Afsnit__: kontrollere denne indstilling
    * __Arbejder__: kontrollere denne indstilling.
    * __ZOOKEEPER__: Lad dette stå tomt.
    * __Parametre__: angive WASB adressen til kontoen objektbeholder og lager, der indeholder krukker. For eksempel __wasbs://libs@mystorage.blob.core.windows.net/__.

3. I bunden af **Scripthandlinger**, kan du bruge knappen **Vælg** til at gemme konfigurationen.

4. Vælg __Sammenkædede lagerplads konti__ på bladet **Valgfri konfiguration** , og vælg linket __Tilføj en lagerplads nøgle__ . Vælg den konto, lagerplads, der indeholder krukker, og derefter bruge knapperne __Vælg__ til at gemme indstillingerne og vende tilbage bladet __Valgfri konfiguration__ .

5. Bruge knappen **Vælg** i bunden af bladet **Valgfri konfiguration** til at gemme oplysningerne om valgfri konfiguration.

6. Fortsæt med klargøring klyngen, som beskrevet i [klargøring HDInsight klynger på Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Når klynge oprettelse af afsluttes, skal du vil kunne bruge krukker tilføjes via dette script fra Hive uden at bruge den `ADD JAR` sætning.

##<a name="next-steps"></a>Næste trin

Du har lært Sådan føjer du Hive-biblioteker, der er indeholdt i glas filer til en HDInsight klynge under oprettelse af klynge i dette dokument. Du kan finde flere oplysninger om at arbejde med Hive, se [Brug Hive med HDInsight](hdinsight-use-hive.md)

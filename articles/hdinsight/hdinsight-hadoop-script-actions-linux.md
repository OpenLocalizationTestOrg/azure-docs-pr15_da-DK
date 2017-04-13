<properties
    pageTitle="Script handling udvikling med Linux-baserede HDInsight | Microsoft Azure"
    description="Hvordan du tilpasser Linux-baserede HDInsight klynger med scripthandling. Scripthandlinger er en måde at tilpasse Azure HDInsight klynger ved at angive indstillinger for søgekonfiguration klynge eller installerer ekstra tjenester, funktioner eller andre programmer på klyngen. "
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="larryfr"/>

# <a name="script-action-development-with-hdinsight"></a>Script handling udvikling med HDInsight

Scripthandlinger er en måde at tilpasse Azure HDInsight klynger ved at angive indstillinger for søgekonfiguration klynge eller installerer ekstra tjenester, funktioner eller andre programmer på klyngen. Du kan bruge scripthandlinger under oprettelse af klynge eller på en igangværende klynge.

> [AZURE.NOTE] Oplysningerne i dette dokument er specifikke for Linux-baserede HDInsight klynger. Du kan finde oplysninger om brug af scripthandlinger med Windows-baserede klynger [Script handling udvikling med HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## <a name="what-are-script-actions"></a>Hvad er scripthandlinger?

Scripthandlinger er fest scripts, Azure køres på de klyngenoder til at foretage ændringer i konfigurationen eller Installér software. Handlingen script udføres som rod, og giver alle adgangsrettigheder til klyngenoderne.

Scripthandlinger kan anvendes gennem følgende metoder:

| Brug dette til at anvende et script... | Klynge oprettelse under... | På en igangværende klynge... |
| ----- |:-----:|:-----:|
| Azure-portalen | ✓ | ✓ |
| Azure PowerShell | ✓ | ✓ |
| Azure CLI | &nbsp; | ✓ |
| HDInsight .NET SDK | ✓ | ✓ |
| Azure ressourcestyring skabelon | ✓ | &nbsp; |

Du kan finde flere oplysninger om brug af disse metoder til at anvende scripthandlinger, [tilpasse HDInsight klynger ved hjælp af scripthandlinger](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Bedste fremgangsmåder til udvikling af script

Når du udvikler et brugerdefineret script til en HDInsight klynge, er der flere bedste praksis til at huske på:

- [Målrette Hadoop-version](#bPS1)
- [Målret OS-Version](#bps10)
- [Giver dig stabil links til script ressourcer](#bPS2)
- [Brug foruddefinerede kompileret ressourcer](#bPS4)
- [Sikre, at scriptet klynge tilpasning er idempotent](#bPS3)
- [Sikre høj tilgængelighed af klynge arkitekturen](#bPS5)
- [Konfigurere de brugerdefinerede komponenter for at bruge Azure Blob-lager](#bPS6)
- [Skrive oplysninger til STDOUT og STDERR](#bPS7)
- [Gemme filer som ASCII med LF linje slutningen](#bps8)
- [Bruge forsøg til at gendanne fra midlertidige fejl](#bps9)

> [AZURE.IMPORTANT] Scripthandlinger skal udføre på 60 minutter, eller de kan timeout. Under node klargøringen kørte scriptet sammen med andre installation og konfiguration processer. Konkurrencen for de ressourcer, som CPU klokkeslæt eller netværk båndbredde kan medføre scriptet til at tage længere tid at færdiggøre end i dit udviklingsmiljø.

### <a name="bPS1"></a>Målrette Hadoop-version

Forskellige versioner af HDInsight har forskellige versioner af Hadoop-tjenester og komponenter, der er installeret. Hvis dit script forventer en bestemt version af en tjeneste eller komponent, skal du kun bruge scriptet med versionen af HDInsight, der indeholder de påkrævede komponenter. Du kan finde oplysninger om komponent versioner, der følger med HDInsight ved hjælp af [HDInsight komponent versionsstyring](hdinsight-component-versioning.md) dokumentet.

###<a name="bps10"></a>Målret OS-version

Linux-baserede HDInsight er baseret på Ubuntu Linux-fordelingen. Forskellige versioner af HDInsight skal have forskellige versioner af Ubuntu, som kan effekt, hvordan dit script skal reagere. For eksempel HDInsight 3,4 og tidligere er baseret på Ubuntu versioner, der anvender Upstart. Version 3.5 er baseret på Ubuntu 16.04, som bruger Systemd. Systemd og Upstart er afhængige af forskellige kommandoer, så dit script skal skrives til at arbejde med begge.

En anden vigtige forskel mellem HDInsight 3.4 og 3.5 er det `JAVA_HOME` nu peger på Java 8.

Du kan kontrollere OS-version ved hjælp af `lsb_release`. De følgende kodestykker fra farvetone Installer scriptet demonstrerer Sådan afgør, om scriptet kører på Ubuntu 14 eller 16:

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi
    ...
    if [[ $OS_VERSION == 16* ]]; then
        echo "Using systemd configuration"
        systemctl daemon-reload
        systemctl stop webwasb.service    
        systemctl start webwasb.service
    else
        echo "Using upstart configuration"
        initctl reload-configuration
        stop webwasb
        start webwasb
    fi
    ...
    if [[ $OS_VERSION == 14* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
    elif [[ $OS_VERSION == 16* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    fi

Du kan finde det fulde script, der indeholder disse kodestykker på https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Du kan finde versionen af Ubuntu, der bruges af HDInsight [HDInsight komponentversion](hdinsight-component-versioning.md) dokumentet.

Se [Systemd for Upstart brugere](https://wiki.ubuntu.com/SystemdForUpstartUsers)for at forstå forskellene mellem Systemd og Upstart.

### <a name="bPS2"></a>Giver dig stabil links til script ressourcer

Du skal sikre dig, scripts og ressourcer, der bruges af scriptet forbliver tilgængelige i hele levetiden for klyngen, og versionerne af filerne ikke ændres under hele. Disse ressourcer er påkrævet, hvis nye noder føjes til klyngen under skalering handlinger.

Den bedste fremgangsmåde er at hente og arkivere alt i en Azure-lager konto på dit abonnement.

> [AZURE.IMPORTANT] Kontoen lagerplads bruges skal være lagerplads standardkontoen for klyngen eller en offentlig, skrivebeskyttet objektbeholder på en anden konto i lagerplads.

For eksempel gemmes eksemplerne leveres af Microsoft i [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) lagerplads konto, som er en offentlig skrivebeskyttet beholder vedligeholdelsen af HDInsight-teamet.

### <a name="bPS4"></a>Brug foruddefinerede kompileret ressourcer

For at reducere tid, tager det at køre scriptet, skal du undgå handlinger, som samle ressourcer fra kildekode. I stedet, før samle ressourcerne, og gemme den binære version i Azure Blob-lager, så den kan hurtigt hentes til klyngen fra dit script.

### <a name="bPS3"></a>Sikre, at scriptet klynge tilpasning er idempotent

Scripts, som skal være udviklet til at være idempotent på den måde, som hvis scriptet er kørte flere gange, den skal Sørg for, at klyngen returneres til den samme tilstand, hver gang den er stødte.

Eksempelvis hvis et brugerdefineret script installerer et program ved /usr/local/bin på først køre og derefter på hver efterfølgende Kør scriptet skal kontrollere, om programmet allerede findes på /usr/local/bin placeringen, før du fortsætter med andet trin i scriptet.

### <a name="bPS5"></a>Sikre høj tilgængelighed af klynge arkitekturen

Linux-baserede HDInsight klynger giver to hoved noder, der er aktive i klyngen og script handlinger er kørte for begge noder. Hvis du har installeret komponenterne forventer kun én hoved node, skal du designe et script, som kan kun installeres komponenten på en af to hoved noderne i klyngen.

> [AZURE.IMPORTANT] Standard services er installeret som en del af HDInsight er designet til at skifte mellem de to hovedsæde noder efter behov, men denne funktionalitet er ikke udvidet til brugerdefinerede komponenter, der er installeret via script ctions. Hvis du har brug for de komponenter, der er installeret gennem et script handlingen skal være meget tilgængelig, skal du implementere din egen failover ordning, der bruger de to tilgængelige hovedsæde noder.

### <a name="bPS6"></a>Konfigurere de brugerdefinerede komponenter for at bruge Azure Blob-lager

Komponenter, som du har installeret på klyngen muligvis en standardkonfiguration, der bruger Hadoop-distribueret fil System (HDFS) lagerplads. HDInsight bruger Azure Blob Storage (WASB) som standard-lagerplads. Dette giver et HDFS kompatible filsystem, fortsætter data, selvom klyngen slettes. Du skal konfigurere komponenter, du har installeret for at bruge WASB i stedet for HDFS.

For eksempel kopierer følgende filen giraph examples.jar fra det lokale filsystem til WASB:

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Skrive oplysninger til STDOUT og STDERR

Oplysninger, der skrives til STDOUT og STDERR under udførelse af script logføres og kan ses ved hjælp af webdelen Ambari brugergrænseflade.

> [AZURE.NOTE] Ambari vil kun være tilgængelige, hvis klyngen er blevet oprettet. Hvis du bruger en scripthandling under oprettelse af klynge, og oprettelse af mislykkes, kan du se afsnittet fejlfinding [tilpasse HDInsight klynger ved hjælp af script handlingen](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) for at få andre måder at få adgang til registrerede oplysninger.

De fleste funktioner og -pakker skriver allerede oplysninger til STDOUT og STDERR, men du vil tilføje yderligere logføring. At sende tekst til STDOUT Brug `echo`. Eksempel:

        echo "Getting ready to install Foo"

Som standard `echo` sender strengen til STDOUT. Hvis du vil videresende den til STDERR, skal du tilføje `>&2` før `echo`. Eksempel:

        >&2 echo "An error occurred installing Foo"

Dette omdirigerer oplysninger, der sendes til STDOUT (1, som er standard, så ikke vises her,) til STDERR (2). Du kan finde flere oplysninger om EY omdirigering, [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Du kan finde flere oplysninger om visning af oplysninger, der er logget af scripthandlinger, se [tilpasse HDInsight klynger ved hjælp af scripthandling](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

###<a name="bps8"></a>Gemme filer som ASCII med LF linje slutningen

Fest scripts skal gemmes som ASCII-format, med linjer, der er afsluttet af LF. Hvis filerne er gemt som UTF-8, hvilket kan inkludere et Byte rækkefølge mærke i starten af filen eller ved slutningen af linjen af CRLF, der er fælles for Windows redaktører, mislykkes scriptet med fejl, der ligner følgende:

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

###<a name="bps9"></a>Bruge forsøg til at gendanne fra midlertidige fejl

Når du henter filer, installere pakker ved hjælp af Hovedgaden få eller andre handlinger, der sender data via internettet, kan handlingen mislykkes på grund af midlertidige netværk fejl. For eksempel muligvis remote ressourcen du kommunikerer med ved at fejlbehæftede over til en sikkerhedskopi node.

For at gøre dit script tolerant for forbigående fejl, kan du implementere forsøg. Følgende er et eksempel på en funktion, der kører en kommando, der overføres til den og (Hvis kommandoen mislykkes, skal) prøv igen op til tre gange. Det venter to sekunder mellem hvert forsøg igen.

    #retry
    MAXATTEMPTS=3

    retry() {
        local -r CMD="$@"
        local -i ATTMEPTNUM=1
        local -i RETRYINTERVAL=2

        until $CMD
        do
            if (( ATTMEPTNUM == MAXATTEMPTS ))
            then
                    echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                    return 1
            else
                    echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                    sleep $(( RETRYINTERVAL ))
                    ATTMEPTNUM=$ATTMEPTNUM+1
            fi
        done
    }

Følgende er eksempler på brug af denne funktion.

    retry ls -ltr foo

    retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh

## <a name="helpermethods"></a>Hjælper metoder til brugerdefinerede scripts

script handling hjælper metoder er værktøjer, du kan bruge under skrivning brugerdefinerede scripts. Disse er defineret i [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh)og kan medtages i dine scripts ved hjælp af følgende:

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

Dette gør følgende hjælpefunktioner tilgængelig for i dit script:

| Hjælper brugen | Beskrivelse |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Henter en fil fra kilde URL-adressen til den angivne filsti. Som standard overskriver den ikke en eksisterende fil. |
| `untar_file TARFILE DESTDIR` | Henter en tjære-fil (ved hjælp af `-xf`,) til destinationsmappen. |
| `test_is_headnode` | Hvis stødte på en klynge hoved node, returnerer 1; Ellers, 0. |
| `test_is_datanode` | Hvis den aktuelle node er en datanode (arbejder), returnerer værdien 1 Ellers, 0. |
| `test_is_first_datanode` | Hvis den aktuelle node er den første (arbejder) datanode (kaldet workernode0), returnerer værdien 1 Ellers, 0. |
| `get_headnodes` | Returnerer det fulde domænenavn for headnodes i klyngen. Navne er kommasepareret. Der returneres en tom streng om fejl. |
| `get_primary_headnode` | Bliver det fulde domænenavn for den primære headnode. Der returneres en tom streng om fejl. |
| `get_secondary_headnode` | Bliver det fulde domænenavn for den sekundære headnode. Der returneres en tom streng om fejl. |
| `get_primary_headnode_number` | Henter det numeriske suffikset i den primære headnode. Der returneres en tom streng om fejl. |
| `get_secondary_headnode_number` | Får numerisk suffikset i det sekundære headnode. Der returneres en tom streng om fejl. |

## <a name="commonusage"></a>Almindelige brugsmønstre

Dette afsnit indeholder vejledning om implementering af nogle af de almindelige brugsmønstre, som du kan støde på, mens du skriver et brugerdefineret script.

### <a name="passing-parameters-to-a-script"></a>Overføre parametre til et script

I nogle tilfælde kan dit script kræve parametre. Der kan for eksempel op administratoradgangskode for-klyngen for at hente oplysninger fra Ambari REST-API.

Parametre, der overføres til scriptet kaldes _position parametre_og er tildelt til `$1` for den første parameter `$2` for anden, og så-on. `$0`indeholder navnet på selve script.

Værdier, der overføres til scriptet som parametre skal stå i enkelte anførselstegn ('), så den sendte værdi behandles som en konstant, og særbehandling ikke reageret på inkluderede tegn som f.eks '!'.

### <a name="setting-environment-variables"></a>Indstillingen miljøvariabler

Angive en miljøvariablen er udført af følgende:

    VARIABLENAME=value

Hvor VARIABLENAME er navnet på variablen. For at få adgang til variablen efter dette, bruge `$VARIABLENAME`. Du vil for eksempel bruge følgende for at tildele en værdi, der leveres af en parameter for position, som en navngivet adgangskode miljøvariablen:

    PASSWORD=$1

Efterfølgende adgang til oplysninger, der kan derefter bruge `$PASSWORD`.

Der findes kun miljøvariabler, der er angivet i scriptet i omfanget af scriptet. I nogle tilfælde kan du muligvis tilføje system bred miljøvariabler, der bevares, når scriptet er afsluttet. Dette er som regel så brugere skal oprette forbindelse til klynge via SSH kan bruge de komponenter, der er installeret af dit script. Du kan udføre dette ved at tilføje miljøvariablen til `/etc/environment`. For eksempel følgende tilføjer __HADOOP\_CONF\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Adgang til steder, hvor de brugerdefinerede scripts er gemt

Scripts, som bruges til at tilpasse en klynge skal enten være i lagerplads standardkontoen for klynge eller, hvis på en anden konto lager i en offentlig skrivebeskyttet beholder. Hvis dit script får adgang til ressourcer, der findes et andet sted disse også skal være i en offentligt tilgængelig (mindst offentlige skrivebeskyttet). For eksempel kan du vil overføre en fil til den klynge ved hjælp af `download_file`.

Lagring af filen på en Azure-lager-konto, der er tilgængelige ved klynge (såsom lagerplads standardkontoen), der giver hurtig adgang, som denne lagerplads er i Azure netværket.

### <a name="checking-the-operating-system-version"></a>Kontrollere operativsystemversionen

Forskellige versioner af HDInsight skal have bestemte versioner af Ubuntu. Der kan være forskel mellem OS versioner, skal du kontrollere for i dit script. For eksempel skal du muligvis installere en binær fil, der er knyttet til versionen af Ubuntu.

Hvis du vil kontrollere OS-versionen, skal bruge `lsb_release`. For eksempel viser følgende, hvordan du henviser til en anden tjære fil afhængigt af OS-versionen:

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi

## <a name="deployScript"></a>Tjekliste til implementering af en scripthandling

Her er de trin, vi fandt, når du forbereder at implementere disse scripts:

- Placere de filer, der indeholder de brugerdefinerede scripts på et sted, der er tilgængeligt ved klyngenoderne under installationen. Det kan være en standard eller ekstra lagerplads konti, der er angivet på tidspunktet for installation af klynge eller eventuelle andre objektbeholder til lagring af offentligt tilgængelige.

- Tilføj Kontroller til at kontrollere, at udføre de impotently, så scriptet kan udføres flere gange på den samme node scripts.

- Brug en midlertidig fil directory /tmp beholde de hentede filer, der bruges af scriptene og derefter rydde dem op, når scripts, som har udført.

- I tilfælde af, at indstillingerne OS på domæneniveau eller Hadoop-tjenesten konfigurationsfiler er blevet ændret, kan du vil genstarte HDInsight tjenester, så de kan vælge en hvilken som helst OS niveau indstillinger, som de miljøvariabler, der er angivet i scriptene.

## <a name="runScriptAction"></a>Sådan køres en scripthandling

Du kan bruge scripthandlinger til at tilpasse HDInsight klynger ved hjælp af portalen Azure, Azure PowerShell, Azure ressource Manager (ARM) skabeloner eller HDInsight .NET SDK. Finde en vejledning, se, [hvordan du bruger scripthandling](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Eksempler på brugerdefinerede scripts

Microsoft yder eksempelscripts for at installere komponenter på en HDInsight klynge. Eksempel på scripts og oplysninger om, hvordan du bruger dem findes på linkene nedenfor:

- [Installere og bruge farvetone på HDInsight klynger](hdinsight-hadoop-hue-linux.md)
- [Installere og bruge R på HDInsight Hadoop klynger](hdinsight-hadoop-r-scripts-linux.md)
- [Installere og bruge Solr på HDInsight klynger](hdinsight-hadoop-solr-install-linux.md)
- [Installere og bruge Giraph på HDInsight klynger](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE] De dokumenter, der er sammenkædet ovenfor er specifikke for Linux-baserede HDInsight klynger. En scripts, der fungerer sammen med Windows-baseret HDInsight, se [Script handling udvikling med HDInsight (Windows)](hdinsight-hadoop-script-actions.md) eller bruge linkene tilgængelige øverst på hver artikel.

##<a name="troubleshooting"></a>Fejlfinding i forbindelse med

Følgende er en fejl, der kan opstå, når du bruger scripts, som du har udviklet:

__Error__: `$'\r': command not found`. Nogle gange efterfulgt af `syntax error: unexpected end of file`.

_Årsag_: Denne fejl opstår, når linjerne i et script, der slutter med CRLF. UNIX-systemer forventer kun LF som linje slutningen.

Dette problem opstår oftest når scriptet er skrevet på et Windows-miljø, som den CRLF er et almindelige linjer, der slutter for mange tekst editors i Windows.

_Løsning_: Hvis det er en indstilling i tekstredigeringsprogrammet, Vælg Unix-format eller LF til slutningen af linjen. Du kan også bruge følgende kommandoer på et Unix-system til at ændre CRLF til en LF:

> [AZURE.NOTE] Følgende kommandoer er stort set svarer, de skal ændre linjeafslutninger CRLF til LF. Vælg en baseret på de funktioner, der er tilgængelige på computeren.

| Kommandoen | Noter |
| ------- | ----- |
| `unix2dos -b INFILE` | Den oprindelige fil, der sikkerhedskopieres med en. BAK lokalnummer |
| `tr -d '\r' < INFILE > OUTFILE` | OUTFILE skal indeholde en version med kun LF slutningen |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Dette ændrer filen direkte uden at oprette en ny fil |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` | OUTFILE skal indeholde en version med kun LF slutningen.

__Error__: `line 1: #!/usr/bin/env: No such file or directory`.

_Årsag_: Denne fejl opstår, når scriptet blev gemt som UTF-8 med en Byte rækkefølge Marker (Stykliste).

_Løsning_: Gem filen som ASCII- eller som UTF-8 uden en Stykliste. Du kan også bruge følgende kommando på et Linux eller Unix system til at oprette en ny fil uden Styklisten:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Erstat __INFILE__ med den fil, der indeholder Styklisten til kommandoen ovenfor. __OUTFILE__ skal være et nyt filnavn, som skal indeholde scriptet uden Styklisten.

## <a name="seeAlso"></a>Næste trin

* Lær, hvordan du kan [tilpasse HDInsight klynger ved hjælp af scripthandling](hdinsight-hadoop-customize-cluster-linux.md)

* Bruge [HDInsight .NET SDK reference](https://msdn.microsoft.com/library/mt271028.aspx) til at få mere at vide om oprettelse af .NET-programmer, der administrerer HDInsight

* Brug [HDInsight REST-API](https://msdn.microsoft.com/library/azure/mt622197.aspx) til at se, hvordan du bruger RESTEN til at udføre administration handlinger på HDInsight klynger.

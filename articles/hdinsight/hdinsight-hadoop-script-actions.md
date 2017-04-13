<properties
    pageTitle="Script handling udvikling med HDInsight | Microsoft Azure"
    description="Lær, hvordan du tilpasser Hadoop klynger med scripthandling. Scripthandling kan bruges til at installere yderligere software, der kører på en Hadoop klynge eller ændre konfigurationen af programmer, der er installeret på en klynge."
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
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Udarbejd scripthandling scripts til HDInsight Windows-baserede klynger

Lær, hvordan du kan skrive scripthandling scripts til HDInsight. Du kan finde oplysninger om brug af scripthandling scripts [tilpasse HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster.md). Du kan finde den samme artikel skrevet til Linux-baserede HDInsight klynger, [udvikle scripthandling scripts til HDInsight](hdinsight-hadoop-script-actions-linux.md).

> [AZURE.NOTE] Oplysningerne i dette dokument er specifikke for Windows-baseret HDInsight klynger. Du kan finde oplysninger om brug af scripthandlinger med Windows-baserede klynger [Script handling udvikling med HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).


Scripthandling kan bruges til at installere yderligere software, der kører på en Hadoop klynge eller ændre konfigurationen af programmer, der er installeret på en klynge. Scripthandlinger er scripts, der kører på klyngenoderne, når HDInsight klynger er installeret, og de er udført, når noderne i klyngen afslutte konfigurationen af HDInsight. Handlingen script udføres under system administratortilladelser konto og giver alle adgangsrettigheder til klyngenoderne. Hver klynge kan leveres med en liste over scripthandlinger skal udføres i den rækkefølge, hvori de er angivet.

> [AZURE.NOTE] Hvis du får vist følgende fejlmeddelelse:
>
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> Det skyldes, at du ikke medtager hjælper metoderne.  Se [hjælper metoder til brugerdefinerede scripts](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

## <a name="sample-scripts"></a>Eksempel på scripts

Handlingen Script er Azure PowerShell-script til oprettelse af HDInsight klynger på Windows-operativsystemet. Følgende er en stikprøve script til konfigurere websted konfigurationsfiler:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

Scriptet kræver fire parametre, filnavnet konfiguration, den egenskab, du vil ændre den værdi, du vil angive, og en beskrivelse. Eksempel:

    hive-site.xml hive.metastore.client.socket.timeout 90

Disse parametre angivet værdien hive.metastore.client.socket.timeout til 90 i filen hive-site.xml.  Standardværdien er 60 sekunder.

Dette eksempelscript kan også findes på [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight indeholder flere scripts for at installere yderligere komponenter på HDInsight klynger:

Navn | Script
----- | -----
**Installere knallertmotor** | https://hdiconfigactions.BLOB.Core.Windows.NET/sparkconfigactionv03/Spark-Installer-v03.ps1. Se [installation og brug sætte gang på HDInsight klynger][hdinsight-install-spark].
**Installere R** | https://hdiconfigactions.BLOB.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Se [installation og brug R på HDInsight klynger][hdinsight-r-scripts].
**Installere Solr** | https://hdiconfigactions.BLOB.Core.Windows.NET/solrconfigactionv01/solr-Installer-v01.ps1. Se [installation og brug Solr på HDInsight klynger](hdinsight-hadoop-solr-install.md).
- **Installere Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-v01.ps1. Se [installation og brug Giraph på HDInsight klynger](hdinsight-hadoop-giraph-install.md).

Scripthandling kan installeres fra Azure-portalen, Azure PowerShell eller ved hjælp af HDInsight .NET SDK.  Du kan finde flere oplysninger, se [tilpasse HDInsight klynger ved hjælp af Script handlingen][hdinsight-cluster-customize].

> [AZURE.NOTE] Eksempel-scripts fungerer kun med HDInsight klynge version 3.1 eller nyere. Du kan finde flere oplysninger om HDInsight klynge versioner, [HDInsight klynge versioner](hdinsight-component-versioning.md).





## <a name="helper-methods-for-custom-scripts"></a>Hjælper metoder til brugerdefinerede scripts

Script handling hjælper metoder er værktøjer, du kan bruge under skrivning brugerdefinerede scripts. Disse er defineret i [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)og kan medtages i dine scripts ved hjælp af følgende:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Her er de hjælper metoder, der leveres af dette script:

Hjælper metode | Beskrivelse
-------------- | -----------
**Gem HDIFile** | Hente en fil fra den angivne URI Uniform Resource Identifier () til en placering på den lokale disk, der er tilknyttet noden Azure VM, der er tildelt til klyngen.
**Udvid HDIZippedFile** | Udpakke en zip-fil.
**Kalde HDICmdScript** | Køre et script fra cmd.exe.
**Skriv HDILog** | Skrive output fra den brugerdefineret script, der bruges til en scripthandling.
**Get-tjenester** | Få en liste over tjenester, der kører på computeren, hvor scriptet udfører.
**Get-tjenesten** | Med navnet på bestemte tjenesten som input, få detaljerede oplysninger til en bestemt service (nyt navn, proces-ID, tilstand, osv.) på den computer, hvor scriptet udfører.
**Get-HDIServices** | Få en liste over HDInsight services kører på computeren, hvor scriptet udfører.
**Get-HDIService** | Med bestemte HDInsight navnet på tjenesten som input, få detaljerede oplysninger til en bestemt service (nyt navn, proces-ID, tilstand, osv.) på den computer, hvor scriptet udfører.
**Get-ServicesRunning** | Få en liste over tjenester, der kører på computeren, hvor scriptet udfører.
**Get-ServiceRunning** | Kontrollér, om en bestemt service (ved navn) kører på computeren, hvor scriptet udfører.
**Get-HDIServicesRunning** | Få en liste over HDInsight services kører på computeren, hvor scriptet udfører.
**Get-HDIServiceRunning** | Kontrollér, om en bestemt HDInsight service (ved navn) kører på computeren, hvor scriptet udfører.
**Get-HDIHadoopVersion** | Få versionen af Hadoop, der er installeret på den computer, hvor scriptet udfører.
**Test-IsHDIHeadNode** | Kontrollér, om den computer, hvor scriptet udfører vises en hoved node.
**Test-IsActiveHDIHeadNode** | Kontrollér, om den computer, hvor scriptet udfører vises en aktive hoved node.
**Test-IsHDIDataNode** | Kontrollér, om den computer, hvor scriptet udfører vises en datanode.
**Rediger HDIConfigFile** | Redigere config filer hive-site.xml, core-site.xml, hdfs site.xml, mapred site.xml eller garn site.xml.


## <a name="best-practices-for-script-development"></a>Bedste fremgangsmåder til udvikling af script

Når du udvikler et brugerdefineret script til en HDInsight klynge, er der flere bedste praksis til at huske på:

- Kontrollér, om Hadoop-versionen

    Kun HDInsight version 3.1 (Hadoop 2.4) og over understøttelse af brug af scripthandling til at installere brugerdefinerede komponenter på en klynge. I dit eget script, skal du bruge metoden **Get-HDIHadoopVersion** hjælper Hadoop-versionen, før du fortsætter med at udføre andre opgaver i scriptet kontrolleres.


- Giver dig stabil links til script ressourcer

    Brugere skal sikre dig, at alle scriptene og andre elementer, der bruges i tilpasning af en klynge forbliver tilgængelige i hele levetiden for klyngen og, at versionerne af filerne ikke ændres for varighed. Disse ressourcer er påkrævet, hvis igen imaging af knuder på klyngen er påkrævet. Den bedste fremgangsmåde er at hente og arkivere alt i en lagerplads-konto, som brugeren kontrolelementer. Det kan være standardkontoen lagerplads eller en af de ekstra lagerplads konti, der er angivet på tidspunktet for installation til en tilpasset klynge.
    Tilpasset klynge eksempler i tænding og R leveres i dokumentationen til, for eksempel har vi gjort en lokal kopi af ressourcerne, der i denne lagerplads konto: https://hdiconfigactions.blob.core.windows.net/.


- Sikre, at scriptet klynge tilpasning er idempotent

    Du skal forventer, at en HDInsight klynge noder bliver igen scannede klynge levetid. Klynge tilpasning scriptet køres, når en klynge er igen scannede. Dette script skal være udviklet til at være idempotent i mening, efter igen imaging scriptet skal sikre, at klyngen returneres til den samme tilpasset tilstand, den var i, lige efter scriptet kørte for første gang, når klyngen oprindeligt blev oprettet. Eksempelvis hvis et brugerdefineret script installeret et program ved D:\AppLocation på først køre og derefter på hver efterfølgende Kør på imaging igen, scriptet skal kontrollere, om programmet findes på D:\AppLocation placeringen, før du fortsætter med andet trin i scriptet.


- Installere brugerdefinerede komponenter optimal placering

    Når klyngenoder er igen scannede, kan det C:\ ressource drev og D:\ systemdrevet være igen formateret, hvilket resulterer i tab af data og programmer, der blev installeret på disse drev. Det kan også ske, hvis en Azure VM (virtual machine) node, der er en del af klyngen går ned og erstattes af en ny node. Du kan installere komponenter på D:\ drev eller på C:\apps placering på klyngen. Alle andre steder på drevet, C:\ er reserveret. Angiv den placering, hvor programmer eller -biblioteker, der skal installeres i scriptet klynge tilpasning.


- Sikre høj tilgængelighed af klynge arkitekturen

    HDInsight har en aktiv / passiv arkitektur for høj tilgængelighed, hvor en hoved node er i active-tilstand (hvor HDInsight services kører) og den andre hoved node er i standby (i hvilke HDInsight tjenester ikke kører). Noderne skifte aktiv og passiv tilstand, hvis HDInsight services er afbrudt. Hvis handlingen script bruges til at installere tjenester på begge hoved noder til høj tilgængelighed, opmærksom på, at HDInsight failover ordningen ikke vil kunne automatisk skifte over disse bruger installerede tjenester. Så bruger-installerede tjenester på HDInsight hoved noder, der forventes at være meget tilgængeligt skal enten have deres egne failover ordning Hvis i aktiv / passiv tilstand eller være i aktiv-aktiv tilstand.

    Kommandoen HDInsight scripthandling kører på begge hoved noder, når der er angivet i afsnit-node rollen som en værdi i parameteren *ClusterRoleCollection* . Så du når du designer en brugerdefineret script, skal du kontrollere, at dit script er bekendt med denne konfiguration. Du skal ikke støder på problemer, hvor de samme tjenester er installeret og startet på begge noderne hoved og de ender konkurrere med hinanden. Desuden være opmærksom på, data vil gå tabt under imaging igen, så der er installeret via scripthandling skal være tolerant for disse hændelser. Programmer bør være designet til at arbejde med meget tilgængelige data, som er fordelt over mange noder. Bemærk, at op til 1/5 for knuderne i en klynge, kan afbildes igen på samme tid.


- Konfigurere de brugerdefinerede komponenter for at bruge Azure Blob-lager

    De brugerdefinerede komponenter, du installerer på klyngenoderne muligvis en standardkonfiguration til at bruge Hadoop-distribueret fil System (HDFS) lagerplads. Du skal ændre konfiguration for at bruge Azure Blob-lager i stedet. Filsystemet HDFS får formateret på en klynge igen billede, og du vil miste de data, der er gemt her. Bruge Azure Blob-lager i stedet sikrer, at dine data bevares.

## <a name="common-usage-patterns"></a>Almindelige brugsmønstre

Dette afsnit indeholder vejledning om implementering af nogle af de almindelige brugsmønstre, som du kan støde på, mens du skriver et brugerdefineret script.

### <a name="configure-environment-variables"></a>Konfigurere miljøvariabler

I script handling udvikling, vil du ofte føler at skulle angive miljøvariabler. For eksempel er et mest sandsynlige scenarie, når du henter en binær fra et eksternt websted, installere det på klyngen og tilføjer placeringen af hvor den er installeret til miljøvariablen 'PATH'. Følgende kodestykke viser, hvordan du kan indstille miljøvariabler i brugerdefineret script.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Denne sætning indstiller miljøvariablen **MDS_RUNNER_CUSTOM_CLUSTER** til værdien 'true' og også angiver omfanget af denne variablen skal være hele computeren. Nogle gange er det vigtigt, miljøvariabler er angivet i området relevante – maskine eller bruger. Se [her] [ 1] kan finde flere oplysninger om konfiguration af miljøvariabler.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Adgang til steder, hvor de brugerdefinerede scripts er gemt

Scripts, som bruges til at tilpasse en klynge skal enten være i lagerplads standardkontoen for-klyngen eller i en offentlig skrivebeskyttet objektbeholder på en anden konto i lagerplads. Hvis dit script får adgang til ressourcer, der findes et andet sted disse skal være i en offentligt tilgængelig (mindst offentlige skrivebeskyttet). For eksempel kan du få adgang til en fil og gemme den ved hjælp af kommandoen SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

I dette eksempel skal du sikre dig, objektbeholderen 'somecontainer' i lagerplads konto 'somestorageaccount' er offentligt tilgængelig. Ellers skal scriptet Udløs en undtagelse ikke fundet og mislykkes.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Overføre parametre til Cmdletten Tilføj AzureRmHDInsightScriptAction

Hvis du vil overføre flere parametre til Cmdletten Tilføj AzureRmHDInsightScriptAction, skal du formatere strengværdien indeholde alle parametre for scriptet. Eksempel:

    "-CertifcateUri wasbs:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

eller

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Udløs undtagelse for installation mislykkedes klynge

Hvis du vil få præcist besked om, at klynge tilpasning lykkedes ikke som forventet, er det vigtigt at udløse en undtagelse og mislykkes klynge oprettelse. Du kan for eksempel vil til at behandle en fil, hvis den findes, og håndtere fejl sagen, hvor filen ikke findes. Dette vil sikre, at scriptet afsluttet problemfrit og tilstanden for klyngen kendes korrekt. Følgende kodestykke giver et eksempel på, hvordan dette:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

Hvis filen ikke fandtes, i denne kodestykket medfører det en tilstand, hvor scriptet faktisk afslutter problemfrit efter udskrivning af en fejlmeddelelse om, og klyngen når kører tilstand med det "" fuldført processen med tilpasning af klynge. Hvis du vil have præcist besked om, at klynge tilpasning grundlæggende lykkedes ikke som forventet på grund af en manglende fil, er det mere passende Udløs en undtagelse og mislykkes trinnet klynge tilpasning. Du skal bruge det følgende eksempel kodestykke i stedet for at opnå dette.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Tjekliste til implementering af en scripthandling
Her er de trin, vi fandt, når du forbereder at implementere disse scripts:

1. Placere de filer, der indeholder de brugerdefinerede scripts på et sted, der er tilgængeligt ved klyngenoderne under installationen. Det kan være en standard eller ekstra lagerplads konti, der er angivet på tidspunktet for installation af klynge eller eventuelle andre objektbeholder til lagring af offentligt tilgængelige.
2. Tilføj Kontroller til at kontrollere, at de udføre idempotently, så scriptet kan udføres flere gange på den samme node scripts.
3. Bruge **Skriv Output** Azure PowerShell-cmdlet til at udskrive til STDOUT samt STDERR. Brug ikke **Skrive-vært**.
4. Brug af en midlertidig filmappe, som $env: TEMP at beholde den hentede fil, der bruges af scriptene og derefter rydde dem op, når scripts, som har udført.
5. Installere brugerdefinerede software kun på D:\ eller C:\apps. Andre steder på C:-drevet, der ikke bør benyttes, som de er reserveret. Bemærk, at installere filer på C:-drevet uden for mappen C:\apps resultere i installationsprogrammet fejl under igen billeder af noden.
6. I tilfælde af, at indstillingerne OS på domæneniveau eller Hadoop-tjenesten konfigurationsfiler er blevet ændret, kan du vil genstarte HDInsight tjenester, så de kan vælge en hvilken som helst OS niveau indstillinger, som de miljøvariabler, der er angivet i scriptene.

## <a name="debug-custom-scripts"></a>Fejlfinding af brugerdefinerede scripts

Fejllogge script gemmes sammen med andre output i lagerplads standardkontoen, du har angivet for klynge på oprettelsen. Logfiler er gemt i en tabel med navnet *u < \cluster-name-fragment >< \time-stamp > setuplog*. Dette er aggregeret logfiler, der har poster fra alle knuderne (hoved node og arbejder noder), som scriptet kører i klyngen.
En nem måde at kontrollere loggene på er at bruge HDInsight værktøjer til Visual Studio. For at installere værktøjerne, se [Introduktion til brug af Visual Studio Hadoop værktøjer til HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio)

**Kontrollere logfilen ved hjælp af Visual Studio**

1. Åbne Visual Studio.
2. Klik på **Vis**, og klik derefter på **Server Explorer**.
3. Højreklik på "Azure", skal du klikke på Opret forbindelse til **Microsoft Azure abonnementer**, og derefter angive dine legitimationsoplysninger.
4. Udvid **lagerplads**, udvide kontoen Azure-lager, der bruges som standard-filsystemet, udvid **Tables**, og dobbeltklik derefter på tabelnavnet.


Du kan også remote i klyngenoderne for at se de begge STDOUT og STDERR for brugerdefinerede scripts. Logge på hver node er specifikke kun for denne node og er logget på **C:\HDInsightLogs\DeploymentAgent.log**. Disse logfiler optage alle output fra brugerdefineret script. Et eksempel log kodestykke til handlingen knallertmotor script ser sådan ud:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


I denne logfil er det klart, gnister script handlingen er udført på VM med navnet HEADNODE0 og, ingen undtagelser blev udløst under kørsel.

Hvis der opstår en udførelse af fejl, der beskriver det output også være indeholdt i denne logfil. Oplysningerne i disse logfiler skal være nyttig i forbindelse med fejlfinding af problemer med script, der kan opstå.


## <a name="see-also"></a>Se også

- [Tilpasse HDInsight klynger ved hjælp af scripthandling][hdinsight-cluster-customize]
- [Installere og bruge knallertmotor på HDInsight klynger][hdinsight-install-spark]
- [Installere og bruge R på HDInsight klynger][hdinsight-r-scripts]
- [Installation og brug Solr på HDInsight klynger](hdinsight-hadoop-solr-install.md).
- [Installation og brug Giraph på HDInsight klynger](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx

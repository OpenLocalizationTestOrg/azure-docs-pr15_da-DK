<properties
pageTitle="Begrænse HDInsight adgang til data ved hjælp af delt Access signaturer"
description="Lær at bruge delte signaturer i Access til at begrænse HDInsight adgang til data, der er gemt i Azure lagerplads BLOB."
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
ms.date="10/11/2016"
ms.author="larryfr"/>

#<a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-with-hdinsight"></a>Brug Azure lagerplads delt Access signaturer til at begrænse adgangen til data med HDInsight

HDInsight bruger Azure lagerplads BLOB til lagring af data. Mens HDInsight skal have fuld adgang til blob bruges som standard lagerplads for-klyngen, kan du begrænse tilladelser på data, der er gemt i andre blob, der anvendes af klyngen. Du kan for eksempel vil skrivebeskytte nogle data. Du kan gøre dette ved hjælp af delt Access signaturer.

Delte Access signaturer (SAS) er en funktion af Azure lagerplads konti, der gør det muligt at begrænse adgangen til data. For eksempel give skrivebeskyttet adgang til data. I dette dokument, skal du lære, hvordan du anvender SAS til at aktivere læse- og listen kun adgang til en blob objektbeholder fra HDInsight.

##<a name="requirements"></a>Krav

* Et Azure-abonnement

* C# eller Python. C# eksempel-kode er angivet en Visual Studio-løsning.

    * Visual Studio skal være version 2013 eller 2015
    
    * Python skal være version 2,7 eller nyere

* En Linux-baserede HDInsight klynge eller [Azure PowerShell] [ powershell] -Hvis du har en eksisterende Linux-baserede klynge, du kan bruge Ambari til at tilføje en signatur delt adgang til klyngen. Hvis ikke, du kan bruge Azure PowerShell til at oprette en ny klynge og tilføje en delt Access signatur under oprettelse af klynge.

* Eksempel-filer fra [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Denne lager har følgende:

    * Et Visual Studio-projekt, der kan oprette en objektbeholder til lagring, gemte politik og SAS til brug sammen med HDInsight
    
    * Et Python script, der kan oprette en objektbeholder til lagring, gemte politik og SAS til brug sammen med HDInsight
    
    * Et PowerShell-script, der kan oprette en ny HDInsight klynge og Konfigurer den til at bruge Sikkerhedstilknytningerne.

##<a name="shared-access-signatures"></a>Delt adgang signaturer

Der er to former for delt Access signaturer:

* Ad hoc-: starttidspunkt, udløbstid og tilladelser til Sikkerhedstilknytningerne er alle angivne på SAS URI (eller implicit i de tilfælde, hvor starttidspunkt udelades).

* Gemt access-politik: en lagrede access-politik er defineret på objektbeholderen ressource - en blob objektbeholder, en tabel, kø eller filshare - og kan bruges til at administrere begrænsninger til én eller flere delt adgang signaturer. Når du knytter en SAS til en politik for lagrede access, arver Sikkerhedstilknytningerne begrænsninger - starttidspunkt, udløbstid og tilladelser - defineret for den lagrede access-politik.

Forskellen mellem de to formularer er vigtige for et vigtige scenarie: tilbagekaldte certifikater. En SAS er en URL-adresse, så alle, der henter Sikkerhedstilknytningerne kan bruge den, uanset der anmodes om det til at begynde med. Hvis en SAS er offentliggjort, kan den bruges af andre i verden. En Sikkerhedstilknytninger, der er distribueret er gyldig, indtil sker ét af fire ting:

1. Udløbstiden specificeret på Sikkerhedstilknytningerne er nået.

2. Udløbstiden specificeret på den lagrede access-politik, der refereres til af Sikkerhedstilknytningerne er nået frem til (Hvis der refereres til en politik for lagrede access, og det angiver et udløbet tidspunkt). Dette kan ske enten, fordi intervallet, der går, eller fordi du har ændret den lagrede access-politik for at få et udløbet tidspunkt tidligere, som er en metode til at ophæve Sikkerhedstilknytningerne.

3. Den lagrede access-politik, der refereres til af Sikkerhedstilknytningerne slettes, som er en anden måde at ophæve Sikkerhedstilknytningerne. Bemærk, at hvis du genoprette den lagrede access-politik med samme navn, alle eksisterende SAS tokens igen skal gælde ifølge de tilladelser, der er knyttet til den lagrede access-politik (hvis, udløbet tid på Sikkerhedstilknytningerne ikke er overskredet). Hvis du er har til hensigt at ophæve Sikkerhedstilknytningerne, skal du bruge et andet navn, hvis du genoprette access-politikken med et udløbet tidspunkt i fremtiden.

4. Tasten konto, der blev brugt til at oprette Sikkerhedstilknytningerne gendannes. Bemærk, at dette medfører, at alle programkomponenter ved hjælp af kontonøgle mislykkes til at godkende, indtil de er opdateret for at bruge de andre gyldige kontonøgle eller tasten nyligt gendannede konto.

> [AZURE.IMPORTANT] En delt adgang signatur URI er knyttet til tasten konto, der bruges til at oprette signaturen, og den tilknyttede gemt access-politik (hvis relevant). Hvis ingen lagrede access-politik ikke er angivet, er den eneste måde at ophæve en delt adgang signatur til at ændre nøglen konto. 

Det anbefales, at du altid bruger lagrede access politikker, så du kan ophæve signaturer eller udvide udløbsdatoen efter behov. Trinnene i dette dokument bruge gemt access politikker til at generere SAS.

Yderligere oplysninger om delt Access signaturer, kan du se [om SAS modellen](../storage/storage-dotnet-shared-access-signature-part-1.md).

##<a name="create-a-stored-policy-and-generate-a-sas"></a>Oprette en gemte politik og generere en SAS

I øjeblikket skal du oprette en gemte politik fra et program. Du kan finde både C# og Python eksempel på oprettelse af en gemte politik og SAS for [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

###<a name="create-a-stored-policy-and-sas-using-c"></a>Oprette en gemte politik og SAS ved hjælp af C\#

1. Åbn løsningen i Visual Studio.

2. Højreklik på __SASToken__ projektet i Solution Explorer, og vælg __Egenskaber__.

3. Vælg __Indstillinger__ , og tilføje værdier for følgende poster:

    * StorageConnectionString: Forbindelsesstrengen for lagerplads-konto, du vil oprette en gemte politik og SAS for. Formatet skal være `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` hvor `myaccount` er navnet på din konto, lager og `mykey` er tasten for kontoen lagerplads.
    
    * ContainerName: Beholder i lagerplads-konto, du vil begrænse adgangen til.
    
    * SASPolicyName: Navnet skal bruges til den gemte politik, der oprettes.
    
    * FileToUpload: Stien til en fil, som vil blive overført til objektbeholderen.
    
4. Kør projektet. Der vises et console-vindue, og stil med følgende oplysninger der skal vises, når Sikkerhedstilknytningerne er blevet oprettet:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    Gem SAS politik tokenet, som du skal bruge dette, når knyttes kontoen lagerplads til din HDInsight klynge. Du skal også kontonavn lager, og beholder navn.
    
###<a name="create-a-stored-policy-and-sas-using-python"></a>Oprette en gemte politik og SAS ved hjælp af Python

1. Åbn filen SASToken.py og ændre følgende værdier:

    * politik\_navn: navnet for den gemte politik, der oprettes.
    
    * lagerplads\_konto\_navn: navnet på din konto, lagerplads.
    
    * lagerplads\_konto\_nøgle: tasten for kontoen lagerplads.
    
    * lagerplads\_objektbeholder\_navn: objektbeholderen i kontoen lagerplads, du vil begrænse adgangen til.
    
    * Eksempel på\_fil\_sti: stien til en fil, som vil blive overført til objektbeholderen

2. Køre scriptet. Det kan vise SAS tokenet stil med følgende, når scriptet er fuldført:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    Gem SAS politik tokenet, som du skal bruge dette, når knyttes kontoen lagerplads til din HDInsight klynge. Du skal også kontonavn lager, og beholder navn.
    
##<a name="use-the-sas-with-hdinsight"></a>Brug SAS med HDInsight

Når du opretter en HDInsight klynge, skal du angive en primær lagerplads-konto, og du kan eventuelt angive yderligere lagerplads konti. Begge af disse metoder til at tilføje lagerplads kræver fuld adgang til de lagerplads konti og beholdere, der bruges.

For at bruge en delt Access signatur til at begrænse adgang til en objektbeholder, skal du føje en brugerdefineret indtastning af __grundlæggende websted__ konfigurationen for-klyngen.

* Til __Windows-baseret__ eller __Linux-baserede__ HDInsight klynger, kan du gøre dette under oprettelse af klynge ved hjælp af PowerShell.

* Du kan ændre konfigurationen, efter oprettelse af klynge ved hjælp af Ambari til __Linux-baserede__ HDInsight klynger.

###<a name="create-a-new-cluster-that-uses-the-sas"></a>Oprette en ny klynge, der bruger Sikkerhedstilknytningerne

Et eksempel på oprettelse af en HDInsight klynge, der bruger Sikkerhedstilknytningerne er inkluderet i den `CreateCluster` i lageret. Hvis du vil bruge det, skal du følge nedenstående trin:

1. Åbn den `CreateCluster\HDInsightSAS.ps1` filer i et tekstredigeringsprogram og redigere følgende værdier i starten af dokumentet.

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    Eksempelvis ændre `'mycluster'` til navnet på den klynge, du vil oprette. SAS værdierne skal svare til værdierne fra de forrige trin, når du opretter en lagerplads konto og SAS token.
    
    Når du har ændret værdierne, kan du gemme filen.

1. Åbn en ny Azure PowerShell-prompt. Hvis du ikke har kendskab til Azure PowerShell eller ikke har installeret, kan du se [installere og konfigurere Azure PowerShell][powershell].

2. Fra bliver spurgt, skal du bruge følgende til at godkende abonnementet Azure:

        Login-AzureRmAccount
    
    Når du bliver spurgt, logon med på kontoen for abonnementet Azure.
    
    Hvis dit logon er knyttet til flere Azure abonnementer, du skal bruge `Select-AzureRmSubscription` til at markere det abonnement, du vil bruge.

2. Fra bliver spurgt, skal du ændre kataloger til den `CreateCluster` directory, der indeholder fil, HDInsightSAS.ps1. Brug følgende til at køre scriptet
        
        .\HDInsightSAS.ps1
    
    Som scriptet kører, logfører det output til PowerShell-prompt, mens den opretter ressourcen gruppe og lager konti. Det bliver du bedt om at angive HTTP-bruger for HDInsight-klyngen. Dette er den brugerkonto, der bruges til at sikre HTTP/s adgang til klyngen.
    
    Hvis du opretter en Linux-baserede klynge, kan du også bedt om en SSH konto brugernavn og din adgangskode. Dette bruges til at få fjernadgang logon til klyngen.
    
    > [AZURE.IMPORTANT] Når du bliver bedt om det HTTP/s eller SSH brugernavn og adgangskode, skal du angive en adgangskode, der opfylder følgende kriterier:
    >
    > - Skal være mindst 10 tegn lang
    > - Skal indeholde mindst ét ciffer
    > - Skal indeholde mindst én ikke-alfanumeriske tegn
    > - Skal indeholde mindst én øverste eller nederste bogstav


Det kan tage et øjeblik, før dette script til at fuldføre, normalt omkring 15 minutter. Når scriptet er fuldført uden fejl, der er oprettet på klynge.

###<a name="update-an-existing-cluster-to-use-the-sas"></a>Opdatere en eksisterende klynge for at bruge Sikkerhedstilknytningerne

Hvis du har en eksisterende Linux-baserede klynge, kan du føje Sikkerhedstilknytningerne til konfigurationen __core-websted__ ved at benytte følgende fremgangsmåde:

1. Åbn webstedet Ambari brugergrænseflade til din klynge. Adressen på denne side er https://YOURCLUSTERNAME.azurehdinsight.net. Når du bliver bedt om det, du Godkend til klynge med administratornavn (admin) og adgangskode, du brugte, når du opretter klyngen.

2. Vælg __HDFS__ fra venstre side af webdelen Ambari brugergrænseflade, og vælg derefter fanen __konfigurationer__ midt på siden.

3. Vælg fanen __Avanceret__ , og rul derefter ned, indtil du finder sektionen __brugerdefineret core-websted__ .

4. Udvid sektionen __brugerdefineret core-websted__ og derefter rulle til slutningen, og vælg linket __Tilføj egenskab …__ . Du kan bruge følgende værdier for felterne __nøgle__ og __værdi__ :

    * __Nøgle__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __Værdi__: feltet SAS returneres af programmet C# eller Python du kørte tidligere
    
    Erstat __CONTAINERNAME__ med objektbeholderen navn, du har brugt med det C# eller SAS program. Erstat __STORAGEACCOUNTNAME__ med kontonavnet lagerplads du har brugt.

5. Klik på knappen __Tilføj__ for at gemme denne nøgle og værdi, og derefter klikke på knappen __Gem__ for at gemme ændringerne i konfigurationen. Når du bliver bedt om det, tilføje en beskrivelse af ændringen ("tilføje SAS lagerplads access" for eksempel), og klik derefter på __Gem__.

    Klik på __OK__ , når ændringerne, der er fuldført.

    > [AZURE.IMPORTANT] Dette gemmes ændringerne i konfigurationen, men du skal genstarte flere tjenester, før ændringen træder i kraft.

6. Vælg __HDFS__ på listen til venstre i webdelen Ambari brugergrænseflade, og derefter vælge __Genstart alle__ den __Tjeneste handlinger__ rullelisten til højre. Når du bliver bedt om det, Vælg __Slå Vedligeholdelsestilstand__ og derefter vælge __Conform genstart alle".

    Gentag denne proces for MapReduce2 og GARN elementerne på listen til venstre på siden.

7. Når disse har genstartet, markere hvert enkelt og deaktivere rullemenu Vedligeholdelsestilstand fra __Tjeneste handlinger__ .

##<a name="test-restricted-access"></a>Teste begrænset adgang

For at bekræfte, at du har begrænset adgang, skal du bruge følgende metoder:

* Bruge Fjernskrivebord til at oprette forbindelse til klyngen til __Windows-baseret__ HDInsight klynger. Du kan få flere oplysninger i [Connecto til HDInsight ved hjælp af RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) .

    Når forbindelse, kan du bruge ikonet __Hadoop kommandolinjen__ på skrivebordet til at åbne en kommandoprompt.

* Bruge SSH til at oprette forbindelse til klyngen for __Linux-baserede__ HDInsight klynger. Se et af følgende for at få oplysninger om brug af SSH med Linux-baserede klynger:

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, OS X og Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Når forbindelse til klyngen, du følge nedenstående trin for at bekræfte, at du kan kun læse- og listen elementer på SAS lagerplads konto:

1. Fra bliver spurgt, skal du skrive følgende. Erstat __SASCONTAINER__ med navnet på den beholder for kontoen SAS lagerplads har oprettet. Erstat __SASACCOUNTNAME__ med navnet på lagerplads konto, der bruges til Sikkerhedstilknytningerne:

        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    Du får vist indholdet af objektbeholderen, som skal indeholde den fil, der blev overført når beholderen og SAS blev oprettet.
    
2. Brug følgende til at bekræfte, at du kan læse indholdet af filerne. Erstat __SASCONTAINER__ og __SASACCOUNTNAME__ som i det forrige trin. Erstat __FILNAVN__ med navnet på den fil, der vises i den forrige kommando:

        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    Du får vist indholdet af filerne.
    
3. Brug følgende fremgangsmåde til at hente filen til det lokale filsystem:

        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    Dette vil hente filen til en lokal fil med navnet __testfile.txt__.

4. Du kan bruge følgende for at overføre den lokale fil til en ny fil med navnet __testupload.txt__ på SAS lagringen:

        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    Du får en meddelelse, der ligner følgende:
    
        put: java.io.IOException
        
    Denne fejl opstår, fordi lagerplaceringen er læst + liste kun. Du kan bruge følgende til dataene på standard lagerplads for klynge, som er skrivbart:
    
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
        
    Denne gang skal handlingen fuldført.
    
##<a name="troubleshooting"></a>Fejlfinding i forbindelse med

###<a name="a-task-was-canceled"></a>En opgave blev annulleret

__Symptomer__: Når du opretter en klynge ved hjælp af PowerShell-script, modtager du muligvis vist følgende fejlmeddelelse:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__Årsag__: Denne fejl kan opstå, hvis du bruger en adgangskode til administrator/HTTP brugeren for-klyngen eller (for Linux-baserede klynger,) SSH brugeren.

__Opløsning__: bruge en adgangskode, der opfylder følgende kriterier:

- Skal være mindst 10 tegn lang
- Skal indeholde mindst ét ciffer
- Skal indeholde mindst én ikke-alfanumeriske tegn
- Skal indeholde mindst én øverste eller nederste bogstav

##<a name="next-steps"></a>Næste trin

Nu hvor du har lært at tilføje lagerplads begrænset adgang til din HDInsight klynge, få mere at vide om andre måder at arbejde med data i din klynge:

* [Bruge Hive med HDInsight](hdinsight-use-hive.md)

* [Brug gris med HDInsight](hdinsight-use-pig.md)

* [Bruge MapReduce med HDInsight](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md

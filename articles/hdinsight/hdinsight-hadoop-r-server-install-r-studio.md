<properties
    pageTitle="Installere RStudio med R Server på HDInsight (preview) | Microsoft Azure"
    description="Sådan installeres RStudio med R Server på HDInsight (preview)."
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
   ms.date="09/16/2016"
   ms.author="jeffstok"/>


# <a name="installing-rstudio-with-r-server-on-hdinsight-preview"></a>Installation af RStudio med R Server på HDInsight (preview)

Der findes flere integreret udviklingsmiljøer (IDE) til R i dag, herunder Microsofts for nylig offentliggjort [R Tools til Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS), en serie af skrivebord og server værktøjer fra [RStudio](https://www.rstudio.com/products/rstudio-server/)eller Walwares Eklipse-baseret [StatET](http://www.walware.de/goto/statet). Blandt de mest populære på Linux, er brugen af [RStudio Server](https://www.rstudio.com/products/rstudio-server/) , der indeholder en browser-baseret IDE til brug af fjerne klienter.  Installerer RStudio Server på noden kant af en HDInsight Premium klynge giver en fuld ide-oplevelse for udvikling og udførelse af R scripts med R Server på klyngen og kan være meget mere produktiv end standard brug af R-konsollen.

I denne artikel lærer du, hvordan du installerer den community (gratis) version af RStudio Server på noden kant af en klynge ved hjælp af et brugerdefineret script. Hvis du foretrækker den kommercielt licenseret Pro version af RStudio Server, skal du følge installationsvejledning fra [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] Trinnene i dette dokument kræver en R Server på HDInsight klynge og fungerer ikke korrekt, hvis du bruger en HDInsight klynge hvor R blev installeret ved hjælp af [Installere R scripthandling](hdinsight-hadoop-r-scripts-linux.md).

## <a name="prerequisites"></a>Forudsætninger

* En Azure HDInsight klynge med R-Server, der er installeret. Flere oplysninger under [Introduktion til R Server på HDInsight klynger](hdinsight-hadoop-r-server-get-started.md).
* En SSH-klient. For Linux og Unix eller Macintosh OS X på `ssh` kommandoen leveres med operativsystemet. Til Windows anbefaler vi [Cygwin](http://www.redhat.com/services/custom/cygwin/) med indstillingen [OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU), eller [trykfarver](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  


## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Installere RStudio på den klynge ved hjælp af et brugerdefineret script

1. Identificere noden kant af klyngen. For en HDInsight klynge med R-serveren er følgende Navnekonventionen for hoved node og kantnode.

    * I afsnit node-`CLUSTERNAME-ssh.azurehdinsight.net`
    * Kantnode-`R-Server.CLUSTERNAME-ssh.azurehdinsight.net` 

2. SSH til noden kant af klynge ved hjælp af ovenstående naming mønster. 
 
    * Hvis du opretter forbindelse fra en Linux-klient, kan du se [forbinde til en Linux-baserede HDInsight klynge](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
    * Hvis du opretter forbindelse fra en Windows-klient, kan du se [forbinde til en Linux-baserede HDInsight klynge ved hjælp af trykfarver](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. Når du har forbindelse, Bliv en rodbruger på klyngen. Brug følgende kommando i session SSH.

        sudo su -

4. Hent den brugerdefineret script for at installere RStudio. Brug følgende kommando.

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Ændre tilladelser på brugerdefineret script-fil og køre scriptet. Brug følgende kommandoer.

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Hvis du har brugt en SSH adgangskode under oprettelse af en HDInsight klynge med R Server, kan du springe dette trin over og gå videre til næste. Hvis du har brugt en SSH nøgle i stedet for at oprette klyngen, skal du angive en adgangskode for din SSH bruger. Du skal bruge denne adgangskode, når du opretter forbindelse til RStudio. Kør følgende kommandoer. Når du bliver bedt om **aktuelle Kerberos adgangskode**, skal du trykke på **ENTER**.  Bemærk, at du skal erstatte `USERNAME` med en SSH bruger for din HDInsight klynge.

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:
        
    Hvis din adgangskode er korrekt konfigureret, skal du se en meddelelse som denne.

        passwd: password updated successfully


    Afslut SSH session.

7. Oprette en SSH tunnel til klyngen ved at knytte `localhost:8787` på HDInsight klynge til klientmaskinen. Du skal oprette en SSH tunnel inden du åbner en ny browsersession.

    * Åbne en terminal session på en Linux-klient eller en Windows-klient med [Cygwin](http://www.redhat.com/services/custom/cygwin/) derefter og brug følgende kommando.

            ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
            
        Erstatte **brugernavn** med en SSH bruger for din HDInsight klynge, og Erstat **CLUSTERNAME** med navnet på din HDInsight klynge du kan også bruge en SSH nøgle i stedet for en adgangskode ved at tilføje`-i id_rsa_key`     

    * Hvis bruger en Windows-klient med trykfarver er

        1.  Åbn trykfarver, og Angiv dine forbindelsesoplysninger. Hvis du ikke kender med trykfarver, se [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md) for oplysninger om hvordan du kan bruge det sammen med HDInsight.
        2.  Udvid **forbindelse**i sektionen **kategori** til venstre i dialogboksen, udvide **SSH**, og vælg derefter **tunneler**.
        3.  Angiv følgende oplysninger i formularen **Indstillinger, der styrer SSH port videresendelse** :

            * **Kildeport** - port på den klient, som du vil videresende. For eksempel **8787**.
            * **Destination** - destinationen, der skal være knyttet til den lokale klientmaskinen. For eksempel, **localhost:8787**.

            ![Opret en SSH tunnel] (./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Opret en SSH tunnel")

        4. Klik på **Tilføj** for at tilføje indstillingerne, og klik derefter på **Åbn** for at åbne en SSH forbindelse.
        5. Når du bliver bedt om det, log på serveren. Dette vil oprette en SSH session og Aktivér tunnelen.

8. Åbn en webbrowser, og Angiv de følgende URL-adresse, der er baseret på den port, du har angivet for tunnelen.

        http://localhost:8787/ 

9. Du bliver bedt om at angive SSH brugernavnet og adgangskoden til at oprette forbindelse til klyngen. Hvis du har brugt en SSH nøgle mens du opretter klyngen, skal du angive den adgangskode, du oprettede i trin 5 ovenfor.

    ![Oprette forbindelse til R Studio] (./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Opret en SSH tunnel")

10. Hvis du vil teste, om at RStudio installationen er fuldført, kan du køre en test script, som udfører R baseret MapReduce og knallertmotor job på klyngen. Gå tilbage til konsollen SSH, og Skriv følgende kommandoer for at hente Testscriptet til at køre i RStudio.

    * Hvis du har oprettet en Hadoop-klynge med R, kan du bruge denne kommando.
        
            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

    * Hvis du har oprettet en knallertmotor klynge med R, kan du bruge denne kommando.

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. I RStudio, vil du se Testscriptet, du har hentet. Dobbeltklik på filen for at åbne den, markere indholdet i filen, og klik derefter på **Kør**. Du bør se output i ruden **Console** .
 
    ![Test installationen] (./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Test installationen")

En anden mulighed kunne være at skrive `source(testhdi.r)` eller `source(testhdi_spark.r)` til udførelse af script.

## <a name="see-also"></a>Se også

- [Beregne kontekst indstillinger for R Server på HDInsight klynger](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure lagerplads indstillinger for R Server på HDInsight premium](hdinsight-hadoop-r-server-storage.md)


 

<properties 
    pageTitle="Installere Jupyter notesbog på din computer og forbinde den til en HDInsight Spark klynge | Microsoft Azure" 
    description="Få mere at vide om, hvordan du installerer Jupyter notesbog lokalt på din computer og forbinde den til en Apache knallertmotor klynge på Azure HDInsight." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="nitinme"/>


# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-cluster-on-hdinsight-linux"></a>Installere Jupyter notesbog på din computer og oprette forbindelse til Apache knallertmotor klynge på HDInsight Linux

I denne artikel lærer du, hvordan du installerer Jupyter notesbog med brugerdefinerede PySpark (for Python) og knallertmotor (for Scala) kerner med knallertmotor magisk, og Tilslut din notebook til en HDInsight klynge. Der kan være flere årsager til at installere Jupyter på din lokale computer, og der kan være nogle af de udfordringer samt. En liste over årsager og udfordringer, i afsnittet [Hvorfor skal jeg installere Jupyter på min computer](#why-should-i-install-jupyter-on-my-computer) i slutningen af denne artikel.

Der er tre vigtige trin til installation af Jupyter og knallertmotor Magien på din computer.

* Installere Jupyter notesbog
* Installere PySpark og knallertmotor kerner med knallertmotor Magien
* Konfigurere knallertmotor magisk for at få adgang til knallertmotor klynge på HDInsight

Du kan finde flere oplysninger om de brugerdefinerede kerner og knallertmotor Magien tilgængelig for Jupyter notesbøger med HDInsight klynge [kerner, der er tilgængelige for Jupyter notesbøger med Apache knallertmotor Linux klynger på HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

##<a name="prerequisites"></a>Forudsætninger

De forudsætninger, der er angivet her, er ikke til installation af Jupyter. Dette er til at oprette forbindelse Jupyter notesbogen til en HDInsight klynge, når notesbogen er installeret.

- Et Azure-abonnement. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- En Apache knallertmotor klynge på HDInsight Linux. Flere oplysninger under [oprette Apache knallertmotor klynger i Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Installere Jupyter notesbog på din computer

Før du kan installere Jupyter notesbøger, skal du installere Python. Både Python og Jupyter er tilgængelige som en del af den [Ananconda fordeling](https://www.continuum.io/downloads). Når du installerer Anaconda, installere du rent faktisk en fordeling af Python. Når Anaconda er installeret, kan du tilføje Jupyter installationen ved at køre en kommando. Dette afsnit indeholder de instruktioner, der skal du følge.

1. Hent den [Anaconda installer](https://www.continuum.io/downloads) til din platform og kør installationsprogrammet. Kontrollér, at du vælger at føje Anaconda til PATH-variabel, mens du kører konfigurationsguiden.

2. Kør følgende kommando for at installere Jupyter.

        conda install jupyter

    Du kan finde flere oplysninger om installting Jupyter, [Installere Jupyter ved hjælp af Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Installere kerner og knallertmotor magisk

Oplysninger om, hvordan du installerer knallertmotor Magien, se PySpark og knallertmotor kerner, [sparkmagic dokumentation](https://github.com/jupyter-incubator/sparkmagic#installation) på GitHub.

## <a name="configure-spark-magic-to-access-the-hdinsight-spark-cluster"></a>Konfigurere knallertmotor magisk for at få adgang til HDInsight Spark klyngen

I dette afsnit kan du konfigurere knallertmotor Magien, du tidligere har installeret for at oprette forbindelse til en Apache knallertmotor klynge, du skal have allerede oprettet i Azure HDInsight.

1. Jupyter konfigurationsoplysninger er typisk gemt i mappen brugere privat. Skriv følgende kommandoer for at finde dit eget bibliotek på en hvilken som helst OS platform.

    Start af Python shell. På et kommandovindue skal du skrive følgende:

        python

    Angiv følgende kommando for at finde ud af mappen privat shell Python.

        import os
        print(os.path.expanduser('~'))

2. Gå til mappen privat, og Opret en mappe med navnet **.sparkmagic** , hvis den ikke allerede findes.

3. Oprette en fil kaldet **config.json** i mappen, og Tilføj den følgende JSON kodestykke indeni.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Erstatte **{USERNAME}**, **{CLUSTERDNSNAME}**og **{BASE64ENCODEDPASSWORD}** med relevante værdier. Du kan bruge en række værktøjer i din foretrukne programmeringssprog eller online til at oprette en base64-kodet adgangskode for din actualy adgangskode. En simpel Python kodestykke til at køre fra din kommandoprompt ville være:

        python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Start Jupyter. Brug følgende kommando fra kommandoprompten.

        jupyter notebook

6. Kontrollér, at du kan oprette forbindelse til den klynge ved hjælp af Jupyter notesbogen og, kan du bruge knallertmotor Magien tilgængelige med kerner. Udfør følgende trin.

    1. Oprette en ny notesbog. Klik på **Ny**fra højre hjørne. Du bør se standard kernen **Python2** og de to nye kerner, som du har installeret, **PySpark** og **gnister**.

        ![Opret en ny Jupyter notesbog] (./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Opret en ny Jupyter notesbog")

    
        Klik på **PySpark**.


    2. Kør følgende kodestykke.

            %%sql
            SELECT * FROM hivesampletable LIMIT 5

        Hvis du kan hente output korrekt, testet din forbindelse til HDInsight klyngen.

    >[AZURE.TIP] Hvis du vil opdatere notesbog konfigurationen til at oprette forbindelse til en anden klynge, skal du opdatere config.json med det nye sæt af værdier, som vist i trin 3 ovenfor. 

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Hvorfor skal jeg installere Jupyter på min computer?

Der kan være flere årsager, hvorfor du måske installere Jupyter på din computer og forbinde den til en knallertmotor klynge på HDInsight.

* Selvom Jupyter notesbøger findes allerede på knallertmotor klynge i Azure HDInsight, indeholder installerer Jupyter på din computer, du mulighed for at oprette dine notesbøger lokalt, afprøve programmet mod en igangværende klynge, og overfør derefter notesbøgerne til klyngen. Hvis du vil overføre notesbøgerne til klyngen, kan du enten overføre dem ved hjælp af den Jupyter notesbog, der kører eller klyngen, eller gemme dem til mappen /HdiNotebooks i kontoen lagerplads, der er knyttet til klyngen. Se [hvor gemmes Jupyter notesbøger](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)kan finde flere oplysninger om hvordan notesbøger er gemt på klyngen?
* Med de tilgængelige notesbøger lokalt, du kan oprette forbindelse til forskellige knallertmotor klynger baseret på dit program krav.
* Du kan bruge GitHub til at implementere et system til kontrol af kilde og har versionsstyring for notesbøgerne. Du kan også have et samarbejdsmiljø, hvor flere brugere kan arbejde med den samme notesbog.
* Du kan arbejde med notesbøger lokalt helt uden en klynge. Du skal kun bruge en klynge for at teste dine notesbøger mod, ikke til at administrere dine notesbøger eller et udviklingsmiljø manuelt.
* Det kan være nemmere at konfigurere dit eget lokale udviklingsmiljø, end det er at konfigurere Jupyter installationen på klyngen.  Du kan udnytte alle de programmer, du har installeret lokalt uden at konfigurere en eller flere remote klynger.

>[AZURE.WARNING] Med Jupyter installeret på din lokale computer, kan flere brugere køre den samme notesbog på den samme knallertmotor klynge på samme tid. I så fald oprettes flere Livius sessioner. Hvis du støder på et problem og vil foretage fejlfinding, vil det være en kompleks opgave til at registrere hvilke Livius session tilhører hvilken bruger.




## <a name="seealso"></a>Se også


* [Oversigt: Apache knallertmotor på Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Knallertmotor med BI: analyse af interaktive data ved hjælp af knallertmotor i HDInsight med BI-værktøjer](hdinsight-apache-spark-use-bi-tools.md)

* [Knallertmotor med Machine Learning: Brug knallertmotor i HDInsight til analyse af bygning temperaturen ved hjælp af VVS-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Knallertmotor med Machine Learning: Brug knallertmotor i HDInsight til at forudsige mad undersøgelsesresultaterne](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Knallertmotor Streaming: Brug knallertmotor i HDInsight til udvikling af realtid streaming programmer](hdinsight-apache-spark-eventhub-streaming.md)

* [Websted log analyse ved hjælp af knallertmotor i HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Oprette og køre programmer

* [Oprette en enkeltstående program, ved hjælp af Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Køre job fra en fjernplacering på en knallertmotor klynge, ved hjælp af Livius](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Værktøjer og filtypenavne

* [Brug HDInsight værktøjer plug-in til IntelliJ ide at oprette og sende knallertmotor Scala programmer](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Brug HDInsight værktøjer plug-in til IntelliJ ide for at fejlfinde knallertmotor programmer fra en fjernplacering](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Bruge Zeppelin notesbøger med en knallertmotor klynge på HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kerner, der er tilgængelige for Jupyter notesbog i knallertmotor klynge i HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Bruge eksterne pakker med Jupyter notesbøger](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Administrere ressourcer

* [Administrere ressourcer for Apache knallertmotor klynge i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Holde styr på og fejlfinding job, der kører på en Apache knallertmotor klynge i HDInsight](hdinsight-apache-spark-job-debugging.md)

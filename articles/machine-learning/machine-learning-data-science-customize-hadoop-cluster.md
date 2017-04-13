<properties 
    pageTitle="Tilpasse Hadoop klynger for Team Data videnskabelige processen | Microsoft Azure" 
    description="Populære Python moduler gjort tilgængelig i brugerdefineret Azure HDInsight Hadoop klynger."
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="hangzh;bradsev" />

# <a name="customize-azure-hdinsight-hadoop-clusters-for-the-team-data-science-process"></a>Tilpasse Azure HDInsight Hadoop klynger for Team Data videnskabelige processen 

I denne artikel beskrives hvordan du tilpasser en HDInsight Hadoop-klynge ved at installere 64-bit Anaconda (Python 2.7) på hver node, når klyngen er klargjort som en tjeneste, HDInsight. Også se, hvordan du kan få adgang til headnode for at sende brugerdefinerede job til klyngen. Denne tilpasning gør mange populære Python moduler, der er inkluderet i Anaconda nemt tilgængelig til brug i brugerdefinerede funktioner, der er designet til at behandle Hive poster i klyngen. Oplysninger om de anvendte fremgangsmåder i dette scenario, se, [hvordan du sender Hive forespørgsler](machine-learning-data-science-move-hive-tables.md#submit).

I menuen under links til emner, der beskriver, hvordan du konfigurerer de forskellige data videnskabelige miljøer, der er brugt af [Team Data videnskabelige proces (TDSP)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]


## <a name="customize"></a>Tilpasse Azure HDInsight Hadoop klynge

Hvis du vil oprette en tilpasset HDInsight Hadoop-klynge, brugere skal logge på [**Klassisk Portal Azure**](https://manage.windowsazure.com/), skal du klikke på **Ny** i det nederste venstre hjørne og derefter Vælg DATA SERVICES -> HDINSIGHT -> **Brugerdefineret oprette** at få vist vinduet **Klynge detaljer** . 

![Opret arbejdsområde](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

Skriv navnet på klyngen skal oprettes på konfigurationssiden 1, og Accepter standardværdier for de andre felter. Klik på pilen for at gå til konfigurationssiden af næste. 

![Opret arbejdsområde](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

Skriv antallet af **KNUDER DATA**på konfigurationssiden 2, markere det **Område/VIRTUELLE netværk**, og vælg størrelsen på den **i afsnit NODE** og **DATA NODE**. Klik på pilen for at gå til konfigurationssiden af næste.

>[AZURE.NOTE] **Område/VIRTUELLE netværk** skal være den samme som området af lagerplads-konto, der skal bruges for HDInsight Hadoop-klyngen. Ellers i fjerde konfigurationssiden vises kontoen lagerplads, brugerne skal bruge ikke på rullelisten over **KONTONAVN**.

![Opret arbejdsområde](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png)

På konfigurationssiden 3, skal du angive et brugernavn og adgangskode for HDInsight Hadoop-klyngen. **Du skal ikke** markere _Enter Hive/Oozie Metastore_. Klik derefter på pilen for at gå til konfigurationssiden af næste. 

![Opret arbejdsområde](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png)

Angiv lagerplads kontonavnet, objektbeholderen standard af HDInsight Hadoop-klynge på konfigurationssiden 4. Hvis brugerne vælger _oprette standard beholder_ **Standard OBJEKTBEHOLDER** rullelisten, oprettes en objektbeholder med det samme navn som klyngen. Klik på pilen for at gå til den sidste konfigurationssiden.

![Opret arbejdsområde](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png)

Klik på knappen **Føj scripthandling** på den endelige konfigurationssiden **Scripthandlinger** , og udfylde tekstfelterne med følgende værdier.
 
* **Navn** - en streng som navnet på handlingen script. 
* **NODE af TYPE** - Vælg **alle noder**. 
* **SCRIPT URI** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
    * *publicscripts* er en offentlig objektbeholder i lagerplads konto 
    * *getgoing* vi bruger til at dele PowerShell-scriptfiler for at lette brugere arbejde i Azure. 
* **Parametre** - (Lad feltet være tomt)

Klik til sidst på markeringen i afkrydsningsfeltet til at starte oprettelsen af den tilpassede HDInsight Hadoop-klynge. 

![Opret arbejdsområde](./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a>Få adgang til noden i afsnit af Hadoop klynge

Brugere skal aktivere fjernadgang til Hadoop-klynge i Azure, før de kan få adgang til noden hoved af Hadoop-klynge gennem RDP. 

1. Log på [**Klassisk Portal Azure**](https://manage.windowsazure.com/), Vælg **HDInsight** til venstre, vælge din klynge Hadoop på listen over klynger, skal du klikke på fanen **konfiguration** og derefter klikke på ikonet **AKTIVERE REMOTE** nederst på siden.
    
    ![Opret arbejdsområde](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png)

2. Angiv felterne brugernavn og din adgangskode i vinduet **Konfigurere Fjernskrivebord** , og vælg udløbsdatoen for fjernadgang. Derefter skal du markere afkrydsningsfeltet for at aktivere fjernadgang til noden hoved af Hadoop-klynge.

    ![Opret arbejdsområde](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png)
    
>[AZURE.NOTE] Brugernavnet og adgangskoden til den fjernadgang er ikke det brugernavn og adgangskode, du bruger, når du har oprettet Hadoop-klynge. Dette er en separat række legitimationsoplysninger. Desuden skal udløbsdatoen for ekstern adgang være i 7 dage fra dags dato.

Når fjernadgang er aktiveret, klik på **Opret forbindelse** nederst på siden for at remote til noden hoved. Du logger på noden hoved af Hadoop-klynge ved at indtaste legitimationsoplysningerne for fjernadgang til brugeren, som du tidligere har angivet.

![Opret arbejdsområde](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png)

De næste trin i processen avanceret analyse er tilknyttet i [Team Data videnskabelige proces (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) og kan indeholde trin, flytte data til HDInsight, proces, og lyt det der forberedelse til learning fra dataene med Azure maskine læ.

Se, [hvordan du sender Hive forespørgsler](machine-learning-data-science-move-hive-tables.md#submit) for vejledning i at få adgang til de Python moduler, der er inkluderet i Anaconda fra noden hoved af klynge i brugerdefinerede funktioner, der bruges til at behandle Hive poster, der er gemt i klyngen.

 

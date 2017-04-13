<properties
   pageTitle="Hadoop-Selvstudium: Introduktion til Hadoop på Windows | Microsoft Azure"
   description="Introduktion til Hadoop i HDInsight. Lær at oprette Hadoop klynger på Windows, kører en Hive forespørgsel på data og analysere output i Excel."
   keywords="hadoop-selvstudium, hadoop i windows, hadoop klynge, se hadoop, hive forespørgsel"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/07/2016"
   ms.author="nitinme"/>


# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight-on-windows"></a>Hadoop-Selvstudium: Introduktion til brug af Hadoop i HDInsight i Windows

> [AZURE.SELECTOR]
- [Linux-baserede](../hdinsight-hadoop-linux-tutorial-get-started.md)
- [Windows-baseret](../hdinsight-hadoop-tutorial-get-started-windows.md)

For at hjælpe dig med at få mere at vide Hadoop i Windows og begynde at bruge HDInsight viser dette selvstudium, hvordan du kører en Hive forespørgsel på ustrukturerede data i en Hadoop-klynge og derefter analysere resultaterne i Microsoft Excel.

>[AZURE.NOTE] Oplysningerne i dette dokument er specifikke for Windows-baseret HDInsight klynger. Finde oplysninger på Linux-baserede klynger [Hadoop Selvstudium: Introduktion til brug af Linux-baserede Hadoop i HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

Antag, du har et stort ustrukturerede datasæt, og du vil køre en Hive forespørgsel på den for at udtrække nogle betydningsfulde oplysninger. Det er præcis hvad du skal gøre i dette selvstudium. Her er, hvordan du opnår dette:

   !["Hadoop Selvstudium: oprette en konto oprette en Hadoop-klynge; sende en Hive-forespørgslen. analysere data i Excel.][image-hdi-getstarted-flow]

Demovideo af dette selvstudium for at lære Hadoop på HDInsight:

![Video af en første Hadoop-Selvstudium: sende en Hive forespørgsel på en Hadoop-klynge og analysere resultaterne i Excel.][img-hdi-getstarted-video]

**[Se Hadoop selvstudiet for HDInsight på YouTube](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**

Microsoft yder også HDInsight Emulator for Azure, tidligere kendt som *Microsoft HDInsight udvikler Preview*sammen med generelle tilgængeligheden af Azure HDInsight. Emulatoren rettet mod udvikler scenarier og understøtter kun én node installationer. Finde oplysninger om brug af HDInsight Emulator, [Komme i gang med HDInsight emulatoren][hdinsight-emulator].

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet for Hadoop i Windows, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **A workstation-computer** med Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone eller Office 2010 Professional Plus.

### <a name="access-control-requirements"></a>Krav til Access

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-hadoop-clusters"></a>Oprette Hadoop klynger

Når du opretter en klynge, kan du oprette Azure Beregn ressourcer, der indeholder Hadoop og relaterede programmer. I dette afsnit, skal oprette du en HDInsight version 3.2 klynge. Du kan også oprette Hadoop klynger til andre versioner. Flere oplysninger under [oprette HDInsight klynger ved hjælp af brugerdefinerede indstillinger for][hdinsight-provision]. Du kan finde oplysninger om HDInsight versioner og deres SLA [HDInsight komponent versionsstyring](hdinsight-component-versioning.md).


**Sådan oprettes en Hadoop-klynge**

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **Ny**, klik på **Data Analytics**, og klik derefter på **HDInsight**. På portalen åbnes en **Ny HDInsight klynge** blade.

    ![Opret en ny klynge i portalen Azure] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.1.png "Opret en ny klynge i portalen Azure")

3. Angiv eller Vælg følgende:

    ![ENTER klyngenavn og type] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.2.png "ENTER klyngenavn og type")
    
  	|Feltnavn| Værdi|
  	|----------|------|
  	|Klyngenavn| Et entydigt navn til at identificere klyngen|
  	|Klynge Type| Vælg **Hadoop** for dette selvstudium. |
  	|Klynge operativsystem| Vælg **Windows Server 2012 R2 Datacenter** for dette selvstudium.|
  	|HDInsight Version| Vælg den nyeste version for dette selvstudium.|
  	|Abonnement| Vælg det Azure abonnement, der skal bruges for-klyngen.|
  	|Ressourcegruppe | Vælg en eksisterende Azure ressourcegruppe eller Opret en ny ressourcegruppe. En grundlæggende HDInsight klynge indeholder en klynge og dens standardkonto lagerplads.  Du kan gruppere to i en ressourcegruppe til nem administration.|
  	|Legitimationsoplysninger| Angiv klynge login brugernavnet og adgangskoden. Windows-baseret klynge kan have 2 brugerkonti.  Klynge bruger (eller HTTP bruger) bruges til at administrere klyngen og sende job.  Du kan vælge at oprette et Fjernskrivebord (RDP) brugerkonto til remote oprette forbindelse til klyngen. Hvis du vælger at aktivere Fjernskrivebord, kan du oprette RDP-brugerkontoen.|
  	|Datakilde| Klik på Opret ny for at oprette en ny standardkonto for Azure-lager. Brug klyngenavnet på som objektbeholder standardnavnet. Hver HDinsight klynge har en standard Blob objektbeholder forretningskontaktposter en Azure-lager.  Placeringen af Azure lagerplads standardkontoen bestemmer placeringen af HDInsight klyngen.|
  	|Node priser niveauer| Bruge 1 eller 2 arbejder noder med standard arbejder node og hoved noten priser niveau i dette selvstudium.|
  	|Valgfri konfiguration| Springe denne del.|

9. Kontrollér, at **Fastgør til Startboard** er markeret, og klik derefter på **Opret**bladet **Ny HDInsight klynge** . Dette opretter klyngen og føje et felt til det til Startboard af din Azure-Portal. Ikonet indikerer, at klyngen opretter, og ændres for at få vist ikonet HDInsight, når oprettelse af er afsluttet.

  	| Under oprettelse | Oprettelse af fuldført |
  	| ------------------ | --------------------- |
  	| ![Opretter indikator på startboard](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioning.png) | ![Oprettet klynge felt](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioned.png) |

    > [AZURE.NOTE] Det kan tage lidt tid for klynge skal oprettes, normalt omkring 15 minutter. Brug feltet på Startboard eller posten **beskeder** til venstre på siden til at se, om processen til oprettelse af.

10. Når oprettelsen er fuldført, skal du klikke på feltet for klynge fra Startboard til Start bladet klynge.


## <a name="run-a-hive-query-from-the-portal"></a>Køre en Hive forespørgsel fra portalen
Nu hvor du har oprettet en HDInsight klynge, er næste trin at køre et Hive job for at forespørge en Hive eksempeltabel. Vi bruger *hivesampletable*, som følger med HDInsight klynger. Tabellen indeholder data om producenter af mobile enheder, platforme og -modeller. En Hive forespørgsel i denne tabel henter data for mobilenheder efter en bestemt producent.

> [AZURE.NOTE] HDInsight værktøjer til Visual Studio leveres med Azure SDK til .NET version 2.5 eller nyere. Ved hjælp af værktøjerne i Visual Studio, kan du oprette forbindelse til HDInsight klynge, oprette tabeller på Hive og køre Hive forespørgsler. Du kan finde flere oplysninger, se [Introduktion til brug af HDInsight Hadoop-værktøjer til Visual Studio][1].

**Køre et Hive-job fra dashboardet klynge**

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **Gennemse alt** , og klik derefter på **HDInsight klynger** for at få vist en liste over klynger, herunder den klynge, du lige har oprettet i forrige afsnit.
3. Klik på navnet på den-klynge, du vil bruge til at køre Hive jobbet, og klik derefter på **Dashboard** i øverst del af bladet.
4. Der åbnes en webside i en anden browserfane. Angiv den Hadoop-brugerkonto og adgangskode. Standardnavnet er **admin**; adgangskoden er, hvad du har angivet mens du opretter klyngen.
5. Klik på fanen **Editor Hive** dashboard. Følgende websiden åbnes.

    ![Hive under fanen Editor i dashboardet HDInsight klynge.][img-hdi-dashboard]

    Der findes flere faner øverst på siden. Standardfane er **Hive Editor**, og de andre faner er **En oversigt over** og **Fil Browser**. Ved hjælp af dashboard, kan du sende Hive forespørgsler, Kontrollér Hadoop-job loggene og gennemse filer i lagerplads.

    > [AZURE.NOTE] Bemærk, at URL-adressen på webstedet er * &lt;ClusterName&gt;. azurehdinsight.net*. Så i stedet for at åbne dashboard fra portalen, kan du åbne dashboard fra en webbrowser ved hjælp af URL-adressen.

6. Angiv **HTC20**for **Forespørgselsnavn**, under fanen **Hive Editor** .  Forespørgslens navn er stilling. Angiv Hive-forespørgslen i forespørgselsruden som vist i billedet:

    ![Hive forespørgsel, der er angivet i forespørgselsruden i den Hive Editor.][img-hdi-dashboard-query-select]

4. Klik på **Send**. Det tager et øjeblik på at få resultaterne tilbage. Skærmbilledet opdaterer hvert 30. Du kan også klikke på **Opdater** for at opdatere skærmen.

    ![Resultater fra en Hive forespørgsel i vises nederst i dashboardet klynge.][img-hdi-dashboard-query-select-result]

5. Klik på forespørgslens navn på skærmen for at få vist outputtet, når status viser, at jobbet er afsluttet. Skal du notere **Job Start Time (UTC)**. Du skal bruge den senere.

    ![Jobbet starttidspunkt vises under fanen jobs af dashboardet HDInsight klynge.][img-hdi-dashboard-query-select-result-output]

    Siden viser også **Job Output** og **Job Log**. Har du også mulighed for at hente outputfilen (\_stdout) og logfilen \(_stderr).


**Gå til outputfilen**

1. Klik på **Fil Browser**på dashboardet til klynge.
2. Klik på dit kontonavn lager, skal du klikke på objektbeholdernavnet på din (som er den samme som klyngenavnet på din), og klik derefter på **bruger**.
3. Klik på **administrator** , og klik derefter på GUID, der har den seneste ændring (lidt efter jobbet starttidspunktet du nævnt tidligere). Kopiér denne GUID. Du skal bruge den i næste afsnit.


    ![Forespørgslen Hive outputfil GUID vises under fanen fil Browser.][img-hdi-dashboard-query-browse-output]


##<a name="connect-to-microsoft-business-intelligence-tools-for-excel"></a>Oprette forbindelse til Microsoft business intelligence-værktøjer til Excel

Du kan bruge tilføjelsesprogrammet Power-forespørgsel til Microsoft Excel til at importere job output fra HDInsight til Excel, hvor Microsoft business intelligence-værktøjer kan bruges til at analysere resultaterne.

Du skal have Excel 2013 eller 2010 installeret for at kunne udføre denne del af selvstudiet.

**Du kan downloade Microsoft Power-forespørgsel til Excel**

- Hente Microsoft Power-forespørgsel til Microsoft Excel fra [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=39379) , og installer det.

**Importere HDInsight data**

1. Åbn Excel, og Opret en ny projektmappe.
3. Klik på menuen **Power-forespørgsel** , skal du klikke på **Fra andre kilder**, og klik derefter på **Fra Azure HDInsight**.

    ![Excel PowerQuery Import menuen åben til Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Angiv **Kontonavn** på kontoen Azure Blob-lager, der er knyttet til din klynge, og klik derefter på **OK**. (Dette er lagerplads-konto, du oprettede tidligere i selvstudiet).
4. Angiv **Kontonøgle** for kontoen Azure Blob-lager, og klik derefter på **Gem**.
5. Dobbeltklik på blob navnet i højre rude. Som standard er blob navnet den samme som klyngenavnet.

6. Find **stdout** i kolonnen **navn** . Bekræft, at GUID i kolonnen tilsvarende **Mappesti, der** passer til den GUID, du kopierede tidligere. Et match foreslår, at outputdataene svarer til den sag, du har sendt. Klik på **binær** kolonne til venstre i **stdout**.

    ![Finde data output af GUID på listen over indhold.][image-hdi-gettingstarted-powerquery-importdata2]

9. Klik på **Luk og Indlæs** i øverste venstre hjørne for at importere Hive jobbet output til Excel.

##<a name="run-samples"></a>Køre eksempler

HDInsight klynge indeholder en forespørgsel konsol, der indeholder et galleri Introduktion for at køre eksempler direkte fra portalen. Du kan bruge eksemplerne til at få mere for at vide om at arbejde med HDInsight ved gennemgang af nogle grundlæggende scenarier. Disse eksempler leveres med alle de nødvendige komponenter, som data til at analysere og forespørgsler til at køre på dataene. Hvis du vil vide mere om eksemplerne i galleriet Kom godt i gang skal du se [Få mere at vide Hadoop i HDInsight ved hjælp af galleriet HDInsight kom Introduktion](hdinsight-learn-hadoop-use-sample-gallery.md).

**Køre stikprøvernes**

1. Klik på feltet for den klynge, du lige har oprettet fra startboard Azure-portalen.
 
2. Klik på **Dashboard**bladet nye klynge. Når du bliver bedt om det, kan du angive administrator brugernavnet og adgangskoden for-klyngen.

    ![Start klynge dashboard] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.Cluster.Dashboard.png "Start klynge dashboard")
 
3. Klik på fanen **Kom Introduktion galleri** fra den webside, der åbnes, og klik derefter på eksemplet, som du vil køre under kategorien **løsninger med eksempeldata** . Følg vejledningen på websiden til for at afslutte eksemplet. I følgende tabel vises nogle eksempler og indeholder flere oplysninger om, hvad hver enkelt prøve betyder.

Eksempel | Hvad gør den?
------ | ---------------
[Føler dataanalyse][hdinsight-sensor-data-sample] | Lær at bruge HDInsight til at behandle historiske data, der oprettes af varme, ventilation og aircondition (VVS) systemer til at identificere systemer, der ikke er stand til at opretholde et sæt temperaturen pålideligt.
[Websted log analyse][hdinsight-weblogs-sample] | Lær at bruge HDInsight til at analysere websted logfiler for at få indsigt i hyppigheden af besøg på webstedet i en dag fra eksterne websteder og en oversigt over websted fejl, som brugerne oplever.
[Twitter tendensanalyse](hdinsight-analyze-twitter-data.md) | Lær, hvordan du bruger HDInsight til at analysere tendenser i Twitter.

##<a name="delete-the-cluster"></a>Slette klyngen

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="next-steps"></a>Næste trin
I dette selvstudium Hadoop, du har lært hvordan du opretter en Hadoop-klynge i Windows i HDInsight, kører en Hive forespørgsel på data, og Importér resultaterne i Excel, hvor de kan være yderligere behandlet og grafisk vises sammen med et business intelligence-værktøjer. Se følgende selvstudier for at få mere for at vide:

- [Introduktion til brug af HDInsight Hadoop-værktøjer til Visual Studio][1]
- [Komme i gang med HDInsight emulatoren][hdinsight-emulator]
- [Bruge Azure Blob-lager med HDInsight][hdinsight-storage]
- [Administrere HDInsight ved hjælp af PowerShell][hdinsight-admin-powershell]
- [Overføre data til HDInsight][hdinsight-upload-data]
- [Bruge MapReduce med HDInsight][hdinsight-use-mapreduce]
- [Bruge Hive med HDInsight][hdinsight-use-hive]
- [Brug gris med HDInsight][hdinsight-use-pig]
- [Bruge Oozie med HDInsight][hdinsight-use-oozie]
- [Udvikle Java MapReduce programmer til HDInsight][hdinsight-develop-mapreduce]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#step-1-install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
 

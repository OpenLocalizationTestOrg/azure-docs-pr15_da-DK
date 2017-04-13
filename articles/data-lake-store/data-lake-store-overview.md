<properties
   pageTitle="Oversigt over Azure datalager sø | Microsoft Azure"
   description="Forstå, hvad er Azure sø datalager, og den værdi, der giver over butikker andre data"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="overview-of-azure-data-lake-store"></a>Oversigt over Azure sø datalager

Azure datalager sø er en hele virksomheden hyper-skala lager for stor data analytisk arbejdsmængder. Azure Data sø gør det muligt at hente data fra en hvilken som helst størrelse, type og indtagelse hastigheden på ét enkelt sted for drift og undersøgende analyser.

> [AZURE.TIP] Brug den [sø datalager læringssti](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) til at se dig omkring Azure Data sø Store service.

Azure sø datalager kan åbnes fra Hadoop (tilgængelig med HDInsight klynge) ved hjælp af WebHDFS-kompatible REST API'er. Det er udviklet specielt til at aktivere analytics på de lagrede data og er optimeret til ydeevne for data analytics scenarier. Af feltet, den indeholder alle professionel og-funktioner – sikkerhed, administration, skalerbarhed, pålidelighed og tilgængelighed – væsentlige for reale enterprise Brug sager.


![Azure Data sø](./media/data-lake-store-overview/data-lake-store-concept.png)

Nogle af de vigtigste funktioner i Azure Data sø omfatter følgende:

### <a name="built-for-hadoop"></a>Bygget til Hadoop

Azure Data sø store er et Hadoop-Apache filsystem, der er kompatible med Hadoop-distribueret fil System (HDFS) og fungerer med Hadoop økosystemet.  Din eksisterende HDInsight programmer eller tjenester, der bruger API'EN WebHDFS kan nemt integrere med sø datalager. Sø datalager indeholder også en WebHDFS-kompatible RESTEN grænseflade til programmer

Data gemt i sø datalager kan nemt analysere ved hjælp af Hadoop analytisk strukturer som MapReduce eller Hive. Microsoft Azure HDInsight klynger kan klargjort og konfigureret til at direkte adgang til data, der er gemt i sø datalager.

### <a name="unlimited-storage-petabyte-files"></a>Ubegrænset lagring, petabyte filer

Azure datalager sø giver ubegrænset lagring og egner sig til lagring af en række af data for analyser. Det ikke angive nogen begrænsninger på konto størrelser, filstørrelser eller mængden data, der kan være gemt i en data sø. Individuelle filer kan variere fra kilobyte til petabytes i størrelse, der gør det et godt valg for at gemme alle typer data. Data er gemt solidt ved at gøre flere kopier, og der er ingen grænse på tidsrum, hvor dataene kan gemmes i data sø.

### <a name="performance-tuned-for-big-data-analytics"></a>Indstillet performance for stor data analyser

Azure datalager sø er bygget til at køre stor skala analytisk systemer, der kræver massive overførselshastighed til at forespørge og analysere store datamængder. Data sø opslag dele af en fil over et antal individuelle lagerplads servere. Dette forbedrer Læs overførselshastigheden ved læsning af filen parallelt for at udføre analyser data.


### <a name="enterprise-ready-highly-available-and-secure"></a>Virksomhed-klar: Let tilgængelige og sikker

Azure datalager sø giver branchestandard tilgængelighed og pålidelighed. Dine dataaktiver gemmes solidt ved at gøre overflødige Kopier at beskytte mod en uventet fejl. Virksomheder kan bruge Azure Data sø i deres løsninger som en vigtig del af deres eksisterende dataplatform.

Sø datalager indeholder også professionel og sikkerhed i forbindelse med de lagrede data. Få mere at vide under [sikring af data i Azure sø datalager](#DataLakeStoreSecurity).


### <a name="all-data"></a>Alle Data

Azure datalager sø kan gemme alle data i deres oprindelige format, som er, uden at kræve en hvilken som helst forudgående transformationer. Sø datalager kræver ikke et skema til defineres, før data, der indlæses, så det er op til det individuelle analytisk framework til at fortolke dataene og definere et skema på tidspunktet for analysen. Kan gemme filer på vilkårlig størrelser og formater, der gør det muligt for sø datalager til at håndtere strukturerede, semistrukturerede og ustrukturerede data.

Azure sø datalager objektbeholdere til data er grundlæggende mapper og filer. Du arbejder med de lagrede data ved hjælp af SDK'er, Azure Portal og Azure Powershell. Så længe du placerer dine data på den butik, ved hjælp af disse grænseflader og de relevante beholdere, kan du gemme alle typer data. Sø datalager udfører ikke en særlig håndtering af data, der er baseret på typen data, der gemmes.


## <a name="DataLakeStoreSecurity"></a>Sikring af data i Azure sø datalager

Adgangskontrol til listerne (ACLs) for at administrere adgang til dine data og Azure datalager sø bruger Azure Active Directory til godkendelse.

| Funktion                                 | Beskrivelse                              |
|-----------------------------------------|------------------------------------------|
| Godkendelse | Azure datalager sø integrerer med Azure Active Directory (AAD) for identitets- og administration for alle de data, der er gemt i Azure sø datalager. Som et resultat af integrationen, Azure Data sø fordele fra alle AAD funktioner, herunder Multi-Factor authentication, betinget adgang, rollebaseret adgangskontrol, programmet brugen overvågning, sikkerhed overvågning og advarer, osv. Azure datalager sø understøtter OAuth 2.0-protokollen til godkendelse med i grænsefladen RESTEN. |
| Adgangskontrol                          | Azure datalager sø indeholder adgangskontrol ved at understøtte POSIX-typografi tilladelser, der vises af WebHDFS protokollen. I den aktuelle udgave, kan ACLs aktiveres på den rodmappe, undermapper, såvel som individuelle filer. ACLs du anvender på rodmappen der også gælder for alle de underordnede mapper og filer samt.|

Vil du vide mere om sikring af data i sø datalager. Følg nedenstående link.

* Du kan finde vejledning i at sikre data i sø datalager, [sikring af data i Azure sø datalager](data-lake-store-secure-data.md).
* Foretrækker videoer? [Se denne video](https://mix.office.com/watch/1q2mgzh9nn5lx) om, hvordan du secure data gemt i sø datalager.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Programmer, der er kompatible med Azure sø datalager

Azure datalager sø er kompatibelt med mest Åbn kildekomponenter i Hadoop økosystemet. Det kan også integreres fint med andre Azure-tjenester. Dette gør sø datalager valgmuligheden perfekt til dine datalagring. Følge linkene nedenfor for at få mere at vide om, hvordan sø datalager kan bruges både med Åbn kildekomponenter samt andre Azure tjenester.

* Se en liste over Åbn kilde-programmer, der er kompatible med sø datalager [programmer og tjenester, der er kompatible med Azure sø datalager](data-lake-store-compatible-oss-other-applications.md) .
* Se [integration med andre Azure tjenester](data-lake-store-integrate-with-other-services.md) at forstå, hvordan sø datalager kan bruges sammen med andre Azure-tjenester til at aktivere et større udvalg af scenarier.
* Se [scenarier for brug af sø datalager](data-lake-store-data-scenarios.md) for at lære at bruge sø datalager i scenarier som ingesting data, behandling af data, henter data og visualisere data.

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>Hvad er Azure datalager sø filsystem (adl: / /)?

Sø datalager kan få adgang til via nye filsystemet, AzureDataLakeFilesystem (adl: / /), i Hadoop-miljøer (tilgængelig med HDInsight klynge). Programmer og tjenester, der bruger adl: / / kan udnytte yderligere optimering af ydeevne, der ikke er tilgængelige for øjeblikket i WebHDFS. Som et resultat sø datalager giver dig mulighed for at benytte enten den bedste ydeevne med anbefalede mulighed for at bruge adl: / / eller bevare eksisterende kode ved fortsætter med at bruge API'EN WebHDFS direkte. Azure HDInsight bruger fuldt AzureDataLakeFilesystem for at give den bedste ydeevne på sø datalager.

Du kan få adgang til dine data i den sø datalager ved hjælp af `adl://<data_lake_store_name>.azuredatalakestore.net`. Du kan finde flere oplysninger om, hvordan du få adgang til dataene i sø datalager, skal du se [Egenskaber for visning af de lagrede data](data-lake-store-get-started-portal.md#properties)

## <a name="how-do-i-start-using-azure-data-lake-store"></a>Hvordan kommer jeg i gang ved hjælp af Azure datalager sø?

Se [Introduktion til sø datalager ved hjælp af portalen Azure](data-lake-store-get-started-portal.md), om, hvordan du klargør et datalager for sø, der er ved hjælp af portalen Azure. Når du har klargjort Azure Data sø, kan du læse, hvordan du bruger stor data tilbud som Azure Data sø Analytics eller Azure HDInsight med sø datalager. Du kan også oprette en .NET program tilladelse til at oprette en Azure datalager sø konto og udføre handlinger som Overfør data, kan du hente data osv.

- [Introduktion til Azure Data sø Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introduktion til Azure Data sø Store ved hjælp af .NET SDK](data-lake-store-get-started-net-sdk.md)


## <a name="data-lake-store-videos"></a>Sø datalager videoer

Hvis du foretrækker se videoer for at få mere at vide, indeholder sø datalager videoer på en række funktioner.

* [Oprette en Azure datalager sø-konto](https://mix.office.com/watch/1k1cycy4l4gen)
* [Bruge Data Stifinder til at administrere Data i Azure sø datalager](https://mix.office.com/watch/icletrxrh6pc)
* [Oprette forbindelse Azure Data sø Analytics til Azure sø datalager](https://mix.office.com/watch/qwji0dc9rx9k)
* [Access Azure sø datalager via Data sø Analytics](https://mix.office.com/watch/1n0s45up381a8)
* [Oprette forbindelse Azure HDInsight til Azure sø datalager](https://mix.office.com/watch/l93xri2yhtp2)
* [Access Azure sø datalager via Hive og gris](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [Brug DistCp (Hadoop-distribueret kopi) til at kopiere data til og fra Azure sø datalager](https://mix.office.com/watch/1liuojvdx6sie)
* [Brug Apache Sqoop til at flytte data mellem relationelle kilder og Azure sø datalager](https://mix.office.com/watch/1butcdjxmu114)
* [Data organisering med Azure Data Factory til Azure sø datalager](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Sikring af Data i lageret med Azure Data sø](https://mix.office.com/watch/1q2mgzh9nn5lx)




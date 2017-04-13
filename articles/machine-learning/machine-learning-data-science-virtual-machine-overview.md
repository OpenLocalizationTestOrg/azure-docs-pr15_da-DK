<properties
    pageTitle="Hvad er en Data videnskabelige virtuel maskine? | Microsoft Azure"
    description="Lær de vigtigste scenarier, funktioner, og hvordan du kan komme i gang med Data videnskabelige virtuelle maskiner, et miljø og klar til analytics-værktøjskassen."
    keywords="videnskabelige Dataværktøjer, data videnskabelige virtuel maskine, værktøjer til data videnskab, linux data videnskabelige"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="bradsev" />


# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Introduktion til skybaserede Data videnskabelige Virtual Machine til Linux og Windows

Data videnskabelige Virtual Machine er et tilpasset VM billede på Microsofts Azure skyen udviklet specielt til at gøre data videnskabelige. Der er mange populære data videnskab og andre funktioner, der allerede er installeret og konfigureret på forhånd for at Kom hurtigt i gang lave intelligente programmer til avanceret analyse. Den er tilgængelig på Windows Server 2012 eller på OpenLogic 7.2 CentOS-baserede Linux versioner. 

Dette emne beskrives det, hvad du kan gøre med Data videnskabelige VM, er nogle af de vigtigste scenarier for brug af VM, angiver de enkelte de vigtigste funktioner, der er tilgængelige i Windows og Linux versioner og indeholder vejledning i at komme i gang med at bruge dem.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Hvad kan jeg gøre med den Data videnskabelige virtuelle maskine?

Mål af Data videnskabelige Virtual Machine er at tilvejebringe data professionelle på alle færdigheder og roller med et gnidning ledig data videnskabelige-miljø. Denne VM kan du spare betydelige tid, der skal bruges, hvis du har implementeret en tilsvarende miljø på din egen. I stedet starte projektet data videnskabelige med det samme i en nyoprettet VM forekomst. 

Data videnskabelige VM er udviklet og konfigureret til at arbejde med en bred brugsscenarier. Du kan skalere dit miljø, op eller ned som projektet behov ændres. Du kan bruge dit foretrukne sprog til program data videnskabelige opgaver. Du kan installere andre værktøjer og tilpasse systemet til dine behov.
 
## <a name="key-scenarios"></a>Nøglescenarier
Dette afsnit foreslår nogle vigtige scenarier, hvor Data videnskabelige VM kan installeres.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Forudkonfigurerede analytics skrivebord i skyen

Data videnskabelige VM indeholder en oprindelig plan-konfiguration for data videnskabelige teams vil du erstatte deres lokale skriveborde med en administreret skyen skrivebord. Denne oprindelige plan sikrer, at alle data forskere på en gruppe har en ensartet konfiguration, som du vil bekræfte forsøg og fremme samarbejde. Det også reduceres omkostninger ved at reducere systemadministrator arbejdsbyrden og gemme på det tidspunkt, der er behov for at evaluere, installere og vedligehold de forskellige softwarepakker behov for at gøre avanceret analyse.  

### <a name="data-science-training-and-education"></a>Data videnskabelige uddannelse

Enterprise undervisere og undervisere, for at lære data videnskabelige klasser normalt giver et virtuelt billede for at sikre, at eleverne har en ensartet opsætning og eksemplerne arbejde forudsigeligt. Data videnskabelige VM opretter et efter behov miljø med en ensartet installation, der letter udfordringerne, som inkompatibilitet mellem og support. Tilfælde, hvor disse miljøer skal være bygget ofte, især til kortere undervisning fordel væsentligt.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Efter behov elastiske kapacitet til store projekter

Data videnskabelige hackathons/konkurrencer eller store datamodellering og udforske kræver skaleret hardware kapacitet typisk for kortvarigt. Data videnskabelige VM kan hjælpe dig med at gentage data videnskabelige miljøet hurtigt efter behov på skalerede ud servere, der tillader forsøg, der kræver proevningsmetoden dataressourcer skal køres.

### <a name="short-term-experimentation-and-evaluation"></a>Kortvarig forsøg og evaluering

Data videnskabelige VM kan være bruges til at evaluere, eller Lær værktøjer som Microsoft R Server, SQL Server, Visual Studio værktøjer, Jupyter, dybe læ / ML programudviklingsværktøjer og nye værktøjer, der er populære i communityet med minimale installationen indsats. Da Data videnskabelige VM kan konfigureres hurtigt, kan anvendes i andre kortvarig brugsscenarier som replikering publicerede forsøg, udfører demoer, følgende gennemgange i online sessioner eller konference selvstudier.


## <a name="whats-included-in-the-data-science-vm"></a>Hvad der skal medtages i Data videnskabelige VM?

Data videnskabelige Virtual Machine har mange populære videnskabelige Dataværktøjer allerede installeret og konfigureret. Den indeholder også værktøjer, som gør det nemt at arbejde med forskellige Azure data og analytics-produkter. Du kan udforske og udbygges skønnet modeller med store datasæt ved hjælp af Microsoft R-serveren eller ved hjælp af SQL Server 2016. En lang række andre værktøjer for open source community'et og fra Microsoft er også inkluderet, såvel som eksempel kode og notesbøger. Den følgende tabel angiver de enkelte og sammenligner de primære komponenter, der er inkluderet i Windows og Linux udgaver af Data videnskabelige Virtual Machine.


|**Windows Edition** | **Linux Edition** |
|----------------|---------------|
|Microsoft R Server Developer Edition | Microsoft R Server Developer Edition |
|Anaconda Python 2.7, 3.5 | Anaconda Python 2.7, 3.5 |
|Jupyter notesbog Server (R, Python) | JupyterHub: Flerbruger Jupyter notesbøger (R, Python, Julia) |
|SQL Server 2016 Developer Edition: SVG i databasen analytics med R-tjenester | Postgres, SQuirreL SQL (databaseværktøjet), SQL Server-drivere og kommandolinjen (bcp, sqlcmd) |
|Visual Studio Community-udgaven 2015 (IDE) </br> -Azure HDInsight (Hadoop) Data sø, SQL Server Data tools </br> -Node.js, Python og R tools til Visual Studio |IDEs og redaktører </br> -Eklipse med Azure-værktøjskassen plug-in </br> -Emacs (med ESS, auctex) gedit |
|Power BI desktop | -- |
|Learning værktøjsmaskiner </br> -Integration med Azure Machine Learning </br> -CNTK (deep learning/AI) </br> -Xgboost (populære ML værktøj i data videnskabelige konkurrencer) </br> -Vowpal Wabbit (hurtig online læring) </br> -Rattle (visuelle Hurtig start data og analytics værktøj) </br> -Mxnet (deep learning/AI) | Learning værktøjsmaskiner </br> -Integration med Azure Machine Learning </br> -CNTK (deep learning/AI) </br> -Xgboost (populære ML værktøj i data videnskabelige konkurrencer) </br> -Vowpal Wabbit (hurtig online læring) </br> -Rattle (visuelle Hurtig start data og analytics værktøj)  |
| SDK'er adgang til Azure og Cortana Intelligence pakke med tjenester | SDK'er adgang til Azure og Cortana Intelligence pakke med tjenester |
| Værktøjer til flytning af data og administration af Azure og Big Data ressourcer: Azure Storage Explorer CLI PowerShell, AdlCopy (Azure Data sø), AzCopy, og dtui (for DocumentDB), Microsoft Data Management Gateway | Værktøjer til flytning af data og administration af Azure og Big Data ressourcer: Azure Storage Explorer, CLI |
| Ciffer, Visual Studio Team Services-plug-in | Ciffer |
| Windows-port af de mest populære Linux/Unix funktioner fra en kommandolinje tilgængelige via GitBash/kommandoprompt | -- |



## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Hvordan du kan komme i gang med Windows Data videnskabelige VM

- Oprette en forekomst af VM på Windows ved at navigere til [denne side](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) og vælge knappen grønne **oprette virtuelt** .
- Log på VM fra dit Fjernskrivebord, ved hjælp af de legitimationsoplysninger, du angav, da du oprettede VM.
- Hvis du vil finde, og start de tilgængelige værktøjer, skal du klikke på menuen **Start** .


## <a name="get-started-with-the-linux-data-science-vm"></a>Komme i gang med Linux Data videnskabelige VM

- Oprette en forekomst af VM på Linux (OpenLogic CentOS-baseret) ved at navigere til [denne side](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) og vælge knappen **Opret virtuelt** .
- Log på VM fra en SSH klient, som trykfarver eller SSH kommandoen, ved hjælp af de legitimationsoplysninger, du angav, da du oprettede VM.
- I shell bliver spurgt, skal du angive dsvm-mere-info.
- Hente X2Go-klienten til din klient platform for et grafisk skrivebord [her](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) , og følg vejledningen i dokumentets Linux Data videnskabelige VM [klargøring Linux Data videnskabelige Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).


## <a name="next-steps"></a>Næste trin

### <a name="for-the-windows-data-science-vm"></a>For Windows Data videnskabelige VM

- Du kan finde flere oplysninger om, hvordan du køre bestemte værktøjer, der er tilgængelig på Windows-versionen, se [klargøring Microsoft Data videnskabelige Virtual Machine](machine-learning-data-science-provision-vm.md) og
-  Du kan finde flere oplysninger om hvordan du kan udføre forskellige opgaver, der er behov for projektet data videnskabelige på Windows-VM, [Ti ting, du kan udføre på Data videnskabelige virtuelt](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>For Linux Data videnskabelige VM

- Du kan finde flere oplysninger om hvordan du kan køre bestemte værktøjer, der er tilgængelige i versionen af Linux, [klargøring Linux Data videnskabelige Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md).
- Du kan finde en gennemgang, der viser, hvordan du kan udføre mange almindelige data videnskabelige opgaver med Linux VM [Data videnskabelige på Linux Data videnskabelige Virtual Machine](machine-learning-data-science-linux-dsvm-walkthrough.md).

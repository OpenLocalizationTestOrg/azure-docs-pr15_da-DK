<properties
    pageTitle="Azure Machine Learning ofte stillede spørgsmål om | Microsoft Azure"
    description="Azure Machine Learning Introduktion: ofte stillede spørgsmål om fakturering, funktioner og begrænsninger i forbindelse med en skybaseret tjeneste til strømlinet skønnet modellering."
    keywords="Machine learning-Introduktion, skønnet modellering, hvad er maskine læ"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="garye"/>

# <a name="azure-machine-learning-frequently-asked-questions-faq-billing-capabilities-limitations-and-support"></a>Azure Machine Learning ofte stillede spørgsmål (FAQ): fakturering, muligheder, begrænsninger og support

Disse ofte stillede spørgsmål finder du svar på spørgsmål om Azure Machine Learning, en skybaseret tjeneste til udvikling af skønnet modeller og operationalizing løsninger via webtjenester. Disse ofte stillede spørgsmål omhandler spørgsmål om brug af tjenesten, herunder fakturering model, muligheder, begrænsninger og support.

## <a name="general-questions"></a>Generelle spørgsmål

**Hvad er Azure Machine Learning?**

Azure Machine Learning er en komplet administreret tjeneste, som du kan bruge til at oprette, test, betjene og administrere skønnet analytisk løsninger i skyen. Med kun en browser, kan du logge på, overføre data og start med det samme maskine learning forsøg. Træk og slip skønnet modellering, en stor palle af moduler eller et bibliotek med starte skabeloner gør almindelige computer learning opgaver, enkle og hurtig.  Se [Oversigt over Azure Machine Learning service](https://azure.microsoft.com/services/machine-learning/)kan finde flere oplysninger. Du kan finde en machine learning Introduktion, der dækker vigtige terminologi og begreber, [Introduktion til Azure maskine undervisning](machine-learning-what-is-machine-learning.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Hvad er Machine Learning Studio?**

Machine Learning Studio er en workbench-miljøet, kan du finde via en webbrowser. Machine Learning Studio vært en palle moduler med en visuel sammensætning brugergrænseflade, der gør det muligt at opbygge en til slut, data videnskabelige arbejdsproces i form af et forsøg.

Finde flere oplysninger om Machine Learning Studio [Hvad er Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

**Hvad er tjenesten Machine Learning API?**

Tjenesten Machine Learning API gør det muligt at installere skønnet datamodeller som dem, der er indbygget i Machine Learning Studio, som SVG, fejlsikret, web services. Web-tjenester, der er oprettet af tjenesten Machine Learning API er REST API'er, der har en grænseflade til kommunikation mellem eksterne programmer og skønnet analytics-modeller.

Få mere at vide under [oprette forbindelse til en webtjeneste Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).

**Hvor findes mine klassisk webtjenester? Hvor er mit nye Azure ressourcestyring baseret webtjenester angives?**

Klassisk webtjenester og nye Azure ressourcestyring baseret Web services findes på portalen [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) . 

Klassisk webtjenester findes også i [Machine Learning Studio](http://studio.azureml.net) under fanen Web services.

## <a name="microsoft-azure-machine-learning-web-service-questions"></a>Microsoft Azure Machine Learning-webtjenesten spørgsmål

**Hvad er Azure Machine Learning Web-tjenester?**

Machine Learning webtjenester har en grænseflade mellem et program og en vurdering model Machine Learning arbejdsproces. Ved hjælp af webtjenesten Azure Machine Learning, kan et eksternt program kommunikere med en maskine Learning arbejdsproces vurdering model i realtid. En maskine Learning webtjeneste angiver returnerer forudsigelse resultater til et eksternt program. Hvis du vil gøre en Machine Learning-webtjeneste, opkald, skal sende du en API-nøgle, der blev oprettet, da du installerede webtjenesten. Machine Learning-webtjeneste er baseret på RESTEN, et populære arkitektur valg for web programming projekter.

Azure Machine Learning har to typer af tjenester:

* Anmodning om svar Service (Ressourceposter) - en kort ventetid, meget SVG-tjeneste, som indeholder en brugergrænseflade til uden status modeller oprettet og implementeret fra Machine Learning Studio.
* Batchen udførelse af Service (BES) - en asynkron service, pointtal, der er en portion for dataposter.

Der findes flere måder at bruge REST-API og få adgang til webtjenesten. For eksempel kan du skrive et program i C#, R eller Python via det eksempel, der genereres for dig, når du har installeret webtjenesten.

Eksempelkode er tilgængelig på: feltet forbruge siden for webtjenesten i portalen Azure Machine Learning Web Services.
API siden Hjælp i Web-tjenesten dashboardet i Machine Learning Studio.

Eller du kan bruge Microsoft Excel eksempelprojektmappen oprettes til dig (også tilgængeligt i web-tjenesten dashboardet i Studio).

**Hvad er de vigtigste opdateringer med de nye Azure ML webtjenester?**

Se [Relaterede dokumentation](machine-learning-whats-new.md)kan finde flere oplysninger om de nye Azure Machine Learning-webtjenester.

## <a name="machine-learning-studio-questions"></a>Machine Learning Studio spørgsmål

### <a name="importing-and-exporting-data-for-machine-learning"></a>Importere og eksportere data til at lære mere maskine

**Hvilke datakilder understøtter Machine Learning?**

Data kan indlæses i en Machine Learning Studio forsøg på en af tre måder: ved at uploade en lokal fil som et datasæt, ved hjælp af et modul til at importere data fra skyen datatjenester eller ved at importere et datasæt, der er gemt, fra en anden eksperimentere. Få mere at vide om understøttede filformater under [importere kursus data til Machine Learning Studio](machine-learning-data-science-import-data.md).


#### <a id="ModuleLimit"></a>Hvor stor kan datasættet være for min moduler?

Moduler i Machine Learning Studio understøttelse almindelige brug tilfælde datasæt på op til 10 GB tæt numeriske data. Hvis et modul tager mere end én input, er summen af alle input størrelser på 10 GB. Du kan også indsamle store datasæt via Hive eller Azure SQL-Database forespørgsler eller på Learning ved tæller forbehandling af før indtagelse.  

De følgende typer data kan udvide til større datasæt under funktion normalisering og er begrænset til mindre end 10 GB:

- Sparse
- Kategoriske
- Strenge
- Binære data

Følgende moduler er begrænset til datasæt, der er mindre end 10GB:

- Anbefalet af moduler
- SMOTE modul
- Scripting moduler: R, Python, SQL
- Moduler, hvor størrelsen på output dataene kan være større end indtastede data, som Join eller funktion krypteres.
- Kryds-validering, finjustere Model Hyperparameters, ordenstal Regression og én-vs-All Multiclass, når antal gentagelser er meget store.

For datasæt, der er større end et par GB, skal du overføre data til Azure lagerplads eller Azure SQL-Database eller brug HDInsight i stedet for overførsel af direkte fra lokale filer.


####<a id="UploadLimit"></a>Hvad er begrænsningerne for afsendelse af data?
Overføre data til datasæt, der er større end et par GB, til Azure lagerplads eller Azure SQL-Database eller brug HDInsight i stedet for overførsel af direkte fra lokale filer.

**Kan jeg læse data fra Amazon S3?**

Hvis du har en lille mængde data og ønsker at vise den via en http URL-adresse og derefter kan du bruge [Importdata] [ import-data] modul. For en hvilken som helst større datamængder at overføre den til Azure-lager og derefter bruge [Importdata] [ import-data] modul til at hente den til din forsøg.
<!--
<SEE CLOUD DS PROCESS>
-->

**Er der en indbygget billede input egenskab?**

Du kan få at vide om billede input egenskab i [Importere billeder] [ image-reader] reference.

### <a name="modules"></a>Moduler

**Den algoritme, datakilde, dataformat eller data transformation handlingen jeg leder efter, ikke i Azure Machine Learning Studio. Hvilke muligheder har jeg?**

Du kan besøge [bruger feedback-forum](http://go.microsoft.com/fwlink/?LinkId=404231) for at få vist anmodninger om funktion, som vi sporing. Føje din stemme til en anmodning, hvis en egenskab, du leder efter er allerede blevet anmodet om. Hvis den egenskab, du leder efter, ikke findes, skal du oprette en ny anmodning. Du kan se statussen for din anmodning i dette forum for. Vi registrere denne liste tæt og opdatere status for tilgængelighed af funktioner ofte. Desuden med de indbyggede understøttelse af R og Python kan brugerdefinerede konverteringer, der oprettes efter behov.


**Kan jeg overføre min eksisterende kode til Machine Learning Studio?**

Ja, kan du sætter din eksisterende R eller Python kode i Machine Learning Studio, køre i samme forsøget med Azure Machine Learning studerende og installere løsningen som en webtjeneste via Azure Machine Learning. Se [Forlæng din eksperimentere med R](machine-learning-extend-your-experiment-with-r.md) og [udføre Python machine learning-scripts i Azure maskine læ Studio](machine-learning-execute-python-scripts.md)kan finde flere oplysninger.

**Er det muligt at bruge noget i retning af [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) til at definere en model?**

Nej, der ikke understøttes, men brugerdefineret R og Python kode kan bruges til at definere et modul.

**Hvor mange moduler kan jeg udføre parallelt i min forsøg?**  

Du kan udføre op til fire moduler parallelt i et forsøg.


### <a name="data-processing"></a>Behandling af data

**Er der en mulighed for at visualisere data (ud over R visualiseringer) interaktivt i forsøget?**

Du kan få vist dataene og få statistik ved at klikke på output fra et modul.

**Når vise resultater eller data i browseren, antallet af rækker og kolonner er begrænset, hvorfor?**

Da data, der er overført til browseren og kan være stor, er datastørrelsen begrænset for at forhindre Machine Learning Studio gå langsommere. Hvis du vil visualisere alle data/resultatet, er det bedre at hente dataene, og brug af Excel eller et andet værktøj.

### <a name="algorithms"></a>Algoritmer

**Hvilke eksisterende algoritmer understøttes i Machine Learning Studio?**

Machine Learning Studio indeholder de avancerede algoritmer, som SVG øget beslutning træer, Bayesian anbefaling systemer Deep Neural netværk, og beslutning Jungles udviklet på Microsoft Research. SVG open source-machine learning-pakker som Vowpal Wabbit er også inkluderet. Machine Learning Studio understøtter machine learning algoritmer til multiclass og binære klassifikation, regression og klynge. Se den komplette liste over [Machine Learning moduler][machine-learning-modules].

**Du automatisk foreslå højre Machine Learning algoritme for at bruge til mine data?**

Nej, men der er forskellige måder i Machine Learning Studio til sammenligning af resultaterne for hvert algoritme til at bestemme den rigtige for dit problem.

**Har du en hvilken som helst retningslinjer for at vælge én algoritme over et andet til de medfølgende algoritmer?**
Se, [hvordan du vælger en algoritme](machine-learning-algorithm-choice.md).

**Er de medfølgende algoritmer, der er skrevet i R eller Python?**

Nej, er disse algoritmer hovedsageligt skrevet i kompileret sprog til at levere højere ydeevne.

**Er alle oplysninger om de algoritmer, der er angivet?**

Dokumentationen indeholder nogle oplysninger om algoritmerne, og de parametre, der er knyttet til justering af er beskrevet for at optimere algoritmen til brug.  

**Er der en hvilken som helst understøttelse af online læring?**

Nej, i øjeblikket kun programmeringsmæssig omskoling understøttes.

**Kan jeg visualisere lag af en Neural Net Model ved hjælp af indbyggede modulet?**

Nej.

**Kan jeg oprette mine egne moduler i C# eller et andet sprog?**

I øjeblikket nye brugerdefinerede moduler kan kun oprettes i R.

### <a name="r-module"></a>R modul

**Hvilke R-pakker er tilgængelige i Machine Learning Studio?**

Machine Learning Studio understøtter 400 + CRAN R pakker i dag, og her er den [aktuelle liste](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) over alle inkluderet pakker. Se også [Forlæng din eksperimentere med R](machine-learning-extend-your-experiment-with-r.md) lære at hente denne liste dig selv. Hvis pakke, du vil ikke er på denne liste, kan du angive navnet på pakken på [bruger feedback-forummet](http://go.microsoft.com/fwlink/?LinkId=404231).

**Er det muligt at oprette et brugerdefineret R-modul?**

Ja, se [forfatter brugerdefinerede R moduler i Azure Machine Learning](machine-learning-custom-r-modules.md) kan finde flere oplysninger.

**Er der et Erstat miljø til R?**

Nej, der er ingen Erstat miljø til R i studio.

### <a name="python-module"></a>Python modul

**Er det muligt at oprette et brugerdefineret Python modul?**

Ikke i øjeblikket, men du kan bruge en eller flere [Udføre Python Script] [ python] moduler for at få det samme resultat.

**Er der et Erstat miljø til Python?**

Du kan bruge Jupyter notesbøger i Machine Learning Studio. Du kan finde flere oplysninger, se [Introduktion til Jupyter notesbøger i Azure Machine Learning Studio] (http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Webtjeneste

###<a name="retraining-models-programmatically"></a>Omskoling modeller fra et program

**Hvordan får jeg henblik på omstilling Azure Machine Learning modeller fra et program?**

Brug de Retraining API'er. Du kan finde flere oplysninger, se [henblik på omstilling Machine Learning modeller fra et program](machine-learning-retrain-models-programmatically.md). Eksempel på kode findes også i [Microsoft Azure Machine Learning omskoling Demo](https://azuremlretrain.codeplex.com/).

### <a name="create"></a>Oprette

**Kan jeg installere modellen, lokalt eller i et program uden Internetforbindelse?**

Nej.


**Er der en oprindelig plan-ventetid, der forventes for alle webtjenester?**

Se den [Azure abonnement begrænser](../azure-subscription-service-limits.md)

### <a name="use"></a>Brug

**Hvornår skal jeg gerne vil køre min skønnet model som en batchen udførelse af tjeneste kontra en anmode om svar tjeneste?**

Tjenesten anmode om svar (Ressourceposter) er en lav ventetid, høj skalerbarhed webtjeneste, der bruges til at give en brugergrænseflade til uden status modeller, der er oprettet og implementeret fra forsøg-miljø. Tjenesten udførelse af Batch (BES) er en tjeneste til asynkront vundne en mængde dataposter. Input til BES ligner datainput, der bruges i Ressourceposter. Den største forskel er, at BES læser en blok af poster fra en lang række kilder, som tjenesten Blob-tjenesten og tabel i Azure, Azure SQL-Database HDInsight (hive-forespørgsel), og HTTP kilder. Se, [hvordan du forbruge Machine Learning webtjenester](machine-learning-consume-web-services.md)kan finde flere oplysninger.

**Hvordan opdaterer modellen for udløst-webtjenesten?**

Opdatere en skønnet model for en allerede installeret tjeneste er så enkelt som at redigere og køre forsøget, som du brugte til at redigere og gemme erfaren modellen. Når du har en ny version af tilgængelige erfaren modellen, spørger Machine Learning Studio, hvis du vil opdatere din webtjeneste. Du kan finde oplysninger om, hvordan du opdaterer en udløst webtjeneste, [Implementer en Machine Learning webtjeneste](machine-learning-publish-a-machine-learning-web-service.md).

Du kan også bruge de omskoling API'er.
Du kan finde flere oplysninger, se [henblik på omstilling Machine Learning modeller fra et program](machine-learning-retrain-models-programmatically.md). Eksempel på kode findes også i [Microsoft Azure Machine Learning omskoling Demo](https://azuremlretrain.codeplex.com/).

**Hvordan jeg overvåge min webtjeneste, der er implementeret i fremstilling?**

Når en skønnet model er blevet installeret, kan du overvåge den fra den Azure klassiske portal (kun klassisk web services) eller portalen Azure Machine Learning Web Services. Hver udløst tjeneste har sit eget dashboard, hvor du kan se overvågning oplysninger for den pågældende tjeneste. Under [administrere en webtjeneste, ved hjælp af portalen Azure Machine Learning Web Services](machine-learning-manage-new-webservice.md) og [administrere et Azure Machine Learning arbejdsområde](machine-learning-manage-workspace.md)kan finde flere oplysninger om administration af dine udløst webtjenester.

**Er der et sted, hvor jeg kan se output fra min Ressourceposter/BES?**

For Ressourceposter, web service svaret er typisk sted, hvor du kan se resultatet. Du kan også skrive til Azure blob-lager. For BES skrives output til en blob som standard. Du kan også skrive output til en database eller tabel ved hjælp af [Eksporter Data] [ export-data] modul.

**Kan jeg oprette webtjenester kun fra modeller, der er oprettet i Machine Learning Studio?**

Nej, du kan også oprette webtjenester direkte fra Jupyter notesbøger og RStudio.

**Hvor kan jeg finde oplysninger om fejlkoder?**

Du kan finde en liste over fejlkoder og -beskrivelser i [Machine Learning modul fejlkoder](https://msdn.microsoft.com/library/azure/dn905910.aspx) .

## <a name="scalability"></a>Skalerbarhed

**Hvad er skalerbarhed af webtjenesten?**

Standardslutpunktet er i øjeblikket er klargjort med 20 samtidige Ressourceposter anmodninger per slutpunkt. Du kan tilpasse det til 200 samtidige anmodninger per slutpunkt, og du kan skalere hver webtjeneste til 10.000 slutpunkter per webtjeneste, som beskrevet i [Skalering en webtjeneste](machine-learning-scaling-webservice.md). Hvert slutpunkt gør det muligt for behandling af anmodninger om 40 ad gangen BES, og ekstra anmodninger ud over 40 anmodninger er i kø. Forespørgslerne i kø køres automatisk som kø løber.


**Er R job udbrede noder?**

Nej.  


**Hvor meget data kan jeg bruge til uddannelse?**

Moduler i Machine Learning Studio understøttelse almindelige brug tilfælde datasæt på op til 10 GB tæt numeriske data. Hvis et modul tager mere end én input, er den samlede størrelse for alle input sammen 10 GB. Du kan også indsamle store datasæt via Hive eller Azure SQL-Database forespørgsler eller på forbehandling med [læ med tæller] [ counts] moduler før indtagelse.  

De følgende typer data kan udvide til større datasæt under funktion normalisering og er begrænset til mindre end 10 GB:

- sparse
- kategoriske
- strenge
- binære data

Følgende moduler er begrænset til datasæt, der er mindre end 10 GB:

- Anbefalet af moduler
- SMOTE modul
- Scripting moduler: R, Python, SQL
- Moduler, hvor størrelsen på output dataene kan være større end indtastede data, som Join eller funktion krypteres.
- Validere tværs, finjustere Model Hyperparameters, ordenstal Regression og én-vs-All Multiclass, når antallet af gentagelser er meget store.

For datasæt, der er større end et par GB, skal du overføre data til Azure lagerplads eller Azure SQL-Database, eller brug HDInsight i stedet for overførsel af direkte fra en lokal fil.


**Er der en vektor størrelsesbegrænsninger?**

Rækker og kolonner er hver begrænset .NET begrænsning af Max Int: 2.147.483.647.

**Kan størrelsen på den virtuelle maskine, der bruges til at køre webtjenesten justeres?**

Nej.  

## <a name="security-and-availability"></a>Sikkerhed og tilgængelighed

**Hvem har adgang til http slutpunkt for-webtjenesten som standard? Hvordan begrænse adgangen til slutpunktet?**

Når en webtjeneste er installeret, der oprettes en standardslutpunkt for den pågældende tjeneste. Standardslutpunktet kan kaldes ved hjælp af dens API-nøgle. Yderligere slutpunkter kan tilføjes med deres egen taster fra portalen Azure klassisk eller automatisk med de Web Service Management API'er. Access-taster er nødvendige for at foretage opkald til webtjenesten. Få mere at vide under [oprette forbindelse til en webtjeneste Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).


**Hvad sker der, hvis min Azure lagerplads konto ikke blev fundet?**

Machine Learning Studio er baseret på en brugerkonto, der er leveret Azure-lager til at gemme mellemliggende data, under kørsel af arbejdsprocessen. Denne konto lagerplads der leveres til Machine Learning Studio på tidspunktet, der oprettes et arbejdsområde. Når arbejdsområdet er oprettet, hvis lagerplads kontoen slettes og kan ikke længere findes, arbejdsområdet ikke synkroniseres fungerer, og alle forsøg i dette arbejdsområde mislykkes.

Hvis du ved et uheld slettede kontoen lagerplads, Genopret kontoen lagerplads med det samme navn i det samme område som kontoen slettede lagerplads. Synkroniseres igen hurtigtast herefter.


**Hvad sker der, hvis min lagerplads konto hurtigtast er ikke synkroniseret?**

Machine Learning Studio er baseret på en leveret Azure lagerplads brugerkonto til at gemme mellemliggende data, under kørsel af arbejdsprocessen. Denne konto lagerplads der leveres til Machine Learning Studio på tidspunktet, der oprettes et arbejdsområde og Hurtigtasterne er knyttet til dette arbejdsområde. Hvis Hurtigtasterne er ændret, efter arbejdsområdet er oprettet, kan arbejdsområdet ikke længere få adgang til kontoen lagerplads. Den stopper fungerer, og alle forsøg i dette arbejdsområde mislykkes.

Hvis du har ændret lagerplads konto Access-taster, synkroniseres igen hurtigtasterne i arbejdsområdet ved hjælp af portalen Azure klassisk.  


## <a name="azure-marketplace"></a>Azure Marketplace

Se [ofte stillede spørgsmål til at publicere og brug af apps i Machine Learning Marketplace](machine-learning-marketplace-faq.md).

## <a name="support-and-training"></a>Support og undervisning

**Hvor kan jeg få Azure Machine Learning-kurser?**

[Azure Machine Learning dokumentation Center](https://azure.microsoft.com/services/machine-learning/) vært instruktionsvideoer og vejledninger. Disse trinvise vejledninger giver en introduktion til tjenesterne, og gennemgå data videnskabelige på livscyklussen for import af data, rydning af unødvendig data, oprette skønnet modeller og implementerer dem i fremstilling med Azure Machine Learning.

Vi tilføjer nye materiale til Machine Learning Center jævnligt. Du kan sende anmodninger om yderligere learning materiale på Machine Learning Center på [bruger feedback-forummet](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Du kan også finde kurser på [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Hvordan får jeg understøttelse af Azure Machine Learning?**

Gå til [Azure Support](/support/options/) for at få teknisk support til Azure Machine Learning, og vælg **Machine Learning**.

Azure Machine Learning har også et community-forum på MSDN, hvor du kan stille spørgsmål, der er relateret til Azure Machine Learning. Forummet overvåges af Azure Machine Learning-teamet. Besøg [Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Fakturering spørgsmål

**Hvordan fungerer Machine Learning fakturering?**

Der findes to komponenter til Azure Machine Learning-tjenesten. Machine Learning Studio og Machine Learning webtjenester.

Mens du evaluerer Machine Learning Studio, kan du bruge det gratis billing niveau.  Det gratis niveau kan du installere en klassisk webtjeneste med begrænset kapacitet.

Når du har besluttet dig for, at Azure Machine Learning opfylder dine behov, kan du tilmelde dig det standard niveau. For at tilmelde dig, skal du have en Microsoft Azure-abonnement.

I den almindelige lag du faktureret månedligt for de enkelte arbejdsområder, du definerer i Machine Learning Studio. Når du kører et forsøg i studio, er du faktureret for Beregn ressourcer, når du kører et forsøg. Når du installerer en klassisk webtjeneste, transaktioner og beregne timer faktureres på grundlag af betal efterhånden (PAYG).

De nye Machine Learning webtjenester introducere fakturering planer, som giver mulighed for flere forudsigelighed i omkostninger. Lagdelte priser indeholder diskonteret satser til kunder, der har brug for en stor mængde kapacitet.

Når du opretter en plan, skal sende du til en fast omkostning, der følger med en inkluderet mængde API Beregn timer og API transaktioner. Hvis du har brug for mere inkluderet antal, kan du føje flere forekomster for din plan. Hvis du har brug for meget mere inkluderet antal, kan du vælge et højere niveau plan, der indeholder betydeligt mere inkluderet antal og en bedre diskonteret sats.

Når de inkluderede mængder i eksisterende forekomster er brugt, registreres yderligere brugen til den forældede rente, der er knyttet til det fakturering plan niveau.

Bemærk: Inkluderet antal er tildeles hver 30 dage og ubrugt inkluderet antal vente ikke til den næste periode.

Yderligere fakturering og prisoplysninger under [Machine Learning priser](https://azure.microsoft.com/pricing/details/machine-learning/).

**Har Machine Learning en gratis prøveversion?**

 Azure Machine Learning har en indstilling for gratis abonnement (se [Machine Learning priser](https://azure.microsoft.com/pricing/details/machine-learning/) få mere at vide) og Machine Learning Studio har en 8-timers hurtig evaluering prøveversion tilgængelig (log på [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2) for denne prøveversion).

 Desuden, når du tilmelder dig en gratis prøveversion af Azure, kan du prøve en hvilken som helst Azure tjenester til en måned. Hvis du vil vide mere om den Azure gratis prøveversion skal du besøge [Azure gratis prøveversion ofte stillede spørgsmål om](/pricing/free-trial-faq/).

**Hvad er en transaktion?**

En transaktion repræsenterer et API-kald, der Azure Machine Learning reagerer på. Transaktioner fra anmodning om svar Service (Ressourceposter) og batchen udførelse af Service (BES) opkald sammenlagt og betale mod din fakturering plan.

**Kan jeg bruge de inkluderede transaction mængder i en plan til både Ressourceposter og BES transaktioner?**

Ja, dine transaktioner fra din Ressourceposter og BES aggregeret og betale mod din fakturering plan.

**Hvad er en API Beregn time?**

Beregn i timen API er fakturering måleenheden for tid API-kald gennemføre for at køre ved hjælp af ML Beregn ressourcer. Alle dine opkald samles til fakturering.

**Hvor lang tid tager et typisk fremstilling API-opkald?**

Fremstilling API opkald gange kan variere betydeligt, normalt lige fra hundredvis af millisekunder til et par sekunder, men det kræver minutter afhængigt af kompleksiteten af databehandling og machine learning model. Den bedste måde til at anslå fremstilling API opkald gange er at benchmark-en model på tjenesten Machine Learning.

**Hvad er en Studio beregne time?**

En Studio beregne time er fakturering enheden for den samlede gang din forsøg bruger beregne ressourcer i studio.

**I de nye webtjenester, hvad er det Udviklingscenter/afprøve niveau beregnet til?**

De nye webtjenester Azure ML giver flere lag, du kan bruge til at klargøre din fakturering plan. Udviklingscenter/afprøve niveau er et trin, som indeholder et begrænset inkluderet antal, så du kan teste din forsøg som ny webtjeneste uden at skulle betale omkostninger. Du har mulighed for at "Udsmidning feltet dæk" til at se, hvordan det fungerer.

**Er der særskilt opbevaring gebyrer?**

Machine Learning gratis niveau kræver ikke eller tillade separat lagerplads. Machine Learning Standard niveauet kræver, at brugerne skal have en Azure-lager-konto. Azure-lager er [Faktureret separat](https://azure.microsoft.com/pricing/details/storage/).

**Hvordan fungerer Machine Learning understøttelse af høj tilgængelighed?**

Fremstilling API opkald gange kan variere betydeligt, normalt lige fra hundredvis af millisekunder til et par sekunder, men det kræver minutter afhængigt af kompleksiteten af databehandling og machine learning model. Den bedste måde til at anslå fremstilling API opkald gange er at benchmark-en model på tjenesten Machine Learning.

**Hvilke bestemte typer Beregn ressourcer vil blive kørt mine fremstilling API-opkald?**

Tjenesten Machine Learning er en multiprofiler tjeneste, og faktisk Beregn ressourcer, der bruges på back-end variere og er optimeret til ydeevne og forudsigelighed.

### <a name="management-of-new-web-services"></a>Administration af nye webtjenester

**Hvad sker der, hvis jeg sletter min plan?**

Planen fjernes fra dit abonnement, og du er faktureret til proportionelt brug.

Bemærk: Du kan ikke slette en plan, der bruges af en webtjeneste. Hvis du vil slette planen, skal du tildele en ny plan til webtjenesten eller slette webtjenesten.

**Hvad er en forekomst af plan?**

En forekomst af plan er en enhed af inkluderet antal, du kan føje til din fakturering plan. Når du vælger et fakturering trin for din fakturering plan, er den leveres med én forekomst. Hvis du har brug for mere inkluderet antal, kan du tilføje forekomster af det valgte fakturering niveau for din plan.

**Hvor mange forekomster af plan kan jeg tilføje?**

Du kan have én forekomst af Udviklingscenter/afprøve niveauet i et abonnement.

Du kan føje op til det er nødvendigt for niveauer S1, S2 og S3.

Bemærk: Afhængigt af din forventede brugen, kan det være mere omkostninger effektivt at opgradere til et højere niveau inkluderet antal i stedet for at føje forekomster til det aktuelle niveau.

**Hvad sker der, når feltstørrelsen ændres plan niveauer (opgradere / nedgradere)?**

Den gamle plan er blevet slettet, og det aktuelle forbrug er faktureret på grundlag af proportionelt. Der oprettes en ny plan med hele inkluderet antallet af opgraderet/sænket niveauet for resten af perioden.

Bemærk: Inkluderet antal tildeles hver periode og ubrugt antal vente ikke.

**Hvad sker der, når jeg øge forekomster i en plan?**

Antal medtages på grundlag af proportionelt og det kan tage 24 timer skal være effektiv.

**Hvad sker der, hvis jeg sletter en forekomst af en plan?**

Forekomsten fjernes fra dit abonnement, og du er faktureret til proportionelt brug.


### <a name="signing-up-for-new-web-services-plans"></a>Tilmelding til nye webtjenester planer

**Hvordan logger jeg for en plan?**

Har du to måder at oprette fakturering planer.

Når du først installere en ny webtjeneste, kan du vælge en eksisterende plan eller oprette en ny plan.

Planer, der oprettes på denne måde er i din standardområde, og din webtjeneste som skal installeres i dette område.

Hvis du vil installere services på områder end din standardområde, skal overveje du at definere dine planer, fakturering, inden du implementerer din tjeneste

Du kan i så fald log på portalen Azure Machine Learning webtjenester og gå til siden planer. Derfra, kan du tilføje og slette planer, samt ændre eksisterende planer.

**Hvilken plan skal jeg vælge til starter med?**

Vi anbefaler, at du starter med standard S1 klasse og overvåge din tjeneste for at få hjælp. Hvis du synes, at du bruger din inkluderet antal hurtigt, kan du tilføje forekomster eller flytter til et højere niveau og få bedre diskonteret satser. Du kan justere din fakturering plan efter behov i hele din faktureringscyklussen.

**Hvilke områder er de nye planer, der er tilgængelige i?**

De nye fakturering planer er tilgængelige i de tre fremstilling områder, hvor vi understøtter de nye webtjenester:

* Syd centrale USA
* Vest Europe
* Syd Østasien

**Jeg har webtjenester i flere områder. Har jeg brug for en plan for hvert område?**

Ja. Plan priser varierer efter område. Når du installerer en webtjeneste til et andet område, skal du tildele den til en bestemt plan til dette område.

### <a name="new-web-services---overages"></a>Nye webtjenester - overskud

**Hvordan kontrollerer jeg, hvis min web-tjenester findes i overskud?**

Du kan få vist brug på alle dine planer på siden planer i portalen Azure Machine Learning Web Services. Log på portalen, og klik på menupunktet planer.

I transaktionerne, og Beregn kolonner i tabellen, kan du se inkluderet antal af planen, og den anvendte procentdel.

**Hvad sker der, når jeg bruger Medtag mængder i Udviklingscenter/afprøve lag?**

Tjenester, der har et Udviklingscenter/afprøve trin, der er tildelt dem er standset indtil den næste periode, eller du kan flytte dem til en af de betalte lag.

**Klassisk webtjenester og overskud af nye webtjenester, hvordan priser beregnes for anmode om svar (Ressourceposter) og Batch (BES) arbejdsbelastninger?**

For en Ressourceposter arbejdsbyrde betaler for hvert API transaction opkald, du foretager og Beregn gang der er knyttet til disse anmodninger. Din Ressourceposter fremstilling API transaktion omkostninger beregnes som det samlede antal API-opkald, du skal multipliceret ved prisen på 1.000 transaktioner (proportionalt af individuelle transaktion). Din Ressourceposter API fremstilling API beregne time omkostninger beregnes som mængden tid, der kræves for hver API-opkald til at køre, multipliceret med det samlede antal API transaktioner ganget med pris i fremstilling API beregne timen.

For eksempel til Standard S1 overskud 1.000.000 API transaktioner, tage 0.72 sekunder at køre resulterer i (1.000.000 *$0,50 / 1K API transaktioner) i 500 i fremstilling API transaktion omkostninger og (1.000.000* 0.72 sec * $2 / hr) $400 i fremstilling API beregne timer, giver en total på $900.

For en BES arbejdsbyrde du betaler på samme måde, dog API transaktionsomkostninger repræsenterer antallet af batchjob, som du indsender og Beregn omkostningerne repræsenterer den Beregn tid, der er knyttet til disse batchjob. Din BES fremstilling API transaktion omkostninger beregnes som det samlede antal sager sendt multipliceret med prisen på 1.000 transaktioner (proportionalt af individuelle transaktion). Din BES API fremstilling API beregne time omkostninger beregnes som mængden tid, der kræves for hver række i tingene for at køre multipliceret med det samlede antal af rækker i tingene multipliceret med det samlede antal sager multipliceret med pris i fremstilling API beregne timen. Når du bruger Machine Learning Lommeregner, transaktion tælleren repræsenterer antallet af opgaver, som du planlægger at sende og tid per transaktions feltet repræsenterer det kombinerede klokkeslæt, der er behov for alle rækker i hver sag for at køre.

For eksempel med Standard S1 overskud, hvis du har indsendt 100 job dagen, hver sammensat af 500 rækker, der tager 0.72 sekunder, og klik derefter din månedlige forældede omkostninger ville være (100 job dagen = 3,100 job/må *$0,50 / 1K API transaktioner) 1.55 i fremstilling API transaktion omkostninger og (500 rækker* 0.72 sec *3,100 job* $2/hr) 620 i fremstilling API beregne timer , i alt $621.55.

### <a name="azure-ml-classic-web-services"></a>Azure ML klassisk webtjenester

**Er betal efterhånden stadig tilgængelig?**
Ja, klassisk webtjenester er stadig tilgængelige i Azure Machine Learning.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Azure Machine Learning gratis og standardversionen niveau

**Hvad der skal medtages i Azure Machine Learning gratis niveau?**

Azure Machine Learning gratis niveau er beregnet til at give en detaljeret introduktion til Azure Machine Learning Studio. Du skal blot en Microsoft-konto til at tilmelde. Det gratis niveau omfatter gratis adgang til én Azure Machine Learning Studio arbejdsområde i [Microsoft-konto](https://www.microsoft.com/account/default.aspx). Den indeholder muligheden for at bruge op til 10 GB lagerplads og muligheden for at giver muligheder for modeller som midlertidige API'er. Gratis niveau arbejdsmængder er ikke omfattet af en SERVICENIVEAUAFTALE og er beregnet til udvikling og personlig brug. Ledig niveau arbejdsbelastninger, som kan€™ t access-data ved at oprette forbindelse til en lokal SQL server.

**Hvad der skal medtages i Azure Machine Learning Standard niveau og planer?**

Azure Machine Learning Standard niveau er en betalt version af Azure Machine Learning Studio. Azure ML service Studio månedligt gebyr er faktureret på en per arbejdsområde hver måned datotype og proportionelt for delvise måneder. Azure ML Studio forsøg timer faktureres i Beregn timen for aktive forsøg. Fakturering proportionalt for delvis timer.  

Tjenesten Azure ML API faktureres afhængigt af om det er en klassisk webtjenester eller en ny webtjeneste.

De følgende gebyrer samles per arbejdsområde til dit abonnement.

* Machine Learning arbejdsområde abonnement - abonnementets Machine Learning arbejdsområde er et gebyr, der giver adgang til et arbejdsområde ML Studio og kræves for at køre forsøg både i studio og udnytte fremstilling API'er.
* Studio eksperimentere timer - denne dokumentmåler samlinger alle beregne gebyrer periodiseres ved at køre forsøg i ML Studio og køre fremstilling API-kald i det midlertidige miljø.
* Access-data ved at oprette forbindelse til en lokal SQL server på din modeller til din uddannelse og vundne.
* Til klassisk webtjenester:
    * Fremstilling API beregne timer - denne dokumentmåler omfatter Beregn gebyrer opsummeret efter Web services kører i fremstilling.
    * Fremstilling API transaktioner (i 1000) - denne dokumentmåler omfatter gebyrer periodiseres per opkald til din fremstilling webtjeneste.

Ud over de foregående gebyrer nye webtjenester, samles gebyrer til den plan, der er markeret:

* Standard S1/S2/S3 API planlægge (enheder) - denne dokumentmåler repræsenterer typen forekomst, der er valgt til nye webtjenester
* Standard S1/S2/S3 forældede API beregne timer - denne dokumentmåler omfatter Beregn gebyrer periodiseres ved de nye webtjenester kører i fremstilling, når de inkluderede mængder i eksisterende forekomster bruges. Yderligere brug som registreres til den overate rente, der er knyttet til S1/S2/S3 plan niveau.
* Standard S1/S2/S3 overskud API transaktioner (i 1,000s) - denne dokumentmåler omfatter gebyrer periodiseres per opkald til din fremstilling ny webtjeneste, når de inkluderede mængder i eksisterende forekomster bruges. Yderligere brug som registreres til den overate rente, der er knyttet til S1/S2/S3 plan niveau.
* Inkluderet antal API Beregn repræsenterer timer - med de nye webtjenester denne dokumentmåler inkluderet antallet af API beregne timer
* Inkluderet antal API transaktioner (i 1,000s) - med de nye webtjenester, denne dokumentmåler repræsenterer inkluderet antallet af API transaktioner


**Hvordan logger jeg til Azure ML gratis niveau?**

Du skal blot en Microsoft-konto. Gå til [Azure Machine Learning hjem](https://azure.microsoft.com/services/machine-learning/), og klik på **Start nu**. Log på med din Microsoft-konto og et arbejdsområde i Free niveau er oprettet for dig. Du kan begynde at udforske og oprette Machine Learning forsøg det samme.

**Hvordan logger jeg til Azure ML Standard niveau?**

Du skal først har adgang til et Azure-abonnement for at oprette et almindeligt ML arbejdsområde. Du kan tilmelde sig en 30-dages gratis prøveversion Azure abonnement og nyere opgraderer til et betalt Azure abonnement, eller du kan købe et betalt Azure abonnement direkte. Du kan derefter oprette en Machine Learning arbejdsområde fra portalen Microsoft Azure klassisk efter at få adgang til abonnementet. Få vist [en trinvis vejledning](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Du kan alternativt inviteres en Standard ML arbejdsområde ejeren af at få adgang til ejerens arbejdsområde.

**Kan jeg angive mine egne Azure blob storage konto skal bruges sammen med det gratis niveau?**

Nej, er det Standard niveau svarer til versionen af tjenesten Machine Learning, der var tilgængelig, før lag, der blev introduceret.

**Kan jeg installere computeren læ modeller som API'er i det gratis niveau?**

Ja, kan du giver muligheder machine learning modeller til at arrangere API tjenester som en del af det gratis niveau. For at sætte tjenesten midlertidige API i fremstilling og få en fremstilling slutpunkt for tjenesten operationalized, skal du bruge det Standard niveau.

**Hvad er forskellen mellem Azure gratis prøveversion og Azure Machine Learning gratis niveau?**

[Gratis prøveversion af Microsoft Azure](https://azure.microsoft.com/free/) tilbyder kredit, der kan anvendes på en hvilken som helst Azure service til en måned. Azure Machine Learning gratis niveau indeholder kontinuert adgang specifikt til tjenesten Azure Machine Learning for arbejdsbelastninger, som ikke-produktiv.

**Hvordan flytter jeg et forsøg fra Free niveau til Standard niveau?**

For at kopiere dine forsøg fra gratis niveau til Standard niveau:

1.  Log på Azure Machine Learning Studio, og Sørg for, at du kan se både gratis arbejdsområdet og Standard arbejdsområdet i vælgeren arbejdsområde i den øverste navigationslinje.
2.  Skifte til gratis arbejdsområde, hvis du er i arbejdsområdet Standard.
3.  Vælg et forsøg, du vil kopiere, og klik på knappen Kopiér kommandoen i listevisningen forsøg.
4.  Vælg Standard arbejdsområdet i pop op-dialogboksen, og klik på knappen Kopiér.
    Alle tilknyttede datasæt, erfaren model, osv., kopieres sammen med forsøget til Standard arbejdsområdet.
6.  Du skal køre forsøget og genudgive din webtjeneste i arbejdsområdet Standard.

### <a name="studio-workspace"></a>Studio arbejdsområde

**Får jeg vist forskellige fakturaer for forskellige arbejdsområder?**

Arbejdsområde gebyrer opdelt separat for hver gældende dokumentmåler på en enkelt faktura.

**Hvilke bestemte typer Beregn ressourcer vil blive kørt min forsøg?**

Tjenesten Machine Learning er en multiprofiler tjeneste, og faktisk Beregn ressourcer, der bruges på back-end variere og er optimeret til ydeevne og forudsigelighed.

### <a name="guest-access"></a>Gæsteadgang

**Hvad er gæsteadgang til Azure Machine Learning Studio?**

Gæsteadgang er en begrænset prøveversion oplevelse, hvor du kan oprette og køre forsøg i Azure Machine Learning Studio uden omkostninger og uden godkendelse. Gæstesessioner er ikke-fast (kan ikke gemmes) og begrænset til otte timer. Andre begrænsninger omfatter manglende R og Python support, manglende arrangere API'er og begrænset datasæt størrelse og lager kapacitet. Sammenligning brugere, der vælger at logge på med en Microsoft-konto har fuld adgang til gratis niveau i Machine Learning Studio er beskrevet ovenfor, indeholder et fast arbejdsområde og mere omfattende egenskaber. Vælg din gratis Machine Learning oplevelse ved at klikke på **Introduktion** i [https://studio.azureml.net](https://studio.azureml.net)og vælge enten gæt Access eller logge på med en Microsoft-konto.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx

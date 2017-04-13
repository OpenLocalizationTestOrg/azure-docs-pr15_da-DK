<properties 
    pageTitle="Data videnskabelige på Linux Data videnskabelige Virtual Machine | Microsoft Azure" 
    description="Hvordan du kan udføre mange almindelige data videnskabelige opgaver med Linux Data videnskabelige VM." 
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
    ms.date="09/12/2016" 
    ms.author="bradsev;paulsh" />


# <a name="data-science-on-the-linux-data-science-virtual-machine"></a>Data videnskabelige på Linux Data videnskabelige Virtual Machine

Denne gennemgang viser, hvordan du udføre mange almindelige data videnskabelige opgaver med Linux Data videnskabelige VM. Linux Data videnskabelige Virtual Machine (DSVM) er et virtuelt billede, der er tilgængelige på Azure, der er forudinstalleret med en samling af funktioner, der ofte anvendte i data analyser og maskine læring. De vigtigste softwarekomponenter er specificeret i emnet [klargøring Linux Data videnskabelige Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md) . VM billedet gør det nemt at komme i gang at gøre data videnskabelige i minutter, uden at installere og konfigurere hvert af værktøjerne enkeltvis. Du kan nemt skalere op VM, hvis det er nødvendigt, og stop den når den ikke er i brug. Denne ressource er så både elastiske og økonomisk. 

Data videnskabelige opgaver, der er vist i denne gennemgang Følg trinnene beskrevet i [Team Data videnskabelige proces](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Denne proces indeholder en systematisk fremgangsmåde til data videnskabelige, der gør det muligt for teams for videnskabelige eksperter data til at samarbejde effektivt i opbygning af intelligente programmer levetid. Data videnskabelige processen indeholder også en gentagen ramme til data videnskabelige, der kan efterfølges af en enkelt bruger.

Vi analysere [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) datasættet i denne gennemgang. Dette er et sæt af mails, der er markeret som spam eller Skinke (dvs. de ikke er spam), og indeholder også nogle statistik på indholdet af e-mails. Statistikken inkluderet er beskrevet i næste men én sektion. 


## <a name="prerequisites"></a>Forudsætninger

Før du kan bruge en Linux Data videnskabelige virtuel maskine, skal du have følgende:

- En **Azure-abonnement**. Hvis du ikke allerede har en, kan du se [oprette gratis Azure kontoen i dag](https://azure.microsoft.com/free/).
- En [**Linux data videnskabelige VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Du kan finde oplysninger om klargøring af denne VM ved [klargøring Linux Data videnskabelige Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md). 
- [X2Go](http://wiki.x2go.org/doku.php) installeret på din computer og åbnes en XFCE session. Finde oplysninger om installation og konfiguration af en **X2Go klient**, skal du se [installere og konfigurere X2Go klient](machine-learning-data-science-linux-dsvm-intro.md#Installing-and-configuring-X2Go-client). 
- En **AzureML konto**. Hvis du ikke allerede har en tilmelde sig forfra på [AzureML-hjemmesiden](https://studio.azureml.net/). Der er et gratis brugen trin kan hjælpe dig med at komme i gang.


## <a name="download-the-spambase-dataset"></a>Hente spambase datasættet

[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) datasættet er et lille sæt af data, der indeholder kun 4601 eksempler. Dette er en praktisk størrelse skal bruges, når demonstration af, at nogle af de vigtigste funktioner i videnskabelige Data VM som den bevarer ressourcebehovet mindre.

>[AZURE.NOTE] Denne gennemgang blev oprettet på en D2 v2 størrelse Linux Data videnskabelige virtuel maskine. Denne størrelse DSVM er i stand til at håndtere fremgangsmåderne i denne gennemgang.

Hvis du har brug for mere lagerplads, kan du oprette ekstra og knytte dem til din VM. Disketterne bruge fast Azure lagerplads, så deres data bevares, selv når serveren er reprovisioned på grund af ændring af størrelsen på eller lukkes ned. For at tilføje en disk, og vedhæft den til din VM skal du følge vejledningen i [Tilføj en disk til en Linux VM](../virtual-machines/virtual-machines-linux-add-disk.md). Disse trin Brug Azure kommandolinjen (Azure CLI), som allerede er installeret på DSVM. Så kan disse procedurer udføres helt fra VM sig selv. En anden indstilling for at øge lagerplads er [Azure](../storage/storage-how-to-use-files-linux.md)-filer.

For at hente dataene, skal du åbne et terminal vindue og kører denne kommando:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Den hentede fil har ikke en kolonneoverskrift, så lad os oprette en anden fil, der har et sidehoved. Kør følgende kommando til at oprette en fil med de relevante sidehoveder:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Derefter sammenkæde de to filer sammen med kommandoen:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Datasættet har flere typer statistik på hver mail: 

- Kolonner som ***word\_freq\_WORD*** angive procentdelen af ord i mailen, der svarer til *WORD*. Eksempelvis hvis *word\_freq\_foretage* er 1, 1% af alle ord i mailen blev *foretage*. 
- Kolonner som ***char\_freq\_CHAR*** angiver procentdelen af alle tegn i mails, der var *tegn*. 
- ***kapital\_køre\_længde\_længste*** er den længste længde af en sekvens af store bogstaver. 
- ***kapital\_køre\_længde\_gennemsnitlige*** er den gennemsnitlige længde af alle serier af store bogstaver. 
- ***kapital\_køre\_længde\_samlede*** er den samlede længde af alle serier af store bogstaver. 
- ***spam*** angiver, om e-mailen blev betragtet som spam, eller ej (1 = spam, 0 = ikke spam).


## <a name="explore-the-dataset-with-microsoft-r-open"></a>Udforske datasæt med Microsoft R åben

Lad os undersøge dataene og gøre nogle grundlæggende maskine læ med R. Data videnskabelige VM leveres med [Microsoft R Åbn](https://mran.revolutionanalytics.com/open/) forudinstalleret. Flertrådet matematikbiblioteker i denne version af R giver bedre ydeevne end forskellige single-threaded versioner. Microsoft R Åbn indeholder også reproducerbarhed ved hjælp af et øjebliksbillede af CRAN pakke lager.

For at få kopier af de kodeeksempler, der bruges i denne gennemgang, klone **Azure-Machine-Learning-Data-videnskabelige** lageret med ciffer, som er forudinstalleret på VM. Kør fra kommandolinjen ciffer:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Åbn et terminal vindue og starte en ny R-session med konsollen R interaktive.

>[AZURE.NOTE] Du kan også bruge RStudio til følgende procedurer. For at installere RStudio skal du udføre denne kommando på en terminal:`./Desktop/DSVM\ tools/installRStudio.sh`

For at importere dataene og konfigurerer miljøet skal du køre:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

For at se oversigt statistik om hver kolonne:

    summary(data)

For en anden visning af dataene:

    str(data)

Dette viser dig typer hver variabel og de første par værdier i datasættet. 

Kolonnen *spam* blev læst som et heltal, men det er faktisk en kategoriske variabel (eller faktor). Sådan angives typen:

    data$spam <- as.factor(data$spam)

Brug pakken [ggplot2](http://ggplot2.org/) , et populære graphing bibliotek til R, der allerede er installeret på VM til at udføre nogle forsøgsfiskeri analyse. Note fra opsummeringsdataene vises tidligere, har vi oversigt statistik på hyppigheden for tegnet udråbstegn. Lad os afbilde disse hyppighed med følgende kommandoer:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Da værktøjslinjen nul er skråstilling området Lad os slippe af med den:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Der er en ikke-trivial tæthed over 1, der ser interessant. Lad os se på kun disse data:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Inddel den derefter ved spam vs skinke:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Disse eksempler skal gør det muligt at foretage lignende grafer over de øvrige kolonner til at udforske data i dem.


## <a name="train-and-test-an-ml-model"></a>Undervise og teste en ML model

Nu Lad os undervise et par machine learning modeller til at klassificere mails i datasættet som med omfanget eller skinke. Vi undervise en beslutning træet model og en tilfældigt skov model i dette afsnit og derefter teste deres nøjagtigheden af deres prognoser. 

>[AZURE.NOTE] Pakken rpart (rekursiv partitionering og Regression træer), der bruges i følgende kode er allerede installeret på Data videnskabelige VM.


Første, Lad os opdele datasættet i kursus og test sæt:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Og derefter oprette et beslutningstræ at klassificere e-mails.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Her er resultatet:

![1](./media/machine-learning-data-science-linux-dsvm-walkthrough/decision-tree.png)

For at finde ud af, hvor godt det udfører på sættet kursus, skal du bruge følgende kode:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Hvis du vil finde ud af, hvor godt udfører den på sættet test:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Lad os prøve også en tilfældigt skov model. Tilfældige områder undervise en lang række beslutningstræer og Medtag en klasse, der er tilstand af klassificeringer fra alle de enkelte beslutning om træer. De indeholder en mere effektiv maskine læ tilgang, som de rette til tendens af en beslutning træet model til at overfit et kursus datasæt. 

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Installere en model til Azure ML

[Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) er en skybaseret tjeneste, der gør det nemt at opbygge og anvende skønnet analytics-modeller. En af de godt funktioner i AzureML er muligheden for at publicere en R funktion som en webtjeneste. Pakken AzureML R gør det nemt at gøre direkte fra vores R session på DSVM installation. 

Hvis du vil installere beslutning træet koden fra den forrige sektion, skal du logge på Azure Machine Learning Studio. Du har brug for dit arbejdsområde-ID og et godkendelse token til sigh i. Sådan finder du disse værdier og initialiseret AzureML variabler med dem:

Vælg **Indstillinger** i menuen til venstre. Bemærk, at dit **ARBEJDSOMRÅDE-ID**. ![2](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-id.png)

Vælg **Godkendelse Tokens** fra menuen indirekte og noter din **Primære godkendelse Token**. ![3](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-token.png)

Indlæse **AzureML** pakken, og angiv derefter værdierne af variablen med token og arbejdsområde-ID i R-sessionen på DSVM:


    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Lad os forenkle model for at gøre det nemmere at implementere denne demonstration. Vælg de tre variabler i det nærmeste i roden beslutningstræ og oprette et nyt træ ved hjælp af de tre variabler:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Vi har brug for en forudsigelse funktion, der tager funktionerne som input og returnerer de estimerede værdier:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publicere funktionen predictSpam til AzureML ved hjælp af funktionen **publishWebService** : 

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Denne funktion kræver funktionen **predictSpam** , opretter en webtjeneste, der hedder **spamWebService** med definerede input og output og returnerer oplysninger om den nye slutpunkt.

Få vist oplysninger om publicerede webtjenesten, herunder dens API slutpunkt og hurtigtasterne med kommandoen:

    spamWebService[[2]]

Prøv det på først 10 rækker af testen Benyt:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Bruge andre værktøjer, der er tilgængelige

De resterende afsnit viser, hvordan du bruger nogle af de værktøjer, der er installeret på Linux Data videnskabelige VM. Her er listen over de værktøjer, som gennemgået:

- XGBoost
- Python
- Jupyterhub
- Babyrangle
- PostgreSQL & Squirrel SQL
- SQL Server datawarehouse


## <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) er et værktøj, der giver en hurtig og nøjagtig øget træet implementering.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost kan også ringe fra python eller en kommandolinje.

## <a name="python"></a>Python

Til udvikling ved hjælp af Python, er Anaconda Python fordelingerne 2.7 og 3.5 installeret i DSVM. 

>[AZURE.NOTE] Anaconda fordeling omfatter [Condas](http://conda.pydata.org/docs/index.html), som kan bruges til at oprette brugerdefinerede miljøer til Python, der har forskellige versioner og/eller pakker, der er installeret.

Lad os læse i nogle af spambase datasættet og klassificere på mails med support vektor maskiner i scikit-få mere at vide:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Sådan gøres prognoser:

    clf.predict(X.ix[0:20, :])

For at vise, hvordan du publicerer et AzureML slutpunkt, Lad os gøre en enklere model tre variabler, som vi har, når vi tidligere publiceret R-modellen. 

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Publicere modellen til AzureML:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

>[AZURE.NOTE] Dette er kun tilgængelig for python 2.7 og er endnu ikke er understøttet på 3.5. Køre med **/anaconda/bin/python2.7**.


## <a name="jupyterhub"></a>Jupyterhub

Anaconda fordeling i DSVM leveres med en Jupyter notesbog, en på tværs af platforme miljø til at dele Python, R eller Julia kode og analyse. Jupyter notesbogen åbnes via JupyterHub. Du logger på med dit lokale Linux-brugernavn og adgangskode på ***https://\<VM DNS navn eller IP-adresse\>: 8000 /***. Alle konfigurationsfiler, til JupyterHub findes i mappen **/etc/jupyterhub**.

Flere eksempel notesbøger er allerede installeret på VM:

- Se [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) til en stikprøve Python notesbog.
- Du kan se [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) til en stikprøve **R** notesbog.
- Se [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) til et andet eksempel **Python** notesbog.

>[AZURE.NOTE] Julia sprog er også tilgængelig fra kommandolinjen på Linux Data videnskabelige VM.


## <a name="rattle"></a>Babyrangle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (den R Analytical værktøj til at få mere at vide nemt) er et grafisk R værktøj til datamining. Den har en intuitiv brugergrænseflade, der gør det nemt at indlæse, udforske, og transformere data og opbygge og evaluerer modeller.  I artiklen [Rattle: A Data Mining grafiske for R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) indeholder en gennemgang, der viser de tilgængelige funktioner.

Installer og start Babyrangle med følgende kommandoer:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

>[AZURE.NOTE] Installationen er ikke påkrævet på DSVM. Men Babyrangle bede dig om at installere flere pakker, når der genindlæses.

Babyrangle benytter en fanen-baseret brugergrænseflade. De fleste af fanerne, der svarer til trin [Data videnskabelige proces](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), som indlæsning af data eller bag den. Processen data videnskabelige løber fra venstre til højre gennem fanerne. Men den sidste fane indeholder en logfil over kommandoerne R køre ved Babyrangle. 


At indlæse og konfigurere datasættet:

- For at indlæse fil, Vælg fanen **Data** , derefter 
- Vælg valgknappen ud for **filnavnet** , og vælg **spambaseHeaders.data**. 
- At indlæse filen. Vælg **Udfør** i den øverste række af knapper. Du bør se en oversigt over hver kolonne, herunder dets identificerede datatype, uanset om det er et input, en destination eller andre typer variabel, og antallet af entydige værdier.
- Babyrangle har korrekt identificeret kolonnen **spam** som destination. Vælg kolonnen spam, og derefter angive den **Destinationsadresse-datatype** til **Categoric**.

For at undersøge data: 

- Vælg fanen **Gennemse** . 
- Klik på **Oversigt**og derefter **Execute**, for at se oplysninger om de variable og nogle oversigt statistik. 
- Vælg andre indstillinger, som **beskrives** eller **grundlæggende**for at få vist andre typer statistik om hver variabel.

Fanen **Gennemse** kan du også til at generere mange nyttige områder. Afbilde et histogram data:


- Vælg **Salgsdistributioner**.
- Se **Histogram** for **word_freq_remove** og **word_freq_you**.
- Vælg **Udfør**. Du bør se begge tætheden af områder i et enkelt graph-vindue, hvor det er tydeligt, ordet "du" vises oftere i mails end "Fjern".

Korrelations-områder er også interessant. Sådan oprettes en:


- Vælg **Korrelations** som **Filtype**, derefter 
- Vælg **Udfør**. 
- Babyrangle advarer dig, at det anbefales, at maksimalt 40 variabler. Vælg **Ja** til at få vist afbildning. 

Der er nogle interessante korrelationer, der opstår: "teknologi" er på det kraftigste mellem til "HP" og "øvelser", f.eks. Det er også på det kraftigste mellem til "650", fordi områdenummer af datasæt donorer er 650.

De numeriske værdier for korrelationer mellem ordene er tilgængelige i Stifinder-vinduet. Det er interessante for note, for eksempel, er negativt mellem "teknologi" med "din" og "penge".

Babyrangle kan forvandle datasæt for at håndtere nogle almindelige problemer. For eksempel kan du skalere igen funktioner, impute manglende værdier, håndtere outliers og fjerne variabler eller observationer med manglende data. Babyrangle kan også angive regler for tilknytning mellem observationer og/eller variabler. Disse faner er uden for området for denne indledende gennemgang.

Babyrangle kan også udføre klynge analyse. Lad os udelade nogle funktioner for at gøre det nemmere at læse output. Vælg **Ignorer** ud for hver af variablerne undtagen disse ti elementer under fanen **Data** :

- word_freq_hp
- word_freq_technology
- word_freq_george
- word_freq_remove
- word_freq_your
- word_freq_dollar
- word_freq_money
- capital_run_length_longest
- word_freq_business
- spam

Gå derefter tilbage til fanen **klynge** , Vælg **KMeans**, og angiv *antallet af klynger* til 4. Derefter **udføre**. Resultaterne vises i outputvinduet. En klynge har høj hyppigheden for "Jens" og "hp" og er sandsynligvis en mail med gyldige business.

Sådan oprettes en simpel beslutning træet machine learning model: 

- Vælg fanen **Model** 
- Vælg **træet** som **typen**. 
- Vælg **Udfør** for at få vist træet i form af tekst i outputvinduet. 
- Vælg knappen **tegn** for at få vist en grafisk version. Dette ligner helt træet vi hentet tidligere ved hjælp af *rpart*.

En af de godt funktioner i Babyrangle er muligheden for at køre flere machine learning metoder og hurtigt at evaluere dem. Her er proceduren:

- Vælg **alle** for **typen**. 
- Vælg **Udfør**. 
- Når den er færdig kan du klikke på en enkelt **Type**, som **SVM**, og se resultaterne. 
- Du kan også sammenligne ydeevnen for modeller på validering sæt med fanen **Evaluer** . For eksempel viser valgmuligheden **Fejl Matrix** du forvirring matrix, overordnede fejl og gennemsnitlig klasse fejl for hver model på siden validering Angiv. 
- Du kan også afbilde ROC kurver, udføre følsomhedsanalyse og foretage andre typer model evaluering.

Når du er færdig med at oprette modeller, Vælg fanen **Log** til at få vist R koden køre ved Babyrangle under sessionen. Du kan vælge knappen **Eksporter** for at gemme den. 

>[AZURE.NOTE] Er der en fejl i aktuelle udgave af Babyrangle. Hvis du vil ændre scriptet eller bruge det til at gentage dine trin senere, skal du indsætte et #-tegnet foran *eksportere denne log …* i teksten i linket log. 


## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL

DSVM leveres med PostgreSQL, der er installeret. PostgreSQL er en avanceret, open source-relationsdatabase. Dette afsnit viser, hvordan du indlæse vores spam datasæt i PostgreSQL og derefter forespørge på den.

Før du kan indlæse dataene, skal du tillade godkendelse af adgangskode fra localhost. Ved en kommandoprompt:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Er flere linjer, der viser oplysninger om de tilladte forbindelser i bunden af config-filen:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Ændre linjen "IPv4 lokale forbindelser" for at bruge md5 i stedet for ident, så vi kan logge på ved hjælp af et brugernavn og din adgangskode:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Og genstart tjenesten postgres:

    sudo systemctl restart postgresql

For at starte psql, en interaktiv terminal til PostgreSQL, som bruger indbyggede postgres, skal du køre følgende kommando fra en meddelelse om:

    sudo -u postgres psql

Oprette en ny brugerkonto, ved hjælp af det samme brugernavn som Linux-konto, du er logget på som, og give det en adgangskode:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Derefter logge på psql som din bruger:

    psql

Og importere dataene til en ny database:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Nu, Lad os undersøge data og Kør nogle forespørgsler ved hjælp af **Squirrel SQL**, et grafisk værktøj, der gør det muligt at arbejde med databaser via en JDBC driver.

For at komme i gang skal du starte Squirrel SQL i menuen programmer. Konfigurere driveren:

- Vælg **Windows**, derefter **Vis drivere**. 
- Højreklik på **PostgreSQL** , og vælg **Rediger Driver**. 
- Vælg **klasse ekstra sti**og derefter **tilføje**. 
- Angiv ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** for **filnavn** og 
- Vælg **Åbn**.
- Vælg listen drivere, og derefter markere **org.postgresql.Driver** i **Klassenavnet**, og vælg **OK**.

Konfigurere forbindelsen til den lokale server:
 
- Vælg **Windows**, derefter **se aliasser.** 
- Vælg den **+** for at oprette et nyt alias. 
- Kald den *Spam database*skal du vælge **PostgreSQL** i rullemenuen **Driver** .
- Angiv URL-adressen til *jdbc:postgresql://localhost/spam*. 
- Angiv dit *brugernavn* og *adgangskode*. 
- Klik på **OK**. 
- For at åbne vinduet **forbindelse** , skal du dobbeltklikke på ***Spam database*** alias. 
- Vælge **Opret forbindelse**.

For at køre nogle forespørgsler:

- Vælg fanen **SQL** .
- Angiv en simpel forespørgsel f.eks `SELECT * from data;` i tekstfeltet forespørgsel øverst på fanen SQL. 
- Tryk på **Ctrl-Angiv** at køre den. Som standard returnerer Squirrel SQL de første 100 rækker fra din forespørgsel. 

Der er mange flere forespørgsler, du kan køre for at udforske disse data. For eksempel Hvad er hyppigheden for word *gøre* forskellen mellem spam og skinke?

    SELECT avg(word_freq_make), spam from data group by spam;

Eller hvad er de egenskaber for mail, der ofte indeholder *3d*?

    SELECT * from data order by word_freq_3d desc;

De fleste mails, der har en høj forekomst af *3d* er tilsyneladende spam, så det kan være en nyttig funktion til opbygning af en skønnet model at klassificere e-mails.

Hvis du vil udføre machine learning med data, der er gemt i en PostgreSQL-database, kan du overveje at bruge [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server datawarehouse

Azure SQL Data Warehouse er en skybaseret skala op-database kan behandle store mængder data, både relationelle og ikke-relationelle. Du kan finde flere oplysninger, se [Hvad er Azure SQL Data Warehouse?](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

For at oprette forbindelse til datawarehouse og oprette en tabel skal du køre følgende kommando fra en kommandoprompt:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Klik derefter på sqlcmd prompten:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Kopiér data med bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

>[AZURE.NOTE] Linjeslutpunkter i den hentede fil er Windows-typografi, men bcp forventer UNIX-typografi, så vi har brug at fortælle bcp, med flaget - r.

Og forespørgsel med sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Du kan også forespørge med Squirrel SQL. Følg denne fremgangsmåde for PostgreSQL, ved hjælp af Microsoft MSSQL Server JDBC Driver, som kan findes i ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Næste trin

Du kan finde en oversigt over emner, der fører dig gennem de opgaver, der udgør Data videnskabelige processen i Azure, [Team Data videnskabelige proces](http://aka.ms/datascienceprocess).

Du kan finde en beskrivelse af andre til slut gennemgange, der viser trinnene i processen Team Data videnskabelige for bestemte scenarier, [Team Data videnskabelige proces gennemgange](data-science-process-walkthroughs.md). Gennemgangene illustrere også hvordan du kan kombinere skyen og on-premises værktøjer og tjenester i en arbejdsproces eller pipeline til at oprette et intelligent program.


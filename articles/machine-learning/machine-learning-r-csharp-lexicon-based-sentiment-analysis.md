<properties 
    pageTitle="Leksikonet baseret synspunkt analyse | Microsoft Azure" 
    description="Leksikonet baseret synspunkt analyse" 
    services="machine-learning" 
    documentationCenter="" 
    authors="pengxia" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="pengxia"/> 



#<a name="lexicon-based-sentiment-analysis"></a>Leksikonet baseret synspunkt analyse 

Hvordan kan du måle brugernes udtalelser og indstilling mod mærker eller emner i sociale netværk, som Facebook indlæg, tweets, anmeldelser osv.? Synspunkt analyse indeholder en metode til at analysere disse spørgsmål.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Der findes to metoder til synspunkt analyse Generelt. Der bruger en kontrolleret learning algoritme, og den anden kan behandles som fungerer learning. En kontrolleret learning algoritme bygger generelt en klassifikation model på en stor kommenterede indekserede. Nøjagtigheden er især baseret på kvaliteten af anmærkningen, og som regel kursus processen tager lang tid. Ud over, når vi anvender algoritmen til et andet domæne, er resultatet som regel ikke godt. Sammenlignet med kontrolleret undervisning, leksikonet-baserede fungerer learning bruger en ordbog til synspunkt, der ikke kræver gemme en stor data indekserede og kurser – hvilket gør den meget hurtigere hele processen. 

Vores [tjenesten](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) er bygget på den MPQA Subjectivity-leksikonet (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), som er en af de mest almindeligt anvendte subjectivity lexicons. Der er 5097 negative og 2533 positive ord i MPQA. Og alle disse ord kommenterede som rapporterne polaritet. Hele indekserede findes under GNU General Public License. Webtjenesten kan anvendes på en hvilken som helst korte sætninger, som tweets og Facebook indlæg. 

>Denne webtjeneste kan for eksempel blive brugt i brugere – potentielt via en mobilapp, via et websted eller endda på en lokal computer. Men formålet med webtjenesten er også skal fungere som et eksempel på hvordan Azure Machine Learning kan bruges til at oprette webtjenester oven på R kode. Med blot nogle få kodelinjer R og klikker på en knap i Azure Machine Learning Studio kan forsøg oprettes med R kode og publicerede som en webtjeneste. Webtjenesten kan derefter publiceret til Azure Marketplace og consumed for brugere og enheder på tværs af hele verden uden infrastruktur opsætning af forfatteren af webtjenesten.

##<a name="consumption-of-web-service"></a>Forbrug af webtjeneste

Inputdataene kan være noget tekst, men webtjenesten fungerer bedre med korte sætninger. Output er en numerisk værdi mellem 1 og 1. Alle værdier under 0 angiver, at synspunkt af teksten er negative. positive Hvis over 0. Den absolutte værdi af resultatet angiver styrken af den tilknyttede synspunkt. 

>Denne tjeneste, er som hostes på Azure Marketplace, et OData-tjeneste; Disse kan kaldes ved hjælp af POST eller GET metoder. 

Der er flere måder forbrug tjenesten i en automatiseret måde (en eksempel-app er [her](http://microsoftazuremachinelearning.azurewebsites.net/)).

###<a name="starting-c-code-for-web-service-consumption"></a>Starte C#-kode til web serviceforbrug:

    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();
    
                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



Input er "I dag er en god dag". Output er "1", som angiver en positiv synspunkt, der er knyttet til den Skriv sætning. 

##<a name="creation-of-web-service"></a>Oprettelse af webtjeneste
>Denne webtjeneste blev oprettet ved hjælp af Azure Machine Learning. En gratis prøveversion, samt introduktionsvideo om oprettelse af forsøg og [publicering webtjenester](machine-learning-publish-a-machine-learning-web-service.md), skal du se [azure.com/ml](http://azure.com/ml). Nedenfor vises et skærmbillede af forsøget, oprettes web-tjenesten og eksempel koden til hver af modulerne i forsøget.


I Azure Machine Learning blev en ny tom forsøg oprettet. I nedenstående figur vises forsøg strømmen af leksikonet-baserede synspunkt analyse. Filen "sent_dict.csv" er MPQA subjectivity leksikonet, og er angivet som en af input [Udføre R Script][execute-r-script]. En anden input er en indsamlede Gennemse fra Amazon Gennemse datasæt til test, hvor vi udført valg af kolonne omdøbningen og opdele handlinger. Vi bruger en hash-pakke til at gemme subjectivity leksikonet i hukommelsen og fremskynde processen karakteren beregning. Hele teksten er tokenized ved "tm" pakke og sammenlignet med word i synspunkt ordbogen. Til sidst skal beregnes en bogstavkarakter ved at tilføje tykkelsen på hvert subjektiv ord i teksten. 

###<a name="experiment-flow"></a>Forsøget flow:

![eksperimentere flow][2]


####<a name="module-1"></a>Modul 1:
    
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # <a name="install-hash-package"></a>Installere hash-pakke
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }
          
        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
    


##<a name="limitations"></a>Begrænsninger

Leksikonet-baserede synspunkt analyse er et generelt synspunkt analyse-værktøjet, som ikke kan fungere bedre end klassifikationsmetoden for bestemte felter fra en algoritme perspektiv. Negation problemet behandles ikke godt. Vi hardcode flere negation ord i vores program, men en bedre måde ved hjælp af en negation ordbog og opbygge nogle regler. Webtjenesten udfører bedre på korte og enkle sætninger, som tweets og Facebook indlæg end på lange og komplekse sætninger som Amazon anmeldelser. 

##<a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
Ofte stillede spørgsmål om forbrug af webtjeneste eller publicere til Azure Marketplace, skal du se [her](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 

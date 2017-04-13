<properties 
    pageTitle="Klynge Model | Microsoft Azure" 
    description="Klyngemodel" 
    services="machine-learning" 
    documentationCenter="" 
    authors="FrancescaLazzeri" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="lazzeri"/> 


#<a name="cluster-model"></a>Klyngemodel    

Hvordan kan vi forudsige grupper af kredit cardholders funktionsmåder for at reducere risikoen gebyr fra for kreditkort udstedere? Hvordan kan vi definere grupper af personer egenskaber af medarbejdere for at forbedre deres ydeevnen på arbejdet? Hvordan kan læger klassificere patienter i grupper, der er baseret på egenskaberne for deres sygdomme? I princippet kan alle disse spørgsmål besvares via klynge analyse.   


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
   
Klynge analyse klassificerer et sæt observationer i to eller flere udelukker ukendt grupper baseret på kombinationer af variabler. Formålet med klynge analyse er at finde et system til at organisere observationer, som regel personer eller deres egenskaber i grupper, hvor medlemmer af grupperne dele egenskaber til fælles. Denne [tjeneste](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) bruger K-betyder metoden, en ofte anvendte klyngedannelse teknik til klynge vilkårlig data i grupper. Denne webtjeneste tager dataene og antallet af k klynger som input, og giver prognoser hvoraf af grupperne k, som hver observationer tilhører. 

>Denne webtjeneste kunne blive brugt i brugere – potentielt via en mobilapp, via et websted eller endda på en lokal computer, f.eks. Men formålet med webtjenesten er også skal fungere som et eksempel på hvordan Azure Machine Learning kan bruges til at oprette webtjenester oven på R kode. Med blot nogle få kodelinjer R og klikker på en knap i Azure Machine Learning Studio kan forsøg oprettes med R kode og publicerede som en webtjeneste. Webtjenesten kan derefter publiceret til Azure Marketplace og consumed for brugere og enheder på tværs af hele verden uden infrastruktur opsætning af forfatteren af webtjenesten.  

##<a name="consumption-of-web-service"></a>Forbrug af webtjeneste   
Denne webtjeneste grupperer data i et sæt k grupper og skriver gruppetildeling for hver række. Webtjenesten forventer slutbrugeren mulighed for at angive data som en streng, hvor rækkerne er adskilt af kommaer (,) og kolonner er adskilt med semikolon (;). Webtjenesten forventer 1 række ad gangen. Et eksempel datasæt kunne se sådan ud:

![Eksempeldata][1]

Antag, at brugeren ville til at adskille disse data i 3 udelukker grupper. Datainput for ovenstående datasæt ville være følgende: værdi = "10; 5; 2,18; 1; 6,7; 5; 5,22; 3; 4,12; 2; 1,10; 3; 4"; k = "3". Output er estimerede gruppemedlemskabet for hver af rækkerne.

>Denne tjeneste, er som hostes på Azure Marketplace, et OData-tjeneste; Disse kan kaldes ved hjælp af POST eller GET metoder. 

Der er flere måder forbrug tjenesten i en automatiseret måde (en eksempel-app er [her](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Starte C#-kode til web serviceforbrug:

    public class Input
    {
            public string value;
            public string k;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




##<a name="creation-of-web-service"></a>Oprettelse af webtjeneste  
>Denne webtjeneste blev oprettet ved hjælp af Azure Machine Learning. En gratis prøveversion, samt introduktionsvideo om oprettelse af forsøg og [publicering webtjenester](machine-learning-publish-a-machine-learning-web-service.md), skal du se [azure.com/ml](http://azure.com/ml). Nedenfor vises et skærmbillede af forsøget, oprettes web-tjenesten og eksempel koden til hver af modulerne i forsøget.

I Azure Machine Learning en ny tom forsøg blev oprettet ud fra og to [Udføre R Script] [ execute-r-script] moduler, der er trukket til arbejdsområdet. Dataskemaet blev oprettet med et enkelt [Udføre R Script][execute-r-script]. Derefter dataskemaet blev knyttet til sektionen klynge model, igen oprettet med en [Udføre R Script][execute-r-script]. I [Udføre R Script] [ execute-r-script] bruges til klynge modellen, webtjenesten derefter kan anvender funktionen "k-betyder", som er færdige til [Udføre R Script] [ execute-r-script] af Azure Machine Learning.    
   

     
![Forsøget flow][3]

####<a name="module-1"></a>Modul 1: 
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
    
    maml.mapOutputPort("mydata");     
    

####<a name="module-2"></a>Modul 2:
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");
   
 
##<a name="limitations"></a>Begrænsninger
Dette er en meget simpel eksempel på en web-klyngetjeneste. Som kan ses fra ovenstående eksempelkode, ingen fejl fangst er implementeret og tjenesten antages det, at alt er en fortløbende variabel (ingen kategoriske funktioner tilladt), som de service kun input numeriske værdier på tidspunktet for oprettelse af denne webtjeneste. Desuden håndterer tjenesten aktuelt begrænset datastørrelse forfaldsdato anmodning/svar karakter kaldet til webtjenesten og fakultet, modellen er der egnet hver gang webtjenesten hedder. 

##<a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
Ofte stillede spørgsmål om forbrug af webtjeneste eller publicere til Azure Marketplace, skal du se [her](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

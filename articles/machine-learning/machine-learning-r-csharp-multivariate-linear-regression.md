<properties 
    pageTitle="Multivariate lineær Regression | Microsoft Azure" 
    description="Multivariate lineær Regression" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="multivariate-linear-regression"></a>Multivariate lineær Regression   
 

 
Antag, at du har et datasæt og gerne vil hurtigt forudsige en afhængige variabel (y) for hver person (i), der er baseret på inputområder. Lineær regression er en populære statistiske teknik, der bruges til disse prognoser. Her er afhængige variabel y antages, for at være en konstant værdi.  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

Et enkelt scenarie kan være hvor forsker forsøger at forudsige tykkelsen på en enkelt bruger (y) baseret på deres højde (x). Et scenarie med mere avancerede kan være, hvor forsker har yderligere oplysninger om enkelte (såsom vægt, køn, Kør) og forsøger at forudsige tykkelsen på enkelte. Denne [webtjeneste]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) passer til den lineære regressionsmodel til dataene og skriver den estimerede værdi (y) for hvert af bemærkningerne i dataene.

>Denne webtjeneste kunne blive brugt i brugere – potentielt via en mobilapp, via et websted eller endda på en lokal computer, f.eks. Men formålet med webtjenesten er også skal fungere som et eksempel på hvordan Azure Machine Learning kan bruges til at oprette webtjenester oven på R kode. Med blot nogle få kodelinjer R og klikker på en knap i Azure Machine Learning Studio kan forsøg oprettes med R kode og publicerede som en webtjeneste. Webtjenesten kan derefter publiceret til Azure Marketplace og consumed for brugere og enheder på tværs af hele verden uden infrastruktur opsætning af forfatteren af webtjenesten.  

##<a name="consumption-of-web-service"></a>Forbrug af webtjeneste  
Denne webtjeneste giver de estimerede værdier for den afhængige variabel, der er baseret på de uafhængige variabler for alle bemærkningerne. Webtjenesten forventer slutbrugeren mulighed for at angive data som en streng, hvor rækkerne er adskilt af kommaer (,) og kolonner er adskilt med semikolon (;). Webtjenesten forventer 1 række ad gangen og forventer, at den første kolonne skal være den afhængige variabel. Et eksempel datasæt kunne se sådan ud:

![Eksempeldata][1]

Bemærkninger uden en afhængig variabel skal være Indtast som "Ikke.tilgængelig" for y. Datainput for ovenstående datasæt ville være følgende streng: "10; 5; 2,18; 1; 6,6; 5.3; 2.1,7; 5; 5,22; 3; 4,12; 2; 1 ikke.tilgængelig; 3; 4". Output er de estimerede værdier for hver af de rækker, der er baseret på de uafhængige variabler. 

>Denne tjeneste, er som hostes på Azure Marketplace, et OData-tjeneste; Disse kan kaldes ved hjælp af POST eller GET metoder. 

Der er flere måder forbrug tjenesten i en automatiseret måde (en eksempel-app er [her](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Starte C#-kode til web serviceforbrug:

    public class Input
    {
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text };
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


I Azure Machine Learning en ny tom forsøg blev oprettet ud fra og to [Udføre R Script] [ execute-r-script] moduler er trukket til arbejdsområdet. Denne webtjeneste kører Azure Machine Learning forsøg med et underliggende R-script. Der er 2 dele dette forsøg: skemadefinitionen, og uddannelse model + vundne. Det første modul definerer den forventede struktur af input datasættet, hvor den første variabel er den afhængige variabel, og de resterende variabler er uafhængige. Det andet modul passer til en generisk lineær regressionsmodel for inputdataene.  
  
![Forsøget flow][3]

####<a name="module-1"></a>Modul 1:
 
####<a name="schema-definition"></a>Skemadefinitionen  
    data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####<a name="module-2"></a>Modul 2:
####<a name="lm-modeling"></a>LM modellering   
    data <- maml.mapInputPort(1) # class: data.frame  
  
    data.split <- strsplit(data[1,1], ",")[[1]]  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- as.data.frame(t(data.split)) 
    data.split <- data.matrix(data.split) 
    data.split <- data.frame(data.split) 
    model <- lm(data.split)  

    out=data.frame(predict(model,data.split))  
    out <- data.frame(t(out))

    maml.mapOutputPort("out");  
 
##<a name="limitations"></a>Begrænsninger
Dette er en meget simpel eksempel på en flere lineær regression webtjeneste. Som kan ses fra ovenstående eksempelkode, ingen fejl fangst er implementeret og tjenesten antages det, at alt er en fortløbende variabel (ingen kategoriske funktioner tilladt), som de service kun input numeriske værdier på tidspunktet for oprettelse af denne webtjeneste. Desuden håndterer tjenesten aktuelt begrænset datastørrelse forfaldsdato anmodning/svar karakter kaldet til webtjenesten og fakultet, modellen er der egnet hver gang webtjenesten hedder. 

##<a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
Ofte stillede spørgsmål om forbrug af webtjeneste eller publicere til Azure Marketplace, skal du se [her](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

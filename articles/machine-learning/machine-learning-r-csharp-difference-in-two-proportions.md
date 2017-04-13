<properties 
    pageTitle="Forskellen i proportioner Test | Microsoft Azure" 
    description="Forskellen i proportioner Test" 
    services="machine-learning" 
    documentationCenter="" 
    authors="aniedea" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="aniedea"/> 


#<a name="difference-in-proportions-test"></a>Forskellen i proportioner Test


Er der to proportioner statistisk? Antag, at en bruger ønsker at sammenligne to film til at afgøre, om en film har en betydeligt bedre del af 'synes godt om' hvornår sammenlignet med den anden. Med et stort eksempel, kan der være en statistisk signifikant forskellen mellem proportioner 0,50 og 0.51. Med et lille udvalg, der muligvis ikke nok data til at bestemme, om disse proportioner er faktisk forskellige. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Denne [webtjeneste]( https://datamarket.azure.com/dataset/aml_labs/prop_test) udfører en hypotetisk test af forskellen i to proportioner, der er baseret på brugerinput af antallet af gunstige udfald og det samlede antal forsøg for grupperne 2 sammenligning. I et muligt scenario, kan denne webtjeneste kaldes fra i en film sammenligning app, der fortæller brugeren, om en af filmene er virkelig 'synes godt om' oftere end den anden baseret på film bedømmelser.

>Denne webtjeneste kunne blive brugt i brugere – potentielt via en mobilapp, via et websted eller endda på en lokal computer, f.eks. Men formålet med webtjenesten er også skal fungere som et eksempel på hvordan Azure Machine Learning kan bruges til at oprette webtjenester oven på R kode. Med blot nogle få kodelinjer R og klikker på en knap i Azure Machine Learning Studio kan forsøg oprettes med R kode og publicerede som en webtjeneste. Webtjenesten kan derefter publiceret til Azure Marketplace og consumed for brugere og enheder på tværs af hele verden uden infrastruktur opsætning af forfatteren af webtjenesten.


##<a name="consumption-of-web-service"></a>Forbrug af webtjeneste

Denne tjeneste accepterer 4 argumenter og indeholder en hypotetisk test af proportioner.

Argumenterne input er:

* Successes1 - antallet af vellykkede hændelser i eksempel 1.
* Successes2 - antallet af vellykkede hændelser i eksempel 2.
* Total1 - størrelsen på Eksempel 1.
* Total2 - størrelsen på Eksempel 2.

Output fra tjenesten er resultatet af hypotetisk test sammen med chi-square-statistikken, f, p-værdien, og del i eksempel 1/2 og konfidensinterval grænser.

>Denne tjeneste, er som hostes på Azure Marketplace, et OData-tjeneste; Disse kan kaldes ved hjælp af POST eller GET metoder. 

Der er flere måder forbrug tjenesten i en automatiseret måde (en eksempel-app er [her](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Starte C#-kode til web serviceforbrug:

    public class Input
    {
            public string successes1;
            public string successes2;
            public string total1;
            public string total2;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
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

I Azure Machine Learning en ny tom forsøg blev oprettet med to [Udføre R Script] [ execute-r-script] moduler. I det første modul, der er defineret i dataskemaet, mens anden bruger modul kommandoen prop.test i R til at udføre hypotetisk test for 2 proportioner. 


###<a name="experiment-flow"></a>Forsøget flow:

![Forsøget flow][2]


####<a name="module-1"></a>Modul 1:
    ####Schema definition  
    data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
    maml.mapOutputPort("data.set"); #send data to output port
    dataset1 = maml.mapInputPort(1) #read data from input port
    

####<a name="module-2"></a>Modul 2:

    test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

    result=data.frame(
    result=ifelse(test$p.value<0.05,"The proportions are different!",
    "The proportions aren't different statistically."),
    ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
    pvalue=round(test$p.value,4),
    proportion1=round(test$estimate[1],4),
    proportion2=round(test$estimate[2],4),
    confintlow=round(test$conf.int[1],4),
    confinthigh=round(test$conf.int[2],4)) 

    maml.mapOutputPort("result"); #output port
    

##<a name="limitations"></a>Begrænsninger 

Dette er en meget simpel eksempel for en test af forskellen i 2 proportioner. Som kan ses fra ovenstående eksempelkode, ingen fejl fangst er implementeret og tjenesten antager, at alle variabler er sammenhængende.

##<a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
Ofte stillede spørgsmål om forbrug af webtjeneste eller publicere til Azure Marketplace, skal du se [her](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

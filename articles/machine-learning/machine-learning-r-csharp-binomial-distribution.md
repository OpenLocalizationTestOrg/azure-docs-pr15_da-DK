<properties 
    pageTitle="BINOMIALFORDELING pakke | Microsoft Azure" 
    description="BINOMIALFORDELING pakke" 
    services="machine-learning" 
    documentationCenter="" 
    authors="ireiter" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="ireiter"/> 


#<a name="binomial-distribution-suite"></a>BINOMIALFORDELING pakke




Den BINOMIALFORDELING pakke er et sæt af eksempel-webtjenester ([Binomiale Generator](https://datamarket.azure.com/dataset/aml_labs/bdg5), [Sandsynlighed Lommeregner]( https://datamarket.azure.com/dataset/aml_labs/bdp4), [Fraktil Lommeregner]( https://datamarket.azure.com/dataset/aml_labs/bdq5)), hjælpe med at oprette og håndtere binomiale fordeling. Tjenesterne Tillad oprettelse af en binomial fordeling sekvens af enhver længde, beregning af quantiles ud af givet sandsynligheden og beregning af sandsynlighed fra en given fraktil. Hver af tjenesterne udsender forskellige output baseret på den valgte tjeneste (se beskrivelsen nedenfor). Den BINOMIALFORDELING pakke er baseret på den R funktioner qbinom rbinom og pbinom, som er inkluderet i pakken R statistik. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Disse webtjenester kan blive brugt i brugere – potentielt direkte på marketplace gennem en mobilapp, via et websted eller endda på en lokal computer, f.eks. Men formålet med webtjenesten er også skal fungere som et eksempel på hvordan Azure Machine Learning kan bruges til at oprette webtjenester oven på R kode. Med blot nogle få kodelinjer R og klikker på en knap i Azure Machine Learning Studio kan forsøg oprettes med R kode og publicerede som en webtjeneste. Webtjenesten kan derefter publiceret til Azure Marketplace og consumed for brugere og enheder på tværs af hele verden – ingen infrastruktur opsætning af forfatteren af webtjenesten er påkrævet.

##<a name="consumption-of-web-service"></a>Forbrug af webtjeneste
Den BINOMIALFORDELING pakke omfatter følgende 3 tjenester.

###<a name="binomial-distribution-quantile-calculator"></a>BINOMIALFORDELING fraktil Lommeregner
Denne tjeneste accepterer 4 argumenter af normalfordeling og beregner den tilknyttede fraktil.
Argumenterne input er:

- p - en enkelt aggregeret sandsynlighed af flere forsøg.  
- størrelse - antallet af forsøg.
- sandsynlighed - sandsynlighed for succes i en prøveversion.
- Side - L for den nederste side af en fordeling U til den øverste side for den stokastiske variabel. 

Output fra tjenesten er det beregnet fraktil, der er knyttet til den angivne sandsynlighed.

###<a name="binomial-distribution-probability-calculator"></a>BINOMIALFORDELING sandsynlighed Lommeregner
Denne tjeneste accepterer 4 argumenter af en binomial fordeling og beregner sandsynligheden for tilknyttede.
Argumenterne input er:

- spørgsmål - en enkelt fraktil for en begivenhed med binomiale fordeling. 
- størrelse - antallet af forsøg.
- sandsynlighed - sandsynlighed for succes i en prøveversion.
- side - L for den nederste side af en fordeling U til den øverste del af distribution eller E, der svarer til en enkelt antallet af gunstige udfald.

Output fra tjenesten er beregnet sandsynligheden, der er knyttet til den angivne fraktil.

###<a name="binomial-distribution-generator"></a>BINOMIALFORDELING Generator
Denne tjeneste accepterer 3 argumenter af en binomial fordeling og genererer en tilfældig rækkefølge af tal, der er distribueret binomially. Følgende argumenter skal være beregnet til den i anmodningen:

- n - antallet af observationer. 
- størrelse - antallet af forsøg.
- sandsynlighed - sandsynlighed for succes.

Output fra tjenesten er en sekvens af længde n med en binomial fordeling baseret på størrelse og sandsynlighed argumenterne.

>Denne tjeneste, er som hostes på Azure Marketplace, et OData-tjeneste; Disse kan kaldes ved hjælp af POST eller GET metoder. 

Der er flere måder forbrug tjenesten i en automatiseret måde (eksempel apps er her: [Generator](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx), [Sandsynlighed Lommeregner](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx), [Fraktil Lommeregner](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)). 

###<a name="starting-c-code-for-web-service-consumption"></a>Starte C#-kode til web serviceforbrug:

###<a name="binomial-distribution-quantile-calculator"></a>BINOMIALFORDELING fraktil Lommeregner
    public class Input
    {
            public string p;
            public string size;
            public string prob;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void main()
    {
            var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="binomial-distribution-probability-calculator"></a>BINOMIALFORDELING sandsynlighed Lommeregner
    public class Input
    {
            public string q;
            public string size;
            public string prob;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="binomial-distribution-generator"></a>BINOMIALFORDELING Generator
    public class Input
    {
            public string n;
            public string size;
            public string p;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
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

###<a name="binomial-distribution-quantile-calculator"></a>BINOMIALFORDELING fraktil Lommeregner

![Opret arbejdsområde][4]

####<a name="module-1"></a>Modul 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####<a name="module-2"></a>Modul 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }

    if (param$prob < 0 ) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 0
    } else if (param$prob > 1) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
    band=subset(df,x>quantile)
    } else if (param$side =='L') {
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
    band=subset(df,x<=quantile)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###<a name="binomial-distribution-probability-calculator"></a>BINOMIALFORDELING sandsynlighed Lommeregner

![Opret arbejdsområde][5]

####<a name="module-1"></a>Modul 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####<a name="module-2"></a>Modul 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
    prob = 1 - prob
    band=subset(df,x>param$q)
    } else if (param$side =='E') {
    prob = prob.eq
    band=subset(df,x==param$q)
    } else if (param$side =='L') {
    prob = prob
    band=subset(df,x<=param$q)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###<a name="binomial-distribution-generator"></a>BINOMIALFORDELING Generator

![Opret arbejdsområde][6]

####<a name="module-1"></a>Modul 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####<a name="module-2"></a>Modul 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>Begrænsninger 
Dette er meget simpel eksempler, der omgiver den binomiale fordeling. Som kan ses fra ovenstående eksempelkode, er lille fejl fangst implementeret.

##<a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
Ofte stillede spørgsmål om forbrug af webtjeneste eller publicere til Azure Marketplace, skal du se [her](machine-learning-marketplace-faq.md).


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png
 

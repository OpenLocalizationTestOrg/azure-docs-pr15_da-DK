<properties 
    pageTitle="Standardnormalfordelingen Web Service Suite | Microsoft Azure" 
    description="Standardnormalfordelingen Web Service Suite" 
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

#<a name="normal-distribution-suite"></a>Standardnormalfordelingen pakke



Pakken standardnormalfordelingen er et sæt af eksempel-webtjenester ([Generator]( https://datamarket.azure.com/dataset/aml_labs/ndg7), [Fraktil Lommeregner]( https://datamarket.azure.com/dataset/aml_labs/ndq5), [Sandsynlighed Lommeregner]( https://datamarket.azure.com/dataset/aml_labs/ndp5)), hjælpe med at oprette og håndtering af normal salgsdistributioner. Tjenesterne Tillad oprettelse af en standardnormalfordelingen sekvens af enhver længde, beregning af quantiles fra en bestemt sandsynlighed og beregne sandsynlighed fra en given fraktil. Hver af tjenesterne udsender forskellige output baseret på den valgte tjeneste (se beskrivelsen nedenfor). Pakken standardnormalfordelingen er baseret på den R funktioner qnorm rnorm og pnorm, som er inkluderet i R statistik pakke.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Denne webtjeneste kunne blive brugt i brugere – potentielt via en mobilapp, via et websted eller endda på en lokal computer, f.eks. Men formålet med webtjenesten er også skal fungere som et eksempel på hvordan Azure Machine Learning kan bruges til at oprette webtjenester oven på R kode. Med blot nogle få kodelinjer R og klikker på en knap i Azure Machine Learning Studio kan forsøg oprettes med R kode og publicerede som en webtjeneste. Webtjenesten kan derefter publiceret til Azure Marketplace og consumed for brugere og enheder på tværs af hele verden uden infrastruktur opsætning af forfatteren af webtjenesten.  
 

##<a name="consumption-of-web-service"></a>Forbrug af webtjeneste
Standardnormalfordelingen pakken indeholder følgende 3 tjenester.

###<a name="normal-distribution-quantile-calculator"></a>Standardnormalfordelingen fraktil Lommeregner
Denne tjeneste accepterer 4 argumenter af normalfordeling og beregner den tilknyttede fraktil.

Argumenterne input er:

* p - en enkelt sandsynlighed for en begivenhed med normalfordeling. 
* Middelværdi - standardnormalfordelingen middelværdien.
* SD - standardafvigelsen standardnormalfordelingen. 
* Side - L til den nederste side for den stokastiske variabel og U til den øverste side for den stokastiske variabel.

Output fra tjenesten er det beregnet fraktil, der er knyttet til den angivne sandsynlighed.

###<a name="normal-distribution-probability-calculator"></a>Standardnormalfordelingen sandsynlighed Lommeregner
Denne tjeneste accepterer 4 argumenter af normalfordeling og beregner sandsynligheden for tilknyttede.

Argumenterne input er:

* spørgsmål - en enkelt fraktil for en begivenhed med normalfordeling. 
* Middelværdi - standardnormalfordelingen middelværdien.
* SD - standardafvigelsen standardnormalfordelingen. 
* Side - L til den nederste side for den stokastiske variabel og U til den øverste side for den stokastiske variabel.

Output fra tjenesten er beregnet sandsynligheden, der er knyttet til den angivne fraktil.

###<a name="normal-distribution-generator"></a>Standardnormalfordelingen Generator
Denne tjeneste accepterer 3 argumenter af normalfordeling og genererer en tilfældig rækkefølge af tal, der er normalt fordelt. Følgende argumenter skal være beregnet til den i anmodningen:

* n - antallet af observationer. 
* middelværdi - standardnormalfordelingen middelværdien.
* SD - standardafvigelsen standardnormalfordelingen. 

Output fra tjenesten er en sekvens af længde n med normalfordeling baseret på argumenterne middelværdi og sd.

>Denne tjeneste, er som hostes på Azure Marketplace, et OData-tjeneste; Disse kan kaldes ved hjælp af POST eller GET metoder. 

Der er flere måder forbrug tjenesten i en automatiseret måde (eksempel apps er her: [Generator](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx), [Sandsynlighed Lommeregner](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx), [Fraktil Lommeregner](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Starte C#-kode til web serviceforbrug:

###<a name="normal-distribution-quantile-calculator"></a>Standardnormalfordelingen fraktil Lommeregner
    public class Input
    {
            public string p;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="normal-distribution-probability-calculator"></a>Standardnormalfordelingen sandsynlighed Lommeregner
    public class Input
    {
            public string q;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="normal-distribution-generator"></a>Standardnormalfordelingen Generator
    public class Input
    {
            public string n;
            public string mean;
            public string sd;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
>Denne webtjeneste blev oprettet ved hjælp af Azure Machine Learning. En gratis prøveversion, samt introduktionsvideo om oprettelse af forsøg og [publicering webtjenester](machine-learning-publish-a-machine-learning-web-service.md), skal du se [azure.com/ml](http://azure.com/ml). 

Nedenfor vises et skærmbillede af forsøget, oprettes web-tjenesten og eksempel koden til hver af modulerne i forsøget.

###<a name="normal-distribution-quantile-calculator"></a>Standardnormalfordelingen fraktil Lommeregner

Forsøget flow:

![Forsøget flow][2]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }
    q = qnorm(param$p,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    q = 2* param$mean - q
    } else if (param$side =='L') {
    q = q
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(q)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-probability-calculator"></a>Standardnormalfordelingen sandsynlighed Lommeregner
Forsøget flow:

![Forsøget flow][3]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(q=-1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    prob = pnorm(param$q,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    prob = 1 - prob
    } else if (param$side =='B') {
    prob = ifelse(prob<=0.5,prob * 2, 1)
    } else if (param$side =='L') {
    prob = prob
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-generator"></a>Standardnormalfordelingen Generator
Forsøget flow:

![Forsøget flow][4]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,mean=0,sd=1);
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    dist = rnorm(param$n,mean=param$mean,sd=param$sd)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>Begrænsninger 

Dette er meget simpel eksempler, der omgiver normalfordelingen. Som kan ses fra ovenstående eksempelkode, er lille fejl fangst implementeret.

##<a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
Ofte stillede spørgsmål om forbrug af webtjeneste eller publicere til Azure Marketplace, skal du se [her](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
 

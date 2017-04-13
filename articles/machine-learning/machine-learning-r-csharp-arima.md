<properties 
    pageTitle="Prognoser: Autoregressive integreret flytte gennemsnittet (ARIMA) | Microsoft Azure" 
    description="Prognoser - Autoregressive integreret flytte gennemsnittet (ARIMA)" 
    services="machine-learning" 
    documentationCenter="" 
    authors="yijichen" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="yijichen"/> 

 
#<a name="forecasting---autoregressive-integrated-moving-average-arima"></a>Prognoser - Autoregressive integreret flytte gennemsnittet (ARIMA)

Denne [tjeneste]( https://datamarket.azure.com/dataset/aml_labs/arima) implementerer Autoregressive integreret flytte MIDDEL (ARIMA) for at frembringe prognoser baseret på de historiske data, der er angivet af brugeren. Krav om et bestemt produkt, øger år? Kan jeg forudsige min produktsalg for season jul, så jeg effektivt kan planlægge mine lager? Laver prognose modeller er Hovedgaden på disse spørgsmål. Hvis du har de seneste data, Undersøg disse modeller skjulte tendenser og sæsonudsving til at forudsige fremtidige tendenser. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

>Denne webtjeneste kunne blive brugt i brugere – potentielt via en mobilapp, via et websted eller endda på en lokal computer, f.eks. Men formålet med webtjenesten er også skal fungere som et eksempel på hvordan Azure Machine Learning kan bruges til at oprette webtjenester oven på R kode. Med blot nogle få kodelinjer R og klikker på en knap i Azure Machine Learning Studio kan forsøg oprettes med R kode og publicerede som en webtjeneste. Webtjenesten kan derefter publiceret til Azure Marketplace og consumed for brugere og enheder på tværs af hele verden uden infrastruktur opsætning af forfatteren af webtjenesten.

##<a name="consumption-of-web-service"></a>Forbrug af webtjeneste 

Denne tjeneste accepterer 4 argumenter og beregner ARIMA prognoser.
Argumenterne input er:

* Frekvens - angiver hyppigheden for rækkedata (dagligt/uge/måned/kvartalsvis/årligt).
* Horisont - fremtiden forecast tidsramme.
* Dato - tilføje i den nye tid seriedata til tid.
* Værdi - tilføje i de nye række data klokkeslætsværdier.

Output fra tjenesten er de beregnede prognose værdier. 

Eksempel input skyldes: 

* Frekvens - 12
* Horisont - 12
* Dato - 1-15-2012 2-15-2012 3-15-2012; 4-15-2012; 5-15-2012; 6-15-2012; 7-15-2012; 8 / 15 2012-9-15-2012 10-15-2012; 11-15-2012; 12-15-2012 15-1/2013 2013-15/2 3/15/2013; 2013-15/4; 5/15/2013; 2013-15/6, 7/15/2013; 8 / 15 2013-2013-15/9 2013-15/10, 11/15/2013; 12/15/2013 15/1/2014 2-15-2014 3-15-2014; 4-15-2014; 5-15-2014; 6-15-2014; 7-15-2014; 8 / 15-2014, 9-15-2014
* Værdi - 3.479; 3.68; 3.832; 3.941; 3.797; 3.586; 3.508; 3.731; 3.915; 3.844; 3.634; 3.549; 3.557; 3.785; 3.782; 3.601; 3.544; 3.556; 3.65; 3.709; 3.682; 3.511; 3.429 3.51 3.523; 3.525; 3.626; 3.695; 3.711; 3.711; 3.693; 3.571; 3.509
 
>Denne tjeneste, er som hostes på Azure Marketplace, et OData-tjeneste; Disse kan kaldes ved hjælp af POST eller GET metoder. 

Der er flere måder forbrug tjenesten i en automatiseret måde (en eksempel-app er [her](http://microsoftazuremachinelearning.azurewebsites.net/ArimaForecasting.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Starte C#-kode til web serviceforbrug:

    public class Input
    {
        public string frequency;
        public string horizon;
        public string date;
        public string value;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
         byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
         return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

       
    void Main()
    {
        var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
        var json = JsonConvert.SerializeObject(input);
        var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
           
        var httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        var query = httpClient.PostAsync(acitionUri,new StringContent(json));
        var result = query.Result.Content;
        var scoreResult = result.ReadAsStringAsync().Result;
    }

##<a name="creation-of-web-service"></a>Oprettelse af webtjeneste 

>Denne webtjeneste blev oprettet ved hjælp af Azure Machine Learning. En gratis prøveversion, samt introduktionsvideo om oprettelse af forsøg og [publicering webtjenester](machine-learning-publish-a-machine-learning-web-service.md), skal du se [azure.com/ml](http://azure.com/ml). Nedenfor vises et skærmbillede af forsøget, oprettes web-tjenesten og eksempel koden til hver af modulerne i forsøget.

I Azure Machine Learning blev en ny tom forsøg oprettet. Prøveinputdata blev overført med et foruddefineret data skema. Sammenkædet med dataskemaet er et [Udføre R Script] [ execute-r-script] modulet, som genererer ARIMA prognoser model ved hjælp af 'auto.arima' og 'prognose' funktioner fra R. 

###<a name="experiment-flow"></a>Forsøget flow:

![Opret arbejdsområde][2]

####<a name="module-1"></a>Modul 1:
 
    # Add in the CSV file with the data in the format shown below 
![Opret arbejdsområde][3]  

####<a name="module-2"></a>Modul 2:
    # data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)
    
    # preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]
    
    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)
    
    # fit a time-series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- auto.arima(train_ts)
    train_model <- forecast(fit1, h = data$horizon)
    plot(train_model)
    
    # produce forecasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
    
    # data output
    maml.mapOutputPort("data.forecast");


##<a name="limitations"></a>Begrænsninger 

Dette er en meget simpel eksempel til ARIMA prognose. Ingen fejl fangst er implementeret, som kan ses fra ovenstående eksempelkode, og tjenesten antager, at alle variabler er kontinuerlig/positive værdier og hyppigheden skal være et heltal, der er større end 1. Længden af mulighederne dato og værdien skal være den samme. Skal overholde variablen dato i formatet "mm-dd-åååå".

##<a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
Ofte stillede spørgsmål om forbrug af webtjeneste eller publicere til marketplace, skal du se [her](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-arima/arima-img1.png
[2]: ./media/machine-learning-r-csharp-arima/arima-img2.png
[3]: ./media/machine-learning-r-csharp-arima/arima-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

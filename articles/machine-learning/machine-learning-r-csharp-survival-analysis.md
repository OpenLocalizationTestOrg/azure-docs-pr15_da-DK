<properties 
    pageTitle="Overlevelsesguide analyse af resultatopgørelse med Azure Machine Learning | Microsoft Azure" 
    description="Overlevelsesguide analyse begivenhed forekomst sandsynlighed" 
    services="machine-learning" 
    documentationCenter="" 
    authors="zhangya" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="zhangya"/> 


#<a name="survival-analysis"></a>Overlevelsesguide analyse 

I mange situationer er det primære resultatet under vurdering tid på en hændelse af interesse. Med andre ord spørgsmålet "Når begivenheden skal forekomme?" bedt om. Som eksempler på, kan du overveje situationer, hvor data, der beskriver den forløbne tid (dage, år, kørte kilometer osv.) indtil hændelsen i interesse (hos relapse, pH.d.-grad modtaget, målinger tastatur fejl) opstår. Hver forekomst i dataene, der repræsenterer et bestemt objekt (patienter, en studerende, en bil osv.).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Denne [webtjeneste]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) finder du svar på spørgsmålet "Hvad er sandsynligheden for, at hændelsen af interesse skal forekomme ved klokkeslæt n for objektet x?" Ved at oprette en overlevelsesguide analysis model, denne webtjeneste giver brugerne mulighed at angive data for at undervise modellen og tester den. Primære temaet for forsøget er at model længden af den forløbne tid, indtil hændelsen af interesse indtræffer. 

>Denne webtjeneste kunne blive brugt i brugere – potentielt via en mobilapp, via et websted eller endda på en lokal computer, f.eks. Men formålet med webtjenesten er også skal fungere som et eksempel på hvordan Azure Machine Learning kan bruges til at oprette webtjenester oven på R kode. Med blot nogle få kodelinjer R og klikker på en knap i Azure Machine Learning Studio kan forsøg oprettes med R kode og publicerede som en webtjeneste. Webtjenesten kan derefter publiceret til Azure Marketplace og consumed for brugere og enheder på tværs af hele verden uden infrastruktur opsætning af forfatteren af webtjenesten.  

##<a name="consumption-of-web-service"></a>Forbrug af webtjeneste

Inputdataene skemaet for webtjenesten vises i den følgende tabel. Seks dele af oplysninger der skal bruges som input: kursus data, skal du teste data: tidspunktet for interesse, kan du indekset "time" dimension, fortegnelse over "begivenhed" dimension og de variable typer (fortløbende eller faktor). Datatypen kursus repræsenteres med en streng, hvor rækkerne, der er adskilt af komma, og kolonnerne, der er adskilt med semikolon. Antallet af funktioner i dataene, der er fleksibel. Alle elementer i strengen input skal være numerisk. I data kursus angiver dimensionen "tid" antallet af tidsenheder (dage, år, kørte kilometer osv.), der er forløbet siden startpunktet for undersøgelsen (patienter modtager drug behandling programmer, en studerende første pH.d.-undersøgelse, en bil begynder at trække, osv.), indtil hændelsen af interesse (den tålmodig vende tilbage til drug brugen, student bestille pH.d.-antal grader, bil har målinger tastatur fejl osv.) opstår. Dimensionen "begivenhed" Angiver, om begivenheden af interesse optræder i slutningen af undersøgelsen. En værdi på "begivenhed = 1" betyder, at hændelsen af interesse indtræffer på det tidspunkt, der er angivet med dimensionen "tid"; "begivenhed = 0" betyder, at der ikke opstod hændelsen af interesse ved den tid, der er angivet med dimensionen "tid".

- trainingdata - en tegnstreng under sammenligningen. Rækker adskilles med komma, og kolonner er adskilt med semikolon. Hver række indeholder "tidsdimension", "begivenhed" dimension og indikator for stigning variabler.
- testingdata - én række af data, der indeholder indikator for stigning variabler for et bestemt objekt.
- time_of_interest - den forløbne tid af interesse n.
- index_time - kolonneindeks i dimensionen "tid" (begyndende fra 1).
- index_event - kolonneindeks af dimensionen "begivenhed" (begyndende fra 1).
- variable_types - en tegnstreng under sammenligningen med komma som separatorer i den. 0 repræsenterer fortløbende variabler og 1 repræsenterer faktor variabler.


Output er sandsynlighed for en begivenhed, der foregår efter et bestemt tidspunkt. 

>Denne tjeneste, er som hostes på Azure Marketplace, et OData-tjeneste; Disse kan kaldes ved hjælp af POST eller GET metoder. 

Der er flere måder forbrug tjenesten i en automatiseret måde (en eksempel-app er [her](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)). 

###<a name="starting-c-code-for-web-service-consumption"></a>Starte C#-kode til web serviceforbrug:
    public class Input
    {
            public string trainingdata;
            public string testingdata;
            public string timeofinterest;
            public string indextime;
            public string indexevent;
            public string variabletypes;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




Fortolkning af denne test er som følger. Hvis formålet med dataene, er at model den forløbne tid indtil gå tilbage til drug brugen for patienter der fik en af to behandling programmerne. Output fra web service læser: til patienter der 35 år gamle, har du forrige drug behandling 2 gange, tager lang tid lokal behandling programmet, og det med både heroin og cocaine brugen af sandsynligheden for at vende tilbage til drug brugen er 95.64% efter dag 500.

##<a name="creation-of-web-service"></a>Oprettelse af webtjeneste

>Denne webtjeneste blev oprettet ved hjælp af Azure Machine Learning. En gratis prøveversion, samt introduktionsvideo om oprettelse af forsøg og [publicering webtjenester](machine-learning-publish-a-machine-learning-web-service.md), skal du se [azure.com/ml](http://azure.com/ml). Nedenfor vises et skærmbillede af forsøget, oprettes web-tjenesten og eksempel koden til hver af modulerne i forsøget.

I Azure Machine Learning en ny tom forsøg blev oprettet ud fra og to [Udføre R Script] [ execute-r-script] moduler er trukket til arbejdsområdet. Dataskemaet blev oprettet med et enkelt [Udføre R Script][execute-r-script], som definerer inputdataene skemaet til webtjenesten. Dette modul derefter er knyttet til den anden [Udføre R Script] [ execute-r-script] modulet, som vigtige arbejde. Dette modul understøtter forbehandling af data, model bygning og prognoser. I trinnet data forbehandling er de indtastede data, der er repræsenteret af en lang tekststreng transformeret og konverteret til en data ramme. Trinnet model dokumentkomponent installeret en ekstern R-pakke "survival_2.37-7.zip" først til udførelse af overlevelsesguide analyse. Derefter køres funktionen "coxph" efter serie databehandling opgaver. Oplysninger om funktionen "coxph" til overlevelsesguide analyse kan læses fra R dokumentation. En forekomst af test leveres i erfaren datamodel med funktionen "surfit" i trinnet forudsigelse, og overlevelsesguide kurven for denne test forekomst produceret som "kurve" variabel. Til sidst skal hentes sandsynligheden for tidspunktet, hvor renten. 

###<a name="experiment-flow"></a>Forsøget flow:

![eksperimentere flow][1]

####<a name="module-1"></a>Modul 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
    
####<a name="module-2"></a>Modul 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##<a name="limitations"></a>Begrænsninger

Denne webtjeneste kan tage kun numeriske værdier som funktion variabler (kolonner). Kolonnen "hændelse" det kan tage kun værdien 0 eller 1. Kolonnen "time" skal være et positivt heltal.

##<a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
Ofte stillede spørgsmål om forbrug af webtjeneste eller publicere til Azure Marketplace, skal du se [her](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

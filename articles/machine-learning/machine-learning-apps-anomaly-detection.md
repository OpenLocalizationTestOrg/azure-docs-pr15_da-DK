<properties 
    pageTitle="Machine Learning app: anomali registrering Service | Microsoft Azure" 
    description="Anomali registrering API er et eksempel, der er oprettet med Microsoft Azure Machine Learning, der registrerer afvigelser i serie tidsdata med numeriske værdier, der er jævnt fordelte tidspunkt." 
    services="machine-learning" 
    documentationCenter="" 
    authors="alokkirpal" 
    manager="jhubbard"
    editor="cgronlun" /> 

<tags 
    ms.service="machine-learning" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="multiple" 
    ms.date="10/11/2016" 
    ms.author="alokkirpal"/>


# <a name="machine-learning-anomaly-detection-service"></a>Machine Learning anomali registrering Service#

##<a name="overview"></a>Oversigt

[Anomali registrering API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) er et eksempel, der er oprettet med Azure Machine Learning, der registrerer afvigelser i serie tidsdata med numeriske værdier, der er jævnt fordelte tidspunkt. 

Denne API kan registrere følgende typer uoverensstemmende mønstre i serie tidsdata:

* **Positive og negative tendenser**: eksempelvis når overvågning hukommelsesforbrug i computing en opadgående tendens kan have interesse som det kan være vejledende af et hul i hukommelsen,

* **Ændringer i det dynamiske område af værdier**: f.eks, når du overvåger undtagelser udløst af en skybaseret tjeneste, eventuelle ændringer i det dynamiske område af værdier kan angive ustabil i tilstanden for tjenesten, og

* **Højest og Dips**: f.eks, når du overvåger antallet af mislykkede forsøg på login i en tjeneste eller antallet af udtjekninger på et websted til en e-handel, spidser eller fald skyldes unormal funktionsmåde.

Disse machine learning detektorer registrere disse ændringer i værdier over tid og rapportere igangværende ændringer i deres værdier som anomali resultater. De kræver ikke adhoc grænseværdi for justering og deres resultater kan bruges til at styre falsk positiv rente. Anomali påvisning API er nyttig i flere scenarier som serviceovervågning ved at spore KPI'er i tidens løb brugen overvågning gennem målepunkter som antallet af søgninger, antal klik, overvågning gennem tællere som hukommelse, CPU, fil læser osv over tid.

Registrering af anomali tilbyder leveres med praktiske værktøjer til at komme i gang. 

* [Webprogram, der](http://anomalydetection-aml.azurewebsites.net/) hjælper dig med at evaluere og visualisere resultaterne af anomali registrering API'er af dine data. 

* [Eksempel på kode](http://adresultparser.codeplex.com/) viser, hvordan automatisk få adgang til API og fortolke resultaterne i C#.

>[AZURE.NOTE]
>Prøv **IT anomali indsigt løsning** drevet af [Denne API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection)
>
>At få denne start til slut løsning, der er installeret på abonnementet Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **Start her >**</a>


##<a name="api-definition"></a>Definition af API

Tjenesten giver en RESTEN-baseret API over HTTPS, der kan benyttes på forskellige måder, herunder et websted eller mobile-program, R, Python, Excel osv.  Du sender din serie tidsdata til denne tjeneste via et REST-API-opkald, og der køres en kombination af de tre anomali typer, der er beskrevet ovenfor. Tjenesten kører på Azure Machine Learning platformen, som skaleres til virksomhedens behov problemfrit og giver SLA.

###<a name="headers"></a>Sidehoveder
Sørg for at medtage de korrekte sidehoveder i mødeindkaldelsen, som skal være på følgende måde:

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Du kan finde din kontonøgle fra din konto på [Azure Data marked](https://datamarket.azure.com/account/keys). 

###<a name="score-api"></a>Resultat API

Resultat API bruges til at køre anomali registrering på ikke-sæsonbestemte tidsdata serie. API kører et antal anomali detektorer på dataene og returnerer deres anomali resultater. I nedenstående figur viser et eksempel på afvigelser, der kan registrere API'EN resultat. Denne tidsserie har 2 entydige niveau ændringer, og 3 spidser. De røde prikker viser den tid, hvormed niveau registreres ændringen, mens de sorte prikker viser fundet spidser.

![Resultat API][1]
    
**URL-ADRESSE**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Eksempel anmodningsteksten**

I nedenstående anmodningen sender vi en tidsserie med datapunkter fra 3/1/2016 gennem 3/10/2016 og parametre detektorernes samling (vist afkortede) til API til at registrere Hvis nogen af disse datapunkter er uoverensstemmende. 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

Svar til denne bliver: 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

###<a name="scorewithseasonality-api"></a>ScoreWithSeasonality API

ScoreWithSeasonality API bruges til at køre anomali registrering på tidsserie, der har sæsonbestemte mønstre. Denne API er praktisk til at registrere afvigelser i sæsonbestemte mønstre.  

I følgende figur vises et eksempel på afvigelser, der er registreret i en sæsonbestemte tidsserie. Tidsserien har en samling (den 1st sorte prik), to fald (2. sort prik og én i slutningen) og én niveau ændring (rød prik). Bemærk, at begge dip midt i tidsserien og ændring af niveau kun discernable efter sæsonbestemte komponenter er fjernet fra serien.

![Sæsonudsving API][2]

**URL-ADRESSE**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Eksempel anmodningsteksten**

I nedenstående anmodningen sender vi en tidsserie (vist afkortede) med datapunkter fra 3/1/2016 gennem 3/10/2016 og parametre til API til at registrere Hvis nogen af disse datapunkter er uoverensstemmende. 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

Svar til denne bliver: 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
            "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
            "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
            ]
        }"
    }

###<a name="detectors"></a>Detektorer

Anomali påvisning API understøtter detektorer i 3 overordnede kategorier. Finde oplysninger om bestemte inputparametre og afgang for hver registrering ressourcer kan findes i den følgende tabel.

|Registrering ressourcer kategori|Registrering ressourcer|Beskrivelse|Inputparametre|Output
|---|---|---|---|---|
|Samling detektorer|TSpike registrering ressourcer|Registrere spidser og fald baseret på langt værdier er fra første og tredje kvartiler|*tspikedetector.sensitivity:* tager heltalsværdi i området 1-10 standard: 3; Høje værdier vil fange flere største værdier, og som derfor gør det mindre følsomme|TSpike: binære værdier – '1', hvis der ikke er fundet en samling/dip, '0' Ellers|
||ZSpike registrering ressourcer|Registrere spidser og fald afhængigt af, hvor langt datapoints befinder fra stikprøvens middelværdi|*zspikedetector.sensitivity:* tage heltalsværdi i området 1-10 standard: 3; Høje værdier vil fange flere største værdier, der gør det mindre følsomme|ZSpike: binære værdier – '1', hvis der ikke er fundet en samling/dip, '0' Ellers|
|Langsom tendens registrering ressourcer|Langsom tendens registrering ressourcer|Registrere langsom positive tendens ud fra sæt følsomhed|*trenddetector.sensitivity:* grænseværdi på registrering ressourcer resultat (standard: 3,25, 3,25 – 5 er et begrundet område til at markere dette fra. Jo højere den mindre store og små bogstaver)|TScore: flydende tal der repræsenterer anomali resultatet af tendens|
|Ændring af detektorer|Ændring af envejs registrering ressourcer|Registrere op niveau ændre ud fra sæt følsomhed|*upleveldetector.sensitivity:* grænseværdi på registrering ressourcer resultat (standard: 3,25, 3,25 – 5 er et begrundet område til at markere dette fra. Jo højere den mindre store og små bogstaver)|PScore: flydende tal, der repræsenterer anomali resultat på opad niveau ændring|
||Tovejs niveau Skift registrering ressourcer|Registrere både opadgående og nedadgående niveau Skift ud fra sæt følsomhed|*bileveldetector.sensitivity:* grænseværdi på registrering ressourcer resultat (standard: 3,25, 3,25 – 5 er et begrundet område til at markere dette fra. Jo højere den mindre store og små bogstaver)|RPScore: flydende tal der repræsenterer anomali, ændres resultatet af opad og nedad niveau

###<a name="parameters"></a>Parametre

Finde mere detaljerede oplysninger om disse inputparametre er angivet i tabellen nedenfor:

|Inputparametre|Beskrivelse|Standardindstillingen|Type|Gyldige område|Foreslåede område|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|Sammenlægning interval i sekunder for sammenlægning input tidsserie|0 (ikke er udført nogen aggregering)|heltal|0: springe sammenlægning, > 0 ellers|5 minutter til 1 dag, tidsserie afhængige
|preprocess.aggregationFunc|Funktionen bruges til sammenlægning af data i den angivne AggregationInterval|middelværdi|optalt|middelværdien, sum, længde|I/T.|
|preprocess.replaceMissing|Værdier, der bruges til at impute manglende data|lkv (sidste kendt værdi)|optalt|nul, lkv, middelværdi|I/T.|
|detectors.historyWindow|Oversigt (i # datapunkter) bruges til beregning af anomali resultat|500|heltal|10-2000|Tidsserie afhængige|
|upleveldetector.Sensitivity|Følsomhed for op niveau ændre registrering ressourcer. |3,25|dobbelt|Ingen|3,25 5(Lesser values mean more sensitive)|
|bileveldetector.Sensitivity|Følsomhed for tovejs niveau ændre registrering ressourcer. |3,25|dobbelt|Ingen|3,25 5(Lesser values mean more sensitive)|
|trenddetector.Sensitivity|Følsomhed for positive tendens registrering ressourcer. |3,25|dobbelt|Ingen|3,25 5(Lesser values mean more sensitive)|
|tspikedetector.Sensitivity |Følsomhed for TSpike registrering ressourcer|3|heltal|1-10|3-5(Lesser values mean more sensitive)|
|zspikedetector.Sensitivity |Følsomhed for ZSpike registrering ressourcer|3|heltal|1-10 |3-5(Lesser values mean more sensitive)|
|seasonality.enable|Om sæsonudsving analyse der skal udføres|SAND|Boolesk værdi|SAND, FALSK|Tidsserie afhængige|
|seasonality.numSeasonality |Maksimale antal periodiske skifter til findes|1|heltal|1, 2|1-2|
|seasonality.Transform |Om sæsonbestemte (og) tendens komponenter skal fjernes, før du anvender anomali registrering|deseason|optalt|Ingen, deseason, deseasontrend|I/T.|
|postprocess.tailRows |Antallet af de seneste datapunkter skal holdes i outputresultaterne|0|heltal|0 (lade alle datapunkter), eller angive antal punkter skal huske på resultater|I/T.|

###<a name="output"></a>Output
API kører alle detektorer på din tid seriedata og returnerer anomali pointtal og binære samling indikatorer for hvert punkt i gang. Tabellen nedenfor viser output fra API. 

|Output|Beskrivelse|
|---|---|
|Tid|Tidsstempler fra rækkedata eller sammenlagt (og/eller) beregnede data Hvis sammenlægning (og/eller) mangler data fradrag er anvendt|
|OriginalData|Værdier fra rækkedata eller sammenlagt (og/eller) beregnede data, hvis sammenlægning (og/eller) mangler data fradrag er anvendt|
|ProcessedData|Et af følgende: <ul><li>Justeret efter sæsoner tidsserie, hvis betydeligt sæsonudsving er registreret og deseason indstilling er valgt.</li><li>efter sæsoner tilpasses og detrended tidsserie Hvis betydeligt sæsonudsving er registreret og deseasontrend indstillingen markeret</li><li>Ellers er dette den samme som OriginalData</li>|
|TSpike|Binær indikator til at angive, om der findes en samling af TSpike registrering ressourcer|
|ZSpike|Binær indikator til at angive, om der findes en samling af ZSpike registrering ressourcer|
|Pscore|En flydende tal, der repræsenterer anomali resultat på øverste niveau ændre|
|Palert|1/0 værdi, der angiver der er en opadgående niveau ændre anomali baseret på input følsomhed|
|RPScore|En flydende tal, der repræsenterer anomali resultat ved ændring af tovejs niveau|
|RPAlert|1/0 værdi, der angiver der er et tovejs-niveau ændre anomali baseret på input følsomhed|
|TScore|En flydende tal, der repræsenterer anomali resultat på positive tendens|
|TAlert|1/0 værdi, der angiver der er et positivt tendens anomali baseret på input følsomhed|


Dette output kan parses ved hjælp af en [simpel parseren](https://adresultparser.codeplex.com/) – den har eksempelkode, der viser, hvordan du kan oprette forbindelse til API og fortolke outputtet. Afvigelser fundet kan visualized på et dashboard og/eller overføres til human eksperter for korrigerende handlinger eller integreret i billettering systemer.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 

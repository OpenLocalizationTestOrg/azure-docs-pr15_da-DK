<properties 
    pageTitle="Stream Analytics produktbemærkninger | Microsoft Azure" 
    description="Stream Analytics produktbemærkninger" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="stream-analytics-release-notes"></a>Stream Analytics produktbemærkninger

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Noter til 04-15/2016 udgave af Stream Analytics ##

Denne version indeholder følgende opdatering.

Titel | Beskrivelse
---|---
Generelt tilgængelig for Power BI output  | [Power BI skriver](stream-analytics-power-bi-dashboard.md) er nu alment tilgængelig. 90 dage godkendelse udløb til Power BI er blevet fjernet. Yderligere oplysninger om scenarier, hvor godkendelse skal fornys i afsnittet [Forny godkendelse](stream-analytics-power-bi-dashboard.md#Renew-authorization) for at oprette et Power BI-dashboard.

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Noter til 03-03/2016 udgave af Stream Analytics ##

Denne version indeholder følgende opdatering.

Titel | Beskrivelse
---|---
Nye Stream Analytics forespørgselssprog elementer  | SAQL har nu [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN side"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN side") og [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN side").

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Noter i 12-10-2015 udgave af Stream Analytics ##

Denne version indeholder følgende opdatering.

Titel | Beskrivelse
---|---
Opdatering af REST-API-versionen | REST-API-version er blevet opdateret til 2015-10-01. Oplysninger kan findes på MSDN på [Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx) og [Machine Learning integration i Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md).
Azure Machine Learning Integration | Med denne version leveres support til Azure Machine Learning brugerdefinerede funktioner. Se [selvstudium](stream-analytics-machine-learning-integration-tutorial.md) til yderligere oplysninger samt [generelle blog meddelelse](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx).

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Noter i 11-12-2015 udgave af Stream Analytics ##

Denne version indeholder følgende opdatering.

Titel | Beskrivelse
---|---
Nye funktionsmåde for Vælg | Vælg i Stream Analytics er blevet udvidet for at tillade * som en Egenskabstilgang af en indlejret post. Yderligere oplysninger finder du [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Komplekse datatyper").

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Noter til 10-22-2015 udgave af Stream Analytics ##

Denne version indeholder følgende opdateringer.

Titel | Beskrivelse
---|---
Yderligere forespørgsel sprogfunktioner | Stream Analytics har udvidet forespørgselssproget ved at medtage følgende funktioner: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [loft](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EKSP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [Log](https://msdn.microsoft.com/library/azure/mt605290.aspx), [FIRKANT](https://msdn.microsoft.com/library/azure/mt605288.aspx)og [KVROD](https://msdn.microsoft.com/library/azure/mt605238.aspx).
Fjernet Sammenlæg begrænsninger  | Denne version fjerner begrænsning af 15 samlinger i en forespørgsel. Der er nu ingen grænse for antallet af samlinger per forespørgsel.
Tilføjede gruppe ved System.Timestamp funktion | Funktionen [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) giver nu mulighed for enten window_type eller [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx).
Tilføjede forskydning for Tumbling og Hopping windows | Som standard er justeret [Tumbling](https://msdn.microsoft.com/library/azure/dn835055.aspx) og [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) windows mod nul tid (1/1/0001 12:00:00 AM UTC). Den nye (valgfrie) parameter 'offsetsize' kan angive en brugerdefineret forskydning (eller justering).


## <a name="notes-for-09292015-release-of-stream-analytics"></a>Noter til 09-29-2015 udgave af Stream Analytics ##

Denne version indeholder følgende opdateringer.

Titel | Beskrivelse
---|---
Azure IoT pakke Public Preview | Stream Analytics er inkluderet i prøveversionen af Azure IoT pakke.
Azure Portal-integration | Ud over fortsat tilstedeværelse i portalen Azure Management er Stream Analytics nu integreret i [Azure-portalen](https://azure.microsoft.com/overview/preview-portal/). Bemærk, at Stream Analytics funktionalitet i portalen Preview er i øjeblikket et undersæt af funktionaliteten tilbydes i portalen Azure Management uden understøttelse af forespørgselstest af i browseren, Power BI output konfiguration, og navigerer til eller oprette nye input og output ressourcer i abonnementer, du har adgang til.
Understøttelse af DocumentDB output | Stream Analytics job kan nu output til [DocumentDB](https://azure.microsoft.com/services/documentdb/).
Understøttelse af IoT Hub input | Stream Analytics job kan nu indtager data fra IoT Hubs.
TIDSSTEMPEL ved for forskellige hændelser | Når en enkelt datastream indeholder flere typer af begivenheder har tidsstempler i forskellige felter, kan du nu bruge [TIDSSTEMPEL ved](http://msdn.microsoft.com/library/mt573293.aspx) med udtryk til at angive forskellige tidsstempelfelter til hver sag.

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Noter til 09-10-2015 udgave af Stream Analytics ##

Denne version indeholder følgende opdateringer.

Titel|Beskrivelse
---|---
Understøttelse af PowerBI grupper|For at aktivere Deling af data med andre brugere af Power BI, kan Stream Analytics job nu skrive til [PowerBI grupper](stream-analytics-define-outputs.md#power-bi) i din Power BI-konto.

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Noter til 20-08-2015 udgave af Stream Analytics ##

Denne version indeholder følgende opdateringer.

Titel|Beskrivelse
---|---
Tilføjede sidste funktionen |Funktionen [sidste](http://msdn.microsoft.com/library/mt421186.aspx) er nu tilgængelig på Stream Analytics job, så du kan hente den seneste hændelse i en begivenhed stream inden for en bestemt tidsramme.
Nye funktioner i matrix|Matrixfunktioner [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) og [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) er nu tilgængelig.
Nye funktioner i post|Post funktioner [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) og [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) er nu tilgængelig.

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Noter i 07-30-2015 udgave af Stream Analytics ##

Denne version indeholder følgende opdateringer.

Titel|Beskrivelse
---|---
Power BI Org-Id frakoblet fra Azure-Id|Denne funktion, der aktiverer [Power BI output](stream-analytics-power-bi-dashboard.md) ASA job under Azure kontotype (Live-Id eller Org-Id). Du kan desuden har ét Org-Id til kontoen Azure og bruge et andet navn til godkendelse af Power BI output.
Understøttelse af Bus servicekøer output|[Bus servicekøer](stream-analytics-define-outputs.md#service-bus-queues) output er nu tilgængelig på Stream Analytics job.
Understøttelse af tjenesten Bus emner output|[Tjenesten Bus emner](stream-analytics-define-outputs.md#service-bus-topics) output er nu tilgængelig på Stream Analytics job.

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Noter i 07-09-2015 udgave af Stream Analytics ##

Denne version indeholder følgende opdateringer.


Titel|Beskrivelse
---|---
Brugerdefineret Blob Output partitionering|BLOB storage output Tillad nu mulighed for at angive, hvor ofte, der er skrevet BLOB output og struktur og formatet af mappestrukturen output data sti. 

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Noter i 05-03-2015 udgave af Stream Analytics ##

Denne version indeholder følgende opdateringer.


Titel|Beskrivelse
---|---
Øget maksimumværdien for ud for rækkefølge Tolerance vindue|Den maksimale størrelse for vinduet ud for rækkefølge Tolerance er nu 59:59 (MM:SS)
JSON outputformat: Linje adskilt eller en matrix|Der er nu en indstilling, når der gemmes i Blob-lager eller begivenhed Hub til at få vist som enten en matrix af JSON objekter eller ved at adskille JSON objekter med en ny linje. 

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Noter til 04-16-2015 udgave af Stream Analytics ##


Titel|Beskrivelse
---|---
Forsinkelse i Azure-lager konfiguration af konto|Når du opretter en Stream Analytics sag i et område for første gang, bliver du bedt om at oprette en ny firmapost lagerplads eller angive en eksisterende konto til at overvåge Stream Analytics job i det pågældende område. På grund af ventetid i konfigurere overvågning, bliver oprette et andet Stream Analytics-job i det samme område inden for 30 minutter bedt om at angive en anden konto, lager i stedet for viser de senest konfigurerede én i rullemenuen overvågning lagerplads konto. For at undgå at oprette en unødvendig lagerplads konto skal du vente 30 minutter efter oprettelse af en sag i et område for første gang før klargøring flere sager i det pågældende område.
Opgradering af job|Stream Analytics understøtter ikke direkte ændringer til definition eller konfiguration af et job, der kører på nuværende tidspunkt. For at ændre input, output, forespørgsel, skala eller konfiguration af et job, der kører, skal du først stoppe jobbet.
Datatyper, der udledes fra inputkilde|Hvis en sætningen CREATE TABLE ikke bruges, den INPUTTYPE er udledt fra input format, for eksempel alle felter fra CSV er streng. Felter skal eksplicit konverteres til den rigtige type ved hjælp af funktionen CAST for at undgå type uoverensstemmelsesfejl.
Manglende felter er afgangsantal som null-værdier|Refererer til et felt, der ikke findes i inputkilden medfører null-værdier i hændelsen output.
MED sætninger skal indsættes foran SELECT-sætninger|I en forespørgsel, skal de følge underforespørgsler, der er defineret på med sætninger SELECT-sætninger.
Problem med manglende hukommelse|Streaming Analytics-job med en stor tolerance for ud af ordre-begivenheder og/eller komplekse forespørgsler vedligeholde en stor mængde tilstand kan medføre jobbet at løbe tør for hukommelse, hvilket resulterer i et job genstart. Handlingerne start og stop kan ses i det job handlingen logfiler. For at undgå dette ved at skalere forespørgslen ud på tværs af flere partitioner. Denne begrænsning vil blive behandlet af forværrer ydelsen på påvirkede sager i stedet for at genstarte dem i en senere version.
Store blob input uden data tidsstempel kan forårsage problemet med manglende hukommelse|Forbrug store filer fra Blob-lager, kan det medføre Stream Analytics job til at gå ned, hvis der ikke er angivet et tidsstempelfelt via TIDSSTEMPEL ved. For at undgå dette problem skal du være hver blob under 10MB størrelse.
SQL-Database begivenhed lydstyrken begrænsning|Når du bruger SQL-Database som en output destination, kan det medføre Stream Analytics jobbet udløber meget store mængder outputdata. Du kan løse dette problem ved enten reducere outputlydstyrken ved hjælp af samlinger eller filter operatorer, eller Vælg Azure Blob-lager eller begivenhed Hubs som et output mål i stedet.
PowerBI datasæt kan kun indeholde én tabel|PowerBI understøtter ikke mere end én tabel i et givet datasæt.

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

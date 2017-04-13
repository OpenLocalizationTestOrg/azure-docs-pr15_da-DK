<properties
    pageTitle="Implementere Azure ML webtjenester, der bruger Data Import og eksport af Data er moduler | Microsoft Azure"
    description="Lær, hvordan du bruger modulet importdata og eksportere Data til at sende og modtage data fra en webtjeneste."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="v-donglo"/>



# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Implementere Azure ML webtjenester, der bruger Data Import og eksport af Data er moduler 

Når du opretter en skønnet forsøg, tilføjer du typisk en web service input og output. Når du installerer forsøget, kan forbrugere sende og modtage data fra webtjeneste gennem input og output. For nogle programmer, en forbruger data være tilgængeligt fra et datafeed eller allerede findes i en ekstern datakilde som Azure Blob-lager. I disse tilfælde behøver de ikke at læse og skrive data ved hjælp af web service input og output. De kan, i stedet bruge batchen udførelse af Service (BES) til at læse data fra datakilden ved hjælp af et modul importdata og skrive vurdering resultaterne til en anden placering af ved hjælp af et modul Eksporter Data.

Importdata og Eksportér data moduler, kan læse fra og skrive et antal placeringer som en URL-adresse via HTTP, en Hive forespørgsel en Azure SQL-database, Azure Table storage, Azure Blob-lager, et datafeed indeholder data eller en lokal SQL-database.

I dette emne bruges den "eksempel 5: tog, Test, Evaluer til binær klassifikation: voksne datasæt" eksempel og antager datasættet er allerede blevet indlæst i en SQL Azure-tabel med navnet censusdata.

## <a name="create-the-training-experiment"></a>Oprette kursus forsøget 
 
Når du åbner den "eksempel 5: tog, Test, Evaluer til binær klassifikation: voksne datasæt" eksempel bruges eksempel voksne tælling indtægter binære klassifikation datasættet. Og forsøget i lærredet ser nogenlunde sådan følgende billede.

![Indledende konfiguration af forsøget.](./media/machine-learning-web-services-that-use-import-export-modules/initial-look-of-experiment.png)
  

At læse dataene fra tabellen Azure SQL:

1.  Slette modulet datasæt.
2.  Skriv import i søgefeltet komponenter.
3.  Føje en *Importdata* -modul til forsøg lærredet fra på resultatlisten.
4.  Oprette forbindelse output i *Importdata* modulet input i modulet *Ren manglende Data* .
5.  Vælg **Azure SQL-Database** i rullemenuen **Datakilde** i egenskabsruden.
6.  Angiv de relevante oplysninger for din database **Database servernavn** **Databasenavn**, **brugernavn**og **adgangskode** felter.
7.  Angiv følgende forespørgsel i databasefeltet forespørgsel.

        select [age],
           [workclass],
           [fnlwgt],
           [education],
           [education-num],
           [marital-status],
           [occupation],
           [relationship],
           [race],
           [sex],
           [capital-gain],
           [capital-loss],
           [hours-per-week],
           [native-country],
           [income]
        from dbo.censusdata;

8.  I bunden af forsøget lærred, klik på **Kør**.

## <a name="create-the-predictive-experiment"></a>Oprette skønnet forsøget

Næste, du har konfigureret skønnet forsøget hvorfra du vil installere din webtjeneste.

1.  Klik på **Angiv webtjeneste** i bunden af forsøg lærredet, og vælg **Skønnet webtjeneste (anbefales)**.
2.  Fjern de *Web Service Input* og *Web Service Output moduler* fra skønnet forsøget. 
3.  Skriv Eksportér i søgefeltet komponenter.
4.  Føje en *Eksportér Data* -modul til forsøg lærredet fra på resultatlisten.
5.  Oprette forbindelse output fra modulet *Karakteren Model* input i modulet *Eksporter Data* . 
6.  Vælg **Azure SQL-Database** i data destination rullemenuen i egenskabsruden.
7.  Angiv de relevante oplysninger for din database **Database servernavn** **Databasenavn**, **Server-konto brugernavn**og **adgangskode til brugerkonto Server** felter.
8.  Skriv karakter for etiketter, i feltet **kommasepareret liste over kolonner, der skal gemmes** .
9.  Skriv dbo i den **datatabel navnefeltet**. ScoredLabels. Hvis tabellen ikke findes, oprettes den, når forsøget kører eller webtjenesten kaldes.
10. Skriv ScoredLabels i feltet **kommasepareret liste over datatable kolonner** .

Når du skriver et program, der ringer op til den endelige webtjeneste, kan du vil angive en anden input forespørgsel eller destinationstabellen på kørselstidspunktet. For at konfigurere disse input og output, kan du bruge funktionen Web Serviceparametre til at angive egenskaben *Importdata* modul *datakilde* og egenskaben *Eksportér Data* mode data destination.  Se [AzureML Web Serviceparametre post](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) på Cortana Intelligence og Machine Learning Blog kan finde flere oplysninger om Web Serviceparametre.

Sådan konfigureres Web Serviceparametre for forespørgslen Importér og destinationstabellen:

1.  Klik på ikonet øverst i egenskabsruden for modulet *Importdata* , højre for feltet **databaseforespørgsel** og vælge **Angiv som web service parameter**.
2.  Klik på ikonet øverst i egenskabsruden for modulet *Eksportér Data* , højre for feltet **datatabel navn** og vælge **Angiv som web service parameter**.
3.  Klik på databaseforespørgsel nederst på *Eksporter Data* modul ruden Egenskaber, i sektionen **Web Serviceparametre** og Omdøb den forespørgsel.
4.  Klik på **datatabel navn** , og Omdøb den til **tabel**.

Når du er færdig, skal skal din forsøg ligne følgende billede.
 
![Endelige udseende af forsøg.](./media/machine-learning-web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Nu kan du installere forsøget som en webtjeneste.

## <a name="deploy-the-web-service"></a>Installere webtjenesten 
Du kan installere til en klassisk eller ny webtjeneste.

### <a name="deploy-a-classic-web-service"></a>Installere en klassisk webtjeneste

At implementere som en klassisk webtjeneste og oprette et program tilladelse til at bruge den:

1.  Klik på Kør nederst på lærredet forsøg.
2.  Når afkrydsningsfeltet Kør er fuldført, skal du klikke på **Installere webtjeneste** , og vælg **Installere webtjeneste [klassisk]**.
3.  Find din API-nøgle på dashboardet web-tjenesten. Kopiere og gemme den til senere brug.
4.  Klik på linket **Batchen udførelse af** for at åbne siden Hjælp til API i tabellen **Standard slutpunkt** .
5.  Oprette et C# console program i Visual Studio.
6.  På siden API hjælp, kan du finde afsnittet **Kodeeksempler** nederst på siden.
7.  Kopiér og Indsæt C# eksempelkoden i Program.cs filen, og Fjern alle referencer til blob-lager.
8.  Opdatere værdien af variablen *apiKey* med den gemte tidligere API-nøgle.
9.  Find anmodning om angivelsen og opdatere værdierne i Web-tjenesteparametre, der sendes til at *Importere Data* og *Eksportere Data* moduler. I dette tilfælde vil du bruge den oprindelige forespørgsel, men du kan definere et nyt tabelnavn.

        var request = new BatchExecutionRequest() 
        {   
            GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
            }
        };

10. Kør programmet. 

Kør er afsluttet føjes en ny tabel til den database, der indeholder vurdering resultaterne.

### <a name="deploy-a-new-web-service"></a>Installere en ny webtjeneste

Installere som en ny webtjeneste og oprette et program tilladelse til at bruge den:

1.  I bunden af forsøget lærredet, klik på **Kør**.
2.  Når afkrydsningsfeltet Kør er fuldført, skal du klikke på **Installere webtjeneste** , og vælg **Installere webtjeneste [ny]**.
3.  Angiv et navn til din webtjeneste på siden installere forsøg og vælg en plan, priser og derefter på **Implementer**.
4.  Klik på **forbrug**på siden **Hurtig start** .
5.  Klik på **Batch**i sektionen **Eksempelkode** .
6.  Oprette et C# console program i Visual Studio.
7.  Kopiere og indsætte C#-kode af eksempel i filen Program.cs.
8.  Opdatere værdien af variablen *apiKey* med den **Primærnøgle** , findes i afsnittet **grundlæggende forbrug oplysninger** .
9.  Find *scoreRequest* angivelsen og opdatere værdierne i Web-tjenesteparametre, der sendes til at *Importere Data* og *Eksportere Data* moduler. I dette tilfælde vil du bruge den oprindelige forespørgsel, men du kan definere et nyt tabelnavn.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                 { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };

10. Kør programmet. 
 


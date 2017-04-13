<properties 
   pageTitle="Udvikle U-SQL brugerdefinerede operatorer til Azure Data sø Analytics job | Azure" 
   description="Lær, hvordan du udvikler brugerdefinerede operatorer kan bruges og igen i Data sø Analytics sager. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# <a name="develop-u-sql-user-defined-operators-for-azure-data-lake-analytics-jobs"></a>Udvikle U-SQL brugerdefinerede operatorer til Azure Data sø Analytics-job

Lær, hvordan du udvikler brugerdefinerede operatorer kan bruges og igen i Data sø Analytics sager. Du vil udvikle en brugerdefineret operator for at konvertere land navne.

##<a name="prerequisites"></a>Forudsætninger

- Visual Studio 2015, Visual Studio 2013 opdatere 4 eller Visual Studio 2012 med Visual C++ installeret 
- Microsoft Azure SDK til .NET version 2.5 eller nyere.  Installere det ved hjælp af Web platform installationsprogrammet.
- En Data sø Analytics-konto.  Se [Introduktion til Azure Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-get-started-portal.md).
- Gå gennem selvstudiet [Introduktion til Azure Data sø Analytics U SQL Studio](data-lake-analytics-u-sql-get-started.md) .
- Oprette forbindelse til Azure, skal du se [Introduktion til Azure Data sø Analytics U SQL Studio](data-lake-analytics-u-sql-get-started.md#connect-to-azure). 
- Overføre kildedataene, skal du se [Introduktion til Azure Data sø Analytics U SQL Studio](data-lake-analytics-u-sql-get-started.md#upload-source-data-files). 

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>Definere og bruge brugerdefinerede operator i U-SQL

**Oprette og sende et U-SQL-job** 

1. Klik på **Ny**i menuen **filer** , og klik derefter på **projekt**.
2. Vælg projekttype **U-SQL** .

    ![nyt U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Klik på **OK**. Visual studio opretter en løsning med en Script.usql fil.
4. Udvid Script.usql fra **Solution Explorer**, og dobbeltklik derefter på **Script.usql.cs**.
5. Indsæt følgende kode i filen:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;
        
        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Schwiiz", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };
        
                public override IRow Process(IRow input, IUpdatableRow output)
                {
        
                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");
        
                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);
        
                    return output.AsReadOnly();
                }
            }
        }

5. Åbn Script.usql, og Indsæt følgende U-SQL-script:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);
        
        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    
        
        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);

6. Højreklik på **Script.usql**fra **Solution Explorer**, og klik derefter på **Opret Script**.
6. Højreklik på **Script.usql**fra **Solution Explorer**, og klik derefter på **Send Script**.
7. Hvis du ikke har forbindelse til abonnementet Azure, bliver du Spørg angive dine legitimationsoplysninger Azure-konto.
7. Klik på **Send**. Resultater for afsendelse og joblink er tilgængelige i vinduet resultater når afsendelsen er fuldført.
8. Skal du klikke på knappen Opdater for at se de seneste jobstatus og opdatere skærmen.

**Se tingene output**

1. Udvid **Azure**fra **Server Explorer**, udvide **Data sø Analytics**, udvide din Data sø Analytics-konto, udvide **Lagerplads konti**, skal du højreklikke på den standard lagerplads, og klik derefter på **Stifinder**. 
2. Udvid eksempler, udvide output, og dobbeltklik derefter på **Drivers.csv**.


##<a name="see-also"></a>Se også

- [Komme i gang med Data sø analyser ved hjælp af PowerShell](data-lake-analytics-get-started-powershell.md)
- [Komme i gang med Data sø analyser ved hjælp af portalen Azure](data-lake-analytics-get-started-portal.md)
- [Bruge Data sø Tools til Visual Studio til U-SQL udviklingsprogrammer](data-lake-analytics-data-lake-tools-get-started.md)

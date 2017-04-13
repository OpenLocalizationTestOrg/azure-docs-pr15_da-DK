<properties
   pageTitle="Bruge Azure Machine Learning med SQL datawarehouse | Microsoft Azure"
   description="Selvstudium til brug af Azure Machine Learning med Azure SQL Data Warehouse til udvikling af løsninger."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Brug Azure Machine Learning med SQL datawarehouse

Azure Machine Learning er en fuldt administreret skønnet analytics-tjeneste, du kan bruge til at oprette skønnet modeller mod dine data i SQL datalager, og derefter publicere som klar til at bruge webtjenester. Du kan lære de grundlæggende regler for skønnet analyser og machine learning-ved at læse [Introduktion til maskine undervisning på Azure][].  Derefter kan du læse hvordan du opretter, uddannelse, resultat og teste en machine learning model ved hjælp af [Opret eksperimentere selvstudium][].

I denne artikel kan du lære, hvordan du gøre følgende med [Azure Machine Learning Studio][]:

- Læse data fra databasen til at oprette, undervise og resultat en skønnet model
- Skrive data til databasen


## <a name="read-data-from-sql-data-warehouse"></a>Læse data fra SQL Data Warehouse

Vi læser data fra produkttabellen i AdventureWorksDW databasen.

### <a name="step-1"></a>Trin 1

Starte en ny forsøg ved at klikke på + ny nederst i vinduet Machine Learning Studio Vælg forsøg, og vælg derefter tom eksperimentere. Vælg forsøg standardnavn øverst på lærredet, og Omdøb den til et sigende navn, for eksempel cykler pris forudsigelse.

### <a name="step-2"></a>Trin 2

Se efter modulet læser i paletten af datasæt og moduler til venstre på lærredet forsøg. Træk modulet til forsøg lærred.
![][drag_reader]

### <a name="step-3"></a>Trin 3

Vælge modulet, der læser og udfylde egenskabsruden.

1. Vælg Azure SQL-Database som datakilde.
2. Databaseservernavnet: Skriv navnet på serveren. Du kan bruge [Azure portal][] for at finde dette.

![][server_name]

3. Databasenavn: Skriv navnet på en database på den server, du lige har angivet.
4. Navnet på Server brugerkonto: Skriv brugernavnet på en konto, der har adgangstilladelser til databasen.
5. Server adgangskode til brugerkonto: angive adgangskoden for den angivne brugerkonto.
6. Acceptere en hvilken som helst servercertifikat: Brug denne indstilling (mindre sikkert), hvis du vil springe over Gennemse webstedets certifikat, inden du læser dine data.
7. Databaseforespørgsel: Indtast en SQL_sætning, der beskriver de data, du vil læse. I dette tilfælde læser vi data fra produkt tabel ved hjælp af følgende forespørgsel.


```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Trin 4

1. Køre forsøget ved at klikke på Kør under forsøg lærredet.
2. Når forsøget afsluttes, skal have modulet læser et grønt flueben til at angive, at den er fuldført. Bemærk også færdig kører status i øverste højre hjørne.

![][run]

3. For at se de importerede data skal du klikke outputport i bunden af bil datasættet og vælge visuelle effekter.


## <a name="create-train-and-score-a-model"></a>Oprette, undervise og resultat en model

Nu kan du bruge dette dataset for at:

- Oprette en Model: behandle data og definere funktioner
- Uddannelse af modellen: vælge og anvende en learning algoritme
- Resultat og test af modellen: forudsige ny cykler pris


![][model]

Oplære resultat for at få mere for at vide om, hvordan du opretter, og teste en maskine læ model Brug [Opret eksperimentere selvstudium][].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Skrive data til Azure SQL Data Warehouse

Vi skriver resultatsættet til ProductPriceForecast tabel i AdventureWorksDW databasen.

### <a name="step-1"></a>Trin 1

Se efter modulet Writer i paletten af datasæt og moduler til venstre på lærredet forsøg. Træk modulet til forsøg lærred.

![][drag_writer]

### <a name="step-2"></a>Trin 2

Vælge modulet Writer og udfylde egenskabsruden.

1. Vælg Azure SQL-Database som datadestination.
2. Databaseservernavnet: Skriv navnet på serveren. Du kan bruge [Azure portal][] for at finde dette.
3. Databasenavn: Skriv navnet på en database på den server, du lige har angivet.
4. Navnet på Server brugerkonto: Skriv navnet på en konto, der har skrivetilladelser til databasen.
5. Server adgangskode til brugerkonto: angive adgangskoden for den angivne brugerkonto.
6. Acceptere en hvilken som helst (usikre) servercertifikat: Vælg denne indstilling, hvis du ikke vil have vist certifikatet.
7. Kommasepareret liste over kolonner, der gemmes: indeholder en liste over kolonnerne datasæt eller resultat, du vil udskrive.
8. Data tabelnavn: Angiv navnet på datatabellen.
9. Kommasepareret liste over datatable kolonner: angive kolonnenavnene skal bruge i den nye tabel. Kolonnenavnene kan være forskellige fra dem i datasættet kilde, men du skal angive det samme antal kolonner her, du definerer for outputtabellen.
10. Antallet af rækker, der skrives i SQL Azure handling: Du kan konfigurere antallet af rækker, der er skrevet til en SQL-database på én gang.

![][writer_properties]

### <a name="step-3"></a>Trin 3

1. Køre forsøget ved at klikke på Kør under forsøg lærredet.
2. Når forsøget er færdig, får alle moduler et grønt flueben til at angive, at de blev fuldført.

## <a name="next-steps"></a>Næste trin

Du kan finde flere tip til udvikling, [SQL Data Warehouse udvikling oversigt][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[Oversigt over SQL Data Warehouse udvikling]: ./sql-data-warehouse-overview-develop.md
[Oprette forsøg selvstudium]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduktion til machine learning på Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure-portalen]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/

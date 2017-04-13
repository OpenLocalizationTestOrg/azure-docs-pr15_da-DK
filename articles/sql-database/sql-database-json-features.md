<properties
    pageTitle="Azure SQL Database JSON-funktioner | Microsoft Azure"
    description="Azure SQL-Database gør det muligt at analysér, forespørgsel og formatere data i JavaScript Object Notation (JSON) notation."
    services="sql-database"
    documentationCenter=""
    authors="jovanpop-msft"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/17/2016"
    ms.author="jovanpop"
   ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Introduktion til JSON-funktioner i Azure SQL-Database

Azure SQL-Database kan du analysere og forespørgsel data, der repræsenteres i JavaScript Object Notation [(JSON)](http://www.json.org/) format, og Eksportér dine relationelle data som JSON tekst.

JSON er et populære dataformat, der bruges til udveksling af data i moderne web og mobilprogrammer. JSON bruges også til lagring af semistrukturerede data i logfiler eller i NoSQL databaser som [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). Mange RESTEN-webtjenester returnerer resultater er formateret som JSON tekst eller acceptere data, der er formateret som JSON. Mest Azure tjenester som [Azure søgning](https://azure.microsoft.com/services/search/), [Azure-lager](https://azure.microsoft.com/services/storage/)og [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) har RESTEN slutpunkter, der returnerer eller forbruger JSON.

Azure SQL-Database kan du arbejde med JSON data nemt og integrere din database med moderne tjenester.

## <a name="overview"></a>Oversigt

Azure SQL-Database indeholder følgende funktioner til at arbejde med JSON data:

![JSON-funktioner](./media/sql-database-json-features/image_1.png)

Hvis du har JSON tekst, kan du udtrække data fra JSON eller kontrollerer, at JSON er korrekt formateret ved hjælp af de indbyggede funktioner [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)og [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Funktionen [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) kan du opdatere værdien i JSON tekst. Finde mere avancerede forespørgsler og analyse, kan [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) funktionen transformere en matrix med JSON objekter i et sæt rækker. En SQL-forespørgsel kan udføres på det returnerede resultatsæt. Til sidst, der er en [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) -delsætning, der gør det muligt at formatere data, der er gemt i din relationelle tabeller som JSON tekst.

## <a name="formatting-relational-data-in-json-format"></a>Formatering af relationelle data i JSON-formatet
Hvis du har en webtjeneste, træder data fra databasens lag og giver et svar i JSON formatere eller klientsiden JavaScript strukturer eller biblioteker, der accepterer data, der er formateret som JSON, kan du formatere dit indhold i databasen som JSON direkte i en SQL-forespørgsel. Du ikke længere behøver at skrive programkode, der formaterer resultater fra Azure SQL-Database som JSON eller indeholder nogle JSON serialisering bibliotek for at konvertere tabelformat forespørgselsresultater og derefter sekventielt objekter til JSON-formatet. I stedet kan du bruge delsætningen FOR JSON at formatere SQL forespørgselsresultater som JSON i Azure SQL-Database og bruge det direkte i dit program.

I eksemplet nedenfor er rækker fra tabellen Sales.Customer formateret som JSON ved hjælp af delsætningen FOR JSON:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Delsætningen FOR JSON sti formaterer resultaterne af forespørgslen som JSON tekst. Kolonnenavne bruges som nøgler, mens celleværdier genereres som JSON værdier:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Resultatsættet er formateret som en JSON-matrix, hvor hver række er formateret som en separat JSON-objekt.

STI Angiver, at du kan tilpasse outputformatet af JSON resultatet ved hjælp af prik notation i kolonnen aliasser. Følgende forespørgsel ændrer navnet på tasten "CustomerName" i JSON outputformat og placerer telefonnummer, faxnummer i objektet "Kontakt" underordnede:

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Output fra denne forespørgsel ser sådan ud:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

I dette eksempel returneres vi et enkelt JSON objekt i stedet for en matrix ved at angive indstillingen [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) . Du kan bruge denne indstilling, hvis du ved, at du returnerer et enkelt objekt som et resultat af forespørgslen.

Den primære værdi af delsætningen FOR JSON er, at det gør det muligt at returnere komplekse hierarkiske data fra din database, der er formateret som indlejrede JSON-objekter eller matrixer. I følgende eksempel viser, hvordan medtage ordrer, der hører til kunden som en indlejret matrix af ordrer:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

I stedet for at sende adskille forespørgsler til at få kundedata og derefter for at hente en liste over relaterede ordrer, du kan få alle de nødvendige data med en enkelt forespørgsel, som vist i følgende eksempeloutput:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Arbejde med JSON data

Hvis du ikke har absolut strukturerede data, hvis du har komplekse underordnede objekter, matrixer eller hierarkiske data, eller hvis din datastrukturer udvikler over tid, kan JSON-formatet hjælpe dig til at repræsentere en kompleks datastruktur.

JSON er et tekst-format, der kan bruges som en anden strengtype i Azure SQL-Database. Du kan sende eller gemme JSON data som en standard NVARCHAR:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Den JSON-data, der bruges i dette eksempel er repræsenteret ved hjælp af typen NVARCHAR(MAX). JSON kan indsættes i denne tabel eller leveres som et argument af den lagrede procedure ved hjælp af standard Transact-SQL-syntaks, som vist i følgende eksempel:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

En klientsiden sprog eller et bibliotek, der fungerer sammen med strengdata i Azure SQL-Database der også virker sammen med JSON data. JSON kan gemmes i en tabel, der understøtter typen NVARCHAR, som en hukommelse optimeret tabel eller en System-versionsnummer tabel. JSON introduceres ikke begrænsninger, der enten klientsiden kode eller database lag.

## <a name="querying-json-data"></a>Oprette forespørgsler til JSON data

Hvis du har data, der er formateret som JSON, der er gemt i Azure SQL-tabeller, kan JSON funktioner du bruge disse data i en SQL-forespørgsel.

JSON-funktioner, der er tilgængelige i Azure SQL database Lad du behandle data, der er formateret som JSON som alle andre SQL-datatyper. Du kan nemt udtrække værdier fra JSON-teksten, og brug JSON data i en forespørgsel:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funktionen JSON_VALUE henter en værdi fra JSON-tekst, der er gemt i kolonnen Data. Denne funktion bruges en JavaScript-lignende sti til at referere til en værdi i JSON tekst til at uddrage. Værdien udpakkede kan bruges i en del af SQL-forespørgsel.

Funktionen JSON_QUERY ligner JSON_VALUE. I modsætning til JSON_VALUE henter denne funktion komplekse underordnede objekt som matrixer eller objekter, der er placeret i JSON tekst.

Funktionen JSON_MODIFY kan du angive stien til værdien i den JSON-tekst, der skal opdateres, samt en ny værdi, overskriver den gamle liste. Denne måde, du nemt kan opdatere JSON tekst uden reparsing hele strukturen.

Da JSON er gemt i en tekst, er der ingen garantier, der er, at de værdier, der er gemt i tekstkolonner er formateret korrekt. Du kan kontrollere, at er korrekt formateret tekst, der er gemt i JSON kolonne ved hjælp af standard Azure SQL-Database Kontrollér begrænsninger og funktionen ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Hvis input teksten er korrekt formateret JSON, funktionen ISJSON returnerer værdien 1. På hver Indsæt eller opdatering af JSON kolonne skal kontrollere denne begrænsning, ny tekstværdi ikke er forkert udformet JSON.

## <a name="transforming-json-into-tabular-format"></a>At transformere JSON i tabelformat

Azure SQL-Database kan du transformere JSON samlinger i tabelformat format og Indlæs eller forespørgsel JSON-data.

OPENJSON er en funktion i tabel-værdi, der fortolker JSON tekst, finder en matrix med JSON objekter, gentager listen over elementer i matrixen og returnerer én række i resultatet output for hvert element i matrixen.

![JSON tabelformat](./media/sql-database-json-features/image_2.png)

I det foregående eksempel kan vi angive, hvor du vil placere den JSON-matrix, der skal åbnes (i $. Ordrer stien), hvilke kolonner skal returneres som resultat, og hvor du kan finde de værdier, JSON, der skal returneres som celler.

Vi kan transformere en JSON matrix i den @orders variabel i et sæt rækker, analysere denne resultatsættet eller indsætte rækker i en standardtabel:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
Samling af ordrer, der er formateret som en JSON-matrix og leveret som en parameter til den lagrede procedure kan fortolkes og indsat i tabellen Ordrer.

## <a name="next-steps"></a>Næste trin

Hvis du vil lære at integrere JSON i dit program, skal du se disse ressourcer:

- [TechNet-Blog](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
- [MSDN dokumentation](https://msdn.microsoft.com/library/dn921897.aspx)
- [Kanal 9 video](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Se demoer i denne [kanal 9 video](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) for at få mere for at vide om forskellige scenarier til integrering af JSON i dit program, eller finde et scenarie, der stemmer overens med din use case i [JSON blogindlæg](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

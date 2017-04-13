<properties 
    pageTitle="Data Factory funktioner og systemvariabler | Microsoft Azure" 
    description="Oversigt over Azure Data Factory-funktioner og systemvariabler" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"
    services="data-factory"
/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory - funktioner og systemvariabler
I denne artikel indeholder oplysninger om funktioner og variabler, der understøttes af Azure Data Factory.
  
## <a name="data-factory-system-variables"></a>Data Factory systemvariabler

Variabelnavn | Beskrivelse | Objekt omfang | JSON omfang og Use Cases
------------- | ----------- | ------------ | ------------------------
WindowStart | Start af tidsintervallet for nuværende aktivitet køre vindue | aktivitet | <ol><li>Angiv markering dataforespørgsler. Se forbindelse artikler, der refereres til i artiklen [Data bevægelse aktiviteter](data-factory-data-movement-activities.md) .</li><li>Overføre parametre til Hive script (eksempel vist ovenfor).</li>
WindowEnd | Slutningen af tidsintervallet for nuværende aktivitet køre vindue | aktivitet | samme som ovenfor
SliceStart | Start af tidsinterval for data udsnit, oprettet | aktivitet<br/>datasæt | <ol><li>Angiv dynamisk mappesti og filnavne, mens du arbejder med [Azure Blob](data-factory-azure-blob-connector.md) og [filsystemet datasæt](data-factory-onprem-file-system-connector.md).</li><li>Angiv input afhængigheder med data factory-funktioner i aktivitet input af websteder.</li></ol>
SliceEnd | Slutningen af tidsinterval for aktuelle data udsnit, oprettet | aktivitet<br/>datasæt | det samme som ovenfor. 

> [AZURE.NOTE] I øjeblikket kræver data factory, at planen angivet i aktiviteten nøjagtigt svarer til tidsplanen, der er angivet i tilgængelighed af output datasættet. Det betyder WindowStart, WindowEnd og SliceStart og SliceEnd altid knyttes til den samme tidsperiode og et enkelt output udsnit.
 
## <a name="data-factory-functions"></a>Data Factory-funktioner 

Du kan bruge funktioner i data factory sammen med ovenfor nævnte systemvariabler til følgende formål:

1.  Angive markering dataforespørgsler (se forbindelse artikler, der refereres til af artiklen [Data bevægelse aktiviteter](data-factory-data-movement-activities.md) .

    Syntaksen for kalde en data factory-funktionen er: ** $$ ** til markeringen dataforespørgsler og andre egenskaber i aktivitet og datasæt.  
2. Angive input afhængigheder med data factory-funktioner i aktivitet input af websteder (se eksemplet ovenfor).

    $ ikke er nødvendigt for at angive input afhængighed udtryk.   

I det følgende eksempel tildeles **sqlReaderQuery** egenskab i en JSON-fil til en værdi, der er returneret af funktionen **Text.Format** . Dette eksempel bruger også en systemvariabel med navnet **WindowStart**, som repræsenterer starttidspunktet for den aktivitet køre vindue.
    
    {
        "Type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
    }

### <a name="functions"></a>Funktioner

De følgende tabeller viser alle funktionerne i Azure Data Factory:

Kategori | Funktionen | Parametre | Beskrivelse
-------- | -------- | ---------- | ----------- 
Tid | AddHours(X,Y) | X: dato/klokkeslæt <br/><br/>Y: heltal | Føjer Y timer til det angivne tidspunkt X. <br/><br/>Eksempel: 9/5/2013 12:00:00 PM + 2 timer = 9/5/2013 2:00:00 PM
Tid | AddMinutes(X,Y) | X: dato/klokkeslæt <br/><br/>Y: heltal | Føjer Y minutter til X.<br/><br/>Eksempel: 9/15/2013 12:00:00 PM + 15 minutter = 9/15/2013 12:15:00 PM
Tid | StartOfHour(X) | X: dato/klokkeslæt | Får starttidspunktet for at angive timer, der er repræsenteret af komponenten time af X. <br/><br/>Eksempel: StartOfHour af 9/15/2013 05:10:23 PM er 15-9/2013 05:00:00 PM
Dato | AddDays(X,Y) | X: dato/klokkeslæt<br/><br/>Y: heltal | Lægger Y dage x.<br/><br/>Eksempel: 9/15/2013 12:00:00 PM + 2 dage = 9/17/2013 12:00:00 PM
Dato | AddMonths(X,Y) | X: dato/klokkeslæt<br/><br/>Y: heltal | Føjer Y måneder til X.<br/><br/>Eksempel: 9/15/2013 12:00:00 PM + 1 måned = 10/15/2013 12:00:00 PM 
Dato | AddQuarters(X,Y) | X: dato/klokkeslæt <br/><br/>Y: heltal | Lægger Y * 3 måneder x.<br/><br/>Eksempel: 9/15/2013 12:00:00 PM + 1 kvartal = 12/15/2013 12:00:00 PM
Dato | AddWeeks(X,Y) | X: dato/klokkeslæt<br/><br/>Y: heltal | Lægger Y * 7 dage x<br/><br/>Eksempel: 9/15/2013 12:00:00 PM + 1 uge = 9/22/2013 12:00:00 PM
Dato | AddYears(X,Y) | X: dato/klokkeslæt<br/><br/>Y: heltal | Lægger Y år x.<br/><br/>Eksempel: 9/15/2013 12:00:00 PM + 1 år = 9/15/2014 12:00:00 PM
Dato | Day(X) | X: dato/klokkeslæt | Får komponenten dag af X.<br/><br/>Eksempel: Dag for 2013-15/9 12:00:00 PM er 9. 
Dato | DayOfWeek(X) | X: dato/klokkeslæt | Får dag i ugen komponent af X.<br/><br/>Eksempel: DayOfWeek for 2013-15/9 12:00:00 PM er søndag.
Dato | DayOfYear(X) | X: dato/klokkeslæt | Får dag i året, der er repræsenteret af komponenten år af X.<br/><br/>Eksempler:<br/>12-1-2015: dag 335 i 2015<br/>12-31-2015: ugedag 2015 365<br/>12-31/2016: dag 366 af 2016 (Spring år)
Dato | DaysInMonth(X) | X: dato/klokkeslæt | Får dagene i den måned, der er repræsenteret af komponenten måned på parameter X.<br/><br/>Eksempel: DaysInMonth af 9/15/2013 er 30, fordi der er 30 dage i September måned.
Dato | EndOfDay(X) | X: dato/klokkeslæt | Får den dato og klokkeslæt, der repræsenterer afslutningen af dag (dag komponent) af X.<br/><br/>Eksempel: EndOfDay af 9/15/2013 05:10:23 PM er 15-9/2013 11:59:59 PM.
Dato | EndOfMonth(X) | X: dato/klokkeslæt | Får slutningen af måneden, der er repræsenteret af måned komponent i parameter X. <br/><br/>Eksempel: EndOfMonth af 9/15/2013 05:10:23 PM er 9/30/2013 11:59:59 PM (dato klokkeslæt, der repræsenterer afslutningen af September måned)
Dato | StartOfDay(X) | X: dato/klokkeslæt | Får starten af den dag, der er repræsenteret af komponenten dag på parameter X.<br/><br/>Eksempel: StartOfDay af 9/15/2013 05:10:23 PM er 15-9/2013 12:00:00 AM.
Dato og klokkeslæt | From(X) | X: streng | Oversætte streng X til en dato klokkeslæt.
Dato og klokkeslæt | Ticks(X) | X: dato/klokkeslæt | Får aksemærkerne egenskab for parameteren X. Én aksemærker er lig med 100 nanosekunder. Værdien af denne egenskab repræsenterer antallet af aksemærker, der er forløbet siden midnat 12:00:00, januar 1 0001. 
Tekst | Format(X) | X: strengvariabel | Formaterer teksten.

#### <a name="textformat-example"></a>Eksempel på Text.Format

    "defines": { 
        "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
        "Month" : "$$Text.Format('{0:MM}',WindowStart)",
        "Day" : "$$Text.Format('{0:dd}',WindowStart)",
        "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
    }

Se [brugerdefineret dato og klokkeslæt formatstrenge](https://msdn.microsoft.com/library/8kb3ddd4.aspx) emne, der beskriver forskellige formateringsindstillinger, du kan bruge (for eksempel: åå kontra åååå). 

> [AZURE.NOTE] Når du bruger en funktion i en anden funktion, du ikke skal bruge **$$** præfiks for funktionen indre. For eksempel: $$Text.Format ('PartitionKey eq \\' my_pkey_filter_value\\' og RowKey Flet \\' {0:yyyy-MM-dd HH:mm:ss}\\", Time.AddHours (SliceStart, -6)). Bemærk, at i dette eksempel **$$** præfiks bruges ikke til funktionen **Time.AddHours** . 
  


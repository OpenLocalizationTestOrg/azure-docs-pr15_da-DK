## <a name="repeatability-during-copy"></a>Repeterbarhed under kopiering

Når du kopierer data fra og til relationelle butikker, skal du huske repeterbarhed på for at undgå utilsigtede resultater. 

Et udsnit kan være køre automatisk i Azure Data Factory ud fra politikken forsøg angivet. Vi anbefaler, at du angiver en politik for forsøg igen for at beskytte mod midlertidige fejl. Det vil sige er repeterbarhed et vigtigt aspekt til tager sig af under flytning af data. 

**Som en kilde:**

> [AZURE.NOTE] I følgende eksempler er til Azure SQL, men gælder for en hvilken som helst datalager, der understøtter rektangulære datasæt. Du kan være nødvendigt at justere **typen** kilde- og egenskaben **forespørgsel** (for eksempel: forespørgsel i stedet for sqlReaderQuery) for dataene, gemme.   

Normalt, når du læser fra relationelle butikker, vil du vil læse kun de data, der svarer til udsnittet. En metode til at gøre det ville være ved hjælp af de tilgængelige WindowStart og WindowEnd variabler i Azure Data Factory. Læs mere om variabler og funktioner i Azure Data Factory her i artiklen [planlægning og udførelse af](../articles/data-factory/data-factory-scheduling-and-execution.md) . Eksempel: 
    
      "source": {
        "type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
      },

Denne forespørgsel læser data fra 'MinTabel', der falder i området udsnit varighed. Kør denne udsnit vil også altid sikre dette problem. 

I andre tilfælde, kan du læse hele tabellen (Antag for én gang flytte kun) og kan definere sqlReaderQuery på følgende måde:

    
    "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
              },
    

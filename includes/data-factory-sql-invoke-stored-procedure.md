## <a name="invoking-stored-procedure-for-sql-sink"></a>Aktivering af lagret procedure for SQL synkronisere

Når du kopierer data til SQL Server eller Azure SQL/SQL Server-Database, angives en bruger lagret procedure kunne være konfigureret og aktiveret med yderligere parametre. 

En lagret procedure, kan bruges, når indbyggede kopi mekanismer ikke fungere gruppens formål. Dette er typisk opgraderede når ekstra processing (flette kolonner, leder du efter flere værdier, indsættes i flere tabeller...) skal udføres, før den endelige indsættelse af kildedataene i destinationstabellen. 

Du kan aktivere en lagret procedure efter valg. I følgende eksempel viser, hvordan du bruger en lagret procedure til at gøre en simpel indsætningspunktet i en tabel i databasen. 

**Output datasæt**

I dette eksempel type er indstillet til: SqlServerTable. Angive den til AzureSqlTable til brug med en Azure SQL-database. 

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    
Definere sektionen SqlSink i kopi aktivitet JSON. Hvis du vil ringe til en lagret procedure under Indsæt data, der er nødvendige for både SqlWriterStoredProcedureName og SqlWriterTableType egenskaber.

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

Definere den lagrede procedure med samme navn som SqlWriterStoredProcedureName i din database. Heltal indtastede data fra den angivne kilde, og Indsæt til outputtabellen. Bemærk, at parameternavnet på den lagrede procedure skal være den samme som den tabelnavn, der er defineret i tabellen JSON-filen.

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

Definere typen tabel med det samme navn som SqlWriterTableType i din database. Bemærk, at skemaet af tabeltypen skal være den samme som i skemaet, der returneres af din indtastede data.

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

Funktionen lagret procedure udnytter [Table-Valued parametre](https://msdn.microsoft.com/library/bb675163.aspx).
## <a name="column-mapping-with-translator-rules"></a>Kolonnetilknytning med oversætter regler
Kolonnetilknytning kan bruges til at angive, hvordan kolonner angivet i "struktur" kilde tabel kort til kolonner angivet i "strukturen" af sink tabel. Egenskaben **columnMapping** er tilgængelig i sektionen **typeProperties** aktivitetens kopi.

Kolonnetilknytning understøtter følgende scenarier:

- Alle kolonner i kildetabellen "struktur" er tilknyttet alle kolonner i tabellen sink "struktur".
- Et undersæt af kolonnerne i kildetabellen "struktur" er tilknyttet alle kolonner i tabellen sink "struktur".

Følgende er fejltilstande og medfører en undtagelse:

- Færre kolonner eller flere kolonner i "struktur" sink tabel end angivet i fletningen.
- Duplikere tilknytning.
- Resultat af SQL-forespørgsel har ikke et kolonnenavn, der er angivet i fletningen.

## <a name="column-mapping-samples"></a>Eksempler på kolonne-tilknytning
> [AZURE.NOTE] Eksemplerne nedenfor er beregnet til Azure SQL og Azure Blob, men gælder for en hvilken som helst datalager, der understøtter rektangulære datasæt. Du er nødt til at justere dataset og sammenkædede service definitioner i eksemplerne nedenfor til at pege på data i den relevante datakilde. 

### <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Eksempel 1 – kolonne knytter fra Azure SQL Azure blob
I dette eksempel inputtabellen har en struktur og den peger på en SQL-tabel i en Azure SQL-database.

    {
        "name": "AzureSQLInput",
        "properties": {
            "structure": 
             [
               { "name": "userid"},
               { "name": "name"},
               { "name": "group"}
             ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

I dette eksempel outputtabellen har en struktur og den peger på en blob i en Azure blob-lager.

    {
        "name": "AzureBlobOutput",
        "properties":
        {
             "structure": 
              [
                    { "name": "myuserid"},
                    { "name": "myname" },
                    { "name": "mygroup"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

JSON for aktiviteten er vist nedenfor. Kolonner fra kilde, der er knyttet til kolonner i sink (**columnMappings**) ved hjælp af **Translator** egenskab.

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "Copy",
        "inputs":  [ { "name": "AzureSQLInput"  } ],
        "outputs":  [ { "name": "AzureBlobOutput" } ],
        "typeProperties":    {
            "source":
            {
                "type": "SqlSource"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
            }
        },
       "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

**Kolonne tilknytning flow:**

![Kolonne tilknytning flow](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Eksempel 2 – kolonne knytter med SQL-forespørgsel fra Azure SQL til Azure blob
I dette eksempel bruges en SQL-forespørgsel til at udtrække data fra Azure SQL i stedet for blot angive navnet på den tabel, og kolonnenavnene i "struktur" afsnit. 

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "CopyActivity",
        "inputs":  [ { "name": " AzureSQLInput"  } ],
        "outputs":  [ { "name": " AzureBlobOutput" } ],
        "typeProperties":
        {
            "source":
            {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "Translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
            }
        },
        "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

I dette tilfælde tilknyttes forespørgselsresultaterne først kolonner, der er angivet i "struktur" af kildetypen. Derefter skal er kolonnerne fra kilde "struktur" knyttet til kolonner i sink "struktur" med regler, der er angivet i columnMappings.  Antag, at forespørgslen returnerer 5 kolonner, to ekstra kolonner og derefter dem, der er angivet i "strukturen" af kildetypen.

**Kolonne tilknytning flow**

![Kolonne tilknytning flow-2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)








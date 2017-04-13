### <a name="type-conversion-sample"></a>Type konvertering eksempel
I følgende eksempel er kopierer data fra en Blob til Azure SQL med typekonverteringer.

Antag Blob datasættet er i CSV-format og indeholder 3 kolonner. En af dem er en datetime-kolonne med en brugerdefineret datetime-format, ved hjælp af forkortet franske navne for dag i ugen.

Du kan definere Blob kilde dataset således sammen med definitioner af afhængighedstyper for kolonnerne.

    {
        "name": "AzureBlobTypeSystemInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
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
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

Givet SQL vil typen til .NET type tilknytningstabel over du definere Azure SQL-tabel med følgende skemaet.

| Kolonnenavn | SQL-Type |
| ----------- | -------- |
| bruger-id | bigint |
| Navn | tekst |
| lastlogindate | dato og klokkeslæt |

Du vil derefter definere Azure SQL-datasæt på følgende måde. Bemærk: Du behøver ikke at angive "struktur" sektionen med oplysninger om, da typeoplysninger er allerede angivet i den underliggende datalager.

    {
        "name": "AzureSQLOutput",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

I dette tilfælde vil data factory automatisk gøre typen konverteringer, herunder feltet Dato/klokkeslæt med den brugerdefinerede datetime formatere ved hjælp af fransk-fransk kultur, når du flytter data fra Blob til Azure SQL.



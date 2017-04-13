## <a name="specifying-formats"></a>Angive formater

Azure Data Factory understøtter følgende formattyper: 

- [Tekstformat](#specifying-textformat)
- [JSON-formatet](#specifying-jsonformat)
- [Avro Format](#specifying-avroformat)
- [ORC Format](#specifying-orcformat)
- [Parquet Format](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Angive tekstformat

Hvis formatet er indstillet til **tekstformat**, kan du angive følgende **valgfrie** egenskaber i sektionen **Format** .

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | Det tegn, der bruges til at adskille kolonner i en fil. | Kun ét tegn er tilladt. Standardværdien er komma (','). <br/><br/>Hvis du vil bruge et Unicode-tegn, du referere til [Unicode-tegn](https://en.wikipedia.org/wiki/List_of_Unicode_characters) for at hente den tilsvarende kode til den. For eksempel kan du overveje at bruge en sjældne ikke kan udskrives tegn, som ikke sandsynligt findes i dine data: angive "\u0001" som repræsenterer Start af overskrift (SOH). | Nej |
| rowDelimiter | Det tegn, der bruges til at adskille rækker i en fil. | Kun ét tegn er tilladt. Standardværdien er et af følgende værdier på læst: ["\r\n", "\r", "\n"] og "\r\n" under skrivning. | Nej |
| escapeChar | Det specialtegn, der bruges til at gå en kolonne afgrænser i indholdet af en fil. <br/><br/>Du kan ikke angive både escapeChar og quoteChar for en tabel. | Kun ét tegn er tilladt. Ingen standardværdi. <br/><br/>Eksempel: Hvis du har komma (",") som kolonne afgrænser, men du vil have komma tegnet i teksten (eksempel: "Hej, store verden"), du kan definere '$' som escape-tegn og bruge strengen "$Hej, store verden" i kilden. | Nej | 
| quoteChar | Det tegn, der bruges til at angive en strengværdi. Kolonne- og afgrænsere i tegnet tilbud skal behandles som en del af strengværdien. Denne egenskab er gældende for både input- og outputområder datasæt.<br/><br/>Du kan ikke angive både escapeChar og quoteChar for en tabel. | Kun ét tegn er tilladt. Ingen standardværdi. <br/><br/>For eksempel, hvis du har komma (",") som kolonne afgrænser, men du vil have kommategn i teksten (eksempel: < Hej, store verden >), du kan definere "(dobbelte anførselstegn) som anførselstegn og bruger strengen"Hej, store verden"i kilden. | Nej |
| nullValue | Et eller flere tegn, der bruges til at repræsentere en null-værdi. | Et eller flere tegn. Standardværdierne er "\N" og "NULL" i læse- og "\N" under skrivning. | Nej |
| encodingName | Angiv kodning navnet. | Et gyldigt kodning navn. Se [Encoding.EncodingName egenskab](https://msdn.microsoft.com/library/system.text.encoding.aspx). Eksempel: windows-1250 eller shift_jis. Standardværdien er UTF-8. | Nej | 
| første række som overskrift | Angiver, om skal du tænke på den første række som overskrift. For en input datasæt læser Data Factory første række som overskrift. For et output datasæt skriver Data Factory første række som overskrift. <br/><br/>Du kan se [scenarier for brug af **første række som overskrift** og **skipLineCount** ](#scenarios-for-using-firstrowasheader-and-skiplinecount) til eksempelscenarier. | SAND<br/>FALSK (standard) | Nej |
| skipLineCount | Angiver antallet af rækker for at springe over, når du læser data fra input-filer. Hvis der er angivet både skipLineCount og første række som overskrift linjerne er ignoreret først, og derefter oplysningerne i hovedet læse fra filen input. <br/><br/>Du kan se [scenarier for brug af første række som overskrift og skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) til eksempelscenarier. | Heltal | Nej | 
| treatEmptyAsNull | Angiver, om at behandle null eller en tom streng som en null-værdi, når du læser data fra en input-fil. | SAND (standard)<br/>FALSK | Nej |  

#### <a name="textformat-example"></a>Eksempel på tekstformat
I følgende eksempel viser nogle af format egenskaberne for tekstformat.

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

For at bruge en escapeChar i stedet for quoteChar skal du erstatte linjen med quoteChar med følgende escapeChar:

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scenarier for brug af første række som overskrift og skipLineCount

- Du kopierer fra en ikke-kildefil til en tekstfil og gerne vil tilføje en overskriftslinje, der indeholder skemametadata (for eksempel: SQL skema). Angiv **første række som overskrift** som SAND i output datasættet i dette scenarie. 
- Du kopierer fra en tekstfil, der indeholder en overskriftslinje til en ikke-file sink og gerne vil slippe pågældende linje. Angiv **første række som overskrift** som SAND i input datasættet.
- Du kopierer fra en tekstfil og ønsker at springe et par linjer i starten, der indeholder ingen data eller sidehoved oplysninger. Angiv **skipLineCount** for at angive antallet linjer skal udelades. Hvis resten af filen indeholder en overskriftslinje, kan du også angive **første række som overskrift**. Hvis der er angivet både **skipLineCount** og **første række som overskrift** linjerne er ignoreret først, og derefter oplysningerne i hovedet læse fra filen input

### <a name="specifying-avroformat"></a>Angive AvroFormat
Hvis formatet er indstillet til AvroFormat, behøver du ikke at angive egenskaber for en hvilken som helst i sektionen Format i sektionen typeProperties. Eksempel:

    "format":
    {
        "type": "AvroFormat",
    }

Hvis du vil bruge Avro format i en Hive-tabel, kan du referere til [Apache Hive selvstudium](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Bemærk følgende punkter:  

- [Komplekse datatyper](http://avro.apache.org/docs/current/spec.html#schema_complex) understøttes ikke (poster, optællinger, matrixer, kort, fagforeninger og fast)

### <a name="specifying-jsonformat"></a>Angive JsonFormat

Hvis formatet er indstillet til **JsonFormat**, kan du angive følgende **valgfrie** egenskaber i sektionen **Format** .

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- |
| filePattern | Angive mønster af data, der er gemt i hver JSON-fil. Tilladte værdier er: **setOfObjects** og **arrayOfObjects**. **Standardværdien** er: **setOfObjects**. Se nedenstående afsnit kan du finde oplysninger om disse mønstre.| Nej |
| encodingName | Angiv kodning navnet. På listen over gyldige kodning navne, se: [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) egenskab. For eksempel: windows-1250 eller shift_jis. **Standardværdien** er: **UTF-8**. | Nej | 
| nestingSeparator | Tegn, der bruges til at adskille indlejringsniveauer. Standardværdien er "." (punktum). | Nej | 


#### <a name="setofobjects-file-pattern"></a>setOfObjects fil mønster

Hver fil indeholder enkelt objekt eller linje-afgrænset/sammenføjet flere objekter. Når denne indstilling er valgt i et datasæt output, returnerer kopi aktivitet en enkelt JSON-fil med de enkelte objekter hver linje (linje-afgrænset).

**enkelt objekt** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**linje-afgrænset JSON** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**sammenkædede JSON**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern"></a>arrayOfObjects fil mønster. 

Hver fil indeholder en matrix af objekter. 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>Eksempel på JsonFormat

Hvis du har en JSON-fil med følgende indhold:  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

og du vil kopiere den til en SQL Azure-tabel i følgende format: 

Id  | Name.First | Name.Middle | Name.Last | Mærker
--- | ---------- | ----------- | --------- | ----
1 | John | Null-værdi | Larsen | ["Data Factory", "Azure"]

Indtast datasættet med JsonFormat type defineres således: (delvis definition med de relevante dele)

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

Hvis strukturen ikke er defineret, Kopiér aktiviteten samkopieres strukturen som standard og kopiere alle ting. 

#### <a name="supported-json-structure"></a>Understøttede JSON-struktur
Bemærk følgende punkter: 

- Hvert objekt med en samling af navn/værdi-par er forbundet med én række af data i et tabelformat. Objekter kan indlejres, og du kan definere Sådan udjævne strukturen i et datasæt med indlejre separatoren (.) som standard. Se [Eksempel på JsonFormat](#jsonformat-example) før sektion til et eksempel.  
- Hvis strukturen ikke er defineret i Data Factory datasættet, Kopiér aktiviteten registrerer skemaet fra det første objekt og udjævne hele objektet. 
- Hvis JSON input indeholder en matrix, konverterer den kopi aktivitet værdien hele matrixen ind i en streng. Du kan vælge at springe den ved hjælp af [kolonnetilknytning eller filtrering](#column-mapping-with-translator-rules).
- Hvis der er dublerede navne på det samme niveau, vælger den kopi aktivitet den sidste post.
- Egenskabsnavne er store og små bogstaver. To egenskaber med samme navn, men forskellige tarmene behandles som to separate egenskaber. 

### <a name="specifying-orcformat"></a>Angive OrcFormat
Hvis formatet er indstillet til OrcFormat, behøver du ikke at angive egenskaber for en hvilken som helst i sektionen Format i sektionen typeProperties. Eksempel:

    "format":
    {
        "type": "OrcFormat"
    }

> [AZURE.IMPORTANT] Hvis du ikke kopierer ORC filer **som-er** mellem i det lokale miljø og skyen data butikker, skal du installere JRE 8 (Java Runtime-miljø) på gatewaycomputeren. En 64-bit gateway kræver JRE 64-bit og 32-bit gateway kræver 32-bit JRE. Du kan finde begge versioner fra [her](http://go.microsoft.com/fwlink/?LinkId=808605). Vælg den rigtige fil.

Bemærk følgende punkter:

-   Komplekse data typer, ikke der understøttes (struktur, kort, liste, UNION)
-   ORC fil har tre [komprimering-relaterede indstillinger](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): ingen, ZLIB, SNAPPY. Data Factory understøtter læser data fra ORC fil i en af disse komprimeret formater. Det bruger komprimeringen codec er i metadataene at læse dataene. Dog når du skriver til en ORC fil, vælger Data Factory ZLIB, som er standard for ORC. Der er i øjeblikket ikke muligt at tilsidesætte denne funktionsmåde. 

### <a name="specifying-parquetformat"></a>Angive ParquetFormat
Hvis formatet er indstillet til ParquetFormat, behøver du ikke at angive egenskaber for en hvilken som helst i sektionen Format i sektionen typeProperties. Eksempel:

    "format":
    {
        "type": "ParquetFormat"
    }

> [AZURE.IMPORTANT] Hvis du ikke kopierer Parquet filer **som-er** mellem i det lokale miljø og skyen data butikker, skal du installere JRE 8 (Java Runtime-miljø) på gatewaycomputeren. En 64-bit gateway kræver JRE 64-bit og 32-bit gateway kræver 32-bit JRE. Du kan finde begge versioner fra [her](http://go.microsoft.com/fwlink/?LinkId=808605). Vælg den rigtige fil.

Bemærk følgende punkter:

-   Komplekse data typer, ikke der understøttes (kort, liste)
-   Parquet filen har følgende indstillinger for komprimering beslægtede: ingen, SNAPPY, GZIP og LZO. Data Factory understøtter læser data fra ORC fil i en af disse komprimeret formater. Det bruger komprimering codec i metadataene til at læse dataene. Dog når du skriver til en fil, Parquet, vælger Data Factory SNAPPY, som er standard for Parquet format. Der er i øjeblikket ikke muligt at tilsidesætte denne funktionsmåde. 

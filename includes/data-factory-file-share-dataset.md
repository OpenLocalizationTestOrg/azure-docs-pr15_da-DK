## <a name="fileshare-dataset-type-properties"></a>Egenskaber for filshare, som er datasæt

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](../articles/data-factory/data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er ens for alle typer af datasættet. 

Afsnittet **typeProperties** er forskellig for hver type datasæt. Den indeholder oplysninger, der er specifikke for typen datasæt. Sektionen typeProperties for et datasæt af typen **filshare, som er** datasæt har følgende egenskaber:

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
Mappesti | Sub stien til mappen. Brug escape-tegn ' \ ' for specialtegn i strengen. Se [eksempel sammenkædet tjeneste og datasæt definitioner](#sample-linked-service-and-dataset-definitions) eksempler.<br/><br/>Du kan kombinere denne egenskab med **partitionBy** have mappesti, der er baseret på udsnit start/slut dato-klokkeslæt. | Ja
Filnavn | Angive navnet på filen i **mappesti** , hvis du vil tabellen til at referere til en bestemt fil i mappen. Hvis du ikke angiver en værdi for denne egenskab, tabellen, der peger på alle filer i mappen.<br/><br/>Når filnavn ikke er angivet for en output datasæt, der ville være navnet på den oprettede fil i følgende dette format: <br/><br/>Data. <Guid>.txt (eksempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | Nej
partitionedBy | partitionedBy kan bruges til at angive en dynamisk mappesti, filnavn for klokkeslætsdata serie. For eksempel mappesti, der er parameteriseret for hver time af data. | Nej
Formatér | Følgende formattyper understøttes: **tekstformat**, **AvroFormat**, **JsonFormat**og **OrcFormat**. Angiv egenskaben **type** under formater til en af følgende værdier. Se [Angive tekstformat](#specifying-textformat), [Der angiver AvroFormat](#specifying-avroformat), [Angive JsonFormat](#specifying-jsonformat)og [Angive OrcFormat](#specifying-orcformat) sektioner få mere at vide. Hvis du vil kopiere filer som-er mellem fil-baserede lagre (binære kopi), kan du springe afsnittet format i begge input- og outputområder dataset definitioner. | Nej
fileFilter | Angiv et filter, der bruges til at vælge et undersæt af filer i mappesti i stedet for alle filer.<br/><br/>Tilladte værdier er: `*` (flere tegn) og `?` (enkelt tegn).<br/><br/>Eksempel 1:`"fileFilter": "*.log"`<br/>Eksempel 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter er tilgængelig for et input filshare, som er datasæt. Denne egenskab understøttes ikke med HDFS.  | Nej
| komprimering | Angiv den type og komprimering for dataene. Understøttede datatyper er: **GZip**, **Deflate**og **BZip2** og understøttede niveauer: **Optimal** og **hurtigst**. Komprimeringsindstillinger understøttes i øjeblikket ikke data i **AvroFormat** eller **OrcFormat**. Yderligere oplysninger, se [komprimering support](#compression-support) afsnittet.  | Nej |
| useBinaryTransfer | Angiv, om bruger binær overførselstilstand. SAND til binær tilstand og FALSK ASCII. Standardværdi: SAND. Denne egenskab kan kun bruges, når der er tilknyttet sammenkædede tjenestetype af typen: FtpServer. | Nej | 
 

> [AZURE.NOTE] filnavnet og fileFilter kan ikke bruges samtidigt.

### <a name="using-partionedby-property"></a>Ved hjælp af partionedBy egenskab

Som nævnt i forrige afsnit, kan du angive en dynamisk mappesti, filnavn for serien klokkeslætsdata med partitionedBy. Du kan gøre det med Data Factory-makroer og systemvariablen SliceStart, SliceEnd, der angiver den logiske periode for en given data udsnit. 

Se [Oprette datasæt](../articles/data-factory/data-factory-create-datasets.md), [planlægning & udførelse](../articles/data-factory/data-factory-scheduling-and-execution.md)og [Oprette rørledninger](../articles/data-factory/data-factory-create-pipelines.md) artikler for at få mere for at vide om tid serie datasæt, planlægning og udsnit. 

#### <a name="sample-1"></a>Eksempel 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

I dette eksempel {udsnit} erstattes med den angivne værdi for Data Factory systemvariabel SliceStart i formatet (YYYYMMDDHH). SliceStart henviser til starttidspunktet for udsnittet. Mappesti er forskellig for hvert udsnit. Eksempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Eksempel 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

I dette eksempel er år, måned, dag og klokkeslættet for SliceStart ud i separate variabler, der bruges af egenskaberne mappesti og filnavn.

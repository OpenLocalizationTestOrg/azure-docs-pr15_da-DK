### <a name="compression-support"></a>Understøttelse af komprimering  
Behandling af store datasæt kan medføre flaskehalse i/o- og netværk. Derfor kan komprimerede data i butikker ikke kun hurtigere dataoverførsel på tværs af netværket og spare diskplads, men også tage betydeligt ydeevneforbedringer i behandling af big data. Komprimering understøttes i øjeblikket for fil-baserede data butikker som Azure Blob eller lokale filsystem.  

> [AZURE.NOTE] Komprimeringsindstillinger understøttes ikke for data i **AvroFormat**, **OrcFormat**eller **ParquetFormat**. 

Angive komprimering for et datasæt, skal du bruge egenskaben **komprimering** i datasættet JSON som i følgende eksempel:   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
Sektionen **komprimering** har to egenskaber:  
  
- **Type:** komprimering codec, som kan være **GZIP**, **Deflate** eller **BZIP2**.  
- **Niveau:** komprimeringsforhold, som kan være **Optimal** eller **hurtigst**. 
    - **Hurtigste:** Komprimeringshandlingen skal udføre så hurtigt som muligt, selvom den resulterende fil ikke er komprimeret optimalt. 
    - **Optimal**: Komprimeringshandlingen skal optimalt komprimeres, selvom handlingen tager længere tid at fuldføre. 
    
    Få mere at vide emnet [Komprimeringsniveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Antag, at den ovenstående eksempel dataset bruges som output fra en kopi aktivitet, Kopiér aktivitet komprimerer outputdataene med GZIP codec ved hjælp af optimal forholdet og derefter skrive den komprimerede data i en fil med navnet pagecounts.csv.gz i Azure Blob-lager.   

Når du angiver komprimering egenskab i et input datasæt JSON, kan pipeline kan læse komprimerede data fra kilden, og når du angiver egenskaben i et output datasæt JSON, Kopiér aktivitet skrive komprimerede data til destinationen. Her er nogle eksempler på situationer: 

- Læs GZIP komprimeret data fra en Azure blob komprimere den, og skrive resultatdata til en Azure SQL-database. Du definerer input Azure Blob datasættet med komprimeringen JSON egenskab i dette tilfælde. 
- Læse data fra en fil med almindelig tekst fra lokale filsystem, komprimere det ved hjælp af GZip format og skrive den komprimerede data til en Azure blob. Du definerer et output Azure Blob datasæt med komprimering JSON egenskab i dette tilfælde.  
- Læse en GZIP-komprimerede data fra en Azure blob, komprimere den, komprimere det ved hjælp af BZIP2 og skrive resultatdata til en Azure blob. Du kan definere input Azure Blob datasættet med komprimeringstype, der er angivet til GZIP og output datasættet komprimering typen er angivet til BZIP2 i dette tilfælde.   

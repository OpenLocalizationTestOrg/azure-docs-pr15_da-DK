## <a name="repeatability-during-copy"></a>Repeterbarhed under kopiering

Når du kopierer data til Azure SQL/SQL Server fra andre data gemmer skal en Husk repeterbarhed på for at undgå utilsigtede resultater. 

Når du kopierer data til Azure SQL/SQL Server-Database, der kopi aktivitet som standard Tilføj datasæt til tabellen sink som standard. Når du kopierer data fra en CSV-(kommaseparerede værdier) fil datakilde med to poster til Azure SQL/SQL Server-Database, er dette for eksempel ser tabellen ud:
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00

Antag, at du fandt fejl i kildefilen og opdateret antal af ned røret fra 2 til 4 i kildefilen. Hvis du kører igen dataudsnittet for denne periode, finder du to nye poster, der er føjet til Azure SQL/SQL Server-Database. Den nedenfor antages det, at ingen af kolonnerne i tabellen har en begrænsning på den primære nøgle.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Du kan undgå dette, skal du angive UPSERT semantik ved at udnytte en af de under 2 mekanismer angivet nedenfor.

> [AZURE.NOTE] Et udsnit kan igen køres automatisk på Azure Data fabrik ud fra politikken forsøg angivet.

### <a name="mechanism-1"></a>Metode 1

Du kan udnytte **sqlWriterCleanupScript** egenskab for at udføre først oprydning handling, når du kører et udsnit. 

    "sink":  
    { 
      "type": "SqlSink", 
      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
    }

Oprydning script skal køres første under kopiering for en given udsnit, ville slette dataene fra tabellen SQL svarende til udsnittet. Aktiviteten vil efterfølgende indsætte dataene i tabellen SQL. 

Hvis udsnittet er nu køre igen, og du kan finde antallet opdateres som ønsket.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Antag, at den flade spændeskive post fjernes fra den oprindelige csv. Køre udsnittet igen resulterer i følgende resultat: 
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    7   Down Tube   4           2015-05-01 00:00:00

Ikke noget nyt skulle udføres. Kopiér aktiviteten kørte scriptet oprydning for at slette de tilsvarende data for udsnittet. Vælg derefter den læse input fra den csv (som derefter indeholdt kun 1 post) og indsat i tabellen. 

### <a name="mechanism-2"></a>Metode 2
> [AZURE.IMPORTANT] sliceIdentifierColumnName understøttes ikke af Azure SQL Data Warehouse på nuværende tidspunkt. 

En anden metode til at opnå repeterbarhed er ved at få en dedikeret kolonne (**sliceIdentifierColumnName**) i mållejerens tabel. Denne kolonne bruges af Azure Data Factory til at sikre, at kilde- og holde synkroniseret. Denne metode fungerer, når der er fleksibilitet i ændre eller definition destination SQL-tabel. 

Denne kolonne skal anvendes af Azure Data Factory til repeterbarhed formål, og i processen Azure Data Factory skal ikke foretage skemaændringer til tabellen. Metode til at bruge denne metode:

1.  Definere en kolonne af typen binær (32) destination SQL-tabel. Der skal være nogen begrænsninger på denne kolonne. Lad os Navngiv denne kolonne som 'ColumnForADFuseOnly' i dette eksempel.
2.  Bruge det i kopi aktiviteten på følgende måde:

        "sink":  
        { 
          "type": "SqlSink", 
          "sliceIdentifierColumnName": "ColumnForADFuseOnly"
        }

Azure Data Factory udfylder denne kolonne ud fra dets har du brug for at sikre, at kilde- og holde synkroniseret. Værdierne i denne kolonne der ikke bør benyttes uden for denne forbindelse af brugeren. 

Svarer til ordning 1, Kopiér aktivitet bliver automatisk først rydde op i dataene for det angivne udsnit fra destinationen, SQL-tabel og derefter køre kopi aktiviteten normalt, hvis du vil indsætte dataene fra kilde til destination for udsnittet. 

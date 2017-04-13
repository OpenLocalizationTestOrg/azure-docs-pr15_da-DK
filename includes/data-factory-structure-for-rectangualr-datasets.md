## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Angive struktur definition for rektangulære datasæt
Sektionen strukturen i datasæt JSON er en **valgfri** sektion for rektangulære tabeller (med rækker og kolonner) og indeholder en samling af kolonner til tabellen. Du vil bruge sektionen struktur til enten at give typeoplysninger for typekonverteringer eller at gøre kolonnetilknytningerne. I følgende afsnit beskrives disse funktioner i detaljer. 

Hver kolonne indeholder følgende egenskaber:

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- |
| Navn | Navnet på kolonnen. | Ja |
| type | Datatypen for kolonnen. Se type konverteringer sektion under til flere detaljer om hvornår skal du angive oplysninger om | Nej |
| kultur | .NET baseret kultur skal bruges, når type er angivet og .NET typen Dato/klokkeslæt eller Datetimeoffset. Standard er "da-DK".  | Nej |
| Formatér | Formatere streng, der skal bruges, når type er angivet og .NET typen Dato/klokkeslæt eller Datetimeoffset. | Nej |

I følgende eksempel viser sektionen struktur JSON for en tabel, der har tre kolonner bruger-id, navn og lastlogindate.

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

Brug følgende retningslinjer for hvornår du vil medtage "struktur" oplysninger og hvad der skal medtages i sektionen **struktur** .

- **Til strukturerede datakilder** , der gemmer dataene skema og en type oplysninger sammen med dataene selve (kilder som SQL Server, Oracle, Azure table osv.), skal du angive afsnittet "struktur" kun, hvis du vil gøre kolonnetilknytningen af bestemte kildekolonner til bestemte kolonner i sink og deres navne er ikke den samme (se detaljer om i kolonnen tilknytning afsnittet nedenfor). 

    Som nævnt ovenfor, er typeoplysninger valgfrit i "struktur" afsnit. Typeoplysninger er allerede tilgængelige for strukturerede datakilder, som en del af dataset definition i lageret med data, så du skal ikke medtage typeoplysninger når du medtager afsnittet "struktur".
- **For skema på Læs datakilder (specifikt Azure blob)** du kan vælge at gemme data uden at gemme eventuelle skema, eller skriv oplysninger med data. For disse typer af datakilder skal du medtage "struktur" i følgende tilfælde 2:
    - Du vil gøre kolonnetilknytning.
    - Når datasættet er en kilde i en kopi aktivitet, du kan angive typeoplysninger i "struktur" og data factory bruger denne typeoplysninger til konverteringen tilbage til oprindelige datatyper for sink. Se [flytte data til og fra Azure Blob](../articles/data-factory/data-factory-azure-blob-connector.md) artikel for at få flere oplysninger.

### <a name="supported-net-based-types"></a>Understøttes. Nettooversigt-baserede typer 
Data factory understøtter følgende CLS kompatibel .NET baseret typerværdier for at give typeoplysninger i "struktur" til skemaet på Læs datakilder som Azure blob.

- Int16
- Int32 
- Int64
- Enkelt
- Dobbelt
- Decimal
- Byte]
- Boolesk
- Streng 
- GUID
- Dato og klokkeslæt
- DateTimeOffset
- TimeSpan 

Du kan eventuelt også angive "kultur" og "formatere" streng for at lette parsing af din brugerdefinerede dato/klokkeslæt-streng til Datetime & Datetimeoffset. Se eksempel til konvertering af datatyper nedenfor.


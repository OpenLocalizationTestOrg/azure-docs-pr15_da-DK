<properties
   pageTitle="Generisk SQL Connector | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du konfigurerer Microsofts generisk SQL Connector."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-technical-reference"></a>Generisk SQL Connector tekniske reference

I denne artikel beskrives generisk SQL forbindelsen. I artiklen gælder for følgende produkter:

- Microsoft identitet Manager 2016 (MIM2016)
- Forefront identitet Manager 2010 R2 (FIM2010R2)
    -   Skal bruge programrettelse 4.1.3671.0 eller nyere [KB3092178](https://support.microsoft.com/kb/3092178).

Forbindelsen er for MIM2016 og FIM2010R2 skal hentes fra [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

For at se denne forbindelse i handling skal du se artiklen [Generisk SQL Connector trinvise](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) .

## <a name="overview-of-the-generic-sql-connector"></a>Oversigt over generisk SQL-forbindelse

Generisk SQL forbindelsen gør det muligt at integrere tjenesten til synkronisering med et databasesystem, der indeholder ODBC-forbindelse.  

Følgende funktioner understøttes af den aktuelle udgave af forbindelsen fra et overordnet perspektiv:

Funktion | Support
--- | ---
Forbundne datakilde | Forbindelsen understøttes med alle 64-bit ODBC-drivere. Det er testet med følgende: <li>Microsoft SQL Server og SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 og 11 g</li><li>MySQL 5.x</li>
Scenarier   | <li>Objekt Livscyklusadministration</li><li>Administration af adgangskoder</li>
Handlinger | <li>Fuldstændig Import og Delta Importér, Eksportér</li><li>Eksportér: Tilføj, Slet, Opdater og erstatte</li><li>Angive adgangskode, ændre adgangskode</li>
Skema | <li>Dynamisk registrering af objekter og attributter</li>

### <a name="prerequisites"></a>Forudsætninger
Før du bruger forbindelsen, skal du kontrollere følgende på serveren synkronisering være:

- Microsoft .NET 4.5.2 Framework eller nyere
- 64-bit ODBC-drivere,-klienten

### <a name="permissions-in-connected-data-source"></a>Tilladelser i forbundne datakilde
Hvis du vil oprette eller udføre en af de understøttede opgaver i generisk SQL forbindelse, skal du have:

- db_datareader
- db_datawriter

### <a name="ports-and-protocols"></a>Porte og protokoller
For de porte, der kræves til ODBC-driver til at fungere sammen, dokumentationen leverandør af databasen.

## <a name="create-a-new-connector"></a>Oprette en ny forbindelse
Vælg **Administration Agent** og **oprette**for at oprette en generisk SQL-forbindelse, i **Tjenesten Brugerprofilsynkronisering** . Markér forbindelsen **generisk SQL (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Forbindelse
Forbindelsen bruger en ODBC DSN-fil for forbindelsen. Oprette DSN-filen ved hjælp af **ODBC-datakilder, der** findes i startmenuen under **Administration**. Oprette et **DSN-fil** i værktøjet administrative, så det kan være angivet til forbindelsen.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

Skærmbilledet Connectivity er først, når du opretter en ny generisk SQL-forbindelse. Først skal du angive følgende oplysninger:

- DSN filsti
- Godkendelse
    - Brugernavn
    - Adgangskode

Databasen skal understøtte en af disse metoder til brugergodkendelse:

- **Windows-godkendelse**: godkender databasen bruger Windows-legitimationsoplysninger til at bekræfte brugeren. Det brugernavn/adgangskode angivet bruges til at godkende med databasen. Denne konto skal have tilladelser til databasen.
- **SQL-godkendelse**: de godkender database bruger brugernavn/adgangskoden har defineret en Connectivity skærmen for at oprette forbindelse til databasen. Hvis du gemmer bruger navn/adgangskode i DSN-filen, har de angivne på skærmbilledet Connectivity legitimationsoplysninger rangorden for.
- **Azure SQL-Database godkendelse**: Du kan finde flere oplysninger under [oprette forbindelse til SQL Database ved hjælp af Azure Active Directory Authentication](..\sql-database\sql-database-aad-authentication.md).

**DN er anker**: Hvis du vælger denne indstilling, DN bruges også som attributten anker. Det kan bruges til en simpel implementering, men også har følgende begrænsninger:

-   Forbindelse understøtter kun én objekttype. En hvilken som helst referenceattributter kan derfor kun referere til den samme objekttype.

**Eksportere Type: objekt Erstat**: under eksport, når det er kun nogle attributter er blevet ændret, hele objektet med alle attributter er eksporteret og erstatter det eksisterende objekt.

### <a name="schema-1-detect-object-types"></a>Skema 1 (Find objekttyper)
På denne side vil du konfigurere, hvordan forbindelsen skal finde de forskellige objekttyper i databasen.

Hver objekttype præsenteret som en partition og konfigureret yderligere på **konfigurere partitioner og hierarkier**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Objekttype registrering metode**: tilføjelsesprogrammet Connector understøtter disse metoder til påvisning af objekt.

- **Fast værdi**: du leverer en liste over objekttyper med en kommasepareret liste. For eksempel: `User,Group,Department`.  
![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Tabel/visning/lagrede Procedure**: angive navnet på den tabel/visning/gemt procedure og derefter på det kolonnenavn, der indeholder en oversigt over objekttyper. Hvis du bruger en lagret procedure, skal du også angive parametre for det i formatet **[navn]: [retning]: [værdi]**. Give hver parameter på en separat linje (Brug Ctrl + Enter for at få en ny linje).  
![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **SQL-forespørgsel**: denne indstilling kan du give en SQL-forespørgsel, der returnerer en enkelt kolonne med objekttyper, for eksempel `SELECT [Column Name] FROM TABLENAME`. Kolonnen returnerede skal være af typen streng (varchar).

### <a name="schema-2-detect-attribute-types"></a>Skema 2 (Find attributten typer)
På denne side vil du konfigurere, hvordan attributter navne og typer går til findes. Konfigurationsindstillingerne er angivet for hver objekttype fundet på den forrige side.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Attributten Type registrering metode**: tilføjelsesprogrammet Connector understøtter disse attributten type registreringsmetoder med hver fundet objekttype i skema 1 skærm.

- **Tabel/visning/lagrede Procedure**: Angiv navnet på den tabel/visning/gemt procedure, der skal bruges til at finde navne på attributter. Hvis du bruger en lagret procedure, skal du også angive parametre for det i formatet **[navn]: [retning]: [værdi]**. Give hver parameter på en separat linje (Brug Ctrl + Enter for at få en ny linje). Hvis du vil registrere attribut navnene i en opslagskolonne med flere valgmuligheder attribut, give en kommasepareret liste over tabeller eller visninger. Scenarier med flere værdier understøttes ikke når overordnede og underordnede tabel har samme kolonnenavne.
- **SQL-forespørgsel**: denne indstilling kan du give en SQL-forespørgsel, der returnerer en enkelt kolonne med navne på attributter, for eksempel `SELECT [Column Name] FROM TABLENAME`. Kolonnen returnerede skal være af typen streng (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Skema 3 (definere anker og DN)
Denne side kan du konfigurere anker og DN attribut for hver fundet objekttype. Du kan vælge flere attribut at gøre det entydigt anker.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- Med flere værdier og booleske attributter vises ikke.
- Kan ikke bruge samme attribut for DN og anker, medmindre **DN er anker** er markeret på siden Connectivity.
- Hvis **DN er anker** er markeret på siden Connectivity, kræver denne side kun attributten DN. Denne attribut vil også bruges som attributten anker.
![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Skema 4 (definere attribut, reference, og retning)
Denne side kan du konfigurere den attribut, som heltal, binært tal eller boolesk og retningen for hver attribut. Alle attributter fra siden **skema 2** vises herunder med flere valgmuligheder attributter.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **DataType**: bruges til at knytte attributten type til typerne kendt som Synkroniser-program. Standard er at bruge den samme type, som findes i SQL-skema, men DateTime og Reference er ikke nemt kan registreres. For dem skal du angive **dato/klokkeslæt** eller **Reference**.
- **Retning**: Du kan angive attribut retningen til Importér, Eksportér eller ImportExport. ImportExport er standard.
![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Noter:

- Hvis en attribut af typen ikke der kan registreres via forbindelsen, bruger datatypen streng.
- **Indlejrede tabeller** kan betragtes som én kolonne databasetabeller. Oracle gemmer rækkerne i en indlejret tabel i en bestemt rækkefølge. Når du henter den indlejrede tabel til en PL/SQL-variabel, kan rækkerne, der er givet fortløbende sænket skrift fra og med 1. Får du synes godt om matrix adgang til individuelle rækker.
- **VARRYS** understøttes ikke i forbindelsen.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Skema 5 (definere partition for referenceattributter)
På denne side kan konfigurere du for alle referenceattributter hvilke partition (objekttype) en attribut henviser til.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Hvis du bruger **DN er anker**, skal du bruge den samme objekttype som den, du refererer fra. Du kan ikke referere til en anden objekttype.

### <a name="global-parameters"></a>Globale parametre
Siden globale parametre bruges til at konfigurere Delta Importér, dato/klokkeslæt-format og metoden for adgangskoder.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

Generisk SQL forbindelsen understøtter følgende metoder til Delta Import:

- **Udløser**: se [genererende Delta visninger ved hjælp af udløsere](https://technet.microsoft.com/library/cc708665.aspx).
- **Vandmærke**: en generisk fremgangsmåde, der kan bruges sammen med en database. Forespørgslen vandmærke er udfyldt baseret på leverandøren af databasen. En vandmærke kolonne skal være installeret på hver tabelvisning bruges. Denne kolonne skal registrere indsættelser og opdateringer til tabeller, der indeholder og dens afhængige (med flere værdier eller udnyttelse) tabeller. Klokkeslæt mellem tjenesten Brugerprofilsynkronisering og databaseserveren skal synkroniseres. Hvis ikke, nogle poster i dialogboksen Importer delta kan udelades.  
Begrænsning:
    - Vandmærke strategi indeholder ikke understøttelse af slettede objekter.
- **Øjebliksbillede**: (fungerer kun med Microsoft SQL Server) [generere Delta visninger ved hjælp af Snapshots](https://technet.microsoft.com/library/cc720640.aspx)
- **Registrering af ændringer**: (fungerer kun med Microsoft SQL Server) [om registrering af ændringer](https://msdn.microsoft.com/library/bb933875.aspx)  
Begrænsninger:
    - Anker og DN attribut skal være en del af primærnøgle for det markerede objekt i tabellen.
    - SQL-forespørgsel er ikke-understøttede under Import og eksport med registrering af ændringer.

**Yderligere parametre**: angive tidszone Database Server, der angiver, hvor din databaseserver er placeret. Denne værdi bruges til at understøtte forskellige formater for dato og klokkeslæt attributter.

Forbindelsen gemmer altid dato og dato / klokkeslæt i UTC-format. Hvis du vil kunne korrekt konvertere datoen og klokkeslæt, en tidszone for databaseserveren og det anvendte format skal angives. Formatet skal angives i .net-format.

Under eksport skal hver dato klokkeslæt attribut angives Connector i UTC klokkeslætsformat.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Konfiguration af adgangskode**: forbindelsen indeholder adgangskode synkronisering funktioner og understøtter angive og ændre adgangskoden.

Forbindelsen indeholder to metoder til at understøtte synkronisering af adgangskoder:

- **En lagret Procedure**: Denne metode kræver to lagrede procedurer til at understøtte sæt og Skift adgangskode. Skriv alle parametre for Tilføj og rediger handlingen adgangskode i **Angiv adgangskode SP** og **Ændre adgangskode SP** parametre henholdsvis som per under eksempel.
![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Adgangskode lokalnummer**: Denne metode kræver adgangskode udvidelse DLL (du skal angive DLL filtypenavnet, der implementerer grænsefladen [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) ). Adgangskode lokalnummer samling skal være placeret i lokalnummer mappe, så forbindelsen kan indlæse DLL på kørselstidspunktet.
![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Du har også aktivere for adgangskodeadministration på siden **Konfigurer filtypenavn** .
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Konfigurere partitioner og hierarkier
Vælg alle objekttyper på siden partitioner og hierarkier. De enkelte objekt er en særskilt partition.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Du kan også tilsidesætte de værdier, der er defineret på siden **Connectivity** eller **Globale parametre** .

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Konfigurere ankre
Denne side er skrivebeskyttet, da ankeret er allerede blevet defineret. Attributten markerede anker føjes altid med objekttype for at sikre, at det forbliver entydige på tværs af objekttyper.

![ankre](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Konfigurere køre trin Parameter
Disse trin er konfigureret på de køre profiler på forbindelsen. Disse konfigurationer gør det faktiske arbejde for import og eksport af data.

### <a name="full-and-delta-import"></a>Fuld og Deltaimport
Generisk SQL Connector support fuld og Delta importere ved hjælp af følgende metoder:

- Tabel
- Få vist
- Lagret Procedure
- SQL-forespørgsel

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabelvisning /**  
For at importere flere værdier attributter for et objekt, skal du angive navnet på kommaseparerede tabel/visningen i **navn på Multi-Valued tabel/visninger** og respektive joinforbindelse betingelser i **Join-betingelsen** med den overordnede tabel.

Eksempel:, Du vil importere objektet medarbejder og alle dens med flere valgmuligheder attributter. Der findes to tabeller, kaldet medarbejder (primære tabel) og afdeling (flere værdier).
Benyt følgende fremgangsmåde:

- Skriv **Medarbejder** i **Tabel/visning/SP**.
- Skriv afdeling i **navn på Multi-Valued tabel/visninger**.
- Skriv join-betingelsen mellem medarbejder & afdeling i **Join-betingelsen**, for eksempel `Employee.DEPTID=Department.DepartmentID`.
![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Lagrede procedurer**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Hvis du har mange data, anbefales det at implementere sideinddeling med din lagrede procedurer.
- For din gemt Procedure til at understøtte sideinddeling, skal du angive Start indeks og slutningen indeks. Se: [effektivt sideopdeling gennem store datamængder](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndexog @EndIndex erstattes ved kørselstidspunktet med respektive siden størrelsesværdi, der er konfigureret på siden **Konfigurer trin** . For eksempel, når forbindelsen henter første side og sidestørrelsen er angivet 500, i sådan situation @StartIndex ville være 1 og @EndIndex 500. Disse værdier øge når forbindelsen henter efterfølgende sider og ændre den @StartIndex & @EndIndex værdi.
- For at udføre parameteriseret en lagret Procedure, skal du angive parametre i `[Name]:[Direction]:[Value]` format. Angiv hver parameter i en separat linje (Brug Ctrl + Enter for at få en ny linje).
- Generisk SQL connector understøtter også importhandlingen fra sammenkædede servere i Microsoft SQL Server. Hvis oplysningerne skal hentes fra en tabel i sammenkædede server, skal tabellen angives i formatet:`[ServerName].[Database].[Schema].[TableName]`
- Generisk SQL Connector understøtter kun de objekter, som har lignende struktur (både alias navn og datatype) mellem køre trin oplysninger og -Skemafiler registrering. Hvis det markerede objekt fra skemaet og medfølgende oplysninger på Kør trin er anderledes, derefter er SQL forbindelsen ikke understøtter denne type scenarier.

**SQL-forespørgsel**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- Flere resultatsættet forespørgsler, der ikke understøttes.
- SQL-forespørgsel understøtter sideinddelingen og angive start indeks og slutningen indeks som en variabel til at understøtte sideinddeling.

### <a name="delta-import"></a>Deltaimport
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Delta importkonfiguration kræver nogle flere konfiguration varians sammenlignet med fuld Import.

- Hvis du vælger udløser eller et Snapshot metoden til at registrere delta ændringer, Giv dernæst oversigtstabel eller et Snapshot database i **Oversigt oversigtstabel eller et øjebliksbillede databasenavn** .
- Du skal også give join-betingelsen mellem oversigtstabel og overordnet tabel, f.eks.`Employee.ID=History.EmployeeID`
- Hvis du vil spore posteringen på den overordnede tabel fra tabellen oversigten, skal du angive det kolonnenavn, der indeholder oplysninger, handling (Tilføj/Update/Slet).
- Hvis du vælger at registrere delta ændringer vandmærket, derefter angive det kolonnenavn, der indeholder handlingen oplysningerne i **Vand Markér kolonnenavn**.
- Kolonnen **ændre Type-attribut** er påkrævet for dialogboksen Skift type. Denne kolonne knytter en ændring, der optræder i den primære tabel eller en tabel med flere værdier på en ændring i visningen delta. Denne kolonne kan indeholde ændringstype Modify_Attribute for attributten niveau ændring eller en Tilføj, Rediger eller Slet ændre typen for en objektniveau ændringstype. Hvis det er et andet tal end standardværdien Tilføj, kan Rediger eller Slet, og du definere disse værdier, der bruger denne indstilling.

### <a name="export"></a>Eksportere
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Generisk SQL Connector understøtter eksport med fire understøttede metoder f.eks.:

- Tabel
- Få vist
- Lagret Procedure
- SQL-forespørgsel

**Tabelvisning /**  
Hvis du vælger indstillingen tabelvisning/genererer forbindelsen de respektive forespørgsler for at gøre eksporten.

**Lagrede procedurer**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Hvis du vælger indstillingen en lagret Procedure, kræver Eksportér tre forskellige lagrede procedurer til at udføre Indsæt/opdater/slette handlinger.

- **Tilføj SP navn**: denne SP køres, hvis et objekt, der leveres til forbindelsen til indsættelse i den pågældende tabel.
- **Opdatere SP navn**: denne SP kører Hvis objekter kommer til connector til opdatering i den pågældende tabel.
- **Slette SP navn**: denne SP kører Hvis objekter kommer til connector til sletning i den pågældende tabel.
- Attribut markeret fra skemaet bruges som en parameterværdi til den lagrede procedure. For eksempel `EmployeeName: INPUT: @EmployeeName` (EmployeeName er markeret i forbindelse skemaet og forbindelsen erstatter den respektive værdi, mens du gør Eksportér)
- For at køre parameteriseret lagret procedure skal du angive parametre i `[Name]:[Direction]:[Value]` format. Angiv hver parameter i en separat linje (Brug Ctrl + Enter for at få en ny linje).

**SQL-forespørgsel**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Hvis du vælger indstillingen SQL-forespørgsel, kræver Eksportér tre forskellige forespørgsler til at udføre Indsæt/opdater/slette handlinger.

- **Indsætte forespørgsel**: denne forespørgsel køres, hvis et objekt, der leveres til forbindelsen til indsættelse i den pågældende tabel.
- **Opdater forespørgsel**: denne forespørgsel køres, hvis et objekt, der leveres til forbindelsen til opdatering i den pågældende tabel.
- **Sletteforespørgsel**: denne forespørgsel køres, hvis et objekt, der leveres til forbindelsen til sletning i den pågældende tabel.
- Attributten markeret fra skemaet bruges som en parameterværdi til forespørgslen, f.eks.`Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

-   Du kan finde oplysninger om, hvordan du aktiverer logføring til at foretage fejlfinding af forbindelsen, [hvordan du kan aktivere ETW sporing for forbindelser](http://go.microsoft.com/fwlink/?LinkId=335731).

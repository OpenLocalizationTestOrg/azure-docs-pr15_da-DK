<properties
   pageTitle="Overføre dine data til SQL Data Warehouse | Microsoft Azure"
   description="Tip til at overføre dine data til Azure SQL Data Warehouse til udvikling af løsninger."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="migrate-your-data"></a>Overføre dine Data
Data kan flyttes fra forskellige kilder i din SQL Data Warehouse med en række værktøjer.  ADF kopier, SSIS og bcp kan alle bruges til at opnå dette mål. Dog som mængden data øger skal du overveje opdele dataoverførselsprocessen i trin. Dette giver mulighed for at optimere hvert trin både ydeevne og spændstighed at sikre en jævn dataoverførsel.

I denne artikel beskrives først overførselsscenarier enkel af ADF kopi, SSIS og bcp. Det kan derefter se nærmere på hvordan overførslen kan optimeres.

## <a name="azure-data-factory-adf-copy"></a>Azure Factory (ADF) kopi
[ADF kopi][] er en del af [Azure Data Factory][]. Du kan bruge ADF kopi til at eksportere dataene til flad filer, der er placeret på lokale lager til eksterne flad filer i Azure blob-lager eller direkte i SQL Data Warehouse.

Hvis dine data, der starter i flade filer og derefter skal du først at overføre den til Azure lagerplads blob inden du starter en belastning dem til SQL Data Warehouse. Når dataene er overført til Azure blob-lager kan du vælge at bruge [ADF Kopiér][] igen til at overføre data til SQL Data Warehouse.

PolyBase indeholder også en indstilling for høj ydeevne for indlæsning af dataene. Der betyder dog med to værktøjer i stedet for en. Hvis du har brug for den bedste ydeevne og derefter bruge PolyBase. Hvis du vil have en enkelt værktøj oplevelse (og data, der ikke er massive) er ADF dit svar.

> [AZURE.NOTE] PolyBase kræver dine datafiler skal være i UTF-8. Dette er ADF kopi standard kodning, så der ikke er noget at ændre. Dette er blot en påmindelse om at ændre ikke standardfunktionsmåden for ADF kopi.

Hoved i følgende artikel nogle gode [ADF eksempler][].

## <a name="integration-services"></a>Integrationsservices ##
Integration Services (SSIS) er en effektiv og fleksibel udtrække transformere og Indlæs (ETL) værktøj, der understøtter komplekse arbejdsprocesser, datatransformation og flere indstillinger for indlæsning af data. Brug SSIS mulighed for at overføre data til Azure eller som en del af en bredere migrering.

> [AZURE.NOTE] SSIS kan eksportere til UTF-8 uden byte rækkefølge mærket i filen. For at konfigurere dette skal du først bruge komponenten afledt kolonne til at konvertere tegn dataene i dataflowet til at bruge 65001 UTF-8-tegntabel. Når kolonnerne, der er blevet konverteret, kan du skrive dataene til at sikre, at 65001 også er valgt som tegntabel til filen flad fil destination kortet.

SSIS opretter forbindelse til SQL Data Warehouse, ligesom der skal oprettes forbindelse til en SQL Server-installation. Men dine forbindelser skal bruge en ADO.NET-Forbindelsesstyring. Du bør også sørge for at konfigurere "Brug flere Indsæt når de er tilgængelige" indstilling til at maksimere overførselshastighed. Se artiklen [ADO.NET destination adapter][] mere at vide om denne egenskab

> [AZURE.NOTE] Oprette forbindelse til Azure SQL Data Warehouse ved hjælp af OLEDB understøttes ikke.

Desuden er der altid mulighed, som en pakke, kan muligvis ikke forfaldsdato (throttling)-eller netværksproblemer. Design pakker, så de kan uden genoptages på det pågældende mislykket for arbejde, der er afsluttet før fejlen.

Læs [SSIS dokumentation][]for at få mere at vide.

## <a name="bcp"></a>BCP
BCP er et kommandolinjen værktøj, der er beregnet til flad fil dataimport og eksport. Nogle transformation kan finde sted under eksport af data. Enkel transformationer brug for at udføre en forespørgsel til at vælge og transformere dataene. Når eksporteret, kan flade filer derefter indlæses direkte i destinationen SQL Data Warehouse databasen.

> [AZURE.NOTE] Det er ofte en god ide at omfatter de transformeringer, bruges under eksport af data i en visning på kildesystem. Dette sikrer, at logikken bevares, og processen er gentaget.

Fordelene ved bcp er:

- Enkel. BCP kommandoer er nemme at opbygge og udføre
- Igen kan startes fra disken Indlæs proces. Én gang eksporterede afkrydsningsfeltet Indlæs kan være udført en hvilken som helst antal gange

Begrænsninger i forbindelse med bcp er:

- BCP fungerer med kun tabelform flad filer. Det fungerer ikke med filer som XML- eller JSON
- BCP understøtter ikke eksport til UTF-8. Dette kan forhindre ved hjælp af PolyBase på bcp eksporteres data
- Data transformation funktioner er begrænset til fasen Eksportér og er enkle karakter
- BCP der ikke er tilpasset skal være robust under indlæsning af data via internettet. Manglende netværk stabilitet kan medføre en fejl ved indlæsning.
- BCP er afhængig af skemaet ikke er til stede i destinationsdatabasen før afkrydsningsfeltet Indlæs

Du kan finde flere oplysninger [Brug bcp indlæse data i SQL Data Warehouse][].

## <a name="optimizing-data-migration"></a>Optimering af overflytning af data
En SQLDW dataoverførselsprocessen kan effektivt opdeles i tre separate trin:

1. Eksport af kildedata
2. Overførsel af data til Azure
3. Indlæse i SQLDW destinationsdatabasen

Hvert trin kan optimeres individuelt for at oprette en robust, igen kan startes fra disken og tolerant overførselsprocessen, maksimerer ydeevne ved hvert trin.

## <a name="optimizing-data-load"></a>Optimere indlæsning af data
Se på disse i omvendt rækkefølge for et øjeblik den hurtigste måde at indlæse data er via PolyBase. Optimere til en PolyBase Indlæs proces placerer forudsætninger på de foregående trin så det er bedst at forstå dette IDE. De er:

1. Kodning af datafiler
2. Formatet af datafiler
3. Placeringen af datafiler

### <a name="encoding"></a>Kode
PolyBase kræver datafiler skal være UTF-8-kodet. Det betyder, at når du eksporterer dataene i det skal overholde dette krav. Hvis dine data indeholder kun grundlæggende ASCII-tegn (ikke udvidet ASCII) derefter disse kort direkte UTF-8-standard, og du ikke behøver at bekymre dig for meget om kodningen. Men hvis dine data indeholder nogen specialtegn, som umlauts, accenter eller symboler eller dine data understøtter ikke-latinsk sprog derefter du nødt til at sikre, at eksportere filer er korrekt UTF-8-kodet.

> [AZURE.NOTE] BCP understøtter ikke eksportere data til UTF-8. Derfor er den bedste løsning at bruge enten Integration Services eller ADF kopi til dataeksporten. Det er værd fremhæver, UTF-8 byte rækkefølge mærket (Stykliste) ikke er påkrævet i datafilen.

Alle filer, der er kodet ved hjælp af UTF-16 skal igen skrives ***forudgående*** til dataoverførslen.

### <a name="format-of-data-files"></a>Formatet af datafiler
PolyBase sikrer en fast række terminalpunkt af \n eller ny linje. Dine datafiler skal overholde denne standard. Der ikke er nogen begrænsninger på terminalpunkter for streng eller kolonne.

Du skal definere hver kolonne i filen som en del af dine eksterne tabel i PolyBase. Sørg for, at alle eksporterede kolonner er påkrævet og, at de er i overensstemmelse med de krævede standarder.

Se tilbage til den [overføre dit skema] artikel for at få oplysninger om understøttede datatyper.

### <a name="location-of-data-files"></a>Placeringen af datafiler
SQL Data Warehouse bruger PolyBase til at indlæse data fra Azure Blob-lager udelt adgang. Derfor skal dataene er først overført til blob-lager.

## <a name="optimizing-data-transfer"></a>Optimere dataoverførsel
En af de lavest dele af dataoverførsel er overførsel af dataene til Azure. Ikke kun kan netværksbåndbredde være et problem, men også netværk pålidelighed alvorligt kan medfører status. Er via internettet, så risikoen for overførsel fejl, der opstår er praktisk sandsynligvis som standard overføre data til Azure. Disse fejl kan dog kræve dataene skal sendes igen, enten helt eller delvist.

Heldigvis har du forskellige valgmuligheder til at forbedre hastighed og fleksibilitet af denne proces:

### <a name="expressroute"></a>[ExpressRoute][]
Kan du overveje at bruge [ExpressRoute][] til hurtigere at overførslen. [ExpressRoute][] giver dig en privat forbindelsen til Azure så forbindelsen ikke går via offentlige internettet. Dette er ikke på nogen måde et obligatorisk trin. Men det kan du forbedre ydeevnen når skubbe data til Azure fra et lokalt eller samtidig placering facilitet.

Fordelene ved at bruge [ExpressRoute][] er:

1. Øget pålidelighed
2. Hurtigere netværkshastighed
3. Nederste netværksventetid
4. større netværkssikkerhed

[ExpressRoute][] er nyttige for et antal installationer. ikke blot overførslen.

Interesseret? Yderligere oplysninger og priser skal du besøge [ExpressRoute dokumentation][].

### <a name="azure-import-and-export-service"></a>Azure Import og eksport-tjenesten
Tjenesten Azure importere og eksportere er en data overførslen, der er udviklet til store (GB ++) til massive (TB ++) overførsel af data til Azure. Det indebærer, at oprettelsen af dine data til diske og sende dem til en Azure datacenter. Diskindholdet bliver derefter indlæst i Azure lagerplads BLOB på dine vegne.

En overordnet oversigt over eksport importprocessen er som følger:

1. Konfigurere en Azure Blob-lager objektbeholder for at hente dataene
2. Eksportere dataene til det lokale lager
2. Kopiér dataene til 3,5 SATA II/III harddiskdrev værktøjet [Azure Importér/Eksportér]
3. Oprette et Job til Import af med Azure Import og eksport udbyder journal-filer, der er oprettet med [Azure Importér/Eksportér værktøj]
4. Levere diskene dit udpeges Azure datacenter
5. Dine data er overført til din Azure Blob-lager objektbeholder
6. Indlæs data til SQLDW ved hjælp af PolyBase

### <a name="azcopy-utility"></a>[AZCopy][] hjælpeprogram
Værktøjet [AZCopy][] er et godt værktøj til at hente dine data, der overføres til Azure-lager BLOB. Det er udviklet til små (MB ++) til meget store (GB ++) dataoverførsler. [AZCopy] er også udviklet til at levere god tolerant overførselshastighed, når du overfører data til Azure og det er et godt valg for trinnet data overførsel. Én gang overførte kan du indlæse data med PolyBase i SQL Data Warehouse. Du kan også medtage AZCopy i dine SSIS-pakker ved hjælp af en "Udføre processen" opgave.

Hvis du vil bruge AZCopy skal du først hente og installere den. Der findes en [version af][] og en [prøveversion][] .

Hvis du vil overføre en fil fra filsystemet skal du en kommando som den nedenfor:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

En oversigt på højt niveau proces skyldes:

1. Konfigurere en objektbeholder til Azure lagring blob for at hente dataene
2. Eksportere dataene til det lokale lager
3. AZCopy dine data i beholderen Azure Blob-lager
4. Indlæs data til SQL Data Warehouse ved hjælp af PolyBase

Fuld dokumentation: [AZCopy][].

## <a name="optimizing-data-export"></a>Optimere dataeksport
Ud over at sikre, at eksporten opfylder kravene kortformat ved PolyBase kan du også forsøge at optimere eksport af data til at forbedre processen yderligere.

> [AZURE.NOTE] Som PolyBase kræver, at dataene skal være i UTF-8-format den sandsynligvis vil du bruge bcp til at udføre dataeksporten. BCP understøtter ikke skrive datafiler til UTF-8. SSIS eller ADF kopi er meget bedre egnet til at udføre denne slags eksport af data.

### <a name="data-compression"></a>Datakomprimering
PolyBase kan læse gzip komprimeret data. Hvis du er i stand til at komprimere dine data til gzip filer kan du minimere mængden data, der overføres via netværket.

### <a name="multiple-files"></a>Flere filer
Bryd store tabeller i flere filer ikke kun hjælper med at forbedre Eksportér hastighed, hjælper det også med filoverførsel re-startability og overordnede administrationen af dataene én gang i Azure blob-lager. En af de mange godt funktioner i PolyBase er, at den læser alle filer i en mappe og behandle den som en tabel. Det er derfor en god ide at isolere filer for hver tabel i sin egen mappe.

PolyBase understøtter også en funktion, kaldet "rekursiv mappe gennemgangen". Du kan bruge denne funktion til at forbedre yderligere organisationen af de eksporterede data til at forbedre din datastyring.

Hvis du vil vide mere om indlæsning af data med PolyBase skal du se [Brug PolyBase indlæse data i SQL Data Warehouse][].


## <a name="next-steps"></a>Næste trin
Du kan finde flere oplysninger om overførsel under [overførsel din løsning til SQL Data Warehouse][].
Du kan finde flere tip til udvikling, [udvikling oversigt][].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[ADF kopi]: ../data-factory/data-factory-data-movement-activities.md 
[ADF eksempler]: ../data-factory/data-factory-samples.md
[ADF Copy examples]: ../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md
[Oversigt over udvikling]: sql-data-warehouse-overview-develop.md
[Overføre din løsning til SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Bruge bcp indlæse data til SQL Data Warehouse]: sql-data-warehouse-load-with-bcp.md
[Brug PolyBase at indlæse data til SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute dokumentation]: http://azure.microsoft.com/documentation/services/expressroute/

[version af]: http://aka.ms/downloadazcopy/
[Preview-version]: http://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS dokumentation]: https://msdn.microsoft.com/library/ms141026.aspx

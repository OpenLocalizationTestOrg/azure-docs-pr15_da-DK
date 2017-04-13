<properties 
    pageTitle="DocumentDB hierarkiske ressource model og begreber | Microsoft Azure" 
    description="Få mere at vide om Documentdbs hierarkisk model af databaser, samlinger, brugerdefineret funktion (UDF), dokumenter, tilladelse til at administrere ressourcer og meget mere."
    keywords="Hierarkisk model, documentdb, azure, Microsoft azure"   
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="anhoh"/>

# <a name="documentdb-hierarchical-resource-model-and-concepts"></a>DocumentDB hierarkiske ressource model og begreber

De Databaseenheder, der administrerer DocumentDB der henvises til som **ressourcer**. Hver ressource er entydigt identificeret med en logisk URI. Du kan interagere med de ressourcer, ved hjælp af standard HTTP-verber, anmodning/svar sidehoveder og status-koder. 

Ved at læse denne artikel, vil du kunne besvare spørgsmål, der er følgende:

- Hvad er Documentdbs ressource model?
- Hvad er defineret ressourcer i modsætning til brugerdefinerede ressourcer?
- Hvordan vælger jeg en adresse en ressource?
- Hvordan arbejder jeg med af websteder?
- Hvordan arbejder jeg med lagrede procedurer, udløsere og brugerdefinerede funktioner (brugerdefinerede funktioner)?

## <a name="hierarchical-resource-model"></a>Hierarkisk ressource model
Som i det følgende diagram viser, består DocumentDB hierarkiske **ressource model** af sæt af ressourcer under en database-konto, der er hver tilgængelige via en logisk og stabil URI. Et sæt ressourcer der kaldes en **feed** i denne artikel. 

>[AZURE.NOTE] DocumentDB indeholder en meget effektiv TCP-protokol, der er også RESTful i dets kommunikation-model, der er tilgængelige via [.NET klient-SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

![DocumentDB hierarkiske ressource model][1]  
**Hierarkisk ressource model**   

Hvis du vil begynde at arbejde med ressourcer, skal du [oprette en DocumentDB database-konto](documentdb-create-account.md) ved hjælp af abonnementet Azure. En database-konto kan bestå af et sæt af **databaser**, hver indeholder flere **samlinger**, hver især tur. indeholder **lagrede procedurer, udløser, brugerdefinerede funktioner, dokumenter** og relaterede **vedhæftede filer** (preview funktion). En database er også knyttet **brugere**, hver med et sæt af **tilladelser** til at få adgang til samlinger, lagrede procedurer, udløsere, brugerdefinerede funktioner, dokumenter eller vedhæftede filer. Mens databaser, brugere, tilladelser og samlinger er defineret af systemet ressourcer med kendte skemaer, dokumenter og vedhæftede filer indeholde vilkårlig, brugerdefinerede JSON indhold.  

|Ressource   |Beskrivelse
|-----------|-----------
|Database-konto   |En database-konto er knyttet til en række databaser og en fast mængde blob-lager for vedhæftede filer (preview funktion). Du kan oprette en eller flere database-konti med dit Azure-abonnement. Få mere at vide ved at besøge vores [priser side](https://azure.microsoft.com/pricing/details/documentdb/).
|Database   |En database er en logisk beholder dokument lagerplads opdelt på tværs af websteder. Det er også en objektbeholder til brugere.
|Bruger   |Det logiske navneområde til angivelse af område tilladelser. 
|Tilladelse |En tilladelse token, der er knyttet til en bruger for at få adgang til en bestemt ressource.
|Af websteder |En samling er en beholder af JSON dokumenter og den tilknyttede JavaScript-programlogik. En samling er en fakturerbar enhed, hvor [omkostninger](documentdb-performance-levels.md) , bestemmes af niveauet ydeevne, der er knyttet til samlingen. Af websteder kan strækker sig over en eller flere partitioner/servere og kan skaleres til at håndtere næsten ubegrænset mængde lagerplads eller overførselshastighed.
|Lagret Procedure   |Programmet logik skrevet på JavaScript, som er registreret hos en af websteder og en transaktion udføres i af-databaseprogram.
|Udløser    |Programmet logik, der er skrevet i JavaScript udføres før eller efter enten en Indsæt erstatte eller slette handling.
|UDF    |Programmet logik skrevet på JavaScript. Brugerdefinerede funktioner gør det muligt at model en brugerdefineret forespørgsel operator og dermed udvide core DocumentDB forespørgselssprog.
|Dokument   |Brugerdefinerede (vilkårlig) JSON-indhold. Som standard ingen skema skal være defineret og sekundære indeks skal være knyttet til alle de dokumenter, der er føjet til en samling.
|(Preview) Vedhæftet fil   |En vedhæftet fil er et særligt dokument, der indeholder referencer og tilknyttede metadata for eksterne blob/medier. Udvikleren kan vælge at har blob administreres af DocumentDB eller gemme den med en ekstern blob-udbyder, såsom OneDrive, Dropbox osv. 


## <a name="system-vs-user-defined-resources"></a>System kontra brugerdefinerede ressourcer
Ressourcer som database konti, databaser, samlinger, brugere, tilladelser, lagrede procedurer, udløsere og brugerdefinerede funktioner - alle skal have en fast skemaet og kaldes systemressourcer. I modsætning ressourcer som dokumenter og vedhæftede filer har ingen begrænsninger på skemaet og er eksempler på brugerdefinerede ressourcer. I DocumentDB, både system og brugerdefinerede ressourcer, der er repræsenteret og administrerede som standard-kompatibel JSON. Alle de ressourcer, system eller brugerdefinerede, har følgende egenskaber.

> [AZURE.NOTE] Bemærk, at alle system genereret egenskaber i en ressource er præfikset understregningstegnet (_) i deres repræsentation af JSON.

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Egenskaben</strong></p></td>
            <td valign="top"><p><strong>Bruger angives eller genereres af systemet?</strong></p></td>
            <td valign="top"><p><strong>Formål</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Genereres af systemet</p></td>
            <td valign="top"><p>System oprettet, entydigt og hierarkiske id for ressourcen</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Genereres af systemet</p></td>
            <td valign="top"><p>etag ved en ressource, der er påkrævet for optimistisk kontrolelement</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Genereres af systemet</p></td>
            <td valign="top"><p>Sidste opdaterede tidsstempel ved en ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Genereres af systemet</p></td>
            <td valign="top"><p>Entydige tilgængelige URI ved en ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Genereres af systemet</p></td>
            <td valign="top"><p>Brugerdefinerede entydigt navn på ressourcen (med samme partition nøgleværdi). Hvis brugeren ikke angiver et id, bliver et id genereres af systemet</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Tråd repræsentation af ressourcer
DocumentDB kræver ikke en hvilken som helst beskyttede udvidelser til den JSON standard eller speciel-kodning; det fungerer med standard kompatibel JSON-dokumenter.  
 
### <a name="addressing-a-resource"></a>Vælge en adresse til en ressource
Alle de ressourcer er tilgængelige URI. Værdien af egenskaben **_self** for en ressource repræsenterer den relative URI ved en ressource. Formatet for URI består af den /\<feed\>/ {_rid} stisegmenter:  

|Værdien af _self |Beskrivelse
|-------------------|-----------
|/DBS   |Feed af databaser under en database-konto
|/DBS/ {dbName}  |Databasen med et id, der svarer til værdien {dbName}
|/DBS/ {dbName} /colls/   |Feed af af websteder under en database
|/DBS/ {dbName} /colls/ {collName} |Af websteder med et id, der svarer til værdien {collName}
|/DBS/ {dbName} /colls/ {collName} / dokumenter    |Feed af dokumenter under en samling
|/DBS/ {dbName} /colls/ {collName} /docs/ {docId}    |Dokument med et id, der svarer til værdien {doc}
|Users / /DBS/ {dbName}   |Feed af brugere under en database
|Users / /DBS/ {dbName} {bruger-id}   |Bruger med et id, der svarer til værdien {brugeren}
|Users / /DBS/ {dbName} {bruger-id} / tilladelser   |Feed af tilladelser for en bruger
|/DBS/ {dbName} / Users / {bruger-id} /permissions/ {permissionId}    |Tilladelse med et id, der svarer til værdien {tilladelse}
  
Hver ressource har et entydigt brugerdefinerede navn vises via id-egenskaben. Bemærk: for dokumenter, hvis brugeren ikke angiver et id vores understøttede SDK'er genereres automatisk et entydigt id for dokumentet. Id'et er en brugerdefineret streng med op til 256 tegn, der er entydige i konteksten af en bestemt overordnede ressource. 

Hver ressource har også et genereres af systemet hierarkiske ressource-id (også kaldet en RELATIVE), som er tilgængelig via egenskaben _rid. RELATIVE koder hele hierarkiet for en given ressource, og det er en praktisk interne repræsentation, der bruges til at gennemtvinge referentiel integritet i en fordelt måde. RELATIVE er entydigt i en database-konto, og den bruges internt af DocumentDB til effektiv routing uden cross partition opslag. Værdierne for _self og _rid egenskaber er både alternative og vedtaget repræsentationer af en ressource. 

DocumentDB REST API'er understøtter adresser i forbindelse med ressourcer og routing af anmodninger om ved både felterne id og _rid egenskaber.

## <a name="database-accounts"></a>Database konti
Du kan klargøre en eller flere DocumentDB database konti med dit Azure-abonnement.

Du kan [oprette og administrere DocumentDB database konti](documentdb-create-account.md) via Azure-portalen på [http://portal.azure.com/](https://portal.azure.com/). Oprettelse og administration af en database konto kræver administratoradgang og kan kun udføres under abonnementet Azure. 

### <a name="database-account-properties"></a>Egenskaber for konto-database
Som en del af klargøring og administrere en database-konto kan du konfigurere og læse følgende egenskaber:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Egenskabsnavn</strong></p></td>
            <td valign="top"><p><strong>Beskrivelse</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Konsistens politik</p></td>
            <td valign="top"><p>Angive denne egenskab til at konfigurere konsistens Standardniveau for alle websteder under databasekontoen. Du kan tilsidesætte niveauet konsistens på grundlag per anmodning ved hjælp af anmodning om [x-ms-konsistens-niveau] sidehovedet. <p><p>Bemærk, at denne egenskab kun gælder for den <i>brugerdefinerede ressourcer</i>. Alle system defineret ressourcer er konfigureret til at understøtte læser/forespørgsler med stærke konsistens.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Godkendelse taster</p></td>
            <td valign="top"><p>Dette er de primære og sekundære master og skrivebeskyttet taster, som giver administratoradgang til alle ressourcerne under databasekontoen.</p></td>
        </tr>
    </tbody>
</table>

Bemærk, at ud over klargøring, konfiguration og administration af databasekontoen fra Azure-portalen, kan du også automatisk oprette og administrere DocumentDB database konti ved hjælp af [Azure DocumentDB REST API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx) samt [klient SDK'er](https://msdn.microsoft.com/library/azure/dn781482.aspx).  

## <a name="databases"></a>Databaser
En DocumentDB database er en logisk beholder for en eller flere af websteder og brugere, som vist i følgende diagram. Du kan oprette et vilkårligt antal databaser under en DocumentDB database konto underlagt begrænsninger for tilbud.  

![Konto og samlinger hierarkiske databasemodel][2]  
**En Database er en logisk beholder af brugere og samlinger**

En database kan indeholde næsten ubegrænsede dokument lagerplads opdelt ved samlinger, der udgør transaktion domæner til de dokumenter, der er indeholdt i dem. 

### <a name="elastic-scale-of-a-documentdb-database"></a>Elastiske skaleringen af en DocumentDB database
En DocumentDB database er elastiske som standard – lige fra et par GB til petabytes af SSD sikkerhedskopier lagring af dokumenter og klargjort overførselshastighed. 

I modsætning til en database i traditionelle RDBMS, er en database i DocumentDB ikke fastsat til en enkelt maskine. Med DocumentDB, som dit program skala skal vokse, kan du oprette flere samlinger, databaser eller begge dele. Faktisk har forskellige første part-programmer i Microsoft brugt DocumentDB skaleres til en forbruger ved at oprette meget stor DocumentDB databaser hver med tusindvis af af websteder med TB lagring af dokumenter. Du kan Forøg eller Formindsk en database ved at tilføje eller fjerne af websteder til dit program skala behov. 

Du kan oprette et vilkårligt antal af websteder i en database underlagt tilbuddet. Hver af websteder har SSD sikkerhedskopier lager og overførselshastighed klargjort for dig, afhængigt af det valgte performance niveau.

En DocumentDB database er også en objektbeholder af brugere. En bruger, i slå, er en logisk navneområde for et sæt af tilladelser, der indeholder detaljerede autorisation og adgang til websteder, dokumenter og vedhæftede filer.  
 
Som med andre ressourcer i DocumentDB ressource modellen, databaser kan oprettes, erstattes, slettes, læse eller optælles nemt ved hjælp af [Azure DocumentDB REST API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx) , eller en af [klienten SDK'er](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB garanterer stærke konsistens for at læse eller forespørgsler metadataene i en database ressource. Slette en database automatisk sikrer, at du ikke kan få adgang til en af websteder eller brugere, der er indeholdt i den.   

## <a name="collections"></a>Af websteder
En DocumentDB af websteder er en beholder for dine JSON-dokumenter. En samling er også en måleenhed skala til transaktioner og forespørgsler. 

### <a name="elastic-ssd-backed-document-storage"></a>Elastiske SSD sikkerhedskopier lagring af dokumenter
En samling er reelt elastiske – det vokser og formindskes, når du tilføjer eller fjerner dokumenter automatisk. Samlinger er logiske ressourcer og kan strækker sig over en eller flere fysiske partitioner eller -servere. Antallet af partitioner inden for en samling bestemmes af DocumentDB baseret på lagerstørrelse og den klargjorte overførselshastighed for af websteder. Alle partitioner i DocumentDB har en fast mængde SSD-sikkerhedskopier lagerplads, der er knyttet til den, og replikeres til høj tilgængelighed. Administration af partition administreres fuldt af Azure DocumentDB, og du behøver ikke at skrive komplekse kode eller administrere partitionerne. DocumentDB af websteder er **næsten ubegrænset** med hensyn til lager og overførselshastighed. 

### <a name="automatic-indexing-of-collections"></a>Automatisk indeksering af af websteder
DocumentDB er et SAND skema ledig databasesystem. Den ikke forudsætter eller kræver et skema for JSON-dokumenter. Når du tilføjer dokumenter i en samling, DocumentDB indekserer automatisk dem, og de er tilgængelige for dig at forespørge på. Automatisk indeksering af dokumenter uden skema eller sekundære indeks er en vigtige egenskab af DocumentDB og er aktiveret ved at skrive optimeret, Lås ledig, og log strukturerede indeks vedligeholdelse teknikker. DocumentDB understøtter vedvarende lydstyrken for meget hurtigt skriver mens du stadig fungerer ensartet forespørgsler. Både dokument og indeks lagerplads bruges til at beregne opbevaring consumed ved hver af websteder. Du kan styre de storage og ydeevne kompromiser tilknyttet indeksering ved at konfigurere politikken indeksering for en samling. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Konfiguration af indeksering politikken for en samling
Politikken indeksering af hver enkelt af websteder kan du foretage ydeevne og lagerplads kompromiser der er knyttet til indeksering. Følgende indstillinger er tilgængelige for dig som en del af indeksering konfiguration:  

-   Vælg, om samlingen automatisk indekserer alle dokumenter eller ej. Alle dokumenter er som standard automatisk indekseret. Du kan vælge at deaktivere automatisk indeksering og føje selektivt kun bestemte dokumenter til indekset. Du kan derimod selektivt vælge at udelade kun bestemte dokumenter. Du kan opnå ved at angive egenskaben automatisk til at være SAND eller FALSK på indexingPolicy af en samling og bruge overskriften [x-ms-indexingdirective] anmodning under indsætning, erstatte eller slette et dokument.  
-   Vælg, om du vil medtage eller udelade bestemte stier eller mønstre i dine dokumenter fra indekset. Du kan opnå dette ved at includedPaths og excludedPaths på indexingPolicy af en samling henholdsvis. Du kan også konfigurere de storage og ydeevne kompromiser for område- og hash forespørgsler for bestemte sti mønstre. 
-   Vælge mellem synkron (ensartet) og asynkron (fløde) indeks opdateringer. Som standard opdateres indekset synkront på hver Indsæt, Erstat eller sletning af et dokument til samlingen. Dette gør det muligt for forespørgsler imødekomme konsistens samme niveau som for dokument-læser. Mens DocumentDB skriveadgang optimeret og understøtter vedvarende mængder dokument skriver sammen med synkron indeks vedligeholdelse og fungerer ensartet forespørgsler, kan du konfigurere visse af websteder for at opdatere deres indeks lazily. Fløde indeksering forstærker Skriv ydeevnen yderligere og er velegnet til flere indtagelse scenarier, hvor der primært læst tungt af websteder.

Politikken indeksering kan ændres ved at udføre en læg på gruppen af websteder. Det kan ske enten via [klienten SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) [Azure Portal](https://portal.azure.com) eller [Azure DocumentDB REST API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### <a name="querying-a-collection"></a>Forespørgsler en samling
Dokumenter i en af websteder kan have vilkårlig skemaer og kan du forespørge dokumenter i en samling uden at give et skema eller sekundære indekser forhånd. Du kan forespørge samlingen ved hjælp af [DocumentDB SQL-syntaks](https://msdn.microsoft.com/library/azure/dn782250.aspx), som giver RTF hierarkiske, relationelle, og geografiske operatorer og udvidelsesmuligheder af via JavaScript-baserede brugerdefinerede funktioner. JSON grammatik giver mulighed for modeling JSON dokumenter som træer med etiketter som noderne i træet. Dette er at udnytte både ved Documentdbs automatisk indeksering teknikker samt Documentdb's SQL-dialekten. Forespørgselssprog DocumentDB består af tre primære aspekter:   

1.  Et lille sæt forespørgslerne, der er tilknyttet den træstruktur, herunder hierarkiske forespørgsler og prognoser naturligt. 
2.  Et undersæt af relationelle handlinger, herunder sammensætning, filtrere, prognoser, standardværdier og selv joinforbindelser. 
3.  Ren JavaScript baseret på brugerdefinerede funktioner, der arbejder med (1) og (2).  

DocumentDB forespørgsel modellen forsøger at finde den rigtige balance mellem funktionalitet, effektivitet og enkel. DocumentDB-databaseprogram oprindeligt kompilerer og afvikler SQL-forespørgsel-sætninger. Du kan forespørge en samling ved hjælp af [Azure DocumentDB REST API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx) eller en af [klienten SDK'er](https://msdn.microsoft.com/library/azure/dn781482.aspx). .NET SDK leveres med en udbyder af LINQ.

> [AZURE.TIP] Du kan prøve DocumentDB og kører SQL-forespørgsler i forhold til vores datasæt i [Forespørgsel tennisklub](https://www.documentdb.com/sql/demo).

### <a name="multi-document-transactions"></a>Flere dokumenter transaktioner
Databasetransaktioner indeholder en sikker og mere forudsigelige programming model til håndtering af samtidige ændringer af dataene. I RDBMS er den traditionelle metode til at skrive forretningslogik til at skrive **lagrede procedurer** og/eller **Udløsere** og leverer den til databaseserver for transaktions udførelse af. I programmet programmer i RDBMS, der kræves for at håndtere to separate programmeringssprog: 

- Programmet (ikke-transaktions) programmeringssproget (fx JavaScript, Python, C#, Java osv.)
- T-SQL, transaktions programmeringssproget som oprindeligt er udført af databasen

I henhold til dens deep ønske at JavaScript og JSON direkte i af-databaseprogram giver DocumentDB en intuitiv programming model for at udføre JavaScript baseret programlogik direkte på samlinger med hensyn til lagrede procedurer og udløsere. Dette giver mulighed for begge af følgende:

- Effektiv implementering af på dokumentsammenfald styre, gendannelse, automatisk indeksering af JSON objektgrafer direkte i database engine
- Naturligt udtrykker kontrolflow, variable angivelse af område, tildeling og integration af undtagelse, der håndterer primitiver med databasetransaktioner direkte med hensyn til JavaScript programmeringssproget

JavaScript-logik, der er registreret hos en af websteder kan derefter udstede databasehandlinger på siden dokumenter af den angivne af websteder. DocumentDB ombrydes implicit JavaScript baseret gemt procedurer og udløsere i en omgivende SURT transaktioner med øjebliksbillede isolationsniveauet på tværs af dokumenter i en samling. I løbet af kørslen, hvis JavaScript'et medfører en undtagelse afbrydes derefter hele posteringen. Den resulterende programming model er en meget simpel endnu effektive. JavaScript udviklere få en "robust" programming model, mens du stadig bruger deres velkendte sprog konstruktioner og bibliotek primitiver.   

Muligheden for at udføre JavaScript direkte i databaseprogram i det samme adresseområde som bufferen puljen aktiverer performant og transaktions udførelse af Databasefunktioner mod dokumenterne i en af websteder. Desuden DocumentDB databaseprogram gør en deep forpligtelse til JSON og JavaScript fjerner alle impedans uoverensstemmelse mellem type systemer af programmet og databasen.   

Når du har oprettet en samling, kan du registrere lagrede procedurer, udløsere og brugerdefinerede funktioner med en af websteder ved hjælp af [Azure DocumentDB REST API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx) eller en af [klienten SDK'er](https://msdn.microsoft.com/library/azure/dn781482.aspx). Du kan referere til og udføre dem efter registreringen. Overvej følgende lagrede procedure skrevet helt på JavaScript, nedenstående kode tager to argumenter (adressekartotek navn og forfatternavn) og opretter et nyt dokument, forespørger for et dokument og opdaterer derefter den – alle i en implicit SURT transaktion. Hvis en JavaScript-undtagelse er opstået, når som helst under kørsel afbryder hele posteringen.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()
            
        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);
                
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);
                        
                        context.getResponse().setBody(matchingDocuments.length);
                       
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

Klienten kan "levere" ovenfor JavaScript logik til databasen til transaktions udførelse af via HTTP POST. Du kan finde flere oplysninger om brug af HTTP-metoder [RESTful interaktion med DocumentDB ressourcer](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Bemærk, at da databasen forstår oprindeligt JSON og JavaScript, der er ingen typeuoverensstemmelse system, ingen "Eller tilknytning" eller kode generering af magisk påkrævet.   

Lagrede procedurer og udløsere interagere med en af websteder og dokumenter i en samling via en klart defineret objektmodellen, som viser den aktuelle samling kontekst.  

Af websteder i DocumentDB kan oprettes, slettes, Læs eller optælles nemt ved hjælp af enten [Azure DocumentDB REST API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx) eller en af [klienten SDK'er](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB indeholder altid stærke konsistens for at læse eller forespørgsler metadataene i en samling. Slette en samling automatisk sikrer, at du kan ikke få adgang til alle dokumenter, vedhæftede filer, lagrede procedurer, udløsere, og brugerdefinerede funktioner, der er indeholdt i den.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Lagrede procedurer, udløsere og bruger defineret funktioner (UDF)
Som beskrevet i forrige afsnit, kan du skrive programlogik til at køre direkte i en transaktion inde i af-databaseprogram. Programmet logik kan skrives helt i JavaScript og kan dannes som en lagret procedure, udløser eller en. JavaScript-kode i en lagret procedure eller en udløser kan indsætte, erstatte, slette, som læst eller forespørgsel dokumenter i en samling. På den anden side JavaScript i en, der kan ikke indsætte, erstatte eller slette dokumenter. Brugerdefinerede funktioner Optæl dokumenter af en forespørgsel resultatsæt og frembringe et andet resultatsættet. For flere arkitekturer håndhæves DocumentDB en faste reservation baseret ressource-styring. Hver lagret procedure, udløser eller en får en fast quantum operativsystem ressourcer til at gøre arbejdet. Desuden lagrede procedurer, udløsere eller brugerdefinerede funktioner kan ikke sammenkæde mod eksterne JavaScript-biblioteker og er sortlistede, hvis de overskrider ressource budgetterne tildeles dem. Du kan registrere, unregister lagrede procedurer, udløsere eller brugerdefinerede funktioner med en samling ved hjælp af REST API'er.  Ved registrering er en lagret procedure, udløser eller en udfyldt kompileret og gemt som byte-kode, der udføres senere. Følgende afsnit viser, hvordan du kan bruge DocumentDB JavaScript SDK til at registrere, udføre og fjerne registreringen af en lagret procedure, udløser og en. JavaScript SDK er en simpel slikpapir over [DocumentDB REST API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

### <a name="registering-a-stored-procedure"></a>Registrere en lagret procedure
Registrering af en lagret procedure opretter en ny lagret procedure ressource på en af websteder via HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();
            
            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };
    
    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Udføre en lagret procedure
Udførelse af en lagret procedure gøres ved at udstede en HTTP POST mod en eksisterende lagret procedure ressource ved at overføre parametre til proceduren i anmodningsteksten.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Fjerne registreringen af en lagret procedure
Fjerne registreringen af en lagret procedure gøres blot ved at udstede en HTTP slette mod en eksisterende lagret procedure ressource.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registrere en foreløbig udløser
Registrering af en udløser er færdig med at oprette en ny udløser ressource på en af websteder via HTTP POST. Du kan angive, hvis udløseren er en pre eller en indlæg udløser og typen af handlingen det kan være knyttet til (fx Opret, Erstat, Slet eller alle).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }
    
    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Udføre en foreløbig udløser
Udførelse af en udløser gøres ved at angive navnet på en eksisterende udløser på tidspunktet for udstedelse INDLÆG/læg/slette anmodningen på en dokument via anmodning om sidehovedet.  
 
    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Fjerne registreringen af en foreløbig udløser
Fjerne registreringen af en udløser gøres blot via udstedelse af en HTTP slette mod en eksisterende udløser ressource.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registrere en
Registrering af en gøres ved at oprette en ny UDF ressource på en samling via HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Afholde et UDF som en del af forespørgslen
EN kan angives som en del af SQL-forespørgslen og bruges som en metode til at udvide core [SQL forespørgselssprog af DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Fjerne registreringen af en 
Fjerne registreringen af en gøres blot ved at udstede en HTTP slette mod en eksisterende UDF ressource.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Selvom de ovenstående kodestykker viste registrering (INDLÆG), fjernelse af registrering (læg), læse/lister (få) og udførelse af (INDLÆG) via [DocumentDB JavaScript SDK](https://github.com/Azure/azure-documentdb-js), kan du også bruge [REST API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx) eller anden [klient SDK'er](https://msdn.microsoft.com/library/azure/dn781482.aspx). 

## <a name="documents"></a>Dokumenter
Du kan indsætte, erstatte, slette, læse, Optæl og forespørgsel vilkårlig JSON-dokumenter i en samling. DocumentDB kræver ikke et skema og kræver ikke sekundære indeks for at understøtte forespørgsler over dokumenter i en samling.   

Der er en virkelig åbne database-tjeneste, DocumentDB ikke for lager en hvilken som helst specialiserede datatyper (fx dato klokkeslæt) eller bestemte kodninger for JSON dokumenter. Bemærk, at DocumentDB ikke kræver en speciel JSON konventioner med relationer mellem forskellige dokumenter SQL-syntaks for DocumentDB indeholder meget effektiv hierarkiske og relationelle forespørgsel operatorer til forespørgslen og projektrelaterede dokumenter uden nogen særlige anmærkninger eller nødvendigt med relationer mellem dokumenter ved hjælp af egenskaber, adskiller sig fra hinanden.  
 
Som med alle andre ressourcer, kan dokumenter oprettes, erstattede, slettede, læst, specificeret og forespurgte nemt ved hjælp af REST API'er, eller en af [klienten SDK'er](https://msdn.microsoft.com/library/azure/dn781482.aspx). Slette et dokument med det samme Frigør kvoten for svarer til alle de indlejrede vedhæftede filer. Niveauet Læs konsistens af dokumenter følger politikken konsistens på databasekontoen. Denne politik kan tilsidesættes på grundlag per anmodning afhængigt af dit program data konsistens krav. Når forespørgsler dokumenter, følger Læs konsistens tilstanden indeksering på gruppen af websteder. Dette følger firmaets konsistens politik for "ensartet". 

## <a name="attachments-and-media"></a>Vedhæftede filer og medier
>[AZURE.NOTE] Vedhæftet fil og medier ressourcer er funktionerne preview.
 
DocumentDB kan du gemme binære BLOB/medier, med DocumentDB eller til din egen remote media store. Det gør det også muligt at gengive metadataene i en medier med hensyn til et særligt dokument kaldet vedhæftet fil. En vedhæftet fil i DocumentDB er et særligt (JSON)-dokument, der refererer til medier/blob gemt et andet sted. En vedhæftet fil er blot et særligt dokument, der registrerer metadata (fx placering, forfatter osv.) af et medie, der er gemt i en ekstern medier lagerplads. 

Overvej at et program, sociale læse som bruger DocumentDB til at gemme håndskrevne anmærkninger, og metadata, inklusive kommentarer, fremhæves, bogmærker, bedømmelser, synes godt om/ikke bryder sig om osv., der er knyttet til en e-bog for en bestemt bruger.   

-   Indholdet af bogen selve er gemt i medier lagerplads enten tilgængelig som en del af DocumentDB database konto eller et lager til remote medier. 
-   Et program kan gemme hver enkelt brugers metadata som et entydige dokument – f.eks., Joes metadataene for Mappe1 er gemt i et dokument, der refereres til af /colls/joe/docs/book1. 
-   Vedhæftede filer, der peger på siderne indhold i en given bog for en bruger er gemt under det tilsvarende dokument fx /colls/joe/docs/book1/chapter1 /colls/joe/docs/book1/chapter2 osv. 

Vær opmærksom på, er anført ovenfor Ark6 fulde id'er til at formidle ressourcehierarkiet. Ressourcer er åbnet via REST API'er gennem entydigt ressource-id. 

For de medier, der administreres af DocumentDB, henviser egenskaben _media for den vedhæftede fil, medier af dens URI. DocumentDB sikrer til garbage indsamling medier, når alle de udestående referencer udelades. DocumentDB automatisk genererer den vedhæftede fil, når du overfører nye medier og udfylder _media til at pege på nyligt tilføjede medier. Hvis du vælger at gemme medier i et remote blob-lager, der administreres af du (fx OneDrive, Azure-lager, DropBox osv), kan du stadig bruge vedhæftede filer til at referere til medier. I dette tilfælde skal du selv oprette den vedhæftede fil og udfylde egenskaben _media.   

Som med alle andre ressourcer, vedhæftede filer kan oprettes, erstattes, slettes, Læs eller optælles nemt ved hjælp af REST API'er, eller en af klienten SDK'er. Som med dokumenter, følger niveauet Læs konsistens af vedhæftede filer politikken konsistens på databasekontoen. Denne politik kan tilsidesættes på grundlag per anmodning afhængigt af dit program data konsistens krav. Når forespørgsler for vedhæftede filer, følger Læs konsistens tilstanden indeksering på gruppen af websteder. Dette følger firmaets konsistens politik for "ensartet". 
 
## <a name="users"></a>Brugere
En DocumentDB bruger repræsenterer et logisk navneområde til gruppering af tilladelser. En DocumentDB bruger muligvis svarer til en bruger i et identitet administrationssystem eller en foruddefineret programmet rolle. For DocumentDB repræsenterer en bruger blot en fremstilling gruppere et sæt tilladelser under en database.   

Til implementering af flere arkitekturer i programmet, kan du oprette brugere i DocumentDB, som svarer til dine faktiske brugere eller lejere af dit program. Derefter kan du oprette tilladelser for en given bruger, der svarer til adgangskontrol over forskellige af websteder, dokumenter, vedhæftede filer, osv.   

Som programmerne har brug at skalere med dit bruger-forøgelse, kan du indføre forskellige måder at shard dine data. Du kan modellere hver af dine brugere på følgende måde:   

-   Hver bruger knyttes til en database.
-   Hver bruger, der er tilknyttet en samling. 
-   Dokumenter, der svarer til flere brugere gå til en dedikeret af websteder. 
-   Dokumenter, der svarer til flere brugere gå til en gruppe af websteder.   

Uanset den bestemte sharding strategi, du vælger, kan du faktisk brugerne model som brugere i DocumentDB database og knytte fint høj sikkerhed til hver enkelt bruger.  

![Bruger af websteder][3]  
**Sharding strategier og modellering brugere**

Som alle andre ressourcer, brugere i DocumentDB kan oprettes, erstattes, slettes, Læs eller optælles nemt ved hjælp af REST API'er, eller en af klienten SDK'er. DocumentDB indeholder altid stærke konsistens for at læse eller forespørgsler metadataene i en bruger ressource. Det er værd fremhæver, at slette en bruger automatisk sikrer, at du ikke kan få adgang til alle de tilladelser, der er indeholdt i den. Selvom DocumentDB genanvender efter sletning kvoten af tilladelser som en del af den slettede bruger i baggrunden, de slettede tilladelser er tilgængelige med det samme igen så du kan bruge.  

## <a name="permissions"></a>Tilladelser
Fra en access kontrolelement perspektiv ressourcer som database konti betragtes databaser, brugere og tilladelser som *administrative* ressourcer da de kræver administratorrettigheder. På den anden side er ressourcer, herunder af websteder, dokumenter, vedhæftede filer, lagrede procedurer, udløsere og brugerdefinerede funktioner fastsat under en bestemt database og betragtes som *programmet ressourcer*. Svarer til de to typer af ressourcer og de roller, få adgang til dem (nemlig administrator og bruger), godkendelse modellen definerer to typer *access-taster*: *master nøgle* og *ressource*. Master-nøgle er en del af database-konto og er angivet til udvikler (eller administrator) der klargøring af database-konto. Denne master nøgle har administratoren semantik ved, at det kan bruges til at give adgang til både administrative og program ressourcer. I modsætning er en ressourcenøgle et detaljeret adgangsnøgle, der giver adgang til en *bestemt* program ressource. Dermed, det registrerer forholdet mellem brugeren af en database og de tilladelser, brugeren har for en bestemt ressource (fx af websteder, dokument, vedhæftet fil, lagret procedure, udløser eller UDF).   

Den eneste måde at få en ressourcenøgle er ved at oprette en tilladelse ressource under en bestemt bruger. Bemærk, at kunne oprette eller hente et tilladelsesniveau, en master-nøgle skal vises i overskriften godkendelse. En tilladelse ressource binder ressourcen, dens access og brugeren. Når du har oprettet en tilladelse ressource, skal brugeren kun til at præsentere tasten tilknyttede ressourcer for at få adgang til den relevante ressource. Det vil sige, en ressourcenøgle kan ses som en logisk og kompakt repræsentation af tilladelse ressourcen.  

Som med alle andre ressourcer, tilladelser i DocumentDB kan oprettes, erstattes, slettes, Læs eller optælles nemt ved hjælp af REST API'er, eller en af klienten SDK'er. DocumentDB indeholder altid stærke konsistens for at læse eller forespørgsler metadataene i et tilladelsesniveau. 

## <a name="next-steps"></a>Næste trin
Lær mere om at arbejde med ressourcer ved hjælp af HTTP-kommandoer i [RESTful interaktioner med DocumentDB ressourcer](https://msdn.microsoft.com/library/azure/mt622086.aspx).


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png


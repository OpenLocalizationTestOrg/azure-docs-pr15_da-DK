<properties 
    pageTitle="DocumentDB programmering: lagrede procedurer, databaseudløsere og brugerdefinerede funktioner | Microsoft Azure" 
    description="Lær at bruge DocumentDB til at skrive lagrede procedurer, databaseudløsere og brugerdefinerede funktioner i JavaScript. Få database programing tip og meget mere." 
    keywords="Databaseudløsere, lagret procedure, lagret procedure, databaseprogram, sproc, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="andrl"/>

# <a name="documentdb-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>DocumentDB serversiden programmering: lagrede procedurer, databaseudløsere og brugerdefinerede funktioner

Få mere at vide, hvordan integreret Azure DocumentDB sprog, transaktions udførelse af JavaScript kan udviklere med at skrive **lagrede procedurer**, **Udløsere** og **brugerdefinerede funktioner** indbygget i JavaScript. Dette giver dig mulighed at skrive database program programmet logik, der kan blive leveret og udført direkte på database lagerplads partitioner 

Vi anbefaler, at komme i gang ved at se den følgende video, hvor Andrew Liu giver en kort introduktion til programming Documentdbs serversiden databasemodel. 

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Vend derefter tilbage til denne artikel, hvor du skal lære svar på spørgsmål, der er følgende:  

- Hvordan skriver jeg en en lagret procedure, udløser eller UDF ved hjælp af JavaScript?
- Hvordan garanterer DocumentDB sur?
- Hvordan fungerer transaktioner i DocumentDB?
- Hvad er allerede udløser og udløser efter, og hvordan skriver jeg en?
- Hvordan registrerer og udføre en lagret procedure, udløser eller UDF på en RESTful måde ved hjælp af HTTP?
- Hvad DocumentDB SDK'er er tilgængelige til at oprette og udføre lagrede procedurer, udløsere og brugerdefinerede funktioner?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introduktion til lagrede Procedure og UDF programmering

Denne metode *"JavaScript som en moderne dag T-SQL"* Frigør programmet udviklere fra lede type system uoverensstemmelser og OR tilknytning teknologier. Der er også en række indbyggede fordele, der kan anvendes for at oprette avancerede programmer:  

-   **Beskrivelse af logik:** JavaScript som en høj grad programmeringssproget, indeholder en omfattende og velkendt grænseflade til at udtrykke forretningslogik. Du kan udføre komplekse serier af handlinger tættere til dataene.

-   **Atomiske transaktioner:** DocumentDB sikrer, at databasehandlinger udføres i en enkelt lagret procedure eller udløser er atomisk. Dette betyder, at et program, kombinere relaterede handlinger i en enkelt batch, så enten dem alle lykkes eller ingen af dem lykkes. 

-   **Ydeevne:** Fakultet, JSON, reelt er knyttet til Javascript sprog Typesystemet og er også den grundlæggende enhed lager i DocumentDB giver mulighed for et antal optimeringer som fløde forsikringsbegivenhedens indtræden JSON dokumenter på bufferen puljen og gøre dem tilgængelige efter behov til at udføre kode. Der er flere fordele i ydeevne der er knyttet til forsendelse forretningslogik til databasen:
    -   Samling – udviklere kan gruppere handlinger som indsætter og sende dem ad gangen. Netværk trafik ventetid omkostninger og store omkostninger til at oprette separate transaktioner reduceres betydeligt. 
    -   Foreløbig kompilering – DocumentDB precompiles lagrede procedurer, udløsere og brugerdefinerede funktioner for at undgå JavaScript kompilering omkostninger for hver aktivering. Spild af opbygning af byte koden for den trinvise logik er amortiserede til en minimale værdi.
    -   Sekvensnummerering – mange handlinger har du brug for en side-effekt ("udløser"), der potentielt omfatter at gøre et eller flere sekundære store handlinger. Ud over atomicitet er dette flere performant, når de flyttes til serveren. 
-   **Indkapsling:** Lagrede procedurer kan bruges til at gruppere forretningslogik på ét sted. Dette giver dig to fordele:
    -   Det føjer en fremstilling lag oven på rækkedata, som gør det muligt data arkitekterne mulighed for at udvikle deres programmer uafhængigt af dataene. Dette er særligt fordel, når dataene er skema mindre på grund af de skør forudsætninger, der kan være nødvendigt at være bagt i programmet, hvis de skal håndtere data direkte.  
    -   Denne fremstilling kan virksomheder beskytte deres data ved at strømline adgang fra scriptene.  

Oprettelse og udførelse af databaseudløsere, lagrede procedure og brugerdefineret forespørgsel operatorer understøttes via [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx), [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)og [klienten SDK'er](documentdb-sdk-dotnet.md) i mange platforme, herunder .NET, Node.js og JavaScript.

**Dette selvstudium bruger [Node.js SDK med Q bekræfter](http://azure.github.io/azure-documentdb-node-q/) ** til at illustrere syntaksen for og brugen af lagrede procedurer, udløsere og brugerdefinerede funktioner.   

## <a name="stored-procedures"></a>Lagrede procedurer

### <a name="example-write-a-simple-stored-procedure"></a>Eksempel: Skrive en simpel lagret procedure 
Lad os starte med en enkelt lagret procedure, der returnerer en "Hej verden"-svar.

    var helloWorldStoredProc = {
        id: "helloWorld",
        body: function () {
            var context = getContext();
            var response = context.getResponse();
    
            response.setBody("Hello, World");
        }
    }


Lagrede procedurer er registreret per af websteder, og kan bruges på en hvilken som helst dokument og vedhæftede filer i af websteder. Følgende kodestykke viser, hvordan du registrere helloWorld gemt procedure med en samling. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Når den lagrede procedure er registreret, kan vi køre den op mod samlingen, og Læs resultater tilbage på klienten. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


Objektet kontekst giver adgang til alle handlinger, der kan udføres på DocumentDB lager og adgang til og svar objekterne. I dette tilfælde brugte vi response-objektet til at angive meddelelsens det svar, der blev sendt tilbage til klienten. Få mere at vide ved at referere til [DocumentDB JavaScript server SDK dokumentation](http://azure.github.io/azure-documentdb-js-server/).  

Lad os Udvid i dette eksempel og tilføje mere Webdatabase relaterede funktioner til den lagrede procedure. Lagrede procedurer kan oprette, opdatere, læse, forespørgsler og slette dokumenter og vedhæftede filer i gruppen af websteder.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Eksempel: Skrive en lagret procedure for at oprette et dokument 
Den næste kodestykke viser, hvordan du bruger objektet kontekst til at interagere med DocumentDB ressourcer.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        body: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Denne lagrede procedure fører som input documentToCreate, brødteksten i et dokument, der oprettes i den aktuelle websteder. Alle disse handlinger er asynkron og afhænger af JavaScript funktionen tilbagekald. Tilbagekaldsfunktionen har to parametre, én for fejlobjektet i tilfælde handlingen mislykkes, og én til oprettet objektet. I tilbagekald, kan brugere enten håndtere undtagelsen eller Udløs en fejl. I tilfælde af et tilbagekald ikke har angivet, og der er en fejl, opstår der en fejl i DocumentDB runtime.   

I eksemplet ovenfor opstår der en fejl på tilbagekald, hvis handlingen mislykkedes. Ellers skal angives id'et for det oprettede dokument som brødteksten i svar til klienten. Her er, hvordan denne lagrede procedure skal udføres med inputparametre.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
    
            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };
    
            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });

    
Bemærk, at denne lagrede procedure kan ændres, for at få en matrix med dokument organer som input, og oprette dem alle i den samme lagrede procedure kørsel i stedet for flere netværk anmodninger om at oprette hver af dem enkeltvis. Dette kan bruges til at implementere en effektiv flere importer for DocumentDB (gennemgås senere i dette selvstudium).   

Eksemplet beskrevet se, hvordan du bruger lagrede procedurer. Vi kommer ind udløsere og brugerdefinerede funktioner senere i selvstudiet.

## <a name="database-program-transactions"></a>Databasetransaktioner program
Transaktion i en typisk database kan defineres som en sekvens af handlinger, der udføres som en enkelt logisk enhed for arbejde. Hver transaktion indeholder **sur garanterer**. SUR er et kendt forkortelse, der står for fire egenskaber - stedet atomicitet, konsistens, Isolation og varighed.  

En kort, atomicitet garanterer, at alle arbejde i en transaktion behandles som en enkelt enhed hvor enten hele er anvendt eller ingen. Konsistens sikrer, at dataene altid er i en god interne tilstand på tværs af transaktioner. Isolationsniveauet garanterer, at ingen to transaktioner forstyrre hinanden – Generelt, og de fleste kommercielle systemer giver flere isolationsniveauet niveauer, der kan bruges baseret på programmet skal. Holdbarhed sikrer, at enhver ændring, der er anvendt i databasen altid være Præsenter.   

I DocumentDB, er hostet JavaScript i det samme hukommelse mellemrum som databasen. Det vil sige, anmodninger, der er foretaget i lagrede procedurer og udløsere udføre i samme omfanget af en databasesession. Dette gør det muligt for DocumentDB at garantere sur for alle handlinger, der er en del af en enkelt lagrede procedure/udløser. Overvej følgende lagret procedure definitionen:

    // JavaScript source code
    var exchangeItemsSproc = {
        name: "exchangeItems",
        body: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            var player1Document, player2Document;
    
            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);
    
                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];
    
                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });
    
            if (!accept) throw "Unable to read player details, abort ";
    
            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;
    
                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";
    
                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });
    
                        if (!accept2) throw "Unable to update player 2, abort";
                    });
    
                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }
    
    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Denne lagrede procedure bruger transaktioner inden for en gaming app til handel elementer mellem to spillere ad gangen. Den lagrede procedure forsøger at læse to dokumenter, der hver svarer til player-id'erne givet som argument. Hvis begge player dokumenter findes, opdaterer den lagrede procedure dokumenterne ved Skift deres elementer. Hvis der opstår fejl undervejs, viser en JavaScript-undtagelser, implicit afbryder posteringen.

Hvis samlingen den lagrede procedure er registreret mod er en enkelt partition af websteder og derefter posteringen er fastsat til alle docuemnts i gruppen af websteder. Hvis samlingen er opdelt, udføres lagrede procedurer i transaction omfanget af en enkelt Partitionsnøgle. Hver lagrede procedure udførelse af derefter må indeholde en partition nøgleværdi svarer til det område, som posteringen skal køre under. Yderligere oplysninger finder du [DocumentDB partitionering](documentdb-partition-data.md).

### <a name="commit-and-rollback"></a>Allokering og annullering
Transaktioner er meget og indbygget integreret i Documentdb's JavaScript programming model. I en JavaScript-funktion, er alle handlinger automatisk ombrydes under en enkelt transaktion. Hvis JavaScript'et afslutter uden undtagelser, er handlinger til databasen anvendt. "BEGYNDER transaktion" og "Udfør transaktion"-sætninger i relationsdatabaser er i kraft implicit i DocumentDB.  
 
Hvis der er de undtagelser, der er overført fra scriptet, vil Documentdb's JavaScript runtime gendanne hele posteringen. Som vist i det tidligere eksempel, er opstod en undtagelse effektivt svarer til en "ROLLBACK transaktion" i DocumentDB.
 
### <a name="data-consistency"></a>Data konsistens
Lagrede procedurer og udløsere udføres altid på den primære kopi af samlingen DocumentDB. Dette sikrer, at læser fra i gemt procedurer tilbud stærke konsistens. Forespørgsler med brugerdefinerede funktioner kan udføres på primært eller en sekundær replika, men vi sikre for at opfylde niveauet anmodede konsistens ved at vælge den relevante replika.

## <a name="bounded-execution"></a>Afgrænset udførelse af
Alle DocumentDB handlinger skal fuldføres inden for den angivne server anmode om timeout varighed. Denne begrænsning gælder også for JavaScript-funktioner (lagrede procedurer, udløsere og brugerdefinerede funktioner). Hvis handlingen ikke udføres med denne gang grænse, annulleres posteringen. JavaScript-funktioner skal fuldføres inden for det tidsrum, der er eller implementere en fortsættelse baseret model for at batchen/CV udførelse af.  

For at forenkle udvikling af lagrede procedurer og udløsere til at håndtere tidsgrænser, returnerer alle funktioner under objektet af websteder (for at oprette, læse, Erstat og sletning af dokumenter og vedhæftede filer) en boolesk værdi, der repræsenterer om denne handling fuldfører. Hvis denne værdi er falsk, er det en angivelse, er tidsrum, der er ved at udløbe og, at proceduren skal afrunder udførelse af.  Handlinger i kø forud for den første ikke-accepteret store operation er garanti for at udføre, hvis den lagrede procedure fuldfører tidspunkt og ikke kø flere forespørgsler.  

JavaScript-funktioner er også afgrænset på Ressourceforbrug. DocumentDB forbeholder sig overførselshastighed per af websteder, der er baseret på klargjort størrelsen på en database-konto. Overførselshastighed udtrykkes med hensyn til en standardiseret måleenhed CPU, hukommelse og EY forbrug kaldet anmodning enheder eller RUs. JavaScript-funktioner kan potentielt brug af et stort antal RUs inden for kort tid og får måske rente-begrænset, hvis den samling grænsen er nået. Ressource intensivt lagrede procedurer kan også være sat i karantæne for at sikre tilgængeligheden af enkle databasehandlinger.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Eksempel: Flere importere data til et databaseprogram
Nedenfor er et eksempel på en lagret procedure, der er skrevet til masseimport dokumenter til en samling. Bemærk, hvordan den lagrede procedure håndterer afgrænset udførelse af ved at markere booleske returværdi fra createDocument, og derefter bruger antallet af dokumenter, der er indsat i hver aktivering af den lagrede procedure til at registrere og genoptage status på tværs af navne.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();
    
        // The count of imported docs, also used as current doc index.
        var count = 0;
    
        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");
    
        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }
    
        // Call the create API to create a document.
        tryCreate(docs[count], callback);
    
        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);
    
            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }
    
        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;
    
            // One more document has been inserted, increment the count.
            count++;
    
            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a>Databaseudløsere
### <a name="database-pre-triggers"></a>Før databaseudløsere
DocumentDB indeholder udløsere, der er udført eller som en handling på et dokument. For eksempel kan du angive en foreløbig udløser, når du opretter et dokument – denne foreløbig udløser, køres før dokumentet er oprettet. Følgende er et eksempel på hvordan før udløsere kan bruges til at validere egenskaberne for et dokument, der skal oprettes:

    var validateDocumentContentsTrigger = {
        name: "validateDocumentContents",
        body: function validate() {
            var context = getContext();
            var request = context.getRequest();
    
            // document to be created in the current operation
            var documentToCreate = request.getBody();
    
            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }
    
            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


Og den tilsvarende Node.js klientside registreringskode for udløseren:

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };
    
            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };
    
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Foreløbig udløsere kan ikke have eventuelle inputparametre. Request-objektet kan bruges til at manipulere anmodningsmeddelelsen der er knyttet til handlingen. Her, der køres før udløser med oprettelse af et dokument, og meddelelsen anmodningsteksten indeholder dokumentet, der skal oprettes i JSON-formatet.   

Når udløsere er registreret, kan brugerne angive de handlinger, den kan køre med. Denne udløser blev oprettet med TriggerOperation.Create, hvilket betyder, at følgende ikke er tilladt.

    var options = { preTriggerInclude: "validateDocumentContents" };
    
    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });
    
    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Efter databaseudløsere
Efter udløsere, som foreløbig udløsere, som er knyttet til en handling på et dokument og tage ikke eventuelle inputparametre. De køre, **Når** handlingen er fuldført, og har adgang til svarmeddelelsen, der sendes til klienten.   

I følgende eksempel vises efter udløsere i praksis:

    var updateMetadataTrigger = {
        name: "updateMetadata",
        body: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            // document that was created
            var createdDocument = response.getBody();
    
            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";
     
            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';
                         
                         var metadataDocument = documents[0];
                         
                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                           
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


Udløseren kan registreres, som vist i følgende eksempel.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };
    
            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };
        
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Denne udløser forespørgsler til Metadatadokumentet og opdaterer med oplysninger om det nye dokument.  

Én ting, der er vigtigt at være opmærksom på, er **transaktions** udførelse af udløsere i DocumentDB. Denne efter udløser kører som en del af den samme transaktion som oprettelse af det oprindelige dokument. Derfor, hvis vi Udløs en undtagelse fra efter udløser (f.eks. Hvis det er ikke muligt at opdatere Metadatadokumentet), hele posteringen mislykkes og annulleres. Noget dokument oprettes, og der returneres en undtagelse.  

##<a id="udf"></a>Brugerdefinerede funktioner
Brugerdefinerede funktioner, der bruges til at udvide DocumentDB SQL-forespørgsel sprog grammatik og implementere brugerdefineret forretningslogik. De kan kun kaldes fra i forespørgsler. De har ikke adgang til objektet kontekst og er beregnet til at bruges som kun Beregn JavaScript. Brugerdefinerede funktioner kan derfor køres på sekundær kopier af tjenesten DocumentDB.  
 
I følgende eksempel opretter en UDF for at beregne skat baseret på satser for forskellige indtægter parenteser og derefter kan bruges i en forespørgsel til at finde alle personer, der betales mere end DKK 20.000 i moms.

    var taxUdf = {
        name: "tax",
        body: function tax(income) {
    
            if(income == undefined) 
                throw 'no input';
    
            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


Udf-filen kan derefter bruges i forespørgsler som i følgende eksempel:

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);
    
            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>JavaScript sprog-integreret forespørgsel API
Ud over udsteder forespørgsler med Documentdb's SQL-grammatik, kan serversiden SDK du udføre optimerede forespørgsler ved hjælp af en fluent JavaScript-grænseflade uden kendskab til SQL. Forespørgslen JavaScript API gør det muligt at oprette forespørgsler fra et program ved at overføre prædikat funktioner til chainable funktionen opkald med en velkendt for ECMAScript5's matrix indbyggede funktioner og populære JavaScript-biblioteker som lodash syntaks. Forespørgsler parses ved at JavaScript runtime skal udføres effektivt ved hjælp af Documentdbs indeks.

> [AZURE.NOTE]`__` (dobbelt-understregningstegn) er et alias til `getContext().getCollection()`.
> <br/>
> Med andre ord, du kan bruge `__` eller `getContext().getCollection()` at få adgang til forespørgslen JavaScript API.

Understøttede funktioner omfatter:
<ul>
<li>
<b>... Chain(). værdi ([tilbagekald] [, indstillinger])</b>
<ul>
<li>
Starter et sammenkædet opkald, skal være afsluttet med value().
</li>
</ul>
</li>
<li>
<b>filter (predicateFunction [, indstillinger] [, tilbagekald])</b>
<ul>
<li>
Filtrerer input ved hjælp af en prædikat funktion, der returnerer SAND/FALSK for at filtrere ind/ud-input dokumenter i det resulterende sæt. Dette fungerer ligner en WHERE-delsætning i SQL.
</li>
</ul>
</li>
<li>
<b>kort (transformationFunction [, indstillinger] [, tilbagekald])</b>
<ul>
<li>
Anvender en projicering, der er angivet en transformation funktion, som er tilknyttet en JavaScript objekt eller en værdi hvert input element. Dette fungerer ligner en SELECT-delsætning i SQL.
</li>
</ul>
</li>
<li>
<b>pluck ([egenskabsnavn] [, indstillinger] [, tilbagekald])</b>
<ul>
<li>
Dette er en genvej til et kort, som henter værdien af en enkelt egenskab fra hver input element.
</li>
</ul>
</li>
<li>
<b>udjævne ([isShallow] [, indstillinger] [, tilbagekald])</b>
<ul>
<li>
Kombinerer og samkopieres matrixer fra hver input element i til et enkelt system. Dette fungerer ligner SelectMany i LINQ.
</li>
</ul>
</li>
<li>
<b>menuen Sorter efter ([prædikat] [, indstillinger] [, tilbagekald])</b>
<ul>
<li>
Oprette et nyt sæt af dokumenter ved at sortere dokumenterne i input dokumentstrømmen i stigende rækkefølge ved hjælp af det angivne prædikat. Dette fungerer ligner delsætningen ORDER BY i SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([prædikat] [, indstillinger] [, tilbagekald])</b>
<ul>
<li>
Oprette et nyt sæt af dokumenter ved at sortere dokumenterne i input dokumentstrømmen i faldende rækkefølge ved hjælp af det angivne prædikat. Dette fungerer ligner delsætningen ORDER BY x DESC i SQL.
</li>
</ul>
</li>
</ul>


Når medtages i prædikat og/eller Datavælger funktioner, optimeret følgende JavaScript skemakonstruktioner få automatisk for at køre direkte på DocumentDB indeks:

* Simple operatorer: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Konstanter, herunder objektet ordret: {}
* varians, ENTER

Følgende JavaScript skemakonstruktioner ikke få optimeret til DocumentDB indeks:

* Kontrolflow (f.eks. Hvis til, mens)
* Funktionskald

Du kan finde flere oplysninger, skal du se vores [Serversiden JSDocs](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Eksempel: Skrive en lagret procedure ved hjælp af forespørgslen JavaScript API

Følgende eksempel-kode er et eksempel på hvordan JavaScript forespørgsel API kan bruges i forbindelse med en lagret procedure. Den lagrede procedure indsætter et dokument, fået en Inputparameteren og opdaterer en metadata dokument, ved hjælp af den `__.filter()` metode med minSize, maxSize og totalSize baseret på input dokumentets størrelse egenskab.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>SQL til Javascript snyde ark
Den følgende tabel viser forskellige SQL-forespørgsler og de tilsvarende JavaScript-forespørgsler.

Som med SQL-forespørgsler, dokument egenskaben taster (fx `doc.id`) er store og små bogstaver.

<br/>
<table border="1" width="100%">
<colgroup>
<col span="1" style="width: 40%;">
<col span="1" style="width: 40%;">
<col span="1" style="width: 20%;">
</colgroup>
<tbody>
<tr>
<th>SQL</th>
<th>JavaScript forespørgsel API</th>
<th>Detaljer</th>
</tr>
<tr>
<td>
<pre>
Vælg * fra dokumenter
</pre>
</td>
<td>
<pre>
__.Map(Function(doc) {returnerede dokument;});
</pre>
</td>
<td>Resultater i alle dokumenter (paginated med fortsættelse token), som er.</td>
</tr>
<tr>
<td>
<pre>
Vælg docs.id, docs.message som meddelelse, docs.actions fra dokumenter
</pre>
</td>
<td>
<pre>
__.Map(Function(doc) {returnere {id: doc.id, meddelelse: doc.message, handlinger: doc.actions};});
</pre>
</td>
<td>Projicerer-id, meddelelse (aliasser til meddelelse) og handling fra alle dokumenter.</td>
</tr>
<tr>
<td>
<pre>
Vælg * fra dokumenter hvor docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.filter(Function(doc) {returnere doc.id === "X998_Y998"});
</pre>
</td>
<td>Forespørgsler til dokumenter med prædikatet: id = "X998_Y998".</td>
</tr>
<tr>
<td>
<pre>
Vælg * fra dokumenter hvor ARRAY_CONTAINS(docs. Mærker, 123)
</pre>
</td>
<td>
<pre>
__.filter(Function(x) {returnere x.Tags & & x.Tags.indexOf(123) > -1;});
</pre>
</td>
<td>Forespørgsler til dokumenter, der har en egenskaben i-mærker og de mærker, er en matrix, der indeholder værdien 123.</td>
</tr>
<tr>
<td>
<pre>
Vælg docs.id, docs.message som meddelelse fra dokumenter hvor docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {returnere doc.id === "X998_Y998"}) .map(function(doc) {returnere {id: doc.id, meddelelse: doc.message};}) .value();
</pre>
</td>
<td>Forespørgsler til dokumenter med et prædikat id = "X998_Y998", og klik derefter projekter-id og meddelelse (aliasser til meddelelse).</td>
</tr>
<tr>
<td>
<pre>
Vælg en værdi mærke fra dokumenter Deltag i mærke dokumenter. Mærker ORDER BY docs._ts
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {returnerede dokument. Mærker & & Array.isArray (dokument. Koder), f.eks. }) .sortBy(function(doc) {returnerede doc._ts;}) .pluck("tags") .flatten() .value()
</pre>
</td>
<td>Filtre for dokumenter, som har en matrixegenskab, mærker, og sorterer de resulterende dokumenter efter egenskaben _ts tidsstempel system, og derefter projekter + samkopieres matrixen mærker.</td>
</tr>
</tbody>
</table>

## <a name="runtime-support"></a>Runtime support
[DocumentDB JavaScript serversiden SDK](http://azure.github.io/azure-documentdb-js-server/) indeholder understøttelse af de fleste af de almindelige JavaScript-sprogfunktioner som standardiseret ved [ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Sikkerhed
JavaScript lagrede procedurer og udløsere er sandkassekode, så effekterne i et script ikke trænger til en anden uden at gå gennem øjebliksbillede transaktion isolationsniveauet på niveauet for databasen. Runtime-miljøer er grupperede men ryddet af konteksten efter hver Kør. De er derfor garanti for at være sikker af utilsigtede side effekter fra hinanden.

### <a name="pre-compilation"></a>Foreløbig kompilering
Lagrede procedurer, udløsere og brugerdefinerede funktioner er implicit forudkompileret i formatet byte-kode for at undgå kompilering omkostninger på tidspunktet for hver script aktivering. Dette sikrer starter af lagrede procedurer er hurtig og har en lav miljøet.

## <a name="client-sdk-support"></a>Understøttelse af SDK-klient
Ud over klienten [Node.js](documentdb-sdk-node.md) understøtter DocumentDB [.NET](documentdb-sdk-dotnet.md), [Java](documentdb-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/)og [Python SDK'er](documentdb-sdk-python.md). Lagrede procedurer, udløsere og brugerdefinerede funktioner kan oprettes og udføres ved hjælp af disse SDK'er samt. I følgende eksempel viser, hvordan du opretter og udføre en lagret procedure ved hjælp af .NET-klienten. Bemærk hvordan .NET-datatyper overføres til den lagrede procedure som JSON og læses.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    
    
                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }
    
                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
            }"
    };
    
    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;
    
    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "sproc"), document, 1920);


Dette eksempel viser, hvordan du bruger [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx) til at oprette en foreløbig udløser og oprette et dokument med udløser aktiveret. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };
    
    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


Og i følgende eksempel viser, hvordan du opretter en brugerdefineret funktion (UDF) og bruge det i en [DocumentDB SQL-forespørgsel](documentdb-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };
    
    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>REST-API
Alle DocumentDB handlinger kan udføres på en RESTful måde. Lagrede procedurer, udløsere og brugerdefinerede funktioner kan registreres under en samling ved hjælp af HTTP POST. Følgende er et eksempel på, hvordan du kan registrere en lagret procedure:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    
    
    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


Den lagrede procedure er registreret ved at udføre en POST-anmodning mod URI dbs/testdb/colls/testColl/sprocs med dokumentets brødtekst, der indeholder den lagrede procedure til at oprette. Udløsere og brugerdefinerede funktioner kan registreres på samme måde ved at udstede et INDLÆG mod/udløsere og /udfs henholdsvis.
Dette gemt procedure kan derefter udføres ved at udstede en POST-anmodning mod dens ressource link:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
    
    
    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Her kan sendes input til den lagrede procedure i anmodningsteksten. Bemærk, at der overføres input som en JSON matrix af inputparametre. Den lagrede procedure fører det første input som et dokument, der er et svar brødtekst. Vi modtager svaret er som følger:

    HTTP/1.1 200 OK
     
    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Udløsere, i modsætning til lagrede procedurer, kan ikke udføres direkte. I stedet for udføres de som en del af en handling på et dokument. Vi kan angive udløsere til at køre med en anmodning om ved hjælp af HTTP-headere. Følgende er anmodning om at oprette et dokument.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
    
    
    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Her angives den foreløbige udløser til at blive kørt med anmodningen i x-ms-documentdb-pre-trigger-include sidehovedet. Tilsvarende, eventuelle efter udløsere er givet i x-ms-documentdb-post-trigger-include sidehovedet. Bemærk, at både før og efter udløsere kan angives for en given anmodning.

## <a name="sample-code"></a>Eksempel på kode

Du kan finde flere serversiden kodeeksempler (herunder [flere Slet](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js), og [Opdater](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) på vores [Github lager](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Vil du dele din telefonbog lagret procedure? Skal du sende os en hente-anmodning! 

## <a name="next-steps"></a>Næste trin

Når du har en eller flere lagrede procedurer, udløsere og brugerdefinerede funktioner, der er oprettet, kan du indlæse dem og få dem vist i portalen Azure med Script Stifinder. Yderligere oplysninger finder du under [visning gemt procedurer, udløsere, og brugerdefinerede funktioner med DocumentDB Script Stifinder](documentdb-view-scripts.md).

Du kan også finde følgende referencer og ressourcer, der er nyttig i din sti mere at vide om DocumentDB serversiden programmering:

- [Azure DocumentDB SDK'er](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
- [JSON](http://www.json.org/) 
- [JavaScript ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
- [JavaScript-JSON type system](http://www.json.org/js.html) 
- [Sikre og bærbare Database udvidelsesmuligheder](http://dl.acm.org/citation.cfm?id=276339) 
- [Tjenesten et overblik over Database arkitektur](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
- [Vært .NET Runtime i Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)

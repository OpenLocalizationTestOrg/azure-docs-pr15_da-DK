##<a name="create-client"></a>Oprette en klientforbindelse

Oprette en klientforbindelse ved at oprette en `WindowsAzure.MobileServiceClient` objekt.  Erstatte `appUrl` med URL-adressen til dit-Mobilappen.

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

##<a name="table-reference"></a>Arbejde med tabeller

Til access eller opdatere data, oprette en reference til back end-tabellen. Erstatte `tableName` med navnet på tabellen

```
var table = client.getTable(tableName);
```

Når du har en tabelreference, kan du arbejde yderligere med din tabel:

* [Forespørgsler til en tabel](#querying)
  * [Filtrering af Data](#table-filter)
  * [Sideopdeling gennem Data](#table-paging)
  * [Sortering af Data](#sorting-data)
* [Indsætte Data](#inserting)
* [Redigering af Data](#modifying)
* [Slette Data](#deleting)

###<a name="querying"></a>Sådan: forespørgsel en tabelreference

Når du har en tabelreference, kan du bruge det til at hente data på serveren.  Forespørgsler er foretaget i et "LINQ-synes godt om" sprog.
Brug følgende til at returnere alle data fra tabellen:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Funktionen succes kaldes med resultaterne.   Brug ikke `for (var i in results)` i en vellykket fungerer som, der gentages over oplysninger, der er medtaget i resultaterne, når andre forespørgsel-funktioner (f.eks `.includeTotalCount()`) bruges.

Flere oplysninger om forespørgselssyntaksen, du [forespørgsel objekt dokumentationen].

####<a name="table-filter"></a>Filtrering af Data på serveren

Du kan bruge en `where` delsætningen i tabelreferencen til:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Du kan også bruge en funktion, der filtrerer på objektet.  I dette tilfælde den `this` variabel er tildelt til det aktuelle objekt, der filtreres.  Følgende er funktionelt svarer til det foregående eksempel:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

####<a name="table-paging"></a>Sideopdeling gennem data

Anvende metoderne take() og skip().  For eksempel, hvis du vil opdele tabellen i 100 række poster:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Den `.includeTotalCount()` metode bruges til at føje et totalCount felt til objektet resultater.  Feltet totalCount udfyldes med det samlede antal poster, der skal returneres, hvis ingen sideopdeling ikke bruges.

Du kan derefter bruge variablen sider og nogle UI-knapper til at levere en sideliste; Brug loadPage() til at indlæse de nye poster for hver side.  Du skal implementere en slags cachelagring til hurtig adgang med poster, der allerede er indlæst.


####<a name="sorting-data"></a>Sådan: returnerer data, der er sorteret

Bruge metoderne .orderBy() eller .orderByDescending() forespørgsel:

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Flere oplysninger om objektet forespørgsel, du [forespørgsel objekt dokumentationen].

###<a name="inserting"></a>Sådan: indsætte Data

Oprette et JavaScript-objekt med den relevante dato, og kald table.insert() asynkront:

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

På vellykket indsættelse returneres det indsatte element med de ekstra felter, der kræves til synkronisering af handlinger.  Disse oplysninger til efterfølgende opdateringer skal du opdatere din egen cache.

Bemærk, at Azure Mobile Apps Node.js Server SDK understøtter dynamiske skema til udviklingsformål.
I forbindelse med dynamisk skema opdateres skemaet for tabellen på farten, så du kan føje kolonner til tabellen blot ved at angive dem i en indsætte eller opdatere operation.  Vi anbefaler, at du deaktiverer dynamisk skema før du flytter dit program til fremstilling.

###<a name="modifying"></a>Sådan: ændre Data

Svarer til metoden .insert(), skal du oprette en Opdater objekt og derefter ringe .update().  Opdater objekt skal indeholde ID'ET for posten, der skal opdateres – denne fås, når du læser posten, eller når du ringer til .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

###<a name="deleting"></a>Sådan: slette Data

Kalde metoden .del() for at slette en post.  Overføre ID'ET i et objektreference:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

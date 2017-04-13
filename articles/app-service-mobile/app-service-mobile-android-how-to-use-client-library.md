<properties
    pageTitle="Hvordan du bruger Android Mobile Apps klient biblioteket"
    description="Hvordan du bruger Android klient-SDK til Azure Mobile-Apps."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>


# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>Hvordan du bruger biblioteket Android klient til Mobile-Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Denne vejledning viser, hvordan du bruger Android klienten SDK til Mobile-Apps til at implementere almindelige scenarier, f.eks.:

- Forespørgsler til data (indsætte, opdatere og slette).
- Godkendelse.
- Håndtering af fejl.
- Tilpasning af klienten. 

Det betyder også en gå i dybden til almindelige klient-kode, der bruges i de fleste bærbare apps.

Denne vejledning fokuserer på klientsiden Android SDK.  Hvis du vil vide mere om serversiden SDK'er til Mobile-Apps skal du se [arbejde med .NET back end-SDK] [ 10] eller [hvordan du bruger Node.js back-end SDK][11].

## <a name="reference-documentation"></a>Referencedokumentation

Du kan finde [Javadocs API reference] [ 12] for biblioteket Android-klienten på GitHub.

## <a name="supported-platforms"></a>Understøttede platforme

Azure Mobile Apps Android SDK understøtter API niveauer 19 til 24 (kitkat-operativsystem gennem Nougat).  

"Server-flow" godkendelse bruger en webvisning for præsenteret Brugergrænsefladen. Hvis enheden ikke er stand til at præsentere en WebView UI, derefter andre metoder til godkendelse er det er nødvendigt, der er omfattet af produktet.  Dette SDK er ikke egnet til type af værdikontrol eller på samme måde begrænset enheder.

## <a name="setup-and-prerequisites"></a>Installation og forudsætninger

Udføre selvstudium i [Mobile-Apps Hurtig start](app-service-mobile-android-get-started.md) .  Denne opgave sikrer, at alle forudsætninger til udvikling af Azure Mobile Apps er opfyldt.  På Hurtig start også hjælper dig med at konfigurere din konto og oprette din første Mobile-App backend-version.

Hvis du beslutter dig for ikke at fuldføre Hurtig start selvstudiet, kan du udføre følgende opgaver:

- [oprette en Mobile-App back-end] [ 13] til brug med din Android-app.
- I Android Studio, [opdatere Gradle build filer](#gradle-build).
- [Aktivere internet tilladelse](#enable-internet).

###<a name="gradle-build"></a>Opdatere filen Gradle build

Ændre begge **build.gradle** filer:

1. Tilføje denne kode til *niveau **build.gradle** projektfilen i mærket *buildscript* * :

        buildscript {
            repositories {
                jcenter()
            }
        }

2. Tilføje denne kode i filen *modul app* niveau **build.gradle** i mærket *afhængigheder* :

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    Den nyeste version er i øjeblikket 3.1.0. Understøttede versioner er angivet [her][14].

###<a name="enable-internet"></a>Aktivere internet tilladelse

For at få adgang til Azure, skal din app have tilladelsen INTERNET aktiveret. Hvis den ikke allerede er aktiveret, kan du tilføje følgende linje af kode til filen **AndroidManifest.xml** :

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>Den grundlæggende undersøgelse

Dette afsnit beskrives nogle af kode i Hurtig start app, der vedrører bruge Azure Mobile-Apps.  

###<a name="data-object"></a>Definere klient dataklasser

Definere klient dataklasser, der svarer til tabellerne i Mobile-App back-end for at få adgang til data fra SQL Azure-tabeller. Eksemplerne i dette emne forudsætter en tabel med navnet **ToDoItem**, som har følgende kolonner:

- id
- tekst
- fuldføre

Den tilhørende skrevet klientsiden objekt:

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

Koden er placeret i en fil kaldet **ToDoItem.java**.

Hvis din SQL Azure-tabel indeholder flere kolonner, skal du tilføje de tilsvarende felter til denne klasse.  Eksempelvis hvis DTO (data filoverførsel objekt) havde kolonnen heltal prioritet, og du kan tilføje dette felt sammen med dens getter og opstillertid metoder:

    private Integer priority;

    /**
    * Returns the item priority
    */
    public Integer getPriority() {
        return mPriority;
    }
    
    /**
    * Sets the item priority
    *
    * @param priority
    *            priority to set
    */
    public final void setPriority(Integer priority) {
        mPriority = priority;
    }

Hvis du vil lære at oprette flere tabeller i din Mobile-Apps backend-version, skal du se [Sådan: definere en tabel controller] [ 15] (.NET backend-version) eller [definere tabeller ved hjælp af et dynamisk skema] [ 16] (Node.js backend-version). For en Node.js back-end, kan du også bruge indstillingen **nemt tabeller** i [Azure-portalen].

###<a name="create-client"></a>Sådan: oprette konteksten klient

Denne kode opretter objektet **MobileServiceClient** , der bruges til at få adgang til din Mobile-App backend-version. Koden vises i den `onCreate` metode til at klassen **aktivitet** , der er angivet i *AndroidManifest.xml* som en **hoved** handling og **STARTEREN** kategori. I Hurtig start-kode går det i filen **ToDoActivity.java** .

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

I denne kode, skal du erstatte `MobileAppUrl` med URL-adressen på din Mobile-App backend-version, som kan findes i [Azure portal] i bladet for din Mobile-App backend-version. For denne linje af kode til at oprette, skal du også tilføje følgende **importere** -sætning:

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Sådan: oprette en tabelreference

Den nemmeste måde at forespørge eller ændre data i backend-version er ved hjælp af *skrevet programming model*, da Java er et stærkt indtastede sprog. Denne model indeholder problemfri JSON serialisering og deserialisering ved hjælp af [gson] [ 3] bibliotek, når du sender data mellem klientobjekter og tabeller i back-end Azure SQL.

For at få adgang til en tabel, du først oprette en [MobileServiceTable] [ 8] objekt ved at ringe til den **getTable** metode på [MobileServiceClient][9].  Denne metode har to overloads:

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

I den følgende kode er **mClient** en reference til MobileServiceClient objektet.  Første overbelastning bruges, hvor klassenavnet og tabelnavnet er den samme, og den bruges i på Hurtig start:

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

Anden overbelastning bruges, når tabelnavnet er forskellig fra klassenavnet: den første parameter er tabelnavnet.

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Sådan: binde data til brugergrænsefladen

Databinding omfatter tre komponenter:

- Datakilden
- Skærmlayoutet
- Den adapter, der forbinder to sammen.

I vores eksempelkode, skal returnere vi dataene fra tabellen Mobile Apps SQL Azure **ToDoItem** ind i en matrix. Denne aktivitet er et almindeligt mønster for data-programmer.  Databaseforespørgsler returnerer ofte en samling af rækker, der henter klienten i en liste eller et matrix. I dette eksempel er matrixen datakilden.

Koden angiver et skærmlayout, der definerer visningen af de data, der vises på enheden.  To er bundet sammen med et kort, som denne kode er en udvidelse af den **ArrayAdapter&lt;ToDoItem&gt; ** klasse.

#### <a name="layout"></a>Sådan: definere layoutet

Layoutet er defineret af flere kodestykker på XML-kode. Hvis du har et eksisterende layout, repræsenterer følgende kode **ListView** vi vil udfylde med vores server-data.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

I den foregående kode angiver attributten *listitem* id'et for layout til en enkelt række på listen. Denne kode angiver et afkrydsningsfelt og den tilhørende tekst og får forekomster én gang for hvert element på listen. Dette layout viser ikke **id-** feltet, og et mere komplekse layout vil angive flere felter i visningen. Denne kode er i filen **row_list_to_do.xml** .

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">
        <CheckBox
            android:id="@+id/checkToDoItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/checkbox_text" />
    </LinearLayout>


#### <a name="adapter"></a>Sådan: definere kortet

Da datakilden til vores visning er en matrix med **ToDoItem**, vi underklasse vores kort fra en **ArrayAdapter&lt;ToDoItem&gt; ** klasse. Denne underklasse giver en visning for hver **ToDoItem** ved hjælp af layoutet **row_list_to_do** .

I vores kode vi definerer den følgende klasse, der er en udvidelse af den **ArrayAdapter&lt;E&gt; ** klasse:

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Tilsidesæt metoden kort **getView** . Eksempel:

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


        return row;
    }

Vi oprette en forekomst af denne klasse i vores aktivitet på følgende måde:

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Den anden parameter til ToDoItemAdapter-parametre er en reference til layoutet. Vi kan nu oprette en forekomst af **ListView** og tildele kortet til **ListView**.

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>API-struktur

Mobile-Apps tabel operationer og brugerdefinerede API-kald er asynkron. Bruge objekterne [fremtiden] og [AsyncTask] for de asynkrone metoder, der involverer forespørgsler, indsætter, opdateringer og sletninger. Fremtidige gør det nemmere at udføre flere handlinger på en baggrundstråd uden at håndtere flere indlejrede tilbagekald.

Se filen **ToDoActivity.java** i Android Hurtig start projektet fra [Azure portal] for et eksempel.

#### <a name="use-adapter"></a>Sådan: Brug af kortet

Du er nu klar til at bruge databinding. Følgende kode viser, hvordan du vil hente elementer i tabellen og udfylder den lokale adapter med de returnerede varer.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }

Ringe kortet til enhver tid, du ændrer **ToDoItem** tabel. Da ændringer er udført på grundlag post ved post, håndtere du en enkelt række i stedet for en samling. Når du indsætter et element, du kald metoden **tilføje** på kortet; Når du sletter, kalde metoden **fjerne** .

##<a name="querying"></a>Sådan: forespørge efter data fra din Mobile-App backend-version

Dette afsnit beskrives, hvordan til at stille spørgsmål til den Mobile-App backend-version, som omfatter følgende opgaver:

- [Få vist alle elementer]
- [Filtrering af data skal returneres]
- [Sortér returnerede data]
- [Returnere data i sider]
- [Markere bestemte kolonner]
- [Sammenkædning forespørgsel metoder](#chaining)

### <a name="showAll"></a>Sådan: få vist alle elementer fra en tabel

Følgende forespørgsel returnerer alle elementer i tabellen **ToDoItem** .

    List<ToDoItem> results = mToDoTable.execute().get();

Variablen *resultater* returnerer resultatet af forespørgslen som en liste.

### <a name="filtering"></a>Sådan: Filter returnerede data

Følgende forespørgslen returnerer alle elementer fra tabellen **ToDoItem** hvor **fuldført** er lig med **Falsk**.

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** er referencen til den mobile service-tabel, som vi oprettede tidligere.

Definere et filter ved hjælp af Metodeopkaldet **hvor** i tabelreferencen til. Metoden **hvor** efterfølges af en **felt** metode efterfulgt af en metode, der angiver det logiske prædikat. Mulige prædikat metoder omfatter **eq** (er lig med), **ne** (forskellig fra), **BT** (større end), **Flet** (større end eller lig med), **lt** (mindre end), **le** (mindre end eller lig med). Disse metoder kan du sammenligne tal og streng felter til bestemte værdier.

Du kan filtrere datoer. De følgende metoder gør det muligt at sammenligne vedtagelsesdato for hele eller dele af datoen: **år**, **måned**, **dag**, **time**, **minut**og **andet**. I følgende eksempel adderes et filter for elementer, hvis *Forfaldsdato* er lig med 2013.

    mToDoTable.where().year("due").eq(2013).execute().get();

De følgende metoder understøtter komplekse filtre på strengfelter: **startsWith**, **endsWith**, **Sammenkæd**, **understreng**, **indexOf**, **erstatte**, **toLower**, **toUpper**, **trim**og **længde**. I følgende eksempel filtre for tabelrækker, hvor *tekstkolonnen* starter med "PRI0."

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

Fremgangsmåderne operator understøttes på talfelter: **tilføje**, **sub**, **mul**, **division**, **rest**, **floor**, **loft**og **afrunde**. I følgende eksempel filtrerer for tabelrækker, hvor **varigheden** er et lige tal.

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Du kan kombinere prædikaterne med disse logiske metoder: **og**, **eller** og **ikke**. I følgende eksempel kombinerer to af de foregående eksempler.

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

Gruppere og indlejre logiske operatorer:

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

Mere detaljeret beskrivelse og eksempler på filtrering under [udforske de mange fordele ved Android klient forespørgsel modellen](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Sådan: Sortér returnerede data

Følgende kode returnerer alle elementer fra en tabel over **ToDoItems** sorteret i faldende rækkefølge efter *tekstfeltet* . *mToDoTable* er referencen til den back end-tabel, du har oprettet tidligere:

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Den første parameter i metoden **SorterEfter** er en streng, der er lig med navnet på det felt, der skal sorteres. Den anden parameter bruges **QueryOrder** optælling til at angive, om du vil sortere stigende eller faldende.  Hvis du filtrerer ved hjælp af metoden ***hvor*** , skal ***hvor*** metode aktiveres før metoden ***SorterEfter*** .

### <a name="paging"></a>Sådan: returnere data i sider

Det første eksempel viser, hvordan du vælger de øverste fem elementer fra en tabel. Forespørgslen returnerer elementerne fra en tabel med **ToDoItems**. **mToDoTable** er referencen til den back end-tabel, du har oprettet tidligere:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Her er en forespørgsel, der springer de fem første elementer, og returnerer derefter de næste fem:

    mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>Sådan: markere bestemte kolonner

Følgende kode viser, hvordan til at returnere alle elementer fra en tabel med **ToDoItems**, men kun vises felterne **fuldført** og **tekst** . **mToDoTable** er referencen til back end-tabellen, som vi oprettede tidligere.

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

Parametrene til funktionen Vælg er streng navnene på tabellens kolonner, du vil vende tilbage.

**Vælg** metode skal følge metoder som **hvor** og **SorterEfter**. Det kan efterfølges af sideopdeling metoder som **toppen**.

### <a name="chaining"></a>Sådan: sammenkæde forespørgsel metoder

Kan sammenkædes med de metoder, der bruges i forespørgsler back end-tabeller. Sammenkædning af forespørgsel metoder giver dig mulighed at markere bestemte kolonner med filtrerede rækker, der er sorteret og gruppe. Du kan oprette komplekse logiske filtre.
Hver enkelt metode forespørgsel returnerer et forespørgselsobjekt. For at afslutte en række metoder og faktisk kører forespørgslen, skal du ringe til metoden **udføre** . Eksempel:

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

De sammenkædede forespørgsel metoder skal være bestilt på følgende måde:

1. Filtrere (**hvor**) metoder.
2. Sortere (**SorterEfter**) metoder.
3. Valg (**Vælg**) metoder.
4. sideopdeling (**springe** og **toppen**) metoder.

##<a name="inserting"></a>Sådan: indsætte data i backend-version

Oprette en forekomst af en forekomst af klassen *ToDoItem* og angive dens egenskaber.

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

Brug derefter **insert()** til at indsætte et objekt:

    ToDoItem entity = mToDoTable.insert(item).get();

De returnerede enhed svarer til de data, der er indsat i tabellen back end-medtages felterne ID og eventuelle andre værdier på back-end.

Mobile-Apps tabeller kræver en primær nøglekolonne hedder **id**. Denne kolonne er som standard en streng. Standardværdien for i kolonnen ID er en GUID.  Du kan angive andre entydige værdier, som e-mail-adresser eller brugernavne. Når en strengværdi-ID ikke er angivet for en indsat post, genererer back-end et nyt GUID.

Strengværdier-ID indeholder følgende fordele:

+ Id'er kan oprettes uden at foretage en returkørsel til databasen.
+ Poster er nemmere at flette fra forskellige tabeller eller databaser.
+ Id-værdier integrere bedre med et programs logik.

Streng id-værdier er **nødvendig** for understøttelse af offlinesynkronisering.

##<a name="updating"></a>Sådan: opdatere data i en mobilapp

Hvis du vil opdatere data i en tabel, du Send det nye objekt til **update()** metoden.

    mToDoTable.update(item).get();

I dette eksempel er *elementet* en reference til en række i tabellen *ToDoItem* , som har nogle ændringer.
Rækken med det samme **id** opdateres.

##<a name="deleting"></a>Sådan: slette data i en mobilapp

Følgende kode viser, hvordan du kan slette data fra en tabel ved at angive dataobjektet.

    mToDoTable.delete(item);

Du kan også slette et element ved at angive feltet **-id** i rækken for at slette.

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="lookup"></a>Sådan: søge efter et bestemt element

Slå et element med et bestemt **id** -felt med metoden **lookUp()** :

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="untyped"></a>Sådan: arbejde med den uspecificerede data

Den uspecificerede programming modellen giver dig præcis kontrol over JSON serialisering.  Der er nogle almindelige scenarier, hvor du måske ønsker at bruge en uspecificerede programming model. Eksempelvis hvis back end-tabellen indeholder mange kolonner, og du skal kun bruge til at referere til et undersæt af kolonnerne.  Den indtastede model kræver, at du til at definere alle mobilapps tabellens kolonner i klasse data.  

De fleste API-opkald for at få adgang til data er svarende til de indtastede programming opkald. Den primære forskel er, at den uspecificerede modellen aktivere metoder for objektet **MobileServiceJsonTable** i stedet for objektet **MobileServiceTable** .

### <a name="json_instance"></a>Sådan: oprette en forekomst af en uspecificerede tabel

Svarer til den indtastede model, du starter ved at få en tabelreference, men i dette tilfælde det er et **MobileServicesJsonTable** objekt. Få referencen ved at ringe til den **getTable** metode på en forekomst af klienten:

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Når du har oprettet en forekomst af **MobileServiceJsonTable**, har det næsten samme API tilgængelig med indtastede programming modellen. I nogle tilfælde tage metoderne en uspecificerede parameter i stedet for en indtastede parameter.

### <a name="json_insert"></a>Sådan: indsætte i en uspecificerede tabel

Følgende kode viser, hvordan du gør en Indsæt. Det første trin er at oprette en [JsonObject][1], som er en del af [gson] [ 3] bibliotek.

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

Brug derefter **insert()** til at indsætte objektet uspecificerede i tabellen.

    mJsonToDoTable.insert(jsonItem).get();

Hvis du har brug at få det indsatte objekt ID, kan du bruge metoden **getAsJsonPrimitive()** .

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>Sådan: slette fra en uspecificerede tabel

Følgende kode viser, hvordan du kan slette en forekomst, i dette tilfælde den samme forekomst af en **JsonObject** , der er oprettet i den foregående *Indsæt* eksempel. Koden er den samme som med den indtastede sag, men metoden har en anden signatur, da det refererer til en **JsonObject**.

         mToDoTable.delete(jsonItem);

Du kan også slette en forekomst direkte ved hjælp af dens-ID:

         mToDoTable.delete(ID);

### <a name="json_get"></a>Sådan: returnere alle rækker fra en uspecificerede tabel

Følgende kode viser, hvordan du kan hente en hel tabel. Da du bruger en JSON-tabel, kan du selektivt hente kun nogle af tabellens kolonner.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Det samme sæt af filtrering, filtrering og sideopdeling metoder, der er tilgængelige for den angivne model er tilgængelige for den uspecificerede model.

##<a name="custom-api"></a>Sådan: ringe til en brugerdefineret API

En brugerdefineret API gør det muligt at definere brugerdefinerede slutpunkter, der viser server-funktionalitet, der ikke tilknyttes en insert, opdatere, slette, eller Læs handling. Ved hjælp af en brugerdefineret API, kan du have mere kontrol over messaging, herunder læsning og indstille HTTP brevhoveder for meddelelser og definere formatet for en meddelelsens brødtekst end JSON.

Fra en Android-klienten skal ringe du til metoden **invokeApi** for at ringe til det brugerdefinerede API slutpunkt. I følgende eksempel viser, hvordan til at ringe til API ark med navnet **completeAll**, der returnerer en Samlingsklasse, kaldet **MarkAllResult**.

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }

                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();
                }
            });
        }

Metoden **invokeApi** kaldes på klienten, hvorefter der sendes en anmodning om INDLÆG til det nye brugerdefinerede API. Det resultat, der returneres af brugerdefinerede API vises i meddelelsesdialogboks med en, som er en fejl. Andre versioner af **invokeApi** kan du eventuelt sende et objekt i anmodningsteksten, angive metoden HTTP og sende forespørgselsparametre med anmodningen. Den uspecificerede versioner af **invokeApi** leveres samt.

##<a name="authentication"></a>Sådan: føje godkendelse til din app

Selvstudier allerede indeholder en detaljeret beskrivelse Sådan føjer du disse funktioner.

App Service understøtter [godkendelse af app brugere](app-service-mobile-android-get-started-users.md) ved hjælp af en række forskellige eksterne id-udbydere: Facebook, Google, Microsoft-Account, Twitter og Azure Active Directory. Du kan angive tilladelser på tabeller for at begrænse adgangen til bestemte operationer til kun godkendte brugere. Du kan også bruge identiteten af godkendte brugere for at implementere reglerne i din backend-version.

To godkendelse flyder understøttes: et **server** flow og en **klient** flow. Server strømmen yder den nemmeste godkendelse oplevelse, som den er afhængig af den identity udbydere web-grænseflade.  Ingen yderligere SDK'er skal implementere flow servergodkendelse. Flow servergodkendelse giver ikke en deep integration i den mobile enhed og er kun anbefales til bevis for konceptet scenarier.

Klient strømmen giver mulighed for bedre integration med enhed-specifikke funktioner som enkeltlogon, som den er afhængig af SDK'er leveres af identitetsudbyder.  For eksempel kan du integrere Facebook SDK i din mobilenhed.  Den mobilklient swaps til Facebook-app'en og bekræfter din enkeltlogon før Skift tilbage til din mobile-app.

Fire trin er nødvendige for at aktivere godkendelse i din app:

- Registrere din app til godkendelse til en identitetsudbyder.
- Konfigurere din App Service backend-version.
- Begrænse tabel tilladelser til godkendte brugere kun på App Service back-end.
- Føj godkendelse kode til din app.

Du kan angive tilladelser på tabeller for at begrænse adgangen til bestemte operationer til kun godkendte brugere. Du kan også bruge SID for en godkendt bruger til at redigere anmodninger.  Gennemse [komme i gang med godkendelse] og Server SDK Sådan dokumentation kan finde flere oplysninger.

### <a name="caching"></a>Sådan: Føj godkendelse kode til din app

Følgende kode til at starte en server flow logon ved hjælp af Google-provider:

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Have et ID for den bruger, der er logget på fra en **MobileServiceUser** ved hjælp af metoden **getUserId** . Et eksempel på, hvordan du bruger fremtidige til at ringe til asynkron logon API'er, kan du se [Introduktion til godkendelse].

### <a name="caching"></a>Sådan: Cache godkendelsestokens

Cachelagring godkendelsestokens, skal du gemme det bruger-ID og godkendelse token lokalt på enheden. Næste gang starter appen, skal du kontrollere cachen, og hvis disse værdier er til stede, kan du springe over log in procedure og rehydreres klienten med disse data. Disse data er dog følsomme, og det skal gemmes krypteret til sikkerhed, i tilfælde af telefonen bliver stjæles.

Du kan se en komplet eksempel på hvordan i cachen godkendelsestokens i [cachen godkendelse tokens afsnit][7].

Når du forsøger at bruge et udløbet token, modtager du en *401 Uautoriseret* svar. Du kan håndtere godkendelsesfejl ved hjælp af filtre.  Filtre SKÆRING anmodninger om at App Service back-end. Filter koden tester svar til en 401, udløser processen logon og derefter genoptages anmodningen, som genererede 401. 

## <a name="adal"></a>Sådan: godkende brugere med Active Directory Authentication Library

Du kan bruge Active Directory Authentication Library (ADAL) til at logge brugere på dit program ved hjælp af Azure Active Directory. Ved hjælp af en klient flow logon er ofte bedst at bruge den `loginAsync()` metoder som den indeholder en mere oprindelige UX funktionalitet og giver mulighed for yderligere tilpasning.

1. Konfigurere din mobilapp backend-version til logon AAD ved at følge [Sådan konfigureres App-tjeneste til logon til Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) -selvstudium. Sørg for at fuldføre valgfrit til registrering af et native client-program.

2. Installere ADAL ved at ændre din build.gradle fil for at medtage følgende definitioner:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Føj følgende kode i dit program, foretage de følgende erstatninger:

* Erstat **Indsæt-NØGLECENTER-her** med navnet på den lejer, hvori du klargjort dit program. Formatet skal være https://login.windows.net/contoso.onmicrosoft.com. Denne værdi kan kopieres fra fanen domæne i din Azure Active Directory i [Azure klassisk portalen].

* Erstat **Indsæt-ressource-ID-her** med klient-ID for din back end-til-mobilappen. Du kan få klient-ID fra fanen **Avanceret** under **Azure Active Directory-indstillinger** i portalen.

* Erstat **Indsæt-klient-ID-her** med brugerens klient-ID, du har kopieret fra den oprindelige klientprogrammet.

* Erstat **Indsæt-REDIRECT-URI-her** med webstedets _/.auth/login/done_ slutpunkt, ved hjælp af HTTPS-skemaet. Denne værdi skal være svarende til _https://contoso.azurewebsites.net/.auth/login/done_.

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to-add-push-notification-to-your-app"></a>Sådan: føje opslagsnål meddelelse til din app

Du kan [læse en oversigt over] [ 6] , der beskriver, hvordan Microsoft Azure meddelelse Hubs understøtter en lang række pushmeddelelser.  I [dette selvstudium][5], der sendes en push-meddelelse til alle enheder, hver gang en post er indsat.

## <a name="how-to-add-offline-sync-to-your-app"></a>Sådan: føje offlinesynkronisering til din app

Hurtig start selvstudiet indeholder kode, der implementerer offlinesynkronisering. Se efter kode præfikset kommentarer:

    // Offline Sync

Du kan implementere offlinesynkronisering af uncommenting de følgende kodelinjer, og du kan føje lignende kode til andre Mobile-Apps kode.

##<a name="customizing"></a>Sådan: tilpasse klienten

Der er flere måder at tilpasse standardfunktionsmåden for klienten.

### <a name="headers"></a>Sådan: tilpasse anmode om sidehoveder

Konfigurere en **ServiceFilter** for at tilføje en brugerdefineret HTTP-header til hver enkelt anmodning:

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Sådan: tilpasse serialisering

Klienten antages det, som tabelnavne, kolonnenavne og data skriver på den back-end alle svarer nøjagtigt de dataobjekter, der er defineret i klienten. Der kan være en hvilken som helst antal årsager hvorfor navnene på server og klient ikke muligvis stemmer overens. I det pågældende scenarie vil du gøre følgende typer af tilpasninger:

- Kolonnenavnene bruges i tabellen App Service svarer ikke til navnene, du bruger i klienten.
- Brug en App Service-tabel, der indeholder et andet navn end klassen den knyttes til i klienten.
- Slå automatisk egenskaben brugen af store bogstaver.
- Føje komplekse egenskaber til et objekt.

### <a name="columns"></a>Sådan: tilknytte forskellige klient- og navne

Antag, at Java Klientkoden bruger standardnavne Java-typografi til objektegenskaber **ToDoItem** som følgende egenskaber:

- funktionen midt
- mText
- mComplete
- MVARIGHED

Sekventielt klient navnene i JSON navne, der matcher kolonnenavnene i tabellen **ToDoItem** på serveren. Følgende kode bruger [gson] [ 3] bibliotek til at kommentere egenskaberne:

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>Sådan: tilknytte forskellige tabelnavne mellem klienten og back end-

Knytte tabelnavnet client til navnet på en anden mobile tjenester tabel ved hjælp af en tilsidesættelse af [getTable()] [ 4] metode:

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>Sådan: automatisere kolonnetilknytninger af servernavne

Du kan angive en strategi for konvertering, der gælder for hver kolonne ved hjælp af [gson] [ 3] API. Biblioteket Android klienten bruger [gson] [ 3] i baggrunden til sekventielt Java-objekter til JSON data, inden data, der sendes til Azure App Service.  Følgende kode bruger **setFieldNamingStrategy()** metoden til at angive strategi. I dette eksempel, slettes det første tegn (en "m"), og klik derefter små det næste tegn for hver feltnavn. For eksempel vil det omdanne "midt" til "id".

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

Denne kode skal udføres, før du bruger **MobileServiceClient**.

### <a name="complex"></a>Sådan: gemme et objekt eller en matrix egenskab til en tabel

Indtil videre har vores serialisering eksempler involveret enkle typer som heltal og strenge.  Enkle typer sekventielt nemt i JSON.  Hvis vi vil tilføje en kompleks objekt, der ikke automatisk sekventielt til JSON, har vi brug at angive metoden JSON serialisering.  For at se et eksempel på, hvordan du kan angive brugerdefinerede JSON serialisering skal du gennemse blogindlægget [tilpasse serialisering ved hjælp af biblioteket gson i Mobile tjenester Android-klienten][2].

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Få vist alle elementer]: #showAll
[Filtrering af data skal returneres]: #filtering
[Sortér returnerede data]: #sorting
[Returnere data i sider]: #paging
[Markere bestemte kolonner]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure-portalen]: https://portal.azure.com
[Introduktion til godkendelse]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Fremtiden]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html
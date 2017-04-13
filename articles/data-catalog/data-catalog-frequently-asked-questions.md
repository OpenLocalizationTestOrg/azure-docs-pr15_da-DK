<properties
   pageTitle="Ofte stillede spørgsmål om Azure datakatalog | Microsoft Azure"
   description="Ofte stillede spørgsmål om Azure datakatalog, herunder muligheder for kilde dataregistrering, anmærkninger og administration."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure datakatalog stillede ofte spørgsmål

I denne artikel indeholder svar til ofte stillede spørgsmål, der er relateret til tjenesten Microsoft **Azure-Datakataloget** .

## <a name="q-what-is-azure-data-catalog"></a>Sp: Hvad er Azure datakatalog?

SV: Microsoft Azure datakatalog er en komplet administreret tjeneste, der er placeret i skyen Microsoft Azure, der fungerer som et system af registrering og system af registrering til enterprise-datakilder. Azure datakatalog indeholder funktioner, der aktiverer en bruger – fra analytikere data forskere til udviklere – til at registrere, opdage, forstå og bruge datakilder.

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>Sp: hvilke kunde udfordringer betyder Azure datakatalog løse?

Azure datakatalog adresser udfordring af kilde dataregistrering og "mørke-data" ved at tillade brugere at registrere og forstå enterprise-datakilder.

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>Sp: hvem er målgrupper til Azure datakatalog?

Azure datakatalog indeholder funktioner til tekniske og ikke-tekniske brugere, herunder:

- Data udviklere, BI og Analytics professionelle: hvem er ansvarlig for at producere data og analytics indhold, så andre kan bruge
-   Databehandlerens: Dem, der har viden om dataene, hvad betyder det og hvordan den skal bruges og til hvilke formål
- Dataforbrugere: Dem, der har brug for at kunne finde, forstå og oprette forbindelse til data det er nødvendigt at udføre deres arbejde ved hjælp af værktøjet efter eget valg
- Central IT: dem, der har brug at foretage hundredvis af tilgængelige datakilder, så det bliver nemmere for business-brugere, og hvem der har brug for opsyn over hvordan data bruges og af hvem

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>Sp: Hvad er Azure datakatalog område tilgængeligheden?

Azure datakatalog tjenester er tilgængelige i de følgende datacentre:

- Vest USA
- Indtastning af østasiatiske USA
- Vest Europe
- Nord Europe
- Australien øst
- Sydøstasien

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>Sp: Hvad er begrænsninger på antallet af dataaktiver i Azure datakatalog?

Den gratis version af Azure datakatalog er begrænset til 5.000 registrerede dataaktiver.

Standard Edition af Azure datakatalog understøtter op til 100.000 registrerede dataaktiver.

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>Sp: Hvad er de understøttede-kilde- og aktiv datatyper?

Se [Data katalog Dti](data-catalog-dsr.md) til listen over aktuelt understøttede datakilder.

## <a name="q-how-do-i-request-support-for-another-data-source"></a>Sp: hvordan jeg anmode om support til en anden datakilde?

Anmode om funktioner og andre feedback kan sendes i [Azure datakatalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>Sp: Hvordan kommer jeg i gang med Azure datakatalog?

Der er det bedste sted at komme i gang ved at følge vejledningen i [Introduktion til datakatalog](data-catalog-get-started.md). I denne artikel er en til en komplet oversigt over funktionerne i tjenesten.

## <a name="q-how-do-i-register-my-data"></a>Sp: Hvordan registrerer jeg mine data?

Åbne værktøjet til registrering af Azure datakatalog fra området "Publicer" på portalen Azure datakatalog for at registrere dine data i Azure datakatalog. Log på ved hjælp af de samme legitimationsoplysninger, som du kan bruge til at få adgang til portalen Azure datakatalog, og derefter vælge datakilden og de bestemte aktiver, du vil registrere i programmet Azure datakatalog publicering.

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>Sp: hvilke egenskaber udtrækkes til dataaktiver, der er registreret?

De specifikke egenskaber varierer fra datakilde til datakilde, men generelt tjenesten til publicering af Azure datakatalog kan udtrække følgende oplysninger:

- Aktiver navn
- Aktivtype
- Beskrivelse af aktiver
- Attribut/kolonnenavne
- Attribut/kolonne-datatyper
- Beskrivelse af attribut/kolonne

> [AZURE.IMPORTANT] Registrering af dataaktiver med Azure datakatalog ikke flytte eller kopiere data til skyen. Registrering af aktiver fra en datakilde kan kopiere de Aktiver metadata til Azure, men dataene bliver i eksisterende datakildeplacering. Den eneste undtagelse til denne regel er, hvis en bruger vælger at overføre preview poster eller en data-profil, når registrering af aktiver. Når herunder et eksempel, op til 20 poster skal kopieres fra hver aktiv, og gemmes som et øjebliksbillede i Azure-Datakataloget. Når herunder en data-profil, beregnes samlede oplysninger (såsom størrelsen på tabeller, procentdel null-værdier i kolonnen og de mindste, maksimale og gennemsnitlige værdier for kolonner) og inkluderet i de metadata, der er gemt i kataloget.

<br/>

> [AZURE.NOTE] For datakilder som SQL Server Analysis Services, som har en førsteklasses **Beskrivelse** egenskab, der datakatalog Azure udgivelse af programmet udtrække pågældende egenskabsværdi. For SQL Server-relationsdatabaser, som mangler en førsteklasses **Beskrivelse** egenskab, udtrække programmet Azure datakatalog publicering værdien fra ms_description udvidet egenskab for objekter og kolonner. Du kan finde yderligere oplysninger finder TechNet [Ved hjælp af udvidede egenskaber på databaseobjekter](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>Sp: hvor lang tid fører det for nyligt registrerede aktiver skal vises i Azure datakatalog?

Når du har registreret Aktiver med Azure datakatalog kan der være en periode på 5-10 sekunder, før de vises i portalen Azure datakatalog.

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Sp: hvordan anmærke og forbedre metadataene for min Aktiver registrerede oplysninger?

Den nemmeste måde at give metadata til registrerede aktiver er at markere aktivet i portalen Azure datakatalog og derefter skrive metadata værdierne i egenskabsruden eller skema ruden for det markerede objekt.

Du kan også angive nogle metadata, som eksperter og de mærker, under registreringsprocessen. De værdier, der er angivet i tjenesten til publicering af Azure datakatalog skal gælde for alle anlægsaktiver registreres på det pågældende tidspunkt. For at få vist de senest registreret objekter i portalen til yderligere anmærkning, klik på knappen **Vis Portal** i det sidste skærmbillede af programmet Azure datakatalog publicering.

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>Sp: Hvordan sletter jeg min registrerede dataobjekter?

Du kan slette et objekt fra Azure datakatalog ved at markere objektet på portalen, og derefter klikke på knappen **Slet** . Dette fjerner metadataene for objektet fra Azure datakatalog men påvirker ikke den faktiske underliggende datakilde.

## <a name="q-what-is-an-expert"></a>Sp: Hvad er en ekspert?

En ekspert er en person, som har en informeret perspektiv om et dataobjekt. Et objekt kan have flere eksperter. En ekspert behøver ikke at være "ejer" for et objekt eksperten er blot en person, som ved, hvordan dataene kan og skal bruges.

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>Sp: Hvordan kan jeg dele oplysninger med Azure datakatalog teamet, hvis jeg støder på problemer?

Brug Azure datakatalog forummet til at rapportere problemer, dele oplysninger, og Stil spørgsmål. Forummet kan findes på http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

##<a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>Sp: Azure datakatalog fungerer med denne anden datakilde, jeg er interesseret i?
Vi arbejder aktivt om at tilføje flere datakilder i Azure datakatalog. Hvis der er en datakilde, du vil gerne se understøttes, skal du foreslå den (eller support på din telefonsvarer, hvis det allerede er der foreslået) i [Azure datakatalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Sp: hvordan er Azure datakatalog relateret til datakatalog i Power BI til Office 365?

Du kan betragte Azure datakatalog som en udviklingen af Datakataloget. Azure datakatalog leverer lignende funktioner til datakilde publicering og registrering, men er fokus på bredere scenarier og ikke afhænger af Office 365. Umiddelbart efter datakatalog Azure bliver alment tilgængelig fletter to kataloger til en enkelt tjeneste.

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>Sp: hvilke tilladelser skal en bruger til at registrere Aktiver med Azure datakatalog?

Den bruger, der kører værktøjet til Azure datakatalog registrering skal have tilladelser på den datakilde, der gør det til at læse metadata fra kilden. Hvis brugeren vælger også for at medtage et eksempel, skal brugeren også have tilladelser, der giver ham at læse data fra de objekter, der bliver registreret.

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Sp: vil Azure datakatalog gøres tilgængelige for lokal installation?

Azure datakatalog er en skybaseret tjeneste, der kan arbejde med både skyen og lokale datakilder, fremvisning af en hybrid datakilde registrering løsning. Der er i øjeblikket ingen planer for en version af Azure datakatalog tjenesten, som kører i det lokale miljø.

##<a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>Sp: kan vi udtrække flere / bedre metadata fra de datakilder, vi har registreret?

Vi arbejder aktivt for at udvide funktionerne i Azure datakatalog. Hvis der er flere metadata, som du gerne vil se udtrukket fra datakilden under registrering, skal du foreslå det (eller stemme for den Hvis det allerede er der foreslået) i [Azure datakatalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). I fremtiden vil vi tillade tredjepart mulighed for at tilføje nye datakildetyper gennem en udvidelsesmuligheder af API.

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Sp: hvordan jeg begrænse synligheden af dataaktiver registrerede, så kun bestemte personer kan finde dem?

SV: vælge dataaktiver i Azure-Datakataloget, og klik på knappen "Tage ejerskab". Ejere af dataaktiver i Azure datakatalog kan ændre indstillingerne for synlighed, hvis du vil tillade enten alle katalog brugere at finde de ejet Aktiver eller for at begrænse synligheden til bestemte brugere.

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Sp: Hvordan opdaterer jeg registrering for et aktiv i data, der ændres i datakilden, afspejles i kataloget?

SV: for at opdatere metadataene for dataaktiver, der allerede er registreret i kataloget, skal du blot Registrer igen den datakilde, der indeholder aktiverne. Ændringer i datakilden, som kolonner der tilføjes eller fjernes fra tabeller eller visninger, opdateres i kataloget, men bevares alle anmærkninger, der leveres af brugere.

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>Sp: mit spørgsmål ikke besvaret her – Hvad skal jeg gøre?

Hoved på over til [Azure datakatalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Spørgsmål, der kan finde deres måde her.

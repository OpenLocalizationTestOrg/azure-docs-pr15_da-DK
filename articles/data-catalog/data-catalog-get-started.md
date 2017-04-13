<properties
    pageTitle="Komme i gang med datakatalog | Microsoft Azure"
    description="Til slut selvstudium præsenterer scenarier og funktionerne i Azure datakatalog."
    documentationCenter=""
    services="data-catalog"
    authors="steelanddata"
    manager="jhubbard"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/20/2016"
    ms.author="spelluru"/>

# <a name="get-started-with-azure-data-catalog"></a>Introduktion til Azure datakatalog
Azure datakatalog er en fuldt administreret skybaseret tjeneste, der fungerer som et system af registrering og registrering for enterprise dataaktiver system. Se [Hvad er Azure datakatalog](data-catalog-what-is-data-catalog.md)for en detaljeret oversigt.

Dette selvstudium hjælper dig med at komme i gang med Azure datakatalog. Du kan udføre følgende procedurer i dette selvstudium:

| Procedure | Beskrivelse |
| :--- | :---------- |
| [Klargøring datakatalog](#provision-data-catalog) | I denne procedure, og du klargør eller konfigurere Azure datakatalog. Du kan udføre dette trin kun, hvis kataloget ikke er konfigureret før. Du kan have kun én datakatalog per organisation (Microsoft Azure Active Directory-domæne), selvom der er flere abonnementer, der er knyttet til din Azure-konto. |
| [Registrere over dataaktiver](#register-data-assets) | I denne procedure, og du har registreret dataaktiver fra eksempeldatabasen AdventureWorks2014 med datakataloget. Registrering er processen med at hente vigtige strukturelle metadata som navne, typer og placeringer fra datakilden og kopiere metadata i kataloget. Datakilde og dataaktiver forbliver hvor de er, men metadata, der bruges af kataloget til at gøre dem nemmere kan findes og forstå. |
| [Finde over dataaktiver](#discover-data-assets) | I denne procedure skal bruge du portalen Azure datakatalog for at finde dataaktiver, der er registreret i det forrige trin. Når en datakilde, der er registreret med Azure datakatalog, indekseret dens metadata af tjenesten, så brugerne kan nemt søge efter de ønskede data. |
| [Anmærke over dataaktiver](#annotate-data-assets) | I denne procedure skal angive du anmærkninger (oplysninger som beskrivelser, mærker, dokumentation eller eksperter) for data aktiverne. Disse oplysninger supplerer de metadata, der er hentet fra datakilden og for at få datakilden mere forstå til flere personer. |
| [Oprette forbindelse til over dataaktiver](#connect-to-data-assets) | I denne procedure skal åbne du dataaktiver i integreret klientværktøjer (som Excel og SQL Server Data Tools) og et ikke-integreret værktøj (SQL Server Management Studio). |
| [Administrere dataaktiver](#manage-data-assets) | I denne procedure skal konfigurere du sikkerhed for dine dataaktiver. Datakatalog giver ikke brugere adgang til selve dataene. Ejeren af datakilden styrer dataadgang. <br/><br/> Med datakatalog, kan du finde datakilder og få vist **metadata** , der er relateret til de kilder, der er registreret i kataloget. Der kan være situationer, men hvor datakilder skal være synlige kun til bestemte brugere eller til medlemmer af bestemte grupper. Du kan bruge datakatalog at overtage ejerskabet af registrerede dataaktiver i kataloget, og Kontrollér synligheden af de aktiver, at du ejer for disse scenarier. |
| [Fjerne over dataaktiver](#remove-data-assets) | I denne procedure, og lærer du at fjerne dataaktiver fra datakataloget. |  

## <a name="tutorial-prerequisites"></a>Selvstudium forudsætninger

### <a name="azure-subscription"></a>Azure-abonnement
Hvis du vil konfigurere Azure datakatalog, skal du være ejer eller medejer af et Azure-abonnement.

Azure abonnementer hjælpe dig med at organisere adgang til skyen serviceressourcer som Azure datakatalog. De også Hjælp du styre, hvordan rapporteres Ressourceforbrug, faktureret, og som er betalt. Hvert abonnement kan have en anden fakturering og betaling installation, så du kan have forskellige abonnementer og forskellige planer ved afdeling, project, regionalt kontor osv. Hver skytjeneste hører til et abonnement, og du skal have et abonnement, før du konfigurerer Azure datakatalog. Se [Administrer konti, abonnementer og administrative roller](../active-directory/active-directory-how-subscriptions-associated-directory.md)for at få mere for at vide.

Hvis du ikke har et abonnement, kan du oprette en gratis prøveversion konto på blot et par minutter. Du kan finde oplysninger i [Gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/) .

### <a name="azure-active-directory"></a>Azure Active Directory
Hvis du vil konfigurere Azure datakatalog, skal du være logget på med en brugerkonto Azure Active Directory (Azure AD). Du skal være ejer eller medejer af et Azure-abonnement.  

Azure AD gør det nemt for din virksomhed til at administrere identitet og access, både i skyen og i det lokale miljø. Du kan bruge en enkelt arbejds- eller skolekonto til at logge på en hvilken som helst skyen eller en lokal webprogram. Azure datakatalog bruger Azure AD til at godkende logon. Hvis du vil vide mere, skal du se [Hvad er Azure Active Directory](../active-directory/active-directory-whatis.md).

### <a name="azure-active-directory-policy-configuration"></a>Konfiguration af Azure Active Directory-politik

Du kan støde på en situation, hvor du kan logge på portalen Azure datakatalog, men når du forsøger at logge på værktøjet til registrering datakilde, du får en fejlmeddelelse, der forhindrer dig i at logge på. Denne fejl kan opstå, når du er på virksomhedens netværk, eller når du opretter forbindelse fra uden for virksomhedens netværk.

Værktøjet til registrering bruger *formulargodkendelse* til at validere bruger logon mod Azure Active Directory. Til vellykket logon, skal en Azure Active Directory-administrator aktivere formulargodkendelse i den *globale godkendelsespolitik*.

Med politikken globale godkendelse, kan du aktivere godkendelse separat til intranet og ekstranet forbindelser, som vist på følgende billede. Log på fejl kan opstå, hvis formulargodkendelse ikke er aktiveret for det netværk, hvorfra du opretter forbindelse.

 ![Azure Active Directory globale godkendelsespolitik](./media/data-catalog-prerequisites/global-auth-policy.png)

Se [konfiguration af godkendelsespolitikker](https://technet.microsoft.com/library/dn486781.aspx)kan finde flere oplysninger.

## <a name="provision-data-catalog"></a>Klargøring datakatalog
Du kan klargøre kun én datakatalog per organisation (Azure Active Directory-domæne). Hvis ejeren eller medejer af et Azure-abonnement der er tildelt denne Azure Active Directory-domæne allerede har oprettet et katalog, kan du derfor ikke kunne oprette et katalog igen, selvom du har flere Azure abonnementer. Gå til [Azure datakatalog startsiden](http://azuredatacatalog.com) for at teste, om et datakatalog er blevet oprettet af en bruger i dit Azure Active Directory-domæne, og Kontrollér, om du får vist kataloget. Hvis et katalog er allerede blevet oprettet for dig, gå til følgende procedure, og gå til næste afsnit.    

1. Gå til den [datakatalog service side](https://azure.microsoft.com/services/data-catalog) og klikke på **Introduktion**.

    ![Azure datakatalog – marketing landingssiden](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Logge på med en brugerkonto, der er ejer eller medejer af et Azure-abonnement. Du kan se den efterfølgende side efter logge på.

    ![Azure datakatalog – klargøring datakatalog](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Angiv et **navn** til datakataloget, det **abonnement** , du vil bruge og **placering** for kataloget.
4. Udvid **priser** og vælg en Azure datakatalog **edition** (gratis eller Standard).
    ![Azure datakatalog – Vælg edition](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Udvid **Katalog brugere** , og klik på **Tilføj** for at tilføje brugere til datakataloget. Du føjes automatisk til denne gruppe.
    ![Azure datakatalog - brugere](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Udvid **Katalog administratorer** , og klik på **Tilføj** for at tilføje flere administratorer til datakataloget. Du føjes automatisk til denne gruppe.
    ![Azure datakatalog - administratorer](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. Klik på **Opret katalog** for at oprette datakatalog for organisationen. Du kan se startsiden for datakatalog, når den er oprettet.
    ![Azure datakatalog – oprettet](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Finde et datakatalog i portalen Azure
1. Gå til [Azure-portalen](https://portal.azure.com) på en anden fane i webbrowseren eller i et separat browservindue, og log på med den samme konto, du brugte til at oprette datakatalog i det forrige trin.
2. Vælg **Gennemse** , og klik derefter på **Datakatalog**.

    ![Azure datakatalog – Gennemse Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) du se datakatalog du har oprettet.

    ![Azure datakatalog – Vis katalog i listen](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
4.  Klik på det katalog, du har oprettet. Du kan se bladet **Datakatalog** på portalen.

    ![Azure datakatalog – blade i portalen ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
5. Du kan få vist egenskaber for datakatalog og opdaterer dem. For eksempel skal du klikke på **priser niveau** og ændre udgaven.

    ![Azure datakatalog – priser niveau](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Adventure Works Northwind
Du har registreret dataaktiver (tabeller) fra eksempeldatabasen AdventureWorks2014 til SQL Server-Database Engine i dette selvstudium, men du kan bruge en hvilken som helst understøttet datakilde, hvis du foretrækker at arbejde med data, der er velkendt og relevant for din rolle. Se [understøttede datakilder](data-catalog-dsr.md)for en liste over understøttede datakilder.

### <a name="install-the-adventure-works-2014-oltp-database"></a>Installere Adventure Works 2014 OLTP-databasen
Adventure Works database understøtter standard online behandling af transaktioner scenarier for en opdigtet cykler producent (Adventure Works skifter), som omfatter produkter, køb og salg. I dette selvstudium, kan du registrere oplysninger om produkter til Azure datakatalog.

Sådan installeres eksempeldatabasen Adventure Works:

1. Hent [eventyr fungerer 2014 fuld Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) på CodePlex.
2. For at gendanne databasen på din computer skal du følge vejledningen i [gendanne en sikkerhedskopi af databasen ved hjælp af SQL Server Management Studio](http://msdn.microsoft.com/library/ms177429.aspx)eller ved at følge disse trin:
    1. Åbn SQL Server Management Studio og oprette forbindelse til SQL Server-Database Engine.
    2. Højreklik på **databaser** , og klik på **Gendan Database**.
    3. Klik på indstillingen **enhed** efter **kilde** under **Gendanne databasen**, og klik på **Gennemse**.
    4. Klik på **Tilføj**under **Vælg enheder til sikkerhedskopiering**.
    5. Gå til den mappe, hvor du har **AdventureWorks2014.bak** filen, Vælg fil, og klik på **OK** for at lukke dialogboksen **Find sikkerhedskopifil** .
    6. Klik på **OK** for at lukke dialogboksen **Vælg enheder til sikkerhedskopiering** .    
    7. Klik på **OK** for at lukke dialogboksen **Gendan Database** .

Nu kan du registrere dataaktiver fra eksempeldatabasen Adventure Works ved hjælp af Azure datakatalog.

## <a name="register-data-assets"></a>Registrere over dataaktiver

I denne opgave skal bruge du værktøjet til registrering til at registrere dataaktiver fra Adventure Works-database med kataloget. Registrering er processen med at hente vigtige strukturelle metadata som navne, typer og placeringer fra datakilden og de aktiver, den indeholder, og kopiere metadata i kataloget. Datakilde og dataaktiver forbliver hvor de er, men metadata, der bruges af kataloget til at gøre dem nemmere kan findes og forstå.

### <a name="register-a-data-source"></a>Registrere en datakilde

1.  Gå til [Azure datakatalog startsiden](https://azuredatacatalog.com) , og klik på **Publicer Data**.

    ![Azure Data katalogfletning – publicere Data knappen](media/data-catalog-get-started/data-catalog-publish-data.png)

2.  Klik på **Start program** for at hente, installere og køre værktøjet til registrering på din computer.

    ![Azure datakatalog – Start-knap](media/data-catalog-get-started/data-catalog-launch-application.png)

3. Klik på **Log på** , og Angiv dine legitimationsoplysninger på siden **Velkommen** .    

    ![Azure datakatalog – velkomstsiden](media/data-catalog-get-started/data-catalog-welcome-dialog.png)

4. Klik på **SQL Server** og **Næste**på siden **Microsoft Azure-Datakataloget** .

    ![Azure datakatalog – datakilder](media/data-catalog-get-started/data-catalog-data-sources.png)

5.  Angive forbindelsesegenskaber SQL Server for **AdventureWorks2014** (se eksemplet nedenfor) og klikke på **Opret forbindelse**.

    ![Azure datakatalog – indstillinger for SQL Server-forbindelse](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

6.  Registrere metadata for din data aktiv. I dette eksempel skal registrere du **Fremstilling/produkt** objekter fra navneområdet AdventureWorks fremstilling:

    1. Udvid **AdventureWorks2014** træet **Server hierarki** , og klik på **fremstilling**.
    2. Vælg **produkt**, **ProductCategory**, **ProductDescription**og **ProductPhoto** ved hjælp af Ctrl + klik.
    3. Klik på **Flyt valgte pil** (**>**). Denne handling flytter alle de markerede objekter til listen **objekter, der skal registreres** .

        ![Azure datakatalog selvstudium – Gennemse og vælge objekter](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
    4. Vælg **Medtag en eksempelvisning** til at medtage et øjebliksbillede eksempel på dataene. Snapshot indeholder op til 20 poster fra hver tabel, og den kopieres i kataloget.
    5. Vælg **Medtag Data profil** for at medtage et øjebliksbillede af objekt-statistikken for dataene profil (for eksempel: mindste, maksimale og gennemsnitlige værdier for en kolonne, antallet af rækker).
    6. Angiv i feltet **Tilføj mærker** **adventure fungerer, skifter**. Denne handling tilføjer søgning koder til disse dataaktiver. Mærker er en god måde at hjælpe brugere med at søge efter en registrerede datakilde.
    7. Angiv navnet på en **ekspert** på disse data (valgfrit).

        ![Azure datakatalog selvstudium - objekter, der skal registreres](media/data-catalog-get-started/data-catalog-objects-register.png)

    8. Klik på **Registrer**. Azure datakatalog registrerer din markerede objekter. I denne opgave registreres de markerede objekter fra Adventure Works. Værktøjet til registrering henter metadata fra data aktiv og kopierer data til tjenesten Azure datakatalog. Dataene forbliver hvor den aktuelt er placeret, og det forbliver under kontrol af administratorer og politikker i det aktuelle system.

        ![Azure datakatalog – registrerede objekter](media/data-catalog-get-started/data-catalog-registered-objects.png)

    9. For at se din registrerede datakildeobjekter skal du klikke på **Vis Portal**. Bekræft, at du se alle fire tabeller og databasen i gittervisning i portalen Azure datakatalog.

        ![Objekter i portalen Azure datakatalog ](media/data-catalog-get-started/data-catalog-view-portal.png)


I denne opgave skal registreret du objekter fra eksempeldatabasen Adventure Works, så de kan nemt registreres af brugere på tværs af organisationen. I den næste opgave lære du at finde registrerede dataaktiver.

## <a name="discover-data-assets"></a>Finde over dataaktiver
Registrering i Azure datakatalog bruger to primære mekanismer: søgning og filtrering.

Søgning er udviklet til at være både intuitiv og effektiv. Som standard sammenlignes søgeord med en egenskab i kataloget, herunder leveret anmærkninger.

Filtrering er udviklet supplement til søgning. Du kan vælge bestemte egenskaber som eksperter, datakildetype, objekttype og mærker til at få vist matchende dataaktiver og for at begrænse søgeresultaterne til tilsvarende aktiver.

Ved hjælp af en kombination af søgning og filtrering, kan du hurtigt navigere de datakilder, der er registreret med Azure datakatalog at finde de dataaktiver, du har brug for.

I denne opgave skal bruge du portalen Azure datakatalog for at finde dataaktiver, der er registreret i den forrige opgave. Du kan finde oplysninger om syntaksen i Søg i [Datakatalogsøgning syntaksreference](https://msdn.microsoft.com/library/azure/mt267594.aspx) .

Følgende er nogle eksempler på opdager dataaktiver i kataloget.  

### <a name="discover-data-assets-with-basic-search"></a>Finde dataaktiver med grundlæggende søgning
Grundlæggende søgning kan du søge i et katalog ved hjælp af en eller flere søgeord. Resultaterne er en hvilken som helst aktiver, der svarer til en egenskab med en eller flere af de betingelser, der er angivet.

1. Klik på **Startside** i portalen Azure datakatalog. Hvis du har lukket webbrowseren skal du gå til [Azure datakatalog startsiden](https://www.azuredatacatalog.com).
2. Angiv i søgefeltet, `cycles` og tryk på **ENTER**.

    ![Azure datakatalog – grundlæggende tekst Søg](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Bekræft, at du se alle fire tabeller og databasen (AdventureWorks2014) i resultaterne. Du kan skifte mellem **gittervisning** og **listevisning** ved at klikke på knapperne på værktøjslinjen, som vist på følgende billede. Bemærk, at søgeord er fremhævet i søgeresultaterne, fordi indstillingen **fremhæve** er **på**. Du kan også angive antallet **resultater for hver side** i søgeresultaterne.

    ![Azure datakatalog – grundlæggende tekst søgeresultater](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)

    Panelet **søgninger** er placeret til venstre, og panelet **Egenskaber** er til højre. Du kan ændre søgekriterier og filtrere resultater på panelet **søgninger** . Panelet **Egenskaber** viser egenskaberne for et markeret objekt i gitteret eller listen.

4. Klik på **produkt** i søgeresultaterne. Klik på **Preview**, **kolonner**, **Data profil**og **dokumentation** faner, eller klik på pilen for at udvide den nederste rude.  

    ![Azure datakatalog – nederste rude](media/data-catalog-get-started/data-catalog-data-asset-preview.png)

    Du kan se et eksempel på dataene i tabellen **Product** på fanen **Vis udskrift** .  
5. Klik på fanen **kolonner** for at finde oplysninger om kolonner (såsom **navn** og **datatype**) i data aktiv.
6. Klik på fanen **Data profil** for at få vist profiler data (for eksempel: antal rækker, størrelsen af data eller mindste værdi i en kolonne) i data aktiv.
7. Filtrere resultaterne ved hjælp af **filtre** i venstre side. Eksempelvis klikke på **tabel** for **Objekttype**, og du få vist kun de fire tabeller, ikke databasen.

    ![Azure datakatalog – filtrere søgeresultater](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Finde dataaktiver med egenskaben angivelse af område
Egenskaben angivelse af område hjælper dig med at opdage dataaktiver hvor søgeordet sammenlignes med den angivne egenskab.

1. Fjern filteret **tabel** under **Objekttype** i **filtre**.  
2. Angiv i søgefeltet, `tags:cycles` og tryk på **ENTER**. Se [Datakatalogsøgning syntaksreference](https://msdn.microsoft.com/library/azure/mt267594.aspx) for alle de egenskaber, du kan bruge til at søge datakatalog.
3. Bekræft, at du se alle fire tabeller og databasen (AdventureWorks2014) i resultaterne.  

    ![Datakatalog – egenskab angivelse af område søgeresultater](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Gemme søgningen
1. Angiv et navn til feltet Søg i ruden **søgninger** i sektionen **Aktuelle søgning** , og klik på **Gem**.

    ![Azure datakatalog – Gem søgning](media/data-catalog-get-started/data-catalog-save-search.png)
2. Bekræft, at den gemte søgning vises under **Gemt søgninger**.

    ![Azure datakatalog – gemte søgninger](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Vælg en af de handlinger, du kan foretage på den gemte søgning (**omdøbe**, **slette**, **Gem som standard** søgning).

    ![Azure datakatalog – gemt søgeindstillinger](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Booleske operatorer
Du kan udvide eller begrænse søgningen med booleske operatorer.

1. Angiv i søgefeltet, `tags:cycles AND objectType:table`, og tryk på **ENTER**.
2. Bekræft, at du ser kun tabeller (ikke databasen) i resultaterne.  

    ![Azure datakatalog – boolesk operator i Søg](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Gruppering med parenteser
Du kan gruppere dele af forespørgsel for at opnå logiske isolationsniveauet, især sammen med booleske operatorer ved at gruppere med parenteser.

1. Angiv i søgefeltet, `name:product AND (tags:cycles AND objectType:table)` og tryk på **ENTER**.
2. Bekræft, at du ser kun tabellen **Product** i søgeresultaterne.

    ![Azure datakatalog – gruppering søgning](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Sammenligningsoperatorer
Med sammenligningsoperatorer, kan du bruge sammenligninger end lige til egenskaber, der har numeriske dato og klokkeslæt-datatyper.

1. Angiv i søgefeltet, `lastRegisteredTime:>"06/09/2016"`.
2. Fjern filteret **tabel** under **Objekttype**.
3. Tryk på **ENTER**.
4. Bekræft, at du ser **produkt**, **ProductCategory**, **ProductDescription**og **ProductPhoto** tabeller og den AdventureWorks2014 database, der er registreret i søgeresultaterne.

    ![Azure datakatalog – sammenligning søgeresultater](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Se, [hvordan at finde dataaktiver](data-catalog-how-to-discover.md) detaljerede oplysninger om opdager dataaktiver og [Datakatalogsøgning syntaksreference](https://msdn.microsoft.com/library/azure/mt267594.aspx) for søgning syntaks.

## <a name="annotate-data-assets"></a>Anmærke over dataaktiver
I denne opgave skal du bruge portalen Azure datakatalog til at oprette anmærkninger (Tilføj oplysninger som beskrivelser, mærker eller eksperter) dataaktiver du tidligere har tilmeldt dig i kataloget. Anmærkningerne tillæg og forbedre de strukturelle metadata, der er hentet fra datakilden under registrering og gør det meget lettere at finde og forstå dataaktiver.

I denne opgave skal anmærke du et enkelt data aktiv (ProductPhoto). Du føjer et fuldt navn og beskrivelse til ProductPhoto data aktiv.  

1.  Gå til [startsiden for Azure datakatalog](https://www.azuredatacatalog.com) og søge med `tags:cycles` til at finde de dataaktiver, du har registreret.  
2. Klik på **ProductPhoto** i søgeresultaterne.  
3. Angive **produktbilleder** til **Fulde navn** og **produkt fotos for marketingmaterialer** til **Beskrivelse**.

    ![Azure datakatalog – ProductPhoto beskrivelse](media/data-catalog-get-started/data-catalog-productphoto-description.png)

    **Beskrivelse** hjælper andre med at finde og forstå årsagen og hvordan du bruger de markerede data aktiv. Du kan også tilføje flere mærker og få vist kolonner. Nu kan du prøve søgning og filtrering til at finde dataaktiver ved hjælp af de beskrivende metadata, du har føjet til kataloget.

Du kan også gøre følgende på denne side:

- Tilføj eksperter for aktivet data. Klik på **Tilføj** i området **eksperter** .
- Føje mærker på niveauet for datasættet. Klik på **Tilføj** i området **mærker** . Et mærke kan være et brugermærke eller mærket ordliste. Standard Edition af datakatalog indeholder en business-ordliste, der hjælper katalog administratorer definere en central del af virksomhedens taksonomi. Katalog brugere kan derefter anmærke dataaktiver med ordliste. Se, [hvordan du konfigurerer Business Ordliste til omfattet mærkning](data-catalog-how-to-business-glossary.md) kan finde flere oplysninger
- Føje mærker på niveauet for kolonne. Klik på **Tilføj** under **mærker** for den kolonne, du vil anmærke.
- Tilføj beskrivelse på niveauet for kolonne. Angiv **Beskrivelse** for en kolonne. Du kan også få vist de beskrivelse metadata, der er hentet fra datakilden.
- Tilføj **anmode om adgang** oplysninger, der viser brugere, hvordan du anmoder om adgang til data aktiv.

    ![Azure datakatalog – føje mærker, beskrivelser](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)

- Vælg fanen **dokumentation** og levere dokumentation for aktivet data. Du kan bruge din datakatalog som et indhold lager med Azure datakatalog dokumentation til at oprette en komplet fortælling af dine dataaktiver.

    ![Azure datakatalog – dokumentation fanen](media/data-catalog-get-started/data-catalog-documentation.png)


Du kan også føje en anmærkning til flere dataaktiver. Du kan for eksempel vælge alle de, dataaktiver du registreret og angive en ekspert til dem.

![Azure datakatalog – anmærke flere dataaktiver](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Azure datakatalog understøtter en mængden kilde tilgang til anmærkninger. En hvilken som helst datakatalog brugeren kan tilføje mærker (bruger eller ordliste), beskrivelser og andre metadata, så alle brugere med et perspektiv på et aktiv data og dets anvendelse kan have pågældende perspektiv indsamles og er tilgængelig for andre brugere.

Se, [hvordan du anmærke dataaktiver](data-catalog-how-to-annotate.md) detaljerede oplysninger om anmærkninger dataaktiver.

## <a name="connect-to-data-assets"></a>Oprette forbindelse til over dataaktiver
I denne opgave skal åbne du dataaktiver i et værktøj til integrerede-klient (Excel) og et ikke-integreret værktøj (SQL Server Management Studio) ved hjælp af oplysninger om forbindelsen.

> [AZURE.NOTE] Det er vigtigt at huske, at Azure datakatalog ikke giver dig adgang til den virkelige datakilde – blot gør det lettere for dig at registrere og forstå den. Når du opretter forbindelse til en datakilde, du vælger klientprogrammet bruger dine Windows-legitimationsoplysninger eller beder dig om legitimationsoplysninger efter behov. Hvis du ikke har tidligere har fået adgang til datakilden, skal du have tildelt adgang, før du kan oprette forbindelse.

### <a name="connect-to-a-data-asset-from-excel"></a>Oprette forbindelse til en aktiv data fra Excel

1. Vælg **produkt** fra søgeresultater. Klik på **Åbn i** på værktøjslinjen og **Excel**.

    ![Azure datakatalog – oprette forbindelse til data aktiv](media/data-catalog-get-started/data-catalog-connect1.png)
2. Klik på **Åbn** i download pop op-vinduet. Denne oplevelse kan variere efter browseren.

    ![Azure datakatalog – hentede Excel-forbindelsesfil](media/data-catalog-get-started/data-catalog-download-open.png)
3. Klik på **Aktiver**i vinduet **Sikkerhedsmeddelelse fra Microsoft Excel** .

    ![Azure datakatalog – Excel sikkerhed pop op-vindue](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. Beholde standardindstillingerne i dialogboksen **Importdata** , og klik på **OK**.

    ![Azure datakatalog – Excel importdata](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. Få vist datakilden i Excel.

    ![Azure datakatalog – produkttabellen i Excel](media/data-catalog-get-started/data-catalog-connect2.png)

I denne opgave skal forbindelse du til dataaktiver opdaget ved hjælp af Azure datakatalog. Med portalen Azure datakatalog kan du oprette forbindelse direkte ved hjælp af integreret i menuen **Åbn i** klientprogrammerne. Du kan også oprette forbindelse med et program, du vælger ved hjælp af forbindelsesoplysningerne placering inkluderet i aktiv metadata. Du kan for eksempel bruge SQL Server Management Studio til at oprette forbindelse til AdventureWorks2014 databasen for at få adgang til dataene i de dataaktiver, der er registreret i dette selvstudium.

1. Åbn **SQL Server Management Studio**.
2. Angiv servernavnet fra ruden **Egenskaber** i portalen Azure datakatalog i dialogboksen **Opret forbindelse til serveren** .
3. Brug relevante godkendelse og legitimationsoplysninger for at få adgang til data aktiv. Hvis du ikke har adgang, kan du bruge oplysninger i feltet **Anmode om adgang** til at få den.

    ![Azure datakatalog--anmode om adgang](media/data-catalog-get-started/data-catalog-request-access.png)

Klik på **Vis forbindelsesstrenge** for at få vist og kopiere ADF.NET, ODBC og OLEDB strenge til Udklipsholder til brug i dit program.

## <a name="manage-data-assets"></a>Administrere dataaktiver
I dette trin skal se du, hvordan du konfigurerer sikkerheden for dine dataaktiver. Datakatalog giver ikke brugere adgang til selve dataene. Ejeren af datakilden styrer dataadgang.

Du kan bruge datakatalog at registrere datakilder og få vist de metadata, der er relateret til de kilder, der er registreret i kataloget. Der kan være situationer, men hvor datakilder skal kun være synlige til bestemte brugere eller til medlemmer af bestemte grupper. Du kan bruge datakatalog overtage ejerskabet af registrerede dataaktiver i kataloget for disse scenarier, og at styre, derefter synligheden af aktiverne, du ejer.

> [AZURE.NOTE] Administrationsfunktioner, der er beskrevet i denne opgave findes kun i Standard Edition af Azure-Datakataloget, ikke i den gratis version.
I Azure datakatalog kan du overtage ejerskabet af dataaktiver, tilføjes dataaktiver samtidig ejere og angive synligheden af dataaktiver.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Overtage ejerskabet af dataaktiver og begrænse synlighed

1. Gå til [Azure datakatalog startsiden](https://www.azuredatacatalog.com). Angiv i tekstfeltet **Søg** `tags:cycles` og tryk på **ENTER**.
2. Klik på et element på listen over resultater og **Tage ejerskab** på værktøjslinjen.
3. I sektionen **Administration** i panelet **Egenskaber** skal du klikke på **Tage ejerskab**.

    ![Azure datakatalog – overtage ejerskabet](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. Vælg **ejere og disse brugere** i sektionen **synlighed** for at begrænse synlighed, og klik på **Tilføj**. Angiv bruger mailadresser i tekstfeltet, og tryk på **ENTER**.

    ![Azure datakatalog – Begræns adgang](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Fjerne over dataaktiver

I denne opgave skal bruge du portalen Azure datakatalog fjerne Vis data fra registrerede dataaktiver og slette dataaktiver fra kataloget.

Du kan slette et enkelt aktiv eller slette flere aktiver i Azure-Datakataloget.

1. Gå til [Azure datakatalog startsiden](https://www.azuredatacatalog.com).
2. Angiv i tekstfeltet **Søg** `tags:cycles` , og klik på **ENTER**.
3. Markér et element på listen over resultater, og klik på **Slet** på værktøjslinjen som vist på følgende billede:

    ![Azure datakatalog – Slet gitterelement](media/data-catalog-get-started/data-catalog-delete-grid-item.png)

    Hvis du bruger listevisningen, er markeringen i afkrydsningsfeltet til venstre for elementet, som vist på følgende billede:

    ![Azure datakatalog – Slet element](media/data-catalog-get-started/data-catalog-delete-list-item.png)

    Du kan også vælge flere dataaktiver og slette dem, som vist på følgende billede:

    ![Azure datakatalog – slette flere dataaktiver](media/data-catalog-get-started/data-catalog-delete-assets.png)


> [AZURE.NOTE] Standardfunktionsmåden for kataloget er at tillade brugerne at registrere en datakilde og for at tillade brugerne at slette alle data aktiv, der er registreret. Administrationsfunktioner, der er inkluderet i Standard Edition af Azure-Datakataloget giver yderligere indstillinger til at overtage ejerskabet af aktiver begrænse hvem der kan opdage aktiver, og begrænse, hvem der kan slette aktiver.


## <a name="summary"></a>Oversigt

I dette selvstudium set du væsentlige funktionerne i Azure datakatalog, herunder registrering, anmærkninger, opdager og administrere enterprise dataaktiver. Nu hvor du har færdiggjort selvstudiet, er det tid at komme i gang. Du kan begynde i dag, ved at registrere de datakilder, du og dit team er afhængige af, og ved at invitere kolleger til at bruge kataloget.

## <a name="references"></a>Referencer

- [Sådan registreres over dataaktiver](data-catalog-how-to-register.md)
- [Hvordan du kan finde over dataaktiver](data-catalog-how-to-discover.md)
- [Hvordan du kan oprette anmærkninger i over dataaktiver](data-catalog-how-to-annotate.md)
- [Sådan dokument dataaktiver](data-catalog-how-to-documentation.md)
- [Sådan oprettes forbindelse til over dataaktiver](data-catalog-how-to-connect.md)
- [Sådan administreres over dataaktiver](data-catalog-how-to-manage.md)

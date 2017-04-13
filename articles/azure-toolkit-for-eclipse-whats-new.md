<properties
    pageTitle="Hvad er nyt i Azure værktøjerne til Eklipse"
    description="Få mere at vide om de nyeste funktioner i Azure-værktøjskassen til Eklipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh694270.aspx -->

# <a name="whats-new-in-the-azure-toolkit-for-eclipse"></a>Hvad er nyt i Azure værktøjerne til Eklipse

## <a name="azure-toolkit-for-eclipse-releases"></a>Azure-værktøjskassen til Eklipse versioner

I denne artikel indeholder oplysninger om de forskellige versioner og seneste opdateringer til Azure-værktøjskassen til Eklipse.

> [AZURE.NOTE] Der er også en Azure-værktøjskassen til IntelliJ IDE. Du kan finde flere oplysninger [Azure-værktøjskassen til IntelliJ].

### <a name="august-26-2016"></a>August 26 2016

Azure-værktøjskassen til Eklipse - August 2016 version omfatter følgende forbedringer:

* **Brugerdefinerede JDK Salgsdistributioner**. Azure-værktøjskassen til Eklipse understøtter nu angive og implementere en vilkårlig JDK version til din Azure WebApp objektbeholder:
  - Ud over de JDKs, der leveres af Azure, kan du også vælge fra et bredt udvalg af Zulu OpenJDK versioner gøres tilgængelige på Azure af Azul Systems.
  - Du kan også angive dine egne JDK fordeling, hvis du overfører som en ZIP-fil til kontoen lagerplads.
* **Forbedringer til Azure Stifindervisning**:
  - Understøttelse af virtuelt administration ved hjælp af Azures nye ressourcestyring model: Du kan listen, oprette og slette ressource manager-baserede virtuelle maskiner uden at forlade IDE-enheden.
  - Understøttelse af lagerplads blob kontoadministration ved hjælp af Azures Ressourcestyring, som er et supplement den eksisterende funktionalitet til administration af "klassisk" lagerplads konti.
* **Microsoft JDBC Driver 6.0 til SQL Server**. Denne opdatering indeholder den nyeste JDBC driver til Microsoft SQL Server (v6.0), som er nu medtaget som et bibliotek, kan du nemt føje til dine Java-projekter, og erstatte den gamle version.

### <a name="june-29-2016"></a>29 juni 2016

Azure-værktøjskassen til Eklipse - juni 2016 version omfatter følgende forbedringer:

* **Java 8 krav**. Azure-værktøjskassen til Eklipse kræver nu Java 8, selvom dette krav gælder kun for værktøjerne - programmerne kan fortsætte med at bruge alle versioner af Java, der understøttes af Azure.
* **Understøttelse af den seneste Java JDKs**. De seneste versioner af Java JDKs understøttes nu af Azure-værktøjskassen til Eklipse.
* **Understøttelse af Azure SDK v2.9.1**. Den seneste version af Azure SDK er nu den mindste foreløbig nødvendige til Azure-værktøjskassen til Eklipse.
* **Integreret eksempler**. Azure-værktøjskassen til Eklipse indeholder nu flere eksempler på programmer til at hjælpe udviklere med at komme i gang.
* **Integration af HDInsight værktøj**. Azure's HDInsight værktøjer er nu samlet med Azure-værktøjskassen til Eklipse. Du kan finde yderligere oplysninger finder [HDInsight værktøjer plug-in til Eklipse].
* **Remote fejlfinding af Java Webapps**. Azure-værktøjskassen til Eklipse understøtter nu ekstern fejlfinding af Java webapps på Azure App Service.
* **Understøttelse af den Eklipse Luna udgave.** Den nye minimumversion for påkrævet Eklipse IDE er Luna.

### <a name="april-12-2016"></a>April 12 2016

Azure-værktøjskassen til Eklipse - April 2016 version omfatter følgende forbedringer:

* **Understøttelse af Azure SDK v2.9.0**. Den seneste version af Azure SDK er nu den mindste foreløbig nødvendige til Azure-værktøjskassen til Eklipse.
* **Diverse brugervenligheden, tilgængelighed og ydeevne forbedringer, der er relateret til Azure online support**. Et antal ydeevne optimeringer i hvordan værktøjerne kommunikerer med Azure resultatet i en hurtigere brugergrænseflade.
* **Mulighed for at slette en eksisterende webprogram objektbeholder i Azure fra i Eklipse**. Azure-værktøjskassen til Eklipse nu kan du slette en eksisterende Azure Web objektbeholder uden at forlade Eklipse.

### <a name="march-7-2016"></a>Marts 7 2016

Azure-værktøjskassen til Eklipse - marts 2016 version omfatter følgende forbedringer:

* **Understøttelse af hurtig installation af lette Java-programmer**. Azure-værktøjskassen til Eklipse understøtter nu den hurtige implementering af lette Java-programmer på Azure Web App beholdere, så anvender Java programmer nu tager sekunder i stedet for minutter.
* **Understøttelse af håndtering af Web App med Azure Stifindervisning**. Azure Stifindervisning i værktøjerne understøtter nu til listen over, start og Stop Azure Web Apps.
* **Opdateret Tomcat, Jetty, og Zulu OpenJDK**. Azure-værktøjskassen til Eklipse understøtter opdaterede versioner af Tomcat, Jetty og Zulu OpenJDK til Java-installationer til Azure-skytjenester.

### <a name="january-4-2016"></a>Januar 4 2016

Azure-værktøjskassen til Eklipse - januar 2016 version omfatter følgende forbedringer:

* **Understøttelse af Zulu OpenJDK opdateringer**. Se [Azul systemer webside til Zulu OpenJDK]kan finde flere oplysninger.
* **Opdateret Tomcat og Jetty salgsdistributioner**. Jetty og Tomcat fordelingerne, som er tilgængelig på Microsoft Azure til brug med Azure-værktøjskassen til Eklipse er blevet opdateret.
* **Funktionen fungerer ensartet mellem Eklipse og IntelliJ programudviklingsværktøjer til Azure**. Azure-værktøjskassen til Eklipse og [Azure-værktøjskassen til IntelliJ] understøtter nu det samme sæt af funktioner.

### <a name="september-1-2015"></a>1 september 2015

Azure-værktøjskassen til Eklipse - September 2015 version omfatter følgende forbedringer:

* **Understøttelse af Zulu OpenJDK opdateringer**. Se [Azul systemer webside til Zulu OpenJDK]kan finde flere oplysninger.
* **Opdateret Tomcat og Jetty salgsdistributioner**. Jetty og Tomcat fordelingerne, som er tilgængelig på Microsoft Azure til brug med Azure-værktøjskassen til Eklipse er blevet opdateret. (Disse salgsdistributioner kan udviklere til at oprette hurtig udvikling og teste projekter med Azure-værktøjskassen til Eklipse.
* **Understøttelse af opdateres automatisk Tomcat og Jetty referencer**. Ud over de bestemte versioner af Tomcat og Jetty der er tilgængelige på Azure, udviklere kan nu henvise til en fordeling, kaldes det "seneste (automatisk opdateret)", som opdateres automatisk til den seneste fordelingen af hver overordnet version af Jetty eller Tomcat næste gang din rolle forekomster er flyttet til papirkurven. (Genbrug sker automatisk, men udviklere kan manuelt udløse genbrug via Azure-portalen). Den nye funktion betyder, at udviklere ikke behøver at geninstallere deres program tilladelse til at kunne har deres serversoftware opdateret. (
*  Denne funktionalitet er i øjeblikket kun beregnet til udvikling og test formål eller ikke er vigtige programmer, og der ikke anbefales til fremstilling.)
* **Azure Stifindervisning til BLOB, køer og tabeller i Azure-lager**. Dette giver mulighed for udviklere til at udføre en række almindelige opgaver med deres lagerplads elementer direkte fra Eklipse IDE. For eksempel: slette, overfører eller henter BLOB.

### <a name="august-1-2015"></a>1 august 2015

Azure-værktøjskassen til Eklipse - August 2015 version omfatter følgende forbedringer:

* **Programmet indsigt Instrumentation Key Management**. Denne opdatering kan du få fat, oprette og administrere dine programmet indsigt instrumentation nøgler direkte fra Eklipse IDE.
* **Microsoft JDBC Driver 4.1 til SQL Server**. Denne opdatering indeholder support til den nyeste JDBC driver til Microsoft SQL Server.
* **Version 2.7 af Azure SDK**. Denne den seneste opdatering til Azure SDK er den nye foreløbig nødvendige til værktøjerne når installeret på Windows. (Bemærk dette ikke er nødvendigt på Windows-operativsystemer).
* **Understøttelse af Zulu OpenJDK v7 opdatere**. Se [Azul systemer webside til Zulu OpenJDK]kan finde flere oplysninger.

### <a name="may-1-2015"></a>1 maj 2015

Azure-værktøjskassen til Eklipse - maj 2015 version omfatter følgende forbedringer:

* **Forbedret Server markering brugergrænseflade**. Denne version forenkler brugen af værktøjerne på Windows-operativsystemer.
* **Understøttelse af Maven projekter**. Denne version understøtter Maven projekter som programmer, som kan distribuere værktøjerne til Azure og konfigurere programmet indsigt.
* **Version 2.6 af Azure SDK**. Denne den seneste opdatering til Azure SDK er den nye foreløbig nødvendige til værktøjerne når installeret på Windows. (Bemærk dette ikke er nødvendigt på Windows-operativsystemer).
* **Opgradering af installationen i stedet for genudgive**. Hvis du udgiver et installationsprojekt, når den tidligere version er allerede direkte, bruger værktøjerne nu Azures installation opgradering funktionalitet i stedet for at lukke den forrige installation og genudgive fra bunden, som den gjorde tidligere. Dette gør det muligt for dine skytjeneste til at køre uden forstyrrelser, når det er muligt, hjælp til at opnå høj tilgængelighed selv under en opdatering, og gør det igen publicering hurtigere.
* **Support til den seneste Zulu OpenJDK v8 - opdatere 40**. Se [Azul systemer webside til Zulu OpenJDK]kan finde flere oplysninger.

### <a name="march-9-2015"></a>9 marts 2015

Azure-værktøjskassen til Eklipse - marts 2015 version omfatter følgende forbedringer:

* **Support til Mac, Ubuntu og yderligere Linux typer**. Denne version af Azure-værktøjskassen til Eklipse tilføjer understøttelse af Mac OS og flere Unix-platforme, så udviklere kan installere værktøjerne for at oprette, konfigurere og udgive Java projekter på Azure Cloud Services (PaaS) fra Eklipse, der kører på operativsystemer end Windows.

>[AZURE.NOTE] Denne funktion er i Vis udskrift, og det anbefales ikke til brug i fremstilling miljøer. Der er ingen kundesupport serviceaftale (SLA), men alle feedback vi sætter pris og fremmes.

* **Ny programmet indsigt plug-in'en**. Udviklere kan nu konfigurere automatiske server telemetri ved hjælp af programmet viden om Azure.
* **Vant-baseret kommandolinje installation automatisering**. Denne funktion gør det muligt for udviklere til at automatisere udgivelse til nyere versioner af deres ved hjælp af vant uden for Eklipse installationer. En allerede oprettet script er automatisk konfigureret til et projekt, efter første gang det installeres fra Eklipse, og efterfølgende installationer kan bruge scriptet til at automatisere fuldt installationer via kommandolinjen kun.
* **Tilgængelighed af tomcat og Jetty på Azure til nemmere og hurtigere installation**. Udviklere kan nu henvise til forskellige Tomcat og Jetty versioner, der er tilgængelige på Azure, direkte i stedet for at have til at overføre en Java-server til deres konto (eller via værktøjerne), så det er ikke nødvendigt at overføre en Java server for hurtig og Introduktion scenarier.
* **Genvej til publicering Java Onlines til Azure skytjenester**. Hvis du vil reducere learning kurven for enkel udvikling og test scenarier, kan udviklere nu publicere Java-programmer mere direkte til Azure. I stedet for at de skal gå gennem processen med at oprette og konfigurere et projekt til Azure-installation, som skal installeres programmer med en standardforekomst af Tomcat v8 og Zulu JVM (OpenJDK).

### <a name="january-30-2015"></a>30 januar 2015

Azure-værktøjskassen til Eklipse - januar 2015 version omfatter følgende forbedringer:

* **Understøttelse af IBM® WebSphere® programmet Server frit Core**. Denne version tilføjer IBM WebSphere programmet Server frit Core på listen over understøttede webserverne hvorfra værktøjerne er i stand til at installere på Azure. Den seneste tilføjelse udvider den aktuelle liste over application servere, der understøttes &quot;out box&quot; ved værktøjskassen, som allerede er med forskellige versioner af Tomcat, Jetty, JBoss og GlassFish.
* **Optagelse af programmet indsigt SDK**. Dette netop udgivet klient-API-bibliotek (v0.9.0) er nu en del af funktionen Pakke til Azure-biblioteker til Java.
* **Opdateret pakke til Azure-biblioteker til Java**. Opdateringen omfatter Azure biblioteker til Java v0.7.0 og Storage klient API v2.0.0 samt den nyligt udgivet programmet indsigt SDK v0.9.0.

### <a name="november-12-2014"></a>12 november 2014

Azure-værktøjskassen til Eklipse - November 2014-udgivelsen omfatter følgende forbedringer:

* **Understøttelse af Azure SDK 2,5**. Denne den seneste opdatering til Azure SDK er den nye foreløbig nødvendige til værktøjerne.
* **Understøttelse af opdateret version af Zulu OpenJDK v1.8, v1.7 og v1.6 pakker**. Se [Azul systemer webside til Zulu OpenJDK]kan finde flere oplysninger.
* **Understøttelse af de nye Standard D størrelser til skytjenester**, hvilke tilbud øget ydeevne og ekstra hukommelsesressourcer. Se [virtuelt og skyen Service størrelser til Azure]kan finde flere oplysninger.

### <a name="october-17-2014"></a>17 oktober 2014

Azure-værktøjskassen til Eklipse - oktober 2014-udgivelsen omfatter følgende forbedringer:

* **Forbedring af ydeevnen i Udgiv til skyen scenarier**. Indlæsning af abonnementsoplysninger er meget hurtigere, når brugerne har flere abonnementer og lagerplads konti.
* **Understøttelse af opdaterede version af Zulu OpenJDK v1.8 installationspakken**. Se [Azul systemer webside til Zulu OpenJDK]kan finde flere oplysninger.
* **Understøttelse af fjernelse af ældre versioner af 3 part JDKs**. Frarådede JDK-pakker, ikke længere vises i rullemenuen for nye installation projekter. Eksisterende projekter, der refererer til sig frarådede JDK pakker fortsat at kunne gøre så gang der, men det anbefales at opgradere sådanne projekter for at er afhængige af senest.
* **Opdateret version af funktionen Pakke til Azure biblioteker for Java klient API-bibliotek**. Se [Microsoft Azure klient API]kan finde flere oplysninger.
* **Fejlrettelser.** Denne version indeholder et antal diverse fejlrettelser der er baseret på Brugerrapporter og test.

### <a name="august-5-2014"></a>5 august 2014

Azure-værktøjskassen til Eklipse - August 2014-udgivelsen omfatter følgende forbedringer

* **Understøttelse af Azure SDK 2.4.** Ældre versioner af Eklipse værktøjerne fungerer ikke med denne nyligt frigivne SDK.
* **Opdaterede versioner af v1.6 Zulu OpenJDK 1.7 og v1.8 pakker.** Se [Azul systemer webside til Zulu OpenJDK]kan finde flere oplysninger.
* **Opdaterede version af Azure biblioteker pakke til Java klient API bibliotek.** Se [Microsoft Azure klient API]kan finde flere oplysninger.
* **Understøttelse af seneste publicere indstillinger-filformat.** Understøttelse af blev føjet til version 2.0 af publiceringsindstillinger-filformatet.
* **Arkitektonisk ændringer bag Udgiv til skyen funktion.** Værktøjerne nu bruge nyligt frigivne Microsoft Azure-klienten API for Java til understøttelsen publicere i skyen.
* **Fejlrettelser.** Denne version indeholder et antal bruger anmodet om fejlrettelser.

### <a name="june-12-2014"></a>Juni 12 2014

Azure-værktøjskassen til Eklipse - juni 2014-udgivelsen er en mindre vedligeholdelse opdatering, som indeholder følgende forbedringer:

* **Understøttelse af Zulu OpenJDK pakke v1.8.** Se [Azul systemer webside til Zulu OpenJDK]kan finde flere oplysninger.
* **Opdaterede versioner af Zulu OpenJDK v1.6 og 1,7 pakker.** Se [Azul systemer webside til Zulu OpenJDK]kan finde flere oplysninger.
* **Opdaterede version af Azure biblioteker pakke til Java klient API bibliotek.** Se [Microsoft Azure klient API]kan finde flere oplysninger.
* **Fejlrettelser.** Denne version indeholder et antal bruger anmodet om fejlrettelser.

### <a name="april-4-2014"></a>4 april 2014

Azure-plug-in til Eklipse - April 2014-udgivelsen har udgivet. Dette er en opdatering, der følger version af Azure SDK 2.3, som er en foreløbig nødvendige og hentes automatisk, når du installerer plug-in'et. Denne opdatering indeholder nye funktioner, fejlrettelser og nogle forbedringer af brugervenligheden feedback-driven da februar 2014 få vist et eksempel:

* **Understøttelse af version af Azure SDK 2.3.** Azure-plug-in til Eklipse - April 2014-udgivelsen kræver Azure SDK 2.3. Når du bruger den nye Plug-in, hvis du ikke allerede har Azure SDK 2.3, bliver du bedt om at tillade dens installation. Brug ikke Azure SDK 2.3 med tidligere versioner af plug-in'et.
* **Opgradering af programmer uden komplet pakke installation.** Når du installerer Java-programmer, der er en del af dit projekt, overfører plug-in'et nu automatisk dem til din konto markerede lagerplads så du kan opdatere og Papirkurv rolle forekomster for at installere de seneste programmet bit uden at skulle genopbygge og geninstaller hele pakken.
* **Tomcat 8 er nu en genkendte application server.** Hvis du vælger en Tomcat 8 installationsmappe på computeren under fanen **Server** i dialogboksen **Azure installation Project** , plug-in'et nu automatisk registrerer det og kunne installere Tomcat 8 i en automatiseret måde, som svarer til de ældre versioner af Tomcat, der allerede findes på listen.
* **Azul Zulu OpenJDK pakke opdateringer: v1.7 opdatering 51 og v1.6 opdatere 47.** Effektive med denne version, Azul System Zulu Åbn JDK v7 pakke opdatering 51 er tilgængelig. Zulu Åbn JDK v6 pakker start også bliver tilgængelige, begynder med opdatering 47. Disse opdateringer er ud over den tidligere var tilgængelige Zulu Åbn JDK v7 pakke opdateringer 45, 40 og opdatere 25.
* **Understøttelse af A8 og A9 Microsoft Azure Virtual Machine størrelse.** Nu kan du installere en skybaseret tjeneste til høj hukommelse A8 og A9 virtuelt størrelser. Se [virtuelt og skyen Service størrelser til Azure]kan finde flere oplysninger om disse VM størrelser.
* **Automatisk omdirigering fra HTTP til HTTPS til SSL-aktiverede roller.** Når din skybaseret tjeneste indeholder kun HTTPS roller, hvis brugeranmodningen angiver HTTP, omdirigerer den automatisk til HTTPS. Der er ikke nødvendigt at oprette en separat rolle for at håndtere HTTP-anmodninger.
* **Express Emulator, der bruges til lokale emulering.** Azure Express emulatoren bruges nu som emulatoren, når du foretager fejlfinding programmerne lokalt.
* **Azure er rebrandet som Microsoft Azure.** Brugergrænsefladen skærme afspejle nu, Azure er rebranded og ikke længere kaldet Azure.

### <a name="february-6-2014"></a>6 februar 2014

Azure plug-in'et til Eklipse – februar 2014 Preview har udgivet. Denne opdatering indeholder nye funktioner, fejlrettelser og nogle forbedringer af brugervenligheden feedback-driven, da oktober 2013 Preview:

* **Understøttelse af SSL overføre.** Secure Sockets Layer (SSL) overføre er blevet tilføjet som en funktion, så du kan nemt aktivere understøttelse af Hypertext Transfer Protocol Secure (HTTPS) i din Java-installation på Azure, uden at du skal konfigurere SSL i din Java application server. Dette er især relevant i Session forbindelse og/eller godkendte kommunikationsscenarier. For eksempel, når du bruger filteret Access ACS (Control Service), som allerede understøttes af værktøjerne. Se [SSL overføre] og [Sådan Brug SSL overføre]kan finde flere oplysninger.
* **GlassFish 4 er nu en genkendte application server.** Hvis du vælger en GlassFish 4 installationsmappe på computeren under fanen **Server** i dialogboksen **Azure installation Project** , plug-in'et nu automatisk registrerer det og kunne installere GlassFish Office Server Extensions 4 i et automatiseret samme måde, som den GlassFish Office Server Extensions 3 version allerede findes på listen.
* **Azul Zulu OpenJDK pakke opdatering 45.** Effektive med denne version, Azul System Zulu (Åbn JDK v7 pakke) opdatering 45 er nu tilgængelig; Dette er ud over den tidligere tilgængelige opdatering 40 og Opdater 25.
* **Support til 'automatisk' til privat slutpunkt porte.** Du kan angive en privat port til automatisk til input slutpunkter og interne slutpunkter for at lade Azure tildele en port til pågældende slutpunkt automatisk. Tidligere kan du kun tildele et bestemt portnummer.
* **Understøttelse af tilpasning af navnet på certifikatet (CN) i selvsigneret certifikat oprettelse af Brugergrænsefladen.** Tidligere blev samme faste navn brugt til alle nye certifikater; Nu kan du angive certifikatnavnet på din egen til at skelne mellem flere certifikater i portalen Azure, der bruges til forskellige formål.
* **Azure værktøjslinjen:** Værktøjslinjen Azure har en opdateret med følgende ændringer: 
    * ![][ic710876]Dette ikon blev føjet til **Azure installationsprojekt**.
    * ![][ic710877]Dette ikon blev tilføjet som en genvej til oprettelse af dialogboksen selvsigneret certifikat.
* **Understøttelse af A5 Azure Virtual Machine størrelse.** Nu kan du installere en skybaseret tjeneste til høj hukommelsen A5 virtuelt størrelse. Se [virtuelt og skyen Service størrelser til Azure]kan finde flere oplysninger om denne VM størrelse.
* **Understøttelse af Microsoft Windows Server 2012 R2.** Nu kan du vælge Windows Server 2012 R2 som operativsystemet skyen.

### <a name="october-22-2013"></a>22 oktober 2013

Azure plug-in'et til Eklipse – oktober 2013 Preview har udgivet. Denne opdatering indeholder nye funktioner, fejlrettelser og nogle forbedringer af brugervenligheden feedback-driven, da September 2013 Preview:

* **Understøttelse af version af Azure SDK 2.2.** Azure-plug-in til Eklipse - Preview oktober 2013 understøtter Azure SDK 2.2. Plug-in'et fungerer stadig med Azure SDK 2.1 og installeres automatisk Azure SDK 2.2, hvis du ikke allerede har mindst Azure SDK 2.1, der er installeret.
* **Azul Zulu OpenJDK pakke opdatering 40.** Som offentliggjort for September 2013 Gennemse plug-in nu aktiverer ved hjælp af en tredjepart har udleveret JDK direkte på Azure, uden at du skal overføre din egen JDK. I oktober 2013-versionen er Azul System Zulu (Åbn JDK v7 pakke) opdatering 40 nu tilgængelig; Dette er ud over oprindeligt udgivne opdateringen 25.
* **Skyen installation linket Log aktivitet.** I Azure aktivitet loggen, når din installation har statussen **udgivet**, kan du klikke på **udgivet** eftersom den er nu et link til din installation din installation åbnes i browseren. (Status for **Published** blev tidligere mærket af **kører**.)
* **Valg af target OS findes på Publicer tid.** Dialogboksen **Udgiv til Azure** indeholder et nyt felt, **Target OS**, som indeholder en mere synlig metode til at angive operativsystemet mål.
* **Automatisk overskrive forrige installation.** Dialogboksen **Udgiv til Azure** indeholder en nye afkrydsningsfelt **Overskriv tidligere installation**. Hvis denne indstilling er markeret, når din nye installation er udgivet den automatisk overskrive den forrige installation du ikke vil opleve &quot;409 konflikt&quot; problemer, når du udgiver på den samme placering uden første annullere publicering den forrige installation.
* **Jetty 9 er nu en genkendte application server.** Hvis du vælger en Jetty 9 installationsmappe på computeren under fanen **Server** i dialogboksen **Azure installation Project** , plug-in'et nu automatisk registrerer det og kunne installere Jetty 9 i en automatiseret måde, som svarer til de ældre versioner af Jetty allerede findes på listen.
* **Tilføje en rolle i kontekstmenuen for Project.** Genvejsmenuen **Azure** project indeholder nu et nyt menupunkt, **Tilføje rolle**, hvilket giver en hurtigere og mere synlig metode til at tilføje en ny rolle til projektet Azure.
* **En opdatering til funktionen Pakke til Azure biblioteker for Java-bibliotek.** Dette er baseret på 0.4.6-versionen af [Microsoft Azure klient API].

### <a name="september-25-2013"></a>25 september 2013

Azure plug-in'et til Eklipse – September 2013 Preview har udgivet. Denne opdatering indeholder nye funktioner, fejlrettelser og nogle forbedringer af brugervenligheden feedback-driven, da August 2013 Preview:

* **Mulighed for at installere pakken Azul Zulu OpenJDK tilgængelig på Azure.** En ny indstilling er blevet tilføjet, når du angiver JDK til brug med din installation af Azure. Bruger denne indstilling, kan du installere en tredjeparts JDK pakke direkte på Azure skyen, uden at overføre dine egne. Azul systemer leverer den første sådanne pakke kaldet Zulu, baseret på OpenJDK, som kan nu installeres ved hjælp af denne indstilling.
* **En opdatering til funktionen Pakke til Azure biblioteker for Java-bibliotek.** Dette er baseret på 0.4.5-versionen af [Microsoft Azure klient API].

### <a name="august-1-2013"></a>1 august 2013

Azure plug-in'et til Eklipse - August 2013 Preview har udgivet. Dette er en opdatering, der følger version af Azure SDK 2.1, som er en foreløbig nødvendige og hentes automatisk, når du installerer plug-in'et. Denne opdatering indeholder nye funktioner, fejlrettelser og nogle forbedringer af brugervenligheden feedback-driven, da juli 2013 Preview:

* **Fjernelse af indstillinger for at medtage de lokale JDK og lokale programserver som en del af installationspakken.** Hente JDK og programserver fra cloud storage under implementeringen er bedst at integrere disse komponenter i pakken, siden hente elementer resultaterne i mindre installation pakke, installation hurtigere og nemmere vedligeholdelse. Som et resultat, indstillinger for at medtage JDK og programserver i installationspakken er blevet fjernet. Eksisterende projekter, der er konfigureret til at medtage de lokale JDK og lokale programserver som en del af installationspakken konverteres automatisk til automatisk-Overfør JDK og programserver til skyen-lager.
* **Understøttelse af version af Azure SDK 2.1.** Azure plug-in'et til Eklipse - August 2013 Preview kræver Azure SDK 2.1. Brug ikke August 2013 preview sammen med tidligere versioner af Azure SDK, og brug ikke Azure SDK 2.1 med tidligere versioner af plug-in'en til Azure til Eklipse.
* **Understøttelse af den Eklipse Kepler version.** Relateret til dette, kræves det nye minimum Eklipse IDE Indigo. Azure-plug-in til Eklipse er ikke længere officielt afprøvet på Helios.

### <a name="july-3-2013"></a>3 juli 2013

Azure plug-in'et til Eklipse - juli 2013 Preview har udgivet. Denne opdatering indeholder nye funktioner, fejlrettelser og nogle forbedringer af brugervenligheden feedback-driven, da maj 2013 Preview:

* **Mulighed for at oprette en ny firmapost lagerplads.** Knappen **Ny** er blevet føjet til dialogboksen **Tilføj lagerplads konto** . Dette giver dig mulighed at oprette en lagerplads konto i Eklipse plug-in'en, uden at du skal logge på portalen Azure administration. (Du skal allerede har en Azure-abonnement for at bruge denne funktion). Du kan finde flere oplysninger om at oprette en ny konto lagerplads, se [til at oprette en ny konto lagerplads].
* **Ny &quot;(automatisk)&quot; indstilling for lagerplads konto, der bruges til automatisk installation af JDK og server, og for cachelagring af.** Når du bruger indstillingen **Overfør automatisk** til JDK og programserver, du kan nu angive **(automatisk)** for den URL-adresse og lager konto skal bruges, når overførsel af JDK og programserver, eller når du bruger Azure cachelagring. Disse funktioner Brug derefter automatisk den samme lagerplads konto som den, du vælger i dialogboksen **Udgiv til Azure** . [Oprette et Hej verden program til Azure i Eklipse] selvstudiet er blevet opdateret for at bruge indstillingen nye **(automatisk)** .
* **Mulighed for at angive Azure service slutpunkterne.** Angiv slutpunkterne tjeneste, der bestemmer, om dit program er installeret på og administreres af globale Azure-platformen, Azure drevet af 21Vianet i Kina eller en privat Azure-platformen. Du kan finde flere oplysninger [Azure Service slutpunkter].
* **Store installationer kan angive et lokalt lager ressource.** I tilfælde af, at din installation er for stor til at indgå i approot standardmappen, kan du nu angive et lokalt lager ressource som installation destinationen for din JDK og programserver. Du kan finde yderligere oplysninger finder [Implementering af store installationer].
* **Understøttelse af A6 og A7 Azure Virtual Machine størrelser.** Nu kan du installere en skybaseret tjeneste til høj hukommelse A6 og A7 virtuelt størrelser. Se [virtuelt og skyen Service størrelser til Azure]kan finde flere oplysninger om disse størrelser.
* **En opdatering til funktionen Pakke til Azure biblioteker for Java-bibliotek.** Dette er baseret på 0.4.4-versionen af [Microsoft Azure klient API].

### <a name="may-1-2013"></a>1 maj 2013

Azure-plug-in til Eklipse - kan 2013 Preview har udgivet. Dette er en overordnet opdatering tilhørende version af Azure SDK 2.0, som er en foreløbig nødvendige og hentes automatisk, når du installerer plug-in'et. Denne version omfatter nye funktioner, fejlrettelser og nogle forbedringer af brugervenligheden feedback-driven, da februar 2013 Preview:

* **Automatisk overførsel af JDK og programserver til og installation fra Azure-lager.** En ny funktion, der automatisk overfører de valgte JDK og programserver, når det er nødvendigt, til en bestemt Azure lagerplads konto og installerer disse komponenter derfra i stedet for integrering af i-installationspakken eller har bruger Overfør derefter manuelt. Denne ofte anmodede funktion kan forbedre nemmere at implementere JDK og server komponenterne, især for uerfarne brugere. Se [oprette et Hej verden program til Azure i Eklipse]for en gennemgang, der bruger disse indstillinger.
* **Centraliserede lagerplads konto registrering og muligheden for at reference lagerplads-konti flere nemt (via et kontrolelement i rullemenuen).** Dette gælder for flere funktioner, der er afhænger af lagerplads, som JDK og server-komponent installation samt cachelagring. Få vist [Azure lagerplads konto liste]kan finde flere oplysninger.
* **Forenklet fjernadgang konfiguration i skyen guiden Udgiv til.** Alt, du skal gøre, er skrift i et brugernavn og adgangskode for at aktivere fjernadgang eller lade feltet være tomt for at bevare fjernadgang til deaktiveret.
* **En opdatering til funktionen Pakke til Azure biblioteker for Java-bibliotek.** Dette er baseret på 0.4.2-versionen af [Microsoft Azure klient API].
* **Understøttelse af sticky sessioner på Windows Server 2012.** Tidligere arbejdet sticky sessioner kun på Windows Server 2008 R2, nu både i skyen operativsystem destinationer support session forbindelse.
* **Pakke Overfør forbedring af ydeevnen.** Selvom JDK og programserver er integreret i installationspakken, Overfør del af installationsprocessen kan være cirka to gange som fast i forhold til tidligere versioner.

### <a name="february-8-2013"></a>8 fra februar 2013

Azure plug-in'et til Eklipse – februar 2013 Preview har udgivet. Dette er en mindre opdatering, som omfatter fejlrettelser, feedback-driven brugervenligheden forbedringer og nye funktioner, da November 2012 få vist et eksempel:

* Support til implementering af JDKs, programmet-servere og vilkårlig andre komponenter fra offentlig eller privat Azure blob storage overførsler i stedet for at medtage dem i installationspakken, når du installerer til skyen.
* Mulighed for at ændre den rækkefølge, hvori brugerdefinerede komponenter i en rolle behandles, ved tilføjelse af knapperne **Flyt op** og **Flyt ned** i afsnittet **komponenter** i **Azure rolleegenskaber**.
* En opdatering til biblioteket **pakke til Azure-biblioteker til Java** , baseret på 0.4.0-versionen af [Microsoft Azure klient API].

### <a name="november-5-2012"></a>5 november 2012

Azure plug-in'et til Eklipse - November 2012 Preview har udgivet. Dette er en overordnet opdatering, som indeholder en række nye funktioner, samt yderligere fejlrettelser og forbedringer af brugervenligheden feedback-driven, da September 2012 få vist et eksempel:

* Understøttelse af Microsoft Windows Server 2012 som operativsystemet skyen.
* Understøttelse af Azure samtidig placeret cachelagring understøttelse af memcached klienter.
* Optagelse af Apache Qpid JMS klientbiblioteker til at drage fordel af Azure AMQP-baserede meddelelser.
* En forbedret **Nyt projekt** -guiden, med en ny side i slutningen, som giver brugerne mulighed for at aktivere hurtigt flere almindelige vigtige funktioner i deres projekt: sticky sessioner, cachelagring og ekstern fejlfinding.
* Automatisk reduktion af rolle forekomster til 1, når du kører i Beregn emulatoren, for at undgå port binding konflikter mellem forekomster af server.

### <a name="september-28-2012"></a>28 September 2012

Azure plug-in'et til Eklipse – September 2012 Preview har udgivet. Tjenesteopdateringen indeholder et antal yderligere fejlrettelser, da August 2012 Gennemse samt nogle feedback-driven brugervenligheden forbedringer i eksisterende funktioner:

* Understøttelse af Microsoft Windows 8 og Microsoft Windows Server 2012 som operativsystem udvikling på løse problemer, der tidligere forhindres plug-in'et fungerer korrekt på disse operativsystemer.
* Forbedret understøttelse af angive slutpunkt portområder.
* Fejlrettelser relateret til filen stier, der indeholder mellemrum.
* Rolle kontekst menuen forbedringer til hurtigere adgang til rolle-specifikke konfigurationsindstillinger.
* Mindre forbedringer i guiden **Udgiv til skyen** og et antal yderligere fejlrettelser.

### <a name="august-28-2012"></a>28 august 2012

Azure plug-in'et til Eklipse - August 2012 Preview har udgivet. Opdateringen service indeholder flere fejlrettelser, da juli 2012 Gennemse samt flere forbedringer af brugervenligheden feedback-driven for eksisterende funktioner:

* I dialogboksen Azure Access kontrolelement Services Filter:
    * **Indstilling for at integrere det certifikat** i dit program KRIG fil for at forenkle skyen installation.
    * **Muligheden for at oprette et selvsigneret certifikat** i filteret ACS brugergrænseflade. Se, [hvordan du godkende webbrugere med Azure Access kontrolelement tjenesten ved hjælp af Eklipse]yderligere oplysninger om filteret Azure Access kontrolelement tjenester.
* I guiden Azure installationsprojekt (gælder også for den rolle serverkonfiguration egenskabsside):
    * **Automatisk registrering af JDK placering** på computeren (som du kan tilsidesætte eventuelt).
    * **Automatisk registrering af servertypen** , når du vælger mappen installation server.

### <a name="july-15-2012"></a>15 juli 2012

Azure plug-in'et til Eklipse - juli 2012 Preview, som et tal over de højeste prioritet fejl, der er fundet og/eller brugere har rapporteret efter juni 2012 udgivelsen-adresser, der frigivet. Dette er en tjenesteopdatering, der findes ingen nye funktioner.

### <a name="june-7-2012"></a>7 juni 2012

Azure-plug-in til Eklipse - juni 2012 Le har udgivet. Nye funktioner omfatter:

* **Guiden Ny Azure installationsprojekt:** Gør det muligt at vælge din JDK Java programserver og Java-programmer direkte i guiden forbedret brugergrænseflade. Findes på listen ud for feltet server konfigureringer for at vælge mellem er Tomcat 6, 7 Tomcat, GlassFish Office Server Extensions 3, Jetty 7, Jetty 8, JBoss 6 og JBoss 7 (separat). Desuden kan du tilpasse listen over serverkonfigurationer. Denne brugergrænseflade forbedring er et alternativ til at trække og slippe komprimerede filer og kopiere over scripts til Start, som tidligere var den primære tilgang. Denne metode stadig fungerer fint, men vil sandsynligvis bruges kun til mere avancerede scenarier.
* **Serverkonfiguration rolle egenskabsside:** Gør det muligt for nemt at skifte JDKs, Java programmet servere og programmer, der er knyttet til din installation, når du har oprettet projektet. Se [Egenskaber for Server-konfiguration]kan finde flere oplysninger.
* **&quot;Publicere til skyen&quot; guiden:** er en nem måde at anvende dit projekt til Azure direkte fra Eklipse, automatisere de tidligere manuel tungt-ophævelse af hentning af legitimationsoplysninger, logge på portalen Azure Management, overførsel af en pakke osv. Et eksempel på hvordan du kan installere direkte dit projekt til Azure, i [oprette en Hej verden programmet til Azure i Eklipse].
* **Azure værktøjslinjen:** En Azure værktøjslinje er nu tilgængelig på Eklipse, som indeholder knapper, der aktiverer følgende funktioner:
    * ![][ic710879]**Køre i Azure Emulator**: kører dit projekt i emulatoren.
    * ![][ic710880]**Nulstille Azure Emulator**: nulstiller emulatoren.
    * ![][ic710881]**Opbygge skyen pakke til Azure**: kompilerer pakken til installation.
    * ![][ic710876]**Nyt Azure installationsprojekt**: opretter et nyt projekt Azure-installation.
    * ![][ic710882]**Publicer Azure skyen**: publiceres dit projekt til Azure.
    * ![][ic710883]**Annuller publicering**: Sletter din installation.
    * Mange af disse Azure værktøjslinjeknapperne er bruges til at [oprette et Hej verden program til Azure i Eklipse].
* **Azure biblioteker til Java:** Nu tilgængeligt som en del af den enkelte pakke til Azure-biblioteker til Java-bibliotek i Eklipse, tilhørende plug-in installationen og indeholder alle de nødvendige afhængigheder. At tilføje ved en reference til biblioteket i projektet Java, og du behøver ikke at hente noget separat. Du kan finde flere oplysninger, kan du se [installere Azure-værktøjskassen til Eklipse].
* **Microsoft JDBC Driver 4.0 til SQL Server, der er tilgængelige under installation af plug-in:** Under installation af plug-in'en til nye, kan være installeret den nyeste version af Microsoft JDBC Driver til SQL Server.
* **Azure Access Control Service Filter, der findes under installation af plug-in:** Denne nye komponent, medtages som en Eklipse bibliotek i værktøjskassen, gør det muligt for dine Java webprogram problemfrit udnytte Azure Access ACS (Control Service) godkendelse ved hjælp af forskellige identitetsudbydere, som Google, Live.com og Yahoo!. Du behøver ikke at skrive godkendelse logik dig selv, lige konfigurere et par indstillinger og lade filteret til at gøre det hårde arbejde ved at aktivere brugere til at logge på ved hjælp af ACS. Du kan kun fokusere på at skrive den kode, der giver brugere adgang til ressourcer, der er baseret på deres identitet, som returneret til dit program af filteret i Request-objektet. Selvstudium til ved hjælp af filteret ACS, se, [hvordan du godkende webbrugere med Azure Access kontrolelement tjenesten ved hjælp af Eklipse].
* **Automatisk registrering af de nødvendige forudsætninger Azure SDK 1.7:** Når du opretter et nyt projekt til Azure-installation, hentes Azure SDK 1.7 automatisk, hvis den ikke allerede er installeret.
* **Forekomst slutpunkter:** Giver direkte port slutpunkt adgang til kommunikation med Indlæs afstemmes rolle forekomster. Forekomst slutpunkter kan tilføjes via slutpunkterne brugergrænseflade, der er tilgængelige via siden [slutpunkter egenskaber] . Dette gør det muligt at ekstern fejlfinding og JMX diagnosticering for specifikke beregne kører i skyen i scenarier med flere forekomster-forekomst installationer. 
* **Komponenter brugergrænseflade:** Gør det nemmere for erfarne brugere til at konfigurere Projektafhængigheder mellem individuelle Azure roller i projektet og andre eksterne ressourcer som Java programmet projekter. også gør det nemt at beskrive deres installation logik. Du kan finde yderligere oplysninger finder [komponenter egenskaber].
* **Automatisk opgradering af tidligere versioner af projekter:** Når du åbner et arbejdsområde, der har Azure projekt, der er oprettet med en tidligere version af plug-in'en, kan de gamle projekter ses i Eklipse som lukket, fordi tidligere versioner af projekter ikke er kompatibel med den nye version. Hvis du forsøger at åbne en af disse gamle projekter, starter guiden en opgradering. Hvis du accepterer opgraderingen, oprettes et nyt projekt med **_Upgraded** føjet til navnet, og automatisk opdateret til at arbejde med den nye version. Efter behov, kan du omdøbe det nye projekt. Som en del af opgraderingen projektet oprindelige bliver ikke ændret (og forbliver lukket).

### <a name="december-10-2011"></a>10 december 2011

Azure-plug-in til Eklipse - December 2011 Le har udgivet. Nye funktioner omfatter:

* **Session forbindelse (&quot;sticky sessioner&quot;) understøtter:** Hjælp til at aktivere med høj sikkerhed, grupperet Java-programmer med et enkelt afkrydsningsfelt. Du kan finde flere oplysninger [Session forbindelse].
* **Allerede har foretaget Start scripteksempler:** For de mest populære Java servere (Tomcat, Jetty, JBoss, GlassFish), du kan kun kopierer/indsætter fra dit projekts eksempler mappe i din script til start.
* **Emulator Start output i realtid:** Du kan nu se udførelse af alle trinnene fra startscriptet i en dedikeret console-vinduet viser dig, status og fejl i dit script som den skal udføres ved Azure.
* **Automatisk, nedtonet java.exe overvågning:** Vil tvinge en rolle Papirkurv, når java.exe stopper, ved hjælp af en lette, foruddefineret script automatisk medtaget i din installation.
* **Remote Java app fejlfinding Brugergrænsefladen for konfiguration:** Kan du nemt gøre Eklipses remote fejlfindingsværktøjet til at få adgang til din Java-app, der kører i emulatoren eller Azure skyen, så du kan gennemgå og foretage fejlfinding af Java-kode i realtid. Du kan finde flere oplysninger [Fejlfinding Azure-programmer i Eklipse].
* **Brugergrænsefladen for lokale lager ressource konfiguration:** Så skal du ikke længere konfigurere lokale ressourcer ved manipulere XML direkte. Denne funktion gør det også muligt at adgang til effektive filstien til din lokale ressourcer, når den er installeret via en miljøvariablen, du kan referere til direkte fra din script til start. Se [Egenskaber for lokale storage]kan finde flere oplysninger.
* **Miljø variable konfiguration brugergrænseflade:** Så skal du ikke længere angive miljøvariabler via manuel redigering af XML-konfigurationen. Du kan finde yderligere oplysninger finder [miljø variabler egenskaber].
* **JDBC driver til SQL Azure:** Bliver installeret via plug-in'et som et integreret Eklipse bibliotek, så nemmere programming mod SQL Azure. 
* **Hurtig genvejsmenu adgang til Brugergrænsefladen for konfiguration af rolle**: blot højreklikke på mappen rolle, og klik på **Egenskaber**.
* **Brugerdefineret Azure projekt og rolle mappeikoner:** For større indsigt og nemmere at navigere i dit arbejdsområde og project.

## <a name="see-also"></a>Se også ##

Du kan finde flere oplysninger om Azure programudviklingsværktøjer til Java IDEs følgende links:

- [Azure-værktøjskassen til Eklipse]
  - [Installation af Azure værktøjerne til Eklipse]
  - [Oprette en Hej verden WebApp til Azure i Eklipse]
  - *Hvad er nyt i Azure værktøjerne til Eklipse (i denne artikel)*
- [Azure-værktøjskassen til IntelliJ]
  - [Installation af Azure værktøjerne til IntelliJ]
  - [Oprette en Hej verden WebApp til Azure i IntelliJ]
  - [Hvad er nyt i Azure værktøjerne til IntelliJ]

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

<!-- URL List -->

[Azure-værktøjskassen til Eklipse]: ./azure-toolkit-for-eclipse.md
[Azure-værktøjskassen til IntelliJ]: ./azure-toolkit-for-intellij.md
[Oprette en Hej verden WebApp til Azure i Eklipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Oprette en Hej verden WebApp til Azure i IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation af Azure værktøjerne til Eklipse]: ./azure-toolkit-for-eclipse-installation.md
[Installation af Azure værktøjerne til IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[What's New in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Hvad er nyt i Azure værktøjerne til IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547

[Azul systemer webside til Zulu OpenJDK]: http://go.microsoft.com/fwlink/?LinkId=402457
[Azure Service slutpunkter]: http://go.microsoft.com/fwlink/?LinkID=699526
[Azure-lager konto liste]: http://go.microsoft.com/fwlink/?LinkID=699528
[Egenskaber for komponenter]: http://go.microsoft.com/fwlink/?LinkID=699525#components_properties
[Oprette et Hej verden program til Azure i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Fejlfinding af Azure programmer i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Anvendelse af store installationer]: http://go.microsoft.com/fwlink/?LinkID=699536
[Slutpunkter egenskaber]: http://go.microsoft.com/fwlink/?LinkID=699525#endpoints_properties
[Miljø variabler egenskaber]: http://go.microsoft.com/fwlink/?LinkID=699525#environment_variables_properties
[HDInsight værktøjer plug-in til Eklipse]: ./hdinsight/hdinsight-apache-spark-eclipse-tool-plugin.md
[Sådan godkende webbrugere med Azure Access Control Service ved hjælp af Eklipse]: http://go.microsoft.com/fwlink/?LinkID=264703
[Sådan bruges SSL overføre]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installation af Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Egenskaber for lokale lager]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties
[Klient-API til Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=280397
[Egenskaber for Server-konfiguration]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Session forbindelse]: http://go.microsoft.com/fwlink/?LinkID=699548
[Overføre SSL]: http://go.microsoft.com/fwlink/?LinkID=699549
[Oprette en ny konto lagerplads]: http://go.microsoft.com/fwlink/?LinkID=699528#create_new
[Virtuelt og skyen Service størrelser til Azure]: http://go.microsoft.com/fwlink/?LinkId=466520

<!-- IMG List -->

[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710877]: ./media/azure-toolkit-for-eclipse-whats-new/ic710877.png
[ic710879]: ./media/azure-toolkit-for-eclipse-whats-new/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-whats-new/ic710880.png
[ic710881]: ./media/azure-toolkit-for-eclipse-whats-new/ic710881.png
[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710882]: ./media/azure-toolkit-for-eclipse-whats-new/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-whats-new/ic710883.png

<properties
    pageTitle="Sådan bruger du adgangskontrol (Java) | Microsoft Azure"
    description="Lær at udvikle og anvende adgangskontrol med Java i Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Sådan godkende webbrugere med Azure Access Control Service ved hjælp af Eklipse

Denne vejledning viser dig, hvordan du bruger Azure Access ACS (Control Service) i Azure-værktøjskassen til Eklipse. Du kan finde flere oplysninger om ACS, afsnittet [Næste trin](#next_steps) .

> [AZURE.NOTE]
> Azure Access Services kontrolelement Filter er et community technology preview. Som foreløbig version – understøttes det formelt ikke af Microsoft.

## <a name="what-is-acs"></a>Hvad er ACS?

De fleste udviklere er ikke identitet eksperter og generelt ønsker ikke at bruge en masse tid udvikling godkendelses- og autorisationsmekanismer til deres programmer og tjenester. ACS er en Azure tjeneste, som gør det nemt for at godkende brugere, der skal have adgang til dine web-programmer og tjenester uden at skulle faktor komplekse godkendelse logik i din kode.

Følgende funktioner er tilgængelige i ACS:

-   Integration med Windows Identity Foundation (WIF).
-   Understøttelse af populære web identitetsudbydere (IP'er) herunder Windows Live ID, Google, Yahoo! og Facebook.
-   Understøttelse af Active Directory Federation Services (AD FS) 2.0.
-   En Open Data Protocol (OData)-baseret management-tjenesten, som indeholder programmeringsmæssig adgang til ACS indstillinger.
-   En Management Portal, der tillader administratoradgang til indstillingerne for ACS.

Du kan finde flere oplysninger om ACS, [Access Control Service 2.0][].

## <a name="concepts"></a>Begreber

Azure ACS er bygget på principper af kravbaseret identitet - en ensartet måde til at oprette godkendelsesmetoder til programmer, der kører lokalt eller i skyen. Kravbaseret identitet indeholder et almindelige metode til programmer og tjenester til at hente de ønskede identitetsoplysninger om brugere inden for organisationen, i andre organisationer, og på internettet.

For at fuldføre opgaverne i denne vejledning, skal du kende følgende begreber:

**Klient** - i forbindelse med denne praktisk vejledning dette er en browser, der forsøger at få adgang til dit webprogram.

**Relying part (RP) program** - programmet på en RP er et websted eller -tjeneste, som outsources godkendelse til et eksternt nøglecenter. I identitet jargon at vi, at RP har tillid til nøglecenter. Denne vejledningen beskrives, hvordan du konfigurerer dit program til sikkerhed og rettighedsadministration ACS.

**Token** - et token er en samling af sikkerhedsdata, der er som regel, der udstedes efter vellykket bekræftelse af en bruger. Den indeholder et sæt af *krav*, attributter for den godkendte bruger. Et krav kan repræsentere en brugers navn, et id for rollen som en bruger tilhører, en brugers alder og så videre. Et token er som regel digitalt signeret, hvilket betyder, at det kan altid leveres tilbage til dets udsteder eller ej, og dens indhold kan ikke være ændret. En bruger får adgang til et RP-program ved at præsentere et gyldigt token udstedt af et nøglecenter, der har tillid til programmet RP.

**Identitet udbyder (IP)** - en IP-adresse er et nøglecenter, godkender bruger-id'er og problemer sikkerhedstokens. Det faktiske arbejde for udsteder tokens er implementeret, selvom en særlig tjeneste kaldet sikkerhed Token STS (Service). Typisk eksempler IP'er på Windows Live ID, Facebook, business bruger typer lagre (som Active Directory) osv.
Når ACS er konfigureret til at have tillid til en IP-adresse, acceptere systemet og validere tokens, der er udstedt af IP-adresse. ACS kan have tillid til flere IP'er på én gang, hvilket betyder, at når programmet har tillid til ACS, kan du med det samme tilbyde dit program til alle godkendte brugere fra alle IP'er, ACS har tillid til på dine vegne.

**Sammenslutning udbyder FP ()** - IP'er har direkte kendskab til brugere, og godkende dem ved hjælp af deres legitimationsoplysninger og udstede krav om, hvad de vide om dem. En sammenslutning udbyder (FP) er en anden type nøglecenter: i stedet for godkendelse af brugere direkte, det fungerer som en mellemled og mæglere godkendelse mellem én RP og én eller flere IP-adresser. Både IP-adresser og FPs udstede sikkerhedstokens derfor de begge bruge sikkerhed STS (Token Services). ACS er én FP.

**ACS regel program** - logik bruges til at transformere indgående tokens ud fra der er tillid til IP-adresser til tokens, der er beregnet til at blive brugt i RP kodificeres i form af et enkelt krav transformation regler. ACS indeholder en regel program, der tager sig af at anvende uanset transformation logik, du har angivet for din RP.

**ACS Namespace** - et navneområde er en partition på øverste niveau af ACS, du bruger til at organisere dine indstillinger. Et navneområde indeholder en liste over IP'er du har tillid til, de RP-programmer, du vil udskrive, de regler, du forventer reglen motorens for at behandle indgående tokens med, og så videre. Et navneområde Fremviser forskellige slutpunkter, der bruges af programmet og udvikleren til at få ACS til at udføre dens funktion.

I følgende figur vises, hvordan ACS godkendelse fungerer sammen med et webprogram:

![ACS rutediagram][acs_flow]

1.  Anmoder om en side fra RP klienten (i dette tilfælde en browser).
2.  Da anmodningen ikke endnu er godkendt, RP omdirigerer brugeren til det nøglecenter, der har tillid til, hvilket er ACS. ACS giver brugeren mulighed for at IP-adresser, der er angivet for denne RP. Brugeren vælger den relevante IP-adresse.
3.  Klienten går til den IP-godkendelse siden, og du bliver bedt om at brugeren kan logge.
4.  Når klienten er godkendt (for eksempel, den identitet, legitimationsoplysninger angives), udsteder IP-adresse et sikkerhedstoken.
5.  Når du udsteder et sikkerhedstoken, IP-adresse omdirigerer klienten til ACS og klienten sender det sikkerhedstoken, der er udstedt af IP-adresse til ACS.
6.  ACS validerer den sikkerhedstoken udstedt af IP-adresse, input identitet krav i dette token til ACS regler program, beregner output identitetskrav og problemer i et nyt sikkerheds-id, der indeholder disse output krav.
7.  ACS omdirigerer klienten til RP. Kunden sender det nye sikkerhedstoken udstedt af ACS til RP. RP valideret signatur i det pågældende sikkerhedstoken udstedt af ACS, valideret krav i dette token og returnerer den side, der oprindeligt blev bestilt.

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre opgaverne i denne vejledning, skal du følgende:

- En Java udvikler Kit (JDK), v 1,6 eller nyere.
- Eklipse IDE for Java EE udviklere småt eller nyere. Dette kan hentes fra <http://www.eclipse.org/downloads/>. 
- En fordeling af en Java-baseret webserver eller programserver som Apache Tomcat, GlassFish, JBoss programserver eller Jetty.
- et Azure abonnement, som kan hentes fra <http://www.microsoft.com/windowsazure/offers/>.
- Azure-værktøjskassen til Eklipse, April 2014 slip eller nyere. Du kan finde flere oplysninger, kan du se [installere Azure-værktøjskassen til Eklipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
- Et x.509-certifikat til brug med dit program. Du skal bruge dette certifikat i både offentlige certifikat (.cer) og Personal Information Exchange (. PFX) format. (Indstillinger for oprettelse af dette certifikat skal være beskrevet senere i dette selvstudium).
- Kendskab til Azure beregne emulator og installation teknikker diskuteres på [oprettelse af Hej verden program til Azure i Eklipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Oprette en ACS Namespace

Hvis du vil begynde at bruge Access ACS (Control Service) i Azure, skal du oprette et ACS navneområde. Navneområdet indeholder et entydigt omfang vælge adresser til ACS ressourcer fra dit program.

1. Log på [Azure Management Portal][].
2. Klik på **Active Directory**. 
3. For at oprette et nyt adgangskontrol navneområde, klik på **Ny**, klik på **App-tjenester**, skal du klikke på **Adgangskontrol**og derefter klikke på **Hurtig oprettelse**. 
4. Angiv et navn til navneområdet. Azure kontrollerer, at navnet er entydige.
5. Vælg det område, hvor navneområdet bruges. Den bedste ydeevne ved brug af det område, hvor du installerer programmet.
6. Hvis du har mere end ét abonnement, skal du markere det abonnement, du vil bruge til navneområdet ACS.
7. Klik på **Opret**.

Azure opretter og aktiverer navneområdet. Vent, indtil status for det nye navneområde er **aktiv** , før du fortsætter. 

## <a name="add-identity-providers"></a>Tilføje identitetsudbydere

I denne opgave skal tilføje du IP'er til brug med RP programmet til godkendelse. Denne opgave viser, hvordan du tilføje Windows Live som en IP-adresse til demonstration, men du kan bruge en af de IP-adresser er angivet i portalen ACS Management.


1.  Klik på **Active Directory**i [Azure Management Portal][], Marker et adgangskontrol navneområde, og klik derefter på **Administrer**. Portalen ACS Management åbnes.
2.  Klik på **identitetsudbydere**i den venstre navigationsrude i portalen ACS administration.
3.  Windows Live ID er aktiveret som standard, og kan ikke slettes. For anvendelsen af dette selvstudium bruges kun Windows Live-ID. Dette skærmbillede, er dog, hvor du kan tilføje andre IP'er ved at klikke på knappen **Tilføj** .

Windows Live ID er nu aktiveret som en IP-adresse til dit ACS navneområde. Derefter skal du angive dit Java webprogram (skal have oprettet senere) som en RP.

## <a name="add-a-relying-party-application"></a>Tilføje en afhængige part-program

I denne opgave skal konfigurere du ACS at genkende din Java-webprogrammet som et gyldigt RP-program.

1.  Klik på **Relying part programmer**på portalen ACS administration.
2.  Klik på **Tilføj**på siden **Stole part programmer** .
3.  På siden **Tilføj stole part-program** skal du gøre følgende:
    1.  Skriv navnet på RP i **navnet**. Skriv **Azure Web App**for formålene med dette selvstudium.
    2.  Vælg i **tilstanden** **angive indstillinger manuelt**.
    3.  Skriv den URI, som gælder for det pågældende sikkerhedstoken udstedt af ACS i **ressource**. Denne opgave skal du skrive **http://localhost:8080 /**.
        ![Afhængige part startdomænet til brug i Beregn emulator][relying_party_realm_emulator]
    4.  Skriv URL-adressen, returnerer ACS sikkerheds-id i **Returnere URL-adresse** . Denne opgave skal du skrive **http://localhost:8080/MyACSHelloWorld/index.jsp**
        ![Relying part returnere URL-adresse til brug i Beregn emulator][relying_party_return_url_emulator]
    5.  Acceptere standardværdierne i resten af felterne.

4.  Klik på **Gem**.

Du har nu korrekt konfigureret dit Java webprogram når den kører i Azure Beregn emulatoren (på http://localhost:8080 /) skal være en RP i din ACS navneområde. Opret derefter de regler, der ACS bruger til at behandle krav om RP.

## <a name="create-rules"></a>Oprette regler

I denne opgave definerer du de regler, der styrer, hvordan krav sendes fra IP-adresser til din RP. Til denne vejledning, kan vi blot konfigurere ACS for at kopiere de kravtyper af input og værdier direkte i tokenet output uden at filtrere eller redigere dem.

1.  Klik på **reglen grupper**på siden ACS Management Portal primære.
2.  Klik på **Standard regel gruppe for Azure Web App**på siden **Regelgrupper** .
3.  Klik på **Generer**på siden **Rediger regel gruppe** .
4.  På den **generere regler: standard regel gruppe til Azure online** skal du sikre, at Windows Live ID er markeret og klik derefter på **Opret**.    
5.  Klik på **Gem**på siden **Rediger regel gruppe** .

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Overføre et certifikat til din ACS navneområde

I denne opgave skal du overføre en. PFX certifikat, der skal bruges til at signere token anmodninger, der er oprettet af din ACS navneområde.

1.  Klik på **certifikater og nøgler**på hovedsiden ACS Management Portal.
2.  Klik på **Tilføj** over **Token logge**på siden **certifikater og nøgler** .
3.  På siden **Tilføj Token-signeringscertifikat eller nøgle** :
    1. Klik på **Stole part program** , og vælg **Azure Web App** (som du tidligere har angivet som navnet på dit afhængige part program) i sektionen **bruges til** .
    2. Vælg **X.509-certifikat**i sektionen **Type** .
    3. Klik på knappen Gennemse, og gå til filen x.509-certifikat, som du vil bruge, i sektionen **certifikat** . Dette er en. PFX-fil. Vælg fil, skal du klikke på **Åbn**, og derefter indtaste adgangskoden certifikat i tekstfeltet **adgangskode** . Bemærk, at til testformål, du kan bruge et din egen-signed-certifikat. Brug knappen **Ny** i dialogboksen **ACS Filter bibliotek** (beskrives senere) for at oprette et selvsigneret certifikat skal, eller brug værktøjet **encutil.exe** fra [project-websted][] i Azure Starter Kit til Java.
    4. Sørg for, at **Foretage primære** er markeret. Siden **Tilføj Token-signeringscertifikat eller tast** skal ligne følgende.
        ![Tilføje certifikat til signering med token][add_token_signing_cert]
    5. Klik på **Gem** for at gemme indstillingerne og lukke siden **Tilføj Token-signeringscertifikat eller nede** .

Derefter gennemse oplysningerne på siden Application Integration og kopiere den URI, skal du konfigurere din Java webprogram bruge ACS.

## <a name="review-the-application-integration-page"></a>Gennemgå siden Application Integration

Du kan finde alle oplysninger og koden nødvendige for at konfigurere dit Java webprogram (RP-program) for at arbejde med ACS på siden Application Integration af portalen ACS administration. Du skal bruge disse oplysninger, når du konfigurerer dit Java webprogram til samlet godkendelse.

1.  Klik på **programintegration**på portalen ACS administration.  
2.  Klik på **Login sider**på siden **Integration af programmet** .
3.  Klik på **Azure Web App**på siden **Login siden Integration** .

I den **logon side Integration: Azure online** siden URL-adressen er angivet i **Option 1: Link til en ACS hostet logonside** der skal bruges i din Java-webprogrammet. Du skal bruge denne værdi, når du føjer biblioteket Azure Access kontrolelement Services Filter til Java-program.

## <a name="create-a-java-web-application"></a>Oprette et webprogram Java
1. Klik på **Ny**i menuen Klik på **filer**i Eklipse, og klik **Dynamisk Web Project**. (Hvis du ikke kan se **Dynamisk webprojekt** er angivet som en tilgængelig project ved klik på **filer**, **Ny**gør derefter følgende: Klik på **filer**, klik på **Ny**, klik på **projekt**, udvide **Web**, klik på **Dynamisk webprojekt**og klik på **Næste**.) Navngive projektet **MyACSHelloWorld**henblik på dette selvstudium. (Sikre, at du bruger dette navn, efterfølgende trin i dette selvstudium forvente KRIG filen til navngives MyACSHelloWorld). Skærmen vises som følger:

    ![Oprette et Hej verden projekt til ACS exampple][create_acs_hello_world]

    Klik på **Udfør**.
2. Udvid **MyACSHelloWorld**i Eklipses Projektstifinder visning. Højreklik på **WebContent**, klik på **Ny**, og klik derefter på **JSP fil**.
3. Navngiv filen **index.jsp**i dialogboksen **Ny JSP-fil** . Du kan huske den overordnede mappe som MyACSHelloWorld/WebContent, som vist i følgende:

    ![Tilføje en JSP fil til ACS eksempel][add_jsp_file_acs]

    Klik på **Næste**.

4. Vælg **Ny JSP fil (html)** i dialogboksen **Vælg JSP skabelon** , og klik på **Udfør**.
5. Tilføj, når filen index.jsp åbner i Eklipse i tekst skal vises **Hej ACS verden!** inden for den nuværende `<body>` element. Din opdaterede `<body>` indhold skal vises som følgende:

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Gem index.jsp.
  
## <a name="add-the-acs-filter-library-to-your-application"></a>Føje biblioteket ACS Filter til dit program

1. Højreklik på **MyACSHelloWorld**i Eklipses Projektstifinder, skal du klikke på **Opret sti**, og klik derefter på **Konfigurer opbygge sti**.
2. Klik på fanen **biblioteker** i dialogboksen **Java opbygge sti** .
3. Klik på **Tilføj bibliotek**.
4. Klik på **Azure Access kontrolelement Services Filter (ved MS Åbn Tech)** , og klik derefter på **Næste**. Dialogboksen **Azure Access kontrolelement Services Filter** vises.  (Feltet **placering** kan have en anden sti, afhængigt af hvor du har installeret Eklipse, og versionsnummeret kan være forskellige, afhængigt af softwareopdateringer).

    ![Tilføje ACS Filter dokumentbibliotek][add_acs_filter_lib]

5. Ved hjælp af en browser, der er åbnet til siden **Login siden Integration** af portalen Management kopiere URL-adressen er angivet i den **Option 1: Link til en ACS hostet logonside** felt og indsætte den i feltet **ACS godkendelse slutpunkt** i dialogboksen Eklipse.
6. Ved hjælp af en browser, der er åbnet, på siden **Rediger stole part program** over portalen til administration, Kopiér URL-adressen er angivet i feltet **ressource** og sætte det ind i feltet **Stole part ressource** i dialogboksen Eklipse.
7. I sektionen **sikkerhed** i dialogboksen Eklipse, hvis du vil bruge et eksisterende certifikat, klik på **Gennemse**, gå til det certifikat, du vil bruge, markere den og klikke på **Åbn**. Eller hvis du vil oprette et nyt certifikat skal du klikke på **Ny** for at få vist dialogboksen **Nyt certifikat** , derefter angive den adgangskode, navnet på .cer-fil og navnet på .pfx-fil til det nye certifikat.
8. Markér **Integrer certifikatet i filen KRIG**. Integrere certifikatet på denne måde indeholder den i din installation uden at du skal tilføje den manuelt som en komponent. (Hvis i stedet skal du gemme dit certifikat eksternt fra din KRIG-fil, du kan tilføje certifikatet som en rolle komponent og fjern markeringen i **Integrer certifikatet i filen KRIG**.)
9. [Valgfrit] Behold **kræver HTTPS forbindelser** , der er markeret. Hvis du har angivet denne indstilling, skal du have adgang til dit program ved hjælp af HTTPS-protokollen. Hvis du ikke vil kræve HTTPS-forbindelser, skal du fjerne markeringen i denne indstilling.
10. For en installation til Beregn emulatoren ser filtreringsindstillingerne **Azure ACS** nogenlunde sådan følgende.

    ![Azure ACS filterindstillinger for en installation for at Beregn emulatoren][add_acs_filter_lib_emulator]

11. Klik på **Udfør**.
12. Klik på **Ja** når vises med en dialogboks, hvor der står, at der oprettes en web.xml fil.
13. Klik på **OK** for at lukke dialogboksen **Java opbygge sti** .

## <a name="deploy-to-the-compute-emulator"></a>Installere på Beregn emulatoren

1. Højreklik på **MyACSHelloWorld**i Eklipses Projektstifinder, skal du klikke på **Azure**, og klik derefter på **pakke til Azure**.
2. Skriv **MyAzureACSProject** **Projektnavn**, og klik på **Næste**.
3. Vælg en JDK og programserver. (Disse trin er beskrevet i detaljer i [oprettelse af Hej verden program til Azure i Eklipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) selvstudiet).
4. Klik på **Udfør**.
5. Klik på knappen **Kør i Azure Emulator** .
6. Når din Java-webprogrammet starter i Beregn emulatoren, Luk alle forekomster af din browser (så aktuelle browsersessioner ikke forstyrrer dine ACS login test).
7. Kør programmet ved at åbne <> http://localhost:8080/MyACSHelloWorld/i din browser ( <> eller/https://localhost:8080/MyACSHelloWorld/Hvis du har markeret **kræver HTTPS forbindelser**). Du bliver bedt om til et Windows Live ID-logon, og du skal være i den returnerede URL-adresse, der er angivet for afhængige part programmet.
99.  Klik på knappen **Nulstil Azure Emulator** , når du er færdig med at få vist dit program.

## <a name="deploy-to-azure"></a>Installere på Azure

Hvis du vil installere på Azure, skal du ændre den afhængige part ressource og vend tilbage URL-adressen for din ACS navneområde.

1. Ændre **ressource** for at være URL-adresse på webstedet udløst i portalen Azure-administration på siden **Rediger stole part-program** . Erstat **eksempel** med det angivne til din installation DNS-navn.

    ![Afhængige part startdomænet til brug i fremstilling][relying_party_realm_production]

2. Ændre **Returnere URL-adressen** til at være URL-adressen for dit program. Erstat **eksempel** med det angivne til din installation DNS-navn.

    ![Afhængige part returnerede URL-adresse til brug i fremstilling][relying_party_return_url_production]

3. Klik på **Gem** for at gemme dine opdaterede besvare part ressource og returnere URL-adressen ændringer.
4. Hold siden **Login siden Integration** åben i din browser, skal du kopiere fra det kort.
5. Højreklik på **MyACSHelloWorld**i Eklipses Projektstifinder, skal du klikke på **Opret sti**, og klik derefter på **Konfigurer opbygge sti**.
6. Klik på fanen **biblioteker** , klik på **Azure Access kontrolelement Services Filter**, og klik derefter på **Rediger**.
7. Ved hjælp af en browser, der er åbnet til siden **Login siden Integration** af portalen Management kopiere URL-adressen er angivet i den **Option 1: Link til en ACS hostet logonside** felt og indsætte den i feltet **ACS godkendelse slutpunkt** i dialogboksen Eklipse.
8. Ved hjælp af en browser, der er åbnet, på siden **Rediger stole part program** over portalen til administration, Kopiér URL-adressen er angivet i feltet **ressource** og sætte det ind i feltet **Stole part ressource** i dialogboksen Eklipse.
9. I sektionen **sikkerhed** i dialogboksen Eklipse, hvis du vil bruge et eksisterende certifikat, klik på **Gennemse**, gå til det certifikat, du vil bruge, markere den og klikke på **Åbn**. Eller hvis du vil oprette et nyt certifikat skal du klikke på **Ny** for at få vist dialogboksen **Nyt certifikat** , derefter angive den adgangskode, navnet på .cer-fil og navnet på .pfx-fil til det nye certifikat.
10. Behold **Integrer certifikatet i filen KRIG** markeret, hvis du vil integrere certifikatet i filen KRIG.
11. [Valgfrit] Behold **kræver HTTPS forbindelser** , der er markeret. Hvis du har angivet denne indstilling, skal du have adgang til dit program ved hjælp af HTTPS-protokollen. Hvis du ikke vil kræve HTTPS-forbindelser, skal du fjerne markeringen i denne indstilling.
12. For en installation til Azure ser filtreringsindstillingerne Azure ACS nogenlunde sådan følgende.

    ![Azure ACS filterindstillinger for et produktionsmiljø][add_acs_filter_lib_production]

13. Klik på **Udfør** for at lukke dialogboksen **Rediger bibliotek** .
14. Klik på **OK** for at lukke dialogboksen **Egenskaber for MyACSHelloWorld** .
15. Klik på knappen **Publicer Azure skyen** i Eklipse. Besvare anvisningerne, lignende som udført i afsnittet **installere programmet til Azure** i emnet [oprettelse af Hej verden program til Azure i Eklipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) . 

Når dit webprogram er blevet installeret, Luk alle åbne browsersessioner, køre dit webprogram og skal du blive bedt om at logge på med Windows Live ID-legitimationsoplysninger, efterfulgt af der sendes til dit afhængige part program returnerede URL-adressen.

Når du er færdig ved hjælp af programmets ACS Hej verden Husk at slette den installation, (du kan se, hvordan du sletter en installation i emnet [oprettelse af Hej verden program til Azure i Eklipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).


## <a name="next_steps"></a>Næste trin

Se, [hvordan du får vist SAML, der returneres af Azure Access Control Service][]til en undersøgelse af den sikkerhed program Markup Language (SAML) returneres af ACS til dit program. Yderligere udforske ACSS funktionalitet og for at eksperimentere med mere avancerede scenarier, se [Access Control Service 2.0][].

I dette eksempel bruges også indstillingen **Integrer certifikatet i filen KRIG** . Denne indstilling gør det nemt at installere certifikatet. Hvis du vil i stedet at holde din certifikat til signering adskilt fra filen KRIG, kan du bruge følgende metode:

1. I sektionen **sikkerhed** i dialogboksen **Azure Access kontrolelement Services Filter** skal du skrive **${env. JAVA_HOME}/mycert.cer** og fjerne markeringen i **Integrer certifikatet i filen KRIG**. (Justere mycert.cer, hvis dit certifikat filnavn er anderledes.) Klik på **Udfør** for at lukke dialogboksen.
2. Kopiere certifikatet som en komponent i din installation: I Eklipses Projektstifinder, udvide **MyAzureACSProject**, højreklik på **WorkerRole1**, klik på **Egenskaber**, udvide **Azure rolle**, og klikke på **komponenter**.
3. Klik på **Tilføj**.
4. I dialogboksen **Tilføj komponent** :
    1. I afsnittet **Import** :
        1. Brug knappen **filer** til at gå til det certifikat, du vil bruge. 
        2. Vælg **Kopiér**for **metode**.
    2. Klik på tekstboksen for **Som navn**, og acceptere standardnavnet.
    3. I sektionen **Implementer** :
        1. Vælg **Kopiér**for **metode**.
        2. Skriv **% JAVA_HOME %**for **til mappe**.
    4. Dialogboksen **Tilføj komponent** skal ligne følgende.

        ![Tilføje certifikat komponent][add_cert_component]

    5. Klik på **OK**.

På dette tidspunkt vil dit certifikat skal inkluderes i din installation. Bemærk, uanset om du integrere certifikatet i filen KRIG eller tilføje det som en komponent til din installation, du skal overføre certifikatet til din navneområde som beskrevet i afsnittet [overføre et certifikat til din ACS navneområde][] .

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Overføre et certifikat til din ACS navneområde]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[Project-websted]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Sådan får du vist SAML, der returneres af Azure Access Control Service]: /en-us/develop/java/how-to-guides/view-saml-returned-by-acs/
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management-portalen]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
 

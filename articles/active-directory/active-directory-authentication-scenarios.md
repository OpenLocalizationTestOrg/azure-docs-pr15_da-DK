
<properties
   pageTitle="Godkendelse scenarier, hvor Azure AD | Microsoft Azure"
   description="En oversigt over de fem mest almindelige godkendelse scenarier til Azure Active Directory (AAD)"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="authentication-scenarios-for-azure-ad"></a>Godkendelse scenarier, hvor Azure AD

Azure Active Directory (Azure AD) forenkler godkendelse for udviklere ved at indsende identitet som en tjeneste med understøttelse af branchestandard protokoller som OAuth 2.0 og OpenID forbinde samt Åbn kilde billedbiblioteker til forskellige platforme kan hjælpe dig med at starte kodningssprog hurtigt. Dette dokument hjælper dig med at forstå de forskellige scenarier Azure AD-understøtter og viser dig, hvordan du kommer i gang. Det er inddelt i de følgende afsnit:

- [Grundlæggende om godkendelse i Azure AD](#basics-of-authentication-in-azure-ad)

- [Krav i Azure AD sikkerhedstokens](#claims-in-azure-ad-security-tokens)

- [Grundlæggende regler for registrering af et program i Azure AD](#basics-of-registering-an-application-in-azure-ad)

- [Programmet typer og -scenarier](#application-types-and-scenarios)

  - [Webbrowser til webprogram](#web-browser-to-web-application)

  - [Enkelt side program (SPA)](#single-page-application-spa)

  - [Oprindelige program til Web API](#native-application-to-web-api)

  - [Webprogrammet Web API](#web-application-to-web-api)

  - [Daemon eller Server programmer til Web API](#daemon-or-server-application-to-web-api)



## <a name="basics-of-authentication-in-azure-ad"></a>Grundlæggende om godkendelse i Azure AD

Hvis du har kendskab til grundlæggende begreber af godkendelse i Azure AD, kan du læse dette afsnit. Ellers skal overveje du at springe ned til [programmet typer og -scenarier](#application-types-and-scenarios).

Lad os se på det mest grundlæggende scenario, hvor identitet er påkrævet: en bruger i en webbrowser skal godkendes til et webprogram. Dette scenario er beskrevet mere detaljeret i sektionen [Webbrowser til webprogram](#web-browser-to-web-application) , men det er et nyttigt udgangspunkt til at illustrere funktionerne i Azure AD og nemmere, hvordan dette scenario fungerer. Overvej følgende diagrammet i dette scenarie:

![Oversigt over enkeltlogon til webprogram](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Med diagrammet ovenfor huske er her hvad du bør vide om de forskellige komponenter:

- Azure AD er den identitetsudbyder, der er ansvarlig for at bekræfte identiteten af brugere og programmer, der findes i en organisations mappe og i sidste ende udsteder sikkerhedstokens efter vellykket bekræftelse af disse brugere og programmer.


- Et program, som ønsker at udlicitere godkendelse til Azure AD skal være registreret i Azure AD, der registrerer og identificerer entydigt app i kataloget.


- Udviklere kan bruge Åbn kilde Azure AD-godkendelse biblioteker til at gøre godkendelse nemt ved at håndtere protocol detaljerne for dig. Du kan finde flere oplysninger i [Azure Active Directory Authentication biblioteker](active-directory-authentication-libraries.md) .


•, Når en bruger er blevet godkendt, programmet skal validere brugerens sikkerhedstoken for at sikre, at godkendelse er oprettet for de tilsigtede parter. Udviklere kan bruge de medfølgende godkendelse biblioteker til at håndtere validering af enhver token fra Azure AD, herunder JSON Web Tokens (JWT) eller SAML 2.0. Hvis du vil udføre validering manuelt, skal du se i dokumentationen til [JWT Token Handler](https://msdn.microsoft.com/library/dn205065.aspx) .


> [AZURE.IMPORTANT] Azure AD bruger kryptering med offentlig nøgle til at logge tokens og bekræfte, at de er gyldige. Se [Vigtige oplysninger om signering nøgle overgang i Azure AD](active-directory-signing-key-rollover.md) yderligere oplysninger om det er nødvendigt logik, der skal være i dit program tilladelse til at sikre, at det altid er opdateret med de seneste nøgler.


• Strømmen af anmodninger om og svar til godkendelsesprocessen bestemmes af godkendelsesprotokollen, der blev brugt som OAuth 2.0, der opretter forbindelse mellem OpenID, WS-Federation eller SAML 2.0. Disse protokoller er beskrevet mere detaljeret i emnet [Azure Active Directory Authentication protokoller](active-directory-authentication-protocols.md) og i afsnittene herunder.

> [AZURE.NOTE] Azure AD understøtter OAuth 2.0 og OpenID forbinde standarder, som gør omfattende brug af bæreren tokens, herunder bæreren tokens repræsenteres som JWTs. En bærertoken er et lette sikkerhedstoken, der giver adgang til en beskyttet ressource "bæreren". I denne mening er "bæreren" Enhver part, der kan præsentere tokenet. Selvom fest skal først godkende med Azure AD modtage bærertoken, hvis de nødvendige trin ikke er i secure token i overførslen og lager, kan den opfange og bruges af en utilsigtede part. Mens nogle sikkerhedstokens har en indbygget ordning for at forhindre uautoriserede personer i at bruge dem, bæreren tokens har ikke denne funktion og skal være forsendelse i en sikker kanal som transport lag sikkerhed (HTTPS). Hvis en bærertoken er sendt i Ryd, kan en mand i den midterste angreb bruges af en skadelig part til at købe tokenet og bruge den til en uautoriseret adgang til en beskyttet ressource. De samme sikkerhedsprincipper anvende når lagring eller cachelagring bæreren tokens til senere brug. Altid sikre, at dit program transmitterer og gemmer bæreren tokens på en sikker måde. Du kan finde flere overvejelser om sikkerheden på bæreren tokens, [RFC 6750 trin 5](http://tools.ietf.org/html/rfc6750).


Nu hvor du har en oversigt over de grundlæggende funktioner, Læs afsnittene herunder for at forstå, hvordan klargøring fungerer i Azure AD og de almindelige scenarier Azure AD understøtter.


## <a name="claims-in-azure-ad-security-tokens"></a>Krav i Azure AD sikkerhedstokens

Sikkerhedstokens udstedt af Azure AD indeholde krav eller påstande oplysninger om det emne, der er blevet godkendt. Disse krav kan anvendes af programmet til forskellige opgaver. De kan for eksempel bruges til at validere tokenet, identificere den emne directory lejer, Vis brugeroplysninger, bestemme den emnet godkendelse og så videre. Findes i en given sikkerhedstoken krav er afhængig af typen token, hvilke typer legitimationsoplysninger, der bruges til at godkende brugeren, og konfigurationen af programmet. En kort beskrivelse af hver type af krav fra Azure AD er angivet i tabellen nedenfor. Se [understøttede Token og Kræv typer](active-directory-token-and-claims.md)kan finde flere oplysninger.


| Gør krav | Beskrivelse |
|-------|-------------|
| Program-ID | Identificerer det program, der bruger tokenet.
| Målgruppe | Identificerer modtager ressourcen Tokenet er beregnet til. |
| Programmet godkendelse kontekst klassereference | Angiver, hvordan klienten blev godkendt (offentlige klient kontra fortrolige klienten). |
| Godkendelse Chat | Registrerer dato og klokkeslæt, hvor godkendelsen opstod. |
| Godkendelsesmetode | Angiver, hvordan emnet for Tokenet er blevet godkendt (adgangskode, certifikat osv.). |
| Fornavn | Indeholder navnet på brugeren som angivet i Azure AD. |
| Grupper | Indeholder objektgrupper id'er af Azure AD brugeren er medlem af. |
| Identitetsudbyder | Poster identitetsudbyder, som er godkendt emnet for tokenet. |
| Udstedt på | Den tid, hvormed tokenet, er udstedt, ofte bruges til token friskhed-poster. |
| Udsteder | Identificerer de STS, udsendes tokenet samt Azure AD-lejer. |
| Efternavn | Indeholder efternavn for brugeren, som angivet i Azure AD. |
| Navn | Indeholder en human læsbar værdi, der identificerer emnet for tokenet. |
| Objekt-Id | Indeholder en fast, entydigt id for emnet i Azure AD. |
| Roller | Indeholder kaldenavne over Azure AD-programroller, som brugeren har fået tildelt. |
| Omfang | Angiver de tilladelser, der er tildelt til-klientprogrammet. |
| Emne | Angiver hovedstolen tokenet imperative forudsætninger oplysninger om, hvilke. |
| Lejer-Id | Indeholder en fast, entydigt id for lejeren directory, der har udstedt tokenet. |
| Token levetid | Definerer et tidsinterval, hvor et token er gyldig. |
| Brugerens hovednavn | Indeholder brugerens hovednavn af emnet. |
| Version | Indeholder versionsnummeret for tokenet. |


## <a name="basics-of-registering-an-application-in-azure-ad"></a>Grundlæggende regler for registrering af et program i Azure AD

Alle programmer, der outsources godkendelse til Azure AD skal være registreret i en mappe. Dette trin omfatter fortæller Azure AD om dit program, herunder URL-adressen, hvor det er placeret, URL-adressen til at sende svar efter godkendelse kan URI til at identificere dit program og meget mere. Disse oplysninger er påkrævet af vigtigste årsager:

- Azure AD skal koordinater til at kommunikere med programmet, når du arbejder med enkeltlogon eller at udveksle tokens. Dette omfatter følgende:

  - Program-ID URI: Id for et program. Denne værdi, der sendes til Azure AD under godkendelse til at angive, hvilket program kalderen ønsker et token til. Desuden medtages denne værdi i tokenet, så programmet ved det var tilsigtede destination.


  - Svare URL-adresse og omdirigere URI: en web API eller webprogram, svar URL-adressen er den placering, som Azure AD skal sende svaret godkendelse, herunder et token, hvis godkendelse lykkedes. Den omdirigere URI er et entydigt id, som omdirigerer Azure AD brugeragent i en anmodning om en OAuth 2.0 tale om en oprindelige program.


  - Klient-ID: ID for et program, der oprettes ved Azure AD, når programmet er registreret. Når der anmodes om en tilladelse kode eller token, sendes klient-ID og nøgle til Azure AD under godkendelse.


  - Nøgle: Den nøgle, som sendes sammen med en klient-ID ved godkendelse til Azure AD til at ringe til en web API.


- Azure AD skal sikre, at programmet, har de nødvendige tilladelser til at få adgang til dataene directory, andre programmer i din organisation, og så videre

Klargøring af bliver mere klart, når du forstår, at der findes to kategorier af programmer, der kan være udviklet og integreret med Azure AD:

- Enkelt lejer program: et enkelt lejer program er beregnet til brug i en organisation. Dette er typisk line of business (LoB) programmer, der er skrevet af en enterprise udvikler. En enkelt lejer programmet kun skal åbnes af brugere i én mappe, og som et resultat, skal det kun klargøres i én mappe. Disse programmer registreres typisk af en udvikler i organisationen.


- Flere lejer program: et program med flere lejer er beregnet til brug i mange organisationer, ikke kun én organisation. Dette er typisk software-som-en-(SaaS) tjenesteprogrammer skrevet af en uafhængig softwareproducent (). Flere lejer programmer skal være klargjort i hver mappe, hvor de skal bruges, som kræver, at brugeren eller administratoren samtykke til at registrere dem. Samtykke processen starter, når et program er registreret i kataloget og får adgang til API Graph eller måske en anden web API. Når en bruger eller administrator fra en anden organisation tilmelder sig til at bruge programmet, vises en dialogboks, der viser de tilladelser, der kræver, at programmet. Brugeren eller administratoren kan derefter samtykke programmet, som giver adgang til programmer til de angivne data, og til sidst registrerer programmet i deres mappe. Se [Oversigt over den samtykke, som](active-directory-integrating-applications.md#overview-of-the-consent-framework)du kan finde flere oplysninger.

Nogle yderligere overvejelser opstå, når udvikling af en med flere lejer program i stedet for et enkelt lejer program. Eksempelvis hvis du gør programmet tilgængeligt for brugere i flere mapper, du har brug for en metode til at bestemme, hvilke lejer de er i. Et enkelt lejer program kun skal se ud i et separat mappe for en bruger, mens et program med flere arkitekturer skal bruge til at identificere en bestemt bruger fra alle mapper i Azure AD. For at udføre denne opgave, indeholder Azure AD et almindelige godkendelse slutpunkt, hvor et program med mange brugere kan få logonanmodninger, i stedet for en lejer-specifikke slutpunkt. Dette slutpunkt er https://login.microsoftonline.com/common for alle mapper i Azure AD, mens en lejer-specifikke slutpunkt kan være https://login.microsoftonline.com/contoso.onmicrosoft.com. Almindelige slutpunktet er især vigtigt at overveje, når udvikle dit program, da du skal bruge den nødvendige logik til at håndtere flere lejere under logon, hvor, og token validering.

Hvis du i øjeblikket udvikling af et enkelt lejer til computeren, men du vil gøre det tilgængeligt for mange organisationer, kan du nemt ændre programmet og dens konfiguration i Azure AD for at gøre det med mange brugere kan. Desuden bruger Azure AD den samme signerende nøgle for alle tokens i alle mapper, om du har angivet godkendelse i en enkelt lejer eller flere lejer programmet.

Hvert scenarie, der er angivet i dette dokument indeholder et underordnet afsnit, der beskriver dens klargøring krav. Se [Integrering af programmer med Azure Active Directory](active-directory-integrating-applications.md) til mere dybtgående oplysninger om klargøring af et program i Azure AD og forskellene mellem enkelt og flere lejer programmerne på computeren, kan finde flere oplysninger. Fortsætte med at læse for at forstå de almindelige programscenarier i Azure AD.

## <a name="application-types-and-scenarios"></a>Programmet typer og -scenarier

Hver af de scenarier, der er beskrevet i dette dokument kan udvikles ved hjælp af forskellige sprog og platforme. De understøttes alle af fuldført kodeeksempler, som er tilgængelige i vores [kodeeksempler vejledning](active-directory-code-samples.md), eller direkte fra den tilsvarende [Github eksempel lagre](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory). Desuden, hvis dit program kræver et bestemt stykke eller et afsnit i segmentet i et scenarie til slut, kan i de fleste tilfælde denne funktionalitet tilføjes uafhængigt af hinanden. For eksempel, hvis du har en oprindelige program, der kalder en web API, kan du nemt tilføje et webprogram, der også kalder web API. I følgende diagram vises disse scenarier og typer af programmet, og hvordan forskellige komponenter kan tilføjes:

![Programmet typer og -scenarier](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Dette er de fem primære programscenarier, der understøttes af Azure AD:

- [Webbrowser til webprogram](#web-browser-to-web-application): en bruger skal have til at logge på et webprogram, der er sikret med Azure AD.

- [Enkelt side program (SPA)](#single-page-application-spa): en bruger skal logge på en enkelt side-program, der er sikret med Azure AD.

- [Oprindelige program til Web API](#native-application-to-web-api): en oprindelige program, der kører på en telefon, tablet eller en PC skal godkendes af en bruger for at få ressourcer fra en web API, der er sikret med Azure AD.

- [Webprogrammet Web API](#web-application-to-web-api): et webprogram skal få ressourcer fra en web API sikret med Azure AD.

- [Daemon eller Server programmer til Web API](#daemon-or-server-application-to-web-api): en daemonprogram eller et server-program uden web brugergrænseflade skal få ressourcer fra en web API sikret med Azure AD.

### <a name="web-browser-to-web-application"></a>Webbrowser til webprogram

I dette afsnit beskrives et program, der godkender en bruger i en webbrowser til et webprogram. I dette scenarie skal omdirigerer webprogrammet brugerens browser til at logge dem på Azure AD. Azure AD returnerer svar logon via brugerens browser, som indeholder krav om brugeren i et sikkerhedstoken. Dette scenarie understøtter sign-on – ved hjælp af WS-sammenslutning, SAML 2.0 og OpenID forbinde protokoller.


#### <a name="diagram"></a>Diagram
![Godkendelse flow til browser for at webprogram](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### <a name="description-of-protocol-flow"></a>Beskrivelse af Protocol Flow


1. Når en bruger besøger programmet og de skal logge på, er de omdirigeret via en logon-anmodning til godkendelse slutpunktet i Azure AD.


2. Brugeren logger ind på siden Log på.


3. Hvis godkendelse er gået igennem, Azure AD opretter en godkendelse token og returnerer en logon-svar til programmets svar URL, der blev konfigureret på portalen Azure administration. Denne URL-adresse til svar skal være HTTPS til et fremstilling program. Det returnerede token omfatter krav om bruger og Azure AD, der kræves af programmet til at validere tokenet.


4. Programmet validerer tokenet ved hjælp af en offentlig signerende nøgle og udsteder oplysninger tilgængelige på dokumentets sammenslutning metadata til Azure AD. Når programmet valideret tokenet, starter Azure AD en ny session med brugeren. Denne session kan brugeren adgang til programmet, før det udløber.


#### <a name="code-samples"></a>Kodeeksempler


Du kan se kodeeksemplerne for webbrowser til webprogram scenarier. Og kig forbi ofte – vi tilføje nye prøver hele tiden. [Webbrowser til webprogram](active-directory-code-samples.md#web-browser-to-web-application).


#### <a name="registering"></a>Registrere


- Enkelt lejer: Hvis du bygger et program kun er for din organisation, den skal være registreret i din virksomhed directory ved hjælp af portalen Azure administration.


- Flere lejer: Hvis du bygger et program, der kan anvendes af brugere uden for organisationen, den skal være registreret i virksomhedens adresseliste, men også skal være registreret i hver organisations bibliotek, der skal bruge programmet. Hvis du vil gøre programmet tilgængelig i deres mappe, kan du medtage en tilmeldingsprocessen til dine kunder, der giver dem tilladelse til dit program. Når de tilmelder sig dit program, vises de der en dialogboks, der viser de tilladelser, der kræver, at programmet, og derefter mulighed for at samtykke. Afhængigt af de nødvendige tilladelser, en administrator i anden organisationen muligvis give samtykke. Når brugeren eller administratoren giver du sit samtykke, er programmet registreret i deres mappe. Du kan finde yderligere oplysninger finder [Integrering af programmer med Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Token udløb

Brugerens session udløber, når levetiden for tokenet udstedt af Azure AD udløber. Programmet kan afkorte denne periode, hvis du vil, såsom logge af brugere, der er baseret på et tidsrum uden aktivitet. Når sessionen udløber, bliver brugeren bliver bedt om at logge på igen.





### <a name="single-page-application-spa"></a>Enkelt side program (SPA)

I dette afsnit beskrives godkendelse for en enkelt side-program, som bruger Azure AD og OAuth 2.0 implicit tilladelsen giver for at sikre dens web API tilbage afslutte. Enkelt side programmer er typisk struktureret som et JavaScript-præsentation lag (front-end), der kører i browseren og Web API back-end, der kører på en server og implementerer programmets forretningslogik. For at få mere for at vide om implicit godkendelse Giv og hjælp, du beslutter, om det er perfekt til scenariet programmet på computeren, kan du se [om OAuth2 implicit Giv strømmen i Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

I dette scenarie, når brugeren logger på, af JavaScript front end bruger [Active Directory Authentication Library JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) og yde implicit tilladelse til at hente et ID-token (id_token) fra Azure AD. Tokenet cachelagres, som klienten vedhæftes anmodningen som bærertokenet når du foretager opkald til dens Web API tilbage end, som er beskyttet med OWIN programmer. 
#### <a name="diagram"></a>Diagram

![Enkelt side program-diagram](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Beskrivelse af Protocol Flow

1. Brugeren navigerer til webprogrammet.


2. Programmet returnerer JavaScript front end (præsentation layer) til browseren.


3. Brugeren starter Log på, f.eks ved at klikke på en logge på link. FÅ sender til Azure AD godkendelse slutpunkt til at anmode om et ID-token i browseren. Denne anmodning omfatter klient-ID og svar URL-adressen i parametrene til forespørgslen.


4. Azure AD valideret svar URL-adressen mod registrerede svar URL-adressen, der blev konfigureret på portalen Azure administration.


5. Brugeren logger ind på siden Log på.


6. Hvis godkendelse er gået igennem, Azure AD opretter et ID-token og returnerer den som en URL-adresse-fragment (#) til programmets svar URL-adresse. Denne URL-adresse til svar skal være HTTPS til et fremstilling program. Det returnerede token omfatter krav om bruger og Azure AD, der kræves af programmet til at validere tokenet.


7. JavaScript-klient-kode, der kører i browseren henter tokenet fra svar til brug i sikring af opkald til programmets web API tilbage afsluttes.


8. Browseren kalder programmets web API tilbage slutter med adgangstoken i overskriften godkendelse.

#### <a name="code-samples"></a>Kodeeksempler


Se kodeeksempler for scenarier med enkelt side program (SPA). Sørg for at Kig forbi ofte – vi tilføje nye prøver hele tiden. [Enkelt side program (SPA)](active-directory-code-samples.md#single-page-application-spa).


#### <a name="registering"></a>Registrere


- Enkelt lejer: Hvis du bygger et program kun er for din organisation, den skal være registreret i din virksomhed directory ved hjælp af portalen Azure administration.


- Flere lejer: Hvis du bygger et program, der kan anvendes af brugere uden for organisationen, den skal være registreret i virksomhedens adresseliste, men også skal være registreret i hver organisations bibliotek, der skal bruge programmet. Hvis du vil gøre programmet tilgængelig i deres mappe, kan du medtage en tilmeldingsprocessen til dine kunder, der giver dem tilladelse til dit program. Når de tilmelder sig dit program, vises de der en dialogboks, der viser de tilladelser, der kræver, at programmet, og derefter mulighed for at samtykke. Afhængigt af de nødvendige tilladelser, en administrator i anden organisationen muligvis give samtykke. Når brugeren eller administratoren giver du sit samtykke, er programmet registreret i deres mappe. Du kan finde yderligere oplysninger finder [Integrering af programmer med Azure Active Directory](active-directory-integrating-applications.md).

Når du registrerer programmet, skal den konfigureres for at bruge OAuth 2.0 Implicit Giv-protokollen. Denne protokol er som standard deaktiveret for programmer. For at aktivere OAuth2 Implicit Giv protokollen for dit program skal du hente dens programmanifest fra Azure Management-portalen, værdien "oauth2AllowImplicitFlow" til SAND og overfør derefter manifest tilbage til portalen. Du kan finde detaljerede oplysninger, [Så OAuth 2.0 Implicit Giv for enkelt side-programmer](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Token udløb

Når du bruger ADAL.js til at administrere godkendelse med Azure AD, kan du drage fordel af flere funktioner, som det er nemmere at opdatere et udløbet token samt få tokens for yderligere web API ressourcer, der kan kaldes af programmet. Når brugeren godkender korrekt med Azure AD, oprettes en session, sikret ved en cookie for brugeren i browseren og Azure AD. Det er vigtigt at være opmærksom på, at sessionen findes mellem brugeren og Azure AD og ikke mellem brugeren og webprogrammet kører på serveren. Når et token udløber, bruger ADAL.js for denne session til at hente en anden token automatisk. Det gøres ved hjælp af en skjult iFrame til at sende og modtage anmodningen ved hjælp af OAuth Implicit Giv-protokollen. ADAL.js kan også bruge denne samme metode til at hente uovervåget access tokens fra Azure AD for andre web API-ressourcer, som programmet opkald som disse ressourcer understøtter tværs origin ressourcedeling (CORS), der er registreret i brugerens mappe og eventuelle nødvendige samtykke fik af brugeren under logon.


### <a name="native-application-to-web-api"></a>Oprindelige program til Web API


I dette afsnit beskrives en oprindelige program, der kalder en web API på vegne af en bruger. Dette scenario er bygget på typen OAuth 2.0 godkendelse kode Giv med en offentlig klient, som beskrevet i afsnittet 4.1 i [OAuth 2.0-specifikationen](http://tools.ietf.org/html/rfc6749). Det oprindelige program henter en adgangstoken til brugeren ved hjælp af OAuth 2.0-protokollen. Denne adgangstoken sendes i anmodningen på World Wide web API, som godkender brugeren og returnerer den ønskede ressource.

#### <a name="diagram"></a>Diagram

![Oprindelige program til Web API-Diagram](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Godkendelse flow for oprindelige program til API

#### <a name="description-of-protocol-flow"></a>Beskrivelse af Protocol Flow


Hvis du bruger AD godkendelse biblioteker, håndteres de fleste protocol detaljer beskrevet nedenfor for dig, som browseren pop op-vindue, token cachelagring og håndtering af Opdater tokens.

1. Bruge en pop op, det oprindelige program giver en anmodning til godkendelse slutpunktet i Azure AD browser. Denne anmodning omfatter klient-ID og Omdiriger URI af det oprindelige program, som vist i portalen administration og ID URI-programmet til World Wide web API. Hvis brugeren ikke er logget på, de bliver bedt om at logge på igen


2. Azure AD godkender brugeren. Hvis det er et program med flere arkitekturer og samtykke er påkrævet for at bruge programmet på computeren, skal brugeren tilladelse, hvis de ikke allerede har gjort det. Når du har tilladelsen og efter vellykket bekræftelse udsteder Azure AD et godkendelse kode svar tilbage til den klientprogrammet Omdiriger URI.


3. Når Azure AD problemer er et godkendelse kode svar tilbage til Omdiriger URI klientprogrammet stopper browser interaktion og henter koden tilladelse fra svaret. Ved hjælp af denne tilladelse kode sender klientprogrammet en anmodning til Azure AD token slutpunkt, der omfatter godkendelseskoden, detaljer om klientprogrammet (klient-ID og Omdiriger URI), og den ønskede ressource (program-ID URI til World Wide web API).


4. Godkendelseskode samt oplysninger om klient-program og web API er godkendt af Azure AD. Når bekræftelsen er fuldført, returnerer Azure AD to tokens: en JWT adgangstoken og et JWT Opdater token. Desuden returnerer Azure AD grundlæggende oplysninger om brugeren, som deres viste navn og lejer-ID.


5. Over HTTPS bruges klientprogrammet returnerede JWT adgangstoken til at tilføje JWT strengen med en "Bæreren" angivelse i overskriften godkendelse af anmodning på World Wide web API. World Wide web API derefter valideret JWT tokenet, og hvis validering lykkes, returnerer den ønskede ressource.


6. Når adgangstoken udløber, får klientprogrammet en fejl, der angiver brugeren skal godkendes igen. Hvis programmet, har et gyldigt opdatering token, kan den bruges til at få fat på en ny adgangstoken uden at spørge brugeren til at logge på igen. Hvis Opdater tokenet udløber, skal programmet til interaktivt at godkende brugeren igen.


> [AZURE.NOTE] Opdater tokenet udstedt af Azure AD kan bruges til at få adgang til flere ressourcer. Hvis du har en klientprogrammet, der har tilladelse til at ringe til to web API'er, kan der for eksempel bruges Opdater tokenet til at få en adgang token på andre internettet API samt.


#### <a name="code-samples"></a>Kodeeksempler


Se eksemplerne til oprindelige program til Web API scenarier. Og kig forbi ofte – vi tilføje nye prøver hele tiden. [Oprindelige program til Web API](active-directory-code-samples.md#native-application-to-web-api).


#### <a name="registering"></a>Registrere


- Enkelt lejer: Oprindeligt findes begge programmet og World Wide web API skal være registreret i den samme mappe i Azure AD. World Wide web API kan konfigureres til at få vist et sæt af tilladelser, der bruges til at begrænse det oprindelige program adgang til dens ressourcer. Klientprogrammet derefter markerer de ønskede tilladelser fra rullemenuen "Tilladelser til andre programmer" i portalen Azure administration.


- Flere lejer: Først det oprindelige program kun nogensinde registreret i udvikleren eller Publishers mappe. Andet, det oprindelige program er konfigureret til at angive de tilladelser, der kræves til være funktionelle. Denne liste over de nødvendige tilladelser vises i en dialogboks, når en bruger eller administrator i destinationsmappen giver samtykke til programmet, som gør det tilgængeligt for deres organisation. Nogle programmer kræver blot brugerniveau tilladelser, som alle brugere i organisationen kan samtykke til. Andre programmer kræver administratorrettigheder, som en bruger i organisationen ikke kan samtykke til. Kun en directory-administrator kan give tilladelse til programmer, der kræver dette niveau af tilladelser. Når brugeren eller administratoren giver du sit samtykke, registreres kun web API i deres mappe. Du kan finde yderligere oplysninger finder [Integrering af programmer med Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Token udløb


Når det oprindelige program bruger koden tilladelse til at hente en JWT adgang token, modtager den også et JWT Opdater token. Når adgangstoken udløber, kan Opdater tokenet bruges til at godkende brugeren igen uden at de skal logge på igen. Denne opdatering token derefter bruges til at godkende brugeren, hvilket resulterer i en ny adgangstoken og Opdater token.





### <a name="web-application-to-web-api"></a>Webprogrammet Web API


I dette afsnit beskrives et webprogram, der skal få ressourcer fra en web API. I dette scenarie, der er to typer af identitet, webprogrammet kan bruge til at godkende, og kald web API: et program-id eller et delegeret bruger-id.

*Program-id:* Dette scenarie bruger OAuth 2.0-klienten legitimationsoplysninger Giv til at godkende som programmet, og få adgang til internettet API. Når du bruger et program-id, internettet API kan kun registrere, webprogrammet ringer, som World Wide web modtager API ikke alle oplysninger om brugeren. Hvis programmet modtager oplysninger om brugeren, sendes via programmet protokollen og den er signeret ikke af Azure AD. World Wide web API har tillid til, at webprogrammet godkendes brugeren. Derfor hedder dette mønster et undersystem, der er tillid til.

*Delegerede bruger-id:* Dette scenarie kan gøres på to måder: OpenID Tilslut, og OAuth 2.0 godkendelse kode Giv med en fortrolige klient. Webprogrammet henter en adgangstoken til den bruger, som viser sig på World Wide web API, som brugeren er godkendt webprogrammet og som webprogrammet var i stand til at hente en delegeret brugeridentitet for at ringe web API. Denne adgangstoken sendes i anmodningen på World Wide web API, som godkender brugeren og returnerer den ønskede ressource.

#### <a name="diagram"></a>Diagram

![Webprogrammet Web API-diagram](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### <a name="description-of-protocol-flow"></a>Beskrivelse af Protocol Flow

Både program-id og delegeret bruger identitet typer gennemgås i strømmen nedenfor. Vigtige forskellen mellem dem er, at delegeret bruger-id først skal have en godkendelseskode for, før brugeren kan logge på og få adgang til internettet API.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Program-id med OAuth 2.0-klienten legitimationsoplysninger Giv

1. En bruger er logget på Azure AD i webprogrammet (se [Webbrowser til webprogram](#web-browser-to-web-application) ovenfor).


2. Webprogrammet skal anskaffe et adgangstoken, så den kan godkende på World Wide web API og hente den ønskede ressource. Det giver en anmodning til Azure AD token slutpunkt, give legitimationsoplysninger, klient-ID og -API'EN webprogrammet URI-ID.


3. Azure AD godkender programmet og returnerer en JWT adgangstoken, der bruges til at ringe til World Wide web API.


4. Over HTTPS bruges webprogrammet returnerede JWT adgangstoken til at tilføje JWT strengen med en "Bæreren" angivelse i overskriften godkendelse af anmodning på World Wide web API. World Wide web API derefter valideret JWT tokenet, og hvis validering lykkes, returnerer den ønskede ressource.

##### <a name="delegated-user-identity-with-openid-connect"></a>Delegeret bruger-id med OpenID forbindelse

1. En bruger er logget på et webprogram, ved hjælp af Azure AD (se afsnittet [Webbrowser til webprogram](#web-browser-to-web-application) ovenfor). Hvis brugeren af webprogrammet ikke har endnu accepteret til at tillade webprogrammet til at ringe til World Wide web API på dens vegne, skal brugeren samtykke. Programmet, vises de tilladelser, der kræver, og hvis et af følgende er administratorrettigheder, en normal bruger i mappen ikke vil kunne samtykke. Denne proces samtykke gælder kun for flere lejer programmer, ikke enkelt lejer programmer, som programmet, der allerede har de nødvendige tilladelser. Når brugeren er logget på, modtaget webprogrammet et ID token med oplysninger om brugeren, samt en godkendelseskode for.


2. Ved hjælp af godkendelseskoden udstedt af Azure AD sender-webprogrammet en anmodning til Azure AD token slutpunkt, der omfatter godkendelseskoden, detaljer om klientprogrammet (klient-ID og Omdiriger URI), og den ønskede ressource (program-ID URI til World Wide web API).


3. Godkendelseskode og oplysninger om webprogram og web API er godkendt af Azure AD. Når bekræftelsen er fuldført, returnerer Azure AD to tokens: en JWT adgangstoken og et JWT Opdater token.


4. Over HTTPS bruges webprogrammet returnerede JWT adgangstoken til at tilføje JWT strengen med en "Bæreren" angivelse i overskriften godkendelse af anmodning på World Wide web API. World Wide web API derefter valideret JWT tokenet, og hvis validering lykkes, returnerer den ønskede ressource.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegeret bruger-id med OAuth 2.0 godkendelse kode Giv

1. En bruger er logget på til et webprogram, hvis godkendelsesmetoden er uafhængig af Azure AD.


2. Webprogrammet kræver en kode for tilladelse til at købe et adgangstoken, så den udsteder en anmodning om via browseren til Azure AD godkendelse slutpunkt, give klient-ID og omdirigere URI webprogrammet efter vellykket godkendelse. Brugeren logger ind til Azure AD.


3. Hvis brugeren af webprogrammet ikke har endnu accepteret til at tillade webprogrammet til at ringe til World Wide web API på dens vegne, skal brugeren samtykke. Programmet, vises de tilladelser, der kræver, og hvis et af følgende er administratorrettigheder, en normal bruger i mappen ikke vil kunne samtykke. Denne proces samtykke gælder kun for flere lejer programmer, ikke enkelt lejer programmer, som programmet, der allerede har de nødvendige tilladelser.


4. Når brugeren har accepteret, modtager webprogrammet godkendelse koden, som det skal anskaffe et adgangstoken.


5. Ved hjælp af godkendelseskoden udstedt af Azure AD sender-webprogrammet en anmodning til Azure AD token slutpunkt, der omfatter godkendelseskoden, detaljer om klientprogrammet (klient-ID og Omdiriger URI), og den ønskede ressource (program-ID URI til World Wide web API).


6. Godkendelseskode og oplysninger om webprogram og web API er godkendt af Azure AD. Når bekræftelsen er fuldført, returnerer Azure AD to tokens: en JWT adgangstoken og et JWT Opdater token.


7. Over HTTPS bruges webprogrammet returnerede JWT adgangstoken til at tilføje JWT strengen med en "Bæreren" angivelse i overskriften godkendelse af anmodning på World Wide web API. World Wide web API derefter valideret JWT tokenet, og hvis validering lykkes, returnerer den ønskede ressource.

#### <a name="code-samples"></a>Kodeeksempler

Se eksemplerne til webprogrammet Web API scenarier. Og kig forbi ofte – vi tilføje nye prøver hele tiden. - [Programmet til Web API](active-directory-code-samples.md#web-application-to-web-api).


#### <a name="registering"></a>Registrere

- Enkelt lejer: Til både program-id og delegeret bruger identitet tilfælde, webprogrammet og web API skal være registreret i den samme mappe i Azure AD. World Wide web API kan konfigureres til at få vist et sæt af tilladelser, der bruges til at begrænse den webprogram adgang til dens ressourcer. Hvis der bruges en delegeret bruger identitetstype, skal Vælg de ønskede tilladelser fra rullemenuen "Tilladelser til andre programmer" i portalen Azure Management webprogrammet. Dette trin er ikke påkrævet, hvis identitet programtype bruges.


- Flere lejer: Først webprogrammet er konfigureret til at angive de tilladelser, der kræves til være funktionelle. Denne liste over de nødvendige tilladelser vises i en dialogboks, når en bruger eller administrator i destinationsmappen giver samtykke til programmet, som gør det tilgængeligt for deres organisation. Nogle programmer kræver blot brugerniveau tilladelser, som alle brugere i organisationen kan samtykke til. Andre programmer kræver administratorrettigheder, som en bruger i organisationen ikke kan samtykke til. Kun en directory-administrator kan give tilladelse til programmer, der kræver dette niveau af tilladelser. Når brugeren eller administratoren giver du sit samtykke, registreres webprogrammet og web API begge i deres mappe.

#### <a name="token-expiration"></a>Token udløb

Når webprogrammet bruger koden tilladelse til at hente en JWT adgang token, modtager den også et JWT Opdater token. Når adgangstoken udløber, kan Opdater tokenet bruges til at godkende brugeren igen uden at de skal logge på igen. Denne opdatering token derefter bruges til at godkende brugeren, hvilket resulterer i en ny adgangstoken og Opdater token.


### <a name="daemon-or-server-application-to-web-api"></a>Daemon eller Server programmer til Web API


I dette afsnit beskrives en daemon eller server-program, der skal få ressourcer fra en web API. Der er to underordnede scenarier, der gælder for dette afsnit: en daemon, der skal ringe til en web API, bygget på OAuth 2.0 legitimationsoplysninger Giv klienttype; og et server-program (som en web API), der skal ringe til en web API, bygget på OAuth 2.0 On-Behalf-Of kladde specifikation.

For dette scenario ved en daemonprogram skal ringe til en web API, er det vigtigt at forstå et par ting. Først skal er brugerinput ikke muligt med en daemonprogram, som kræver programmet have sin egen identitet. Et eksempel på en daemonprogram er en kørsel, eller en operativsystem-tjeneste, der kører i baggrunden. Denne type program anmoder om et adgangstoken ved hjælp af dens program-id og præsenterer sin klient-ID, legitimationsoplysninger (adgangskode eller certifikat) og program-ID URI til Azure AD. Efter vellykket godkendelse modtager daemonen et adgangstoken fra Azure AD, som skal bruges til at ringe til World Wide web API.

Til dette scenario når et server-program har brug for til at ringe til en web API, er det nyttigt at bruge et eksempel. Forestil dig, at en bruger er godkendt på en oprindelige program, og denne oprindelige program skal ringe til en web API. Azure AD udsteder en JWT adgangstoken for at ringe web API. Hvis der skal web API til at ringe til en anden efterfølgende web API, kan det bruge på vegne af strømmen til stedfortræder brugerens identitet og godkende sekundære web API.

#### <a name="diagram"></a>Diagram

![Daemon eller Server-programmet til Web API-diagram](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Beskrivelse af Protocol Flow

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Program-id med OAuth 2.0-klienten legitimationsoplysninger Giv

1. Først skal skal serverprogrammet godkende med Azure AD som sig selv uden mennesker som en interaktiv sign-on – dialog. Det giver en anmodning til Azure AD token slutpunkt, der indeholder de legitimationsoplysninger, klient-ID og ID URI-program.


2. Azure AD godkender programmet og returnerer en JWT adgangstoken, der bruges til at ringe til World Wide web API.


3. Over HTTPS bruges webprogrammet returnerede JWT adgangstoken til at tilføje JWT strengen med en "Bæreren" angivelse i overskriften godkendelse af anmodning på World Wide web API. World Wide web API derefter valideret JWT tokenet, og hvis validering lykkes, returnerer den ønskede ressource.


##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegeret bruger-id med OAuth 2.0 på vegne af kladde specifikation

Strømmen beskrevet nedenfor antages det, at en bruger er blevet godkendt på et andet program (som en oprindelige program), og deres brugeridentitet er blevet brugt til at hente et adgangstoken på første niveau internettet API.

1. Det oprindelige program sender adgangstoken til første niveau web API.


2. Det første niveau web API sender en anmodning til Azure AD token slutpunkt, give sin klient-ID og legitimationsoplysninger, samt brugerens adgangstoken. Desuden anmodningen, sendes med en on_behalf_of parameter, der angiver World Wide web API anmoder om nye tokens til at ringe til en efterfølgende web API på vegne af den oprindelige bruger.


3. Azure AD kontrollerer, at første lag web API har tilladelser til at få adgang til den sekundære web API og godkender anmodningen, der returnerer en JWT adgangstoken og en JWT Opdater token på første niveau internettet API.


4. Det første niveau web API kalder over HTTPS, derefter sekundære web API ved at tilføje token strengen i overskriften godkendelse i anmodningen. Første lag web API kan fortsætte med at ringe sekundære web API, så længe adgangstoken og Opdater tokens er gyldige.

#### <a name="code-samples"></a>Kodeeksempler

Se eksemplerne til Daemon eller Server programmer til Web API scenarier. Og kig forbi ofte – vi tilføje nye prøver hele tiden. [Server eller daemonprogram til Web API](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>Registrere

- Enkelt lejer: Til både program-id og delegeret bruger identitet tilfælde, programmet daemon eller server skal være registreret i den samme mappe i Azure AD. World Wide web API kan konfigureres til at få vist et sæt af tilladelser, der bruges til at begrænse daemonen eller serverens adgang til dens ressourcer. Hvis en delegeret bruger identitetstype bruges, skal serverprogrammet til at vælge de ønskede tilladelser fra rullemenuen "Tilladelser til andre programmer" i portalen Azure administration. Dette trin er ikke påkrævet, hvis identitet programtype bruges.


- Flere lejer: Først programmet daemon eller server er konfigureret for at angive de tilladelser, der kræves til være funktionelle. Denne liste over de nødvendige tilladelser vises i en dialogboks, når en bruger eller administrator i destinationsmappen giver samtykke til programmet, som gør det tilgængeligt for deres organisation. Nogle programmer kræver blot brugerniveau tilladelser, som alle brugere i organisationen kan samtykke til. Andre programmer kræver administratorrettigheder, som en bruger i organisationen ikke kan samtykke til. Kun en directory-administrator kan give tilladelse til programmer, der kræver dette niveau af tilladelser. Når brugeren eller administratoren giver du sit samtykke, registreret begge over internettet API'er i deres directory.

#### <a name="token-expiration"></a>Token udløb

Når det første program bruger koden tilladelse til at hente en JWT adgang token, modtager den også et JWT Opdater token. Når adgangstoken udløber, kan Opdater tokenet bruges til at godkende brugeren uden at spørge om legitimationsoplysninger igen. Denne opdatering token derefter bruges til at godkende brugeren, hvilket resulterer i en ny adgangstoken og Opdater token.

## <a name="see-also"></a>Se også

[Azure Active Directory Developer Guide](active-directory-developers-guide.md)

[Azure Active Directory kodeeksempler](active-directory-code-samples.md)

[Vigtige oplysninger om at logge vigtige overgang på Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)

<properties
   pageTitle="Azure Active Directory udvikler ordliste | Microsoft Azure"
   description="En liste over vilkårene for de ofte anvendte Azure Active Directory udvikler begreber og funktioner."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="bryanla"/>

# <a name="azure-active-directory-developer-glossary"></a>Azure Active Directory udvikler ordliste
I denne artikel indeholder definitioner for nogle af de grundlæggende Azure Active Directory (AD) udvikler begreber, som er nyttige, når lære om udviklingen af programmer til Azure AD.

## <a name="access-token"></a>adgangstoken
En type af [sikkerhedstoken](#security-token) udstedt af en [tilladelse server](#authorization-server)og bruges af en [klientprogrammet](#client-application) for at få adgang til en [beskyttet ressource server](#resource-server). Typisk i form af en [JSON Web Token (JWT)][JWT], tokenet indeholder tilladelse til klienten, som [ressource ejer](#resource-owner), til en ønskede adgangsniveau. Tokenet indeholder alle relevante [krav](#claim) om emnet, så klientprogrammet og bruge det som en formular af legitimationsoplysninger, når du åbner en given ressource. Dette fjerner også behovet for ressource ejeren til at fremvise legitimationsoplysninger til klienten.

Access tokens er nogle gange kaldet "Bruger + App" eller "App kun", afhængigt af de legitimationsoplysninger, der repræsenteres. For eksempel, når en klientprogrammet bruger den:

- ["Godkendelse-kode" godkendelse give](#authorization-grant)slutbrugerens godkender først som ejer af ressource uddelegere tilladelse til klienten til at få adgang til ressourcen. Klienten godkender senere, når den henter adgangstoken. Tokenet kan være kaldes mere specifikt et "Bruger + App"-token, som den repræsenterer både den bruger, der godkendt klientprogrammet og programmet på computeren.
- ["Klient legitimationsoplysninger" godkendelse give](#authorization-grant), klienten giver den eneste godkendelse, fungerer uden ressource-ejerens godkendelse/tilladelse, så tokenet kan være kaldes en "Kun App"-token.

Se [Reference til Azure AD Token] [ AAD-Tokens-Claims] kan finde flere oplysninger.

## <a name="application-manifest"></a>progammanifest  
En funktion i [Azure klassisk portal][AZURE-classic-portal], som giver en JSON repræsentation af programmets identitet konfiguration, bruges som en metode til opdatering af det tilknyttede [program] [ AAD-Graph-App-Entity] og [ServicePrincipal] [ AAD-Graph-Sp-Entity] enheder. Kan du se [om programmanifestet Azure Active Directory] [ AAD-App-Manifest] kan finde flere oplysninger.

## <a name="application-object"></a>Application-objektet  
Når du register opdatere et program i [Azure klassisk portal][AZURE-classic-portal], portalen opretter/opdateringer både et programobjekt og et tilsvarende [service primært objekt](#service-principal-object) for lejeren. Programmet objekt *definerer* programmet er identitet konfiguration globalt (på tværs af alle lejere, hvor det har adgang), give en skabelon, hvorfra dens tilsvarende service vigtigste objekter er *udledt* til brug lokalt på kørselstidspunktet (i en bestemt lejer).

Se [programmet på computeren og tjeneste hovedstolen objekter] [ AAD-App-SP-Objects] kan finde flere oplysninger.

## <a name="application-registration"></a>programmet registrering  
For at tillade et program at integrere med og uddelegere tilgængelig funktioner til Azure AD, skal den være registreret hos en Azure AD- [lejer](#tenant). Når du har registreret dit program med Azure AD, har du angivet en identitet konfiguration for dit program, give tilladelse til at integrere med Azure AD og bruge funktioner som f.eks.:

- Robust styring af Single Sign-On ved hjælp af Azure AD identitet administration og [Oprette forbindelse OpenID] [ OpenIDConnect] protocol implementering
- Formidlet adgang til [ressourcer, der er beskyttet](#resource-server) af [klientprogrammer](#client-application), via Azure AD OAuth 2.0 [godkendelse server](#authorization-server) implementering
- [Samtykke framework](#consent) for administration af Klientadgang til beskyttede ressourcer, baseret på ressource ejergodkendelse.

Se [integration programmer med Azure Active Directory] [ AAD-Integrating-Apps] kan finde flere oplysninger.

## <a name="authentication"></a>godkendelse
Act af udfordring fest for gyldige legitimationsoplysninger, give grundlag for oprettelse af en sikkerhedskonto skal bruges til identitet og adgangskontrol. Under en [OAuth2 godkendelse give](#authorization-grant) eksempelvis part godkendelse udfylde rolle [ressource ejer](#resource-owner) eller [-klientprogrammet](#client-application), afhængigt af ydelse bruges.

## <a name="authorization"></a>godkendelse
Kunsten at give en godkendt sikkerhed vigtigste tilladelse til at gøre noget. Der er to primære use cases i den programming Azure AD-model:

- Under en [OAuth2 godkendelse give](#authorization-grant) flow: Når [ressource ejer](#resource-owner) giver tilladelse til at [klientprogrammet](#client-application), så klienten til at få adgang til ejeren ressource ressourcer.
- Under adgang til ressourcen af klienten: som implementeret af [ressource server](#resource-server), ved hjælp af det [gør du krav på](#claim) værdier præsentere i [adgangstoken](#access-token) til at foretage access kontrolelement beslutninger baseret på dem.

## <a name="authorization-code"></a>godkendelseskode
En kort leve "token" leveres til en [klientprogrammet](#client-application) af [godkendelse slutpunkt](#authorization-endpoint), som en del af strømmen "godkendelseskode" en af de fire OAuth2 [tilladelse giver](#authorization-grant). Koden returneres til klientprogrammet som svar på godkendelse af en [ressource ejer](#resource-owner), der angiver ressource ejeren har delegerede tilladelse til at få adgang til de anmodede ressourcer. Koden er senere har indløst for et [adgangstoken](#access-token)som en del af strømmen.

## <a name="authorization-endpoint"></a>godkendelse slutpunkt
Et af slutpunkterne implementeret af [godkendelse server](#authorization-server), bruges til at interagere med [ressource ejer](#resource-owner) for at angive en [give tilladelse](#authorization-grant) under OAuth2 tilladelse give forløb. Afhængigt af give tilladelse strømmen bruges, kan faktisk ret variere, herunder en [tilladelse kode](#authorization-code) eller [sikkerhedstoken](#security-token).

Se specifikationen OAuth2 [godkendelse give typer] [ OAuth2-AuthZ-Grant-Types] og [godkendelse slutpunkt] [ OAuth2-AuthZ-Endpoint] sektioner og [OpenIDConnect specifikation] [ OpenIDConnect-AuthZ-Endpoint] kan finde flere oplysninger.

## <a name="authorization-grant"></a>Giv tilladelse
En legitimationsoplysninger, der repræsenterer [ressource ejerens](#resource-owner) [tilladelse](#authorization) til at få adgang til dets beskyttede ressourcer, der er tildelt til en [-klientprogrammet](#client-application). En klientprogrammet kan bruge en af de [fire Giv typer, der er defineret af OAuth2 godkendelse Framework] [ OAuth2-AuthZ-Grant-Types] til at hente en bonus, afhængigt af klienten type/krav: "godkendelse kode Giv", "klient legitimationsoplysninger give", "implicit Giv" og "ressource ejer adgangskode legitimationsoplysninger give". De legitimationsoplysninger, der returneres til klienten er enten et [adgangstoken](#access-token), eller en [godkendelseskode](#authorization-code) (udvekslet senere for et adgangstoken), afhængigt af typen tilladelse Giv bruges.

## <a name="authorization-server"></a>godkendelse server
Som defineret af [OAuth2 godkendelse Framework][OAuth2-Role-Def], den server, der er ansvarlig for udstedelse access tokens til [klienten](#client-application) efter korrekt godkendelse [ressource ejer](#resource-owner) og bestille tilladelsen. Et [klientprogrammet](#client-application) skal arbejde sammen med godkendelse serveren på kørselstidspunktet via [godkendelse](#authorization-endpoint) og [token](#token-endpoint) slutpunkter i overensstemmelse med OAuth2 defineret [tilladelse giver](#authorization-grant).

I forbindelse med Azure AD-programintegration Azure AD implementerer rollen godkendelse server til Azure AD-programmer og Microsoft-tjeneste API'er, for eksempel [Microsoft Graph API'er][Microsoft-Graph].

## <a name="claim"></a>gøre krav
Et [sikkerhedstoken](#security-token) indeholder krav, som understøtter påstande om ét objekt (som [klientprogrammet](#client-application) eller [ressource ejer](#resource-owner)) til en anden enhed (såsom [ressource server](#resource-server)). Krav er navn/værdi-par, overføre oplysninger om token emnet (for eksempel den sikkerhedsadministrator, der blev godkendt af [godkendelse server](#authorization-server)). Findes i en given token krav er afhængig af flere variabler, herunder typen token, hvilke typer legitimationsoplysninger, der bruges til at godkende emnet, programkonfiguration, osv.

Se [Azure AD token reference] [ AAD-Tokens-Claims] kan finde flere oplysninger.

## <a name="client-application"></a>klientprogrammet  
Som defineret af [OAuth2 godkendelse Framework][OAuth2-Role-Def], et program, der gør beskyttet ressource mødeindkaldelser på vegne af [ressource ejer](#resource-owner). Ordet "klient" indebærer ikke specifikke hardware implementering egenskaber (for eksempel om programmet udføres på en server, en stationær eller andre enheder).  

En klientprogrammet anmoder om [godkendelse](#authorization) fra ejer af en ressource til at deltage i et [OAuth2 godkendelse give](#authorization-grant) flow og kan få adgang til API'er/data på ressource ejerens vegne. OAuth2 godkendelse Framework [definerer to typer af klienter][OAuth2-Client-Types], "Fortroligt" og "offentlige", afhængigt af klientens mulighed for at bevare fortroligheden af dens legitimationsoplysninger. Programmer kan implementere en [webklienten (fortrolige)](#web-client) , der kører på en webserver, en [Oprindelig-klient (offentlige)](#native-client) , der er installeret på en enhed eller en [bruger-agent-baseret klient (offentlige)](#user-agent-based-client) , der kører i et enhedens browser.

## <a name="consent"></a>samtykke
Processen, hvor en [ressource ejer](#resource-owner) give tilladelse til en [-klientprogrammet](#client-application), bestemte [tilladelser](#permissions) til beskyttede ressourcer på vegne af ejeren af ressource. Afhængigt af de tilladelser, der er blevet anmodet af klienten, en administrator eller bruger vil blive bedt om at give adgang til deres organisation/individuelle data henholdsvis tilladelse. Bemærk, i et scenarie med [flere lejer](#multi-tenant-application) , programmets [tjeneste vigtigste](#service-principal-object) også er registreret i lejeren for den bruger, consenting.

## <a name="id-token"></a>Id-token
En [OpenID forbinde] [ OpenIDConnect-ID-Token] [sikkerhedstoken](#security-token) leveres af [godkendelse server](#authorization-server) [godkendelse slutpunkt](#authorization-endpoint), som indeholder [krav](#claim) vedrører godkendelse af en slutbrugerens [ressource ejer](#resource-owner). Som et adgangstoken, ID tokens repræsenteres også som en digitalt signeret [JSON Web Token (JWT)][JWT]. I modsætning til et adgangstoken dog et ID-token krav bruges ikke til formål, der er relateret til ressource access og adgangskontrol specifikt.

Se [Azure AD token reference] [ AAD-Tokens-Claims] kan finde flere oplysninger.

## <a name="multi-tenant-application"></a>flere lejer program
En klasse af [klientprogrammet](#client-application) , som gør det muligt for logge på og [samtykke](#consent) af brugere klargjort i en hvilken som helst Azure AD- [lejer](#tenant), herunder lejere bortset fra den del, hvor klienten er registreret. Derimod vil et program, der er registreret som single-lejeren, kun tillade logon fra brugerkonti, der er klargjort i samme lejer som den, hvor programmet er registreret. [Oprindelig](#native-client) klientprogrammer er flere lejer som standard, mens [web](#web-client) klientprogrammer har mulighed for at vælge mellem enkelt og flere lejer.

Se, [hvordan du logger på en hvilken som helst Azure AD-bruger, der bruger flere lejer programmet mønster] [ AAD-Multi-Tenant-Overview] kan finde flere oplysninger.

## <a name="native-client"></a>Oprindelig-klient
En type af [klientprogrammet](#client-application) , som oprindeligt er installeret på en enhed. Da al kode, der udføres på en enhed, er det betragtes som en "offentlige" klient på grund af dens manglende evne til at gemme legitimationsoplysninger privat/fortroligt. Se [OAuth2 klienttyper og profiler] [ OAuth2-Client-Types] kan finde flere oplysninger.

## <a name="permissions"></a>tilladelser
Et [klientprogrammet](#client-application) får adgang til en [ressource server](#resource-server) ved erklæring af anmodninger om tilladelser. Der findes to typer:

- "Delegeret" tilladelser, der anmoder om [omfang-baserede](#scopes) adgang under delegerede tilladelse fra [ejeren af ressource](#resource-owner), som logget på, præsenteres ressourcen på kørselstidspunktet som ["scp" krav](#claim) i kundens [adgangstoken](#access-token).
- "Programmet" tilladelser, der anmoder om [rollebaseret](#roles) adgang under den klientprogrammet legitimationsoplysninger/identitet, vises for ressourcen på kørselstidspunktet som ["roller" krav](#claim) i kundens adgangstoken.

De også surface under processen [samtykke](#consent) , hvorved administrator eller ressource ejer mulighed for at give/nægte Klientadgang til ressourcer i deres lejer.

Anmodninger om tilladelser er konfigureret på "Applications" / "Konfigurer" under fanen i [Azure klassisk portal][AZURE-classic-portal], under "Tilladelser til andre programmer", ved at vælge de ønskede "delegerede tilladelser" og "Tilladelser for tjenesteprogram" (dette kræver medlemskab af den globale administratorrolle). Fordi en [offentlige klienten](#client-application) ikke vedligeholde legitimationsoplysninger, kan det kun anmode om delegeret tilladelser, mens en [fortrolige klient](#client-application) har mulighed for at anmodningen begge delegerede og tilladelser for tjenesteprogram. Kundens [application-objektet](#application-object) gemmer de angivne tilladelser i dets [requiredResourceAccess egenskaben][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>ressource ejer
Som defineret af [OAuth2 godkendelse Framework][OAuth2-Role-Def], et objekt, der kan give adgang til en beskyttet ressource. Når ejeren af ressource er en person, kaldes det en slutbruger. For eksempel, når et [klientprogrammet](#client-application) ønsker at få adgang til en brugers postkasse via [Microsoft Graph API][Microsoft-Graph], kræver tilladelse fra ejeren af ressourcer på postkassen.

## <a name="resource-server"></a>ressource-server
Som defineret af [OAuth2 godkendelse Framework][OAuth2-Role-Def], en server, hosts beskyttet ressourcer, som kan acceptere og besvare beskyttet ressource-anmodninger om af [klientprogrammer](#client-application) , der udgør en [få adgang til token](#access-token). Også kaldet en beskyttet ressource server eller ressource program.

En ressource server Fremviser API'er og gennemtvinger adgang til dens beskyttede ressourcer via [områder](#scopes) og [roller](#roles), ved hjælp af OAuth 2.0 godkendelse Framework. Som eksempler kan nævnes Azure AD Graph API som giver adgang til Azure AD-lejer data, og Office 365-API'er, der giver adgang til data som mail og kalender. Begge af følgende er også tilgængelige via [Microsoft Graph API][Microsoft-Graph].  

Ligesom en klientprogrammet etableret ressource programmets identitet konfiguration via [registrering](#application-registration) i en Azure AD-lejer, give programmet og tjeneste primært objekt. Nogle Microsoft-produkter API'er som API Azure AD Graph har allerede registreret service principper for gjort tilgængelig i alle lejere under klargøringen.

## <a name="roles"></a>roller
Sådan [områder](#scopes)giver roller mulighed for en [ressource server](#resource-server) til at styre adgang til dens beskyttede ressourcer. Der er to typer: en "brugerrolle" implementerer rollebaseret adgangskontrol for brugere/grupper, der kræver adgang til ressourcen, mens en "programmet" rolle implementerer det samme for [klientprogrammer](#client-application) , der kræver adgang.

Roller er defineret ressource strenge (for eksempel "udgifter godkender", "Skrivebeskyttet", "Directory.ReadWrite.All"), administrerede [Azure klassisk portal] [ AZURE-classic-portal] via ressourcens [programmanifestet](#application-manifest), og gemmes i ressourcens [appRoles egenskaben][AAD-Graph-Sp-Entity]. Portalen Azure klassisk bruges også til at tildele brugere til "brugerroller", og Konfigurer klienten [tilladelser for tjenesteprogram](#permissions) for at få adgang til en rolle "programmet".

Se en detaljeret beskrivelse af programmet roller, der vises af Azure AD Graph API [Graph API tilladelse områder][AAD-Graph-Perm-Scopes]. Finde en trinvis implementering eksempel, under [Rollebaseret adgangskontrol i skyen-programmer ved hjælp af Azure AD][Duyshant-Role-Blog].

## <a name="scopes"></a>områder
Som [roller](#roles)giver områder mulighed for en [ressource server](#resource-server) til at styre adgang til dens beskyttede ressourcer. Områder, der bruges til at implementere [omfang-baseret] [ OAuth2-Access-Token-Scopes] adgangskontrol for [klientprogrammet](#client-application) , der har fået delegeret adgang til ressourcen af ejeren.

Områder er ressource brugerdefineret strenge (for eksempel "Mail.Read", "Directory.ReadWrite.All") administreres i [Azure klassisk portal] [ AZURE-classic-portal] via ressourcens [programmanifestet](#application-manifest), og gemmes i ressourcens [oauth2Permissions egenskaben][AAD-Graph-Sp-Entity]. Portalen Azure klassisk bruges også til at konfigurere klienten programmet [delegerede tilladelser](#permissions) for at få adgang til et område.

En bedste praksis-navngivningskonvention, er at bruge formatet "resource.operation.constraint". Se en detaljeret beskrivelse af de områder, der vises af Azure AD Graph API [Graph API tilladelse områder][AAD-Graph-Perm-Scopes]. Områder, der vises af Office 365-tjenester i [Office 365 API tilladelser reference][O365-Perm-Ref].

## <a name="security-token"></a>sikkerheds-id
Et signeret dokument, der indeholder krav, som en OAuth2 token eller SAML 2.0-program. For en OAuth2 [give tilladelse](#authorization-grant), et [adgangstoken](#access-token) (OAuth2) og et [Id-Token](OpenID Connect) er typer af sikkerhedstokens, hvor begge er implementeret som en [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>Tjenesten primært objekt
Når du register opdatere et program i [Azure klassisk portal][AZURE-classic-portal], portalen opretter/opdateringer både et [application-objektet](#application-object) og en tilsvarende service primært objekt for lejeren. Programmet objekt *definerer* programmets identitet konfiguration globalt (på tværs af alle lejere hvor det tilknyttede program har fået tildelt adgang), og er den skabelon, hvorfra dens tilsvarende service vigtigste objekter er *udledt* til brug lokalt på kørselstidspunktet (i en bestemt lejer).

Se [programmet på computeren og tjeneste hovedstolen objekter] [ AAD-App-SP-Objects] kan finde flere oplysninger.

## <a name="sign-in"></a>Log på
Processen med et [klientprogrammet](#client-application) påbegynde slutbrugerens godkendelse og indsamle relaterede fase, med henblik på indhente et [sikkerhedstoken](#security-token) og angivelse af område programmet sessionen til denne tilstand. State kan indeholde elementer som brugerprofiloplysninger og oplysninger, der er afledt token krav.

Funktionen Log på i et program bruges typisk til at implementere single-sign-on (SSO). Det kan også indledes med en "tilmelding" funktion, som indgangspunkt for slutbrugeren at få adgang til et program (efter første logon). Funktionen tilmelding bruges til at samle og fastholdes yderligere tilstand, der er specifikke for brugeren, og kan kræve [bruger samtykke](#consent).

## <a name="sign-out"></a>hvor
Processen med at ophævelse godkender en slutbruger, fjernelse af tilstanden bruger er knyttet til [klientprogrammet](#client-application) sessionen under [logon](#sign-in)

## <a name="tenant"></a>lejer
En forekomst af et Azure AD-directory kaldes en Azure AD-lejer. Den indeholder en række funktioner, herunder:

- en registreringsdatabasen tjenesten til integrerede programmer
- godkendelse af brugerkonti og registrerede programmer
- RESTEN slutpunkter kræves for at understøtte forskellige protokoller, herunder OAuth2 og SAML, herunder [godkendelse slutpunkt](#authorization-endpoint), [token slutpunkt](#token-endpoint) og "almindelige" slutpunktet bruges af [flere lejer programmer](#multi-tenant-application).

En lejer er også knyttet til en Azure AD eller Office 365-abonnement under klargøring af dit abonnement, skal give identitet & Access Management funktioner for abonnementet. Se, [hvordan du får en Azure Active Directory-lejer] [ AAD-How-To-Tenant] få at vide om de forskellige måder, hvorpå du kan få adgang til en lejer. Se, [hvordan Azure abonnementer der er knyttet til Azure Active Directory] [ AAD-How-Subscriptions-Assoc] yderligere oplysninger om relationen mellem abonnementer og en Azure AD-lejer.

## <a name="token-endpoint"></a>token slutpunkt
Et af slutpunkterne implementeret af [godkendelse server](#authorization-server) til at understøtte OAuth2 [tilladelse giver](#authorization-grant). Afhængigt af ydelse, det kan bruges til at få fat på en [adgangstoken](#access-token) (og relaterede "Opdater"-token) til en [klient](#client-application)eller [id-token](#ID-token) , når den bruges med den [OpenID forbinde] [ OpenIDConnect] protocol.

## <a name="user-agent-based-client"></a>Bruger-agent-baseret klient
En type af [klientprogrammet](#client-application) , der henter kode fra en webserver og udfører inden for en brugeragent (for eksempel en webbrowser), som en enkelt side program (SPA). Da al kode, der udføres på en enhed, er det betragtes som en "offentlige" klient på grund af dens manglende evne til at gemme legitimationsoplysninger privat/fortroligt. Se [OAuth2 klienttyper og profiler] [ OAuth2-Client-Types] kan finde flere oplysninger.

## <a name="user-principal"></a>primært brugernavn
Samme måde som en tjeneste primært objekt bruges til at repræsentere en programforekomst, et vigtigste brugerobjekt er en anden type sikkerhedskonto, der repræsenterer en bruger. Azure AD Graph [brugerobjektet] [ AAD-Graph-User-Entity] definerer skemaet til et brugerobjekt, herunder brugerrelaterede egenskaber som fornavn og efternavn, brugerens hovednavn, directory rollemedlemskab osv. Dette giver brugeren identitet konfigurationen til Azure AD til at oprette en bruger sikkerhedskonto på kørselstidspunktet. Bruger hovedstolen bruges til at repræsentere en godkendt bruger til Single Sign-On, optagelse [samtykke](#consent) delegering, hvilket gør access kontrolelement beslutninger osv.

## <a name="web-client"></a>webklienten
En type af [klientprogrammet](#client-application) , som udfører al kode på en webserver, og i stand til at fungere som et "Fortroligt" klient ved at gemme sikkert dens legitimationsoplysninger på serveren. Se [OAuth2 klienttyper og profiler] [ OAuth2-Client-Types] kan finde flere oplysninger.

## <a name="next-steps"></a>Næste trin
[Azure AD Developer Guide] [ AAD-Dev-Guide] er portal skal bruges til alle Azure AD-udvikling relaterede emner, herunder en oversigt over [integration af programmet] [ AAD-How-To-Integrate] og grundlæggende [Azure AD-godkendelse og understøttede godkendelsestyper scenarier][AAD-Auth-Scenarios].

Brug følgende Disqus kommentarer afsnit til at give feedback og Hjælp os med at tilpasse og forme vores indhold.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ./active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken

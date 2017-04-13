<properties
   pageTitle="Forstå den implicit OAuth2 give flow i Azure Active Directory | Microsoft Azure"
   description="Få mere at vide mere om Azure Active Directorys implementering af den implicit OAuth2 give flow, og om det er bedst til dit program."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="vittorib;bryanla"/>

# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Forstå OAuth2 implicit Giv strømmen i Azure Active Directory (AD)

OAuth2 implicit ydelse er berygtet for at give med den længste liste over sikkerhedsproblemer i specifikationen OAuth2. Og endnu, som er den fremgangsmåde, der er implementeret af ADAL JS og den, som vi anbefaler, at når du skriver SPA programmer. Det, der giver? Det er en noget ud af kompromiserne:, og som den omdannes, implicit ydelse er den bedste metode, du kan fortsætte til programmer, der forbruger en Web API via JavaScript fra en browser.

## <a name="what-is-the-oauth2-implicit-grant"></a>Hvad er OAuth2 implicit ydelse?

Den quintessential [OAuth2 godkendelseskode give](https://tools.ietf.org/html/rfc6749#section-1.3.1) er godkendelse ydelse som bruger to separate slutpunkter. Godkendelse slutpunktet bruges til den bruger interaktion fase, hvilket resulterer i en godkendelseskode for. Token slutpunktet bruges derefter af klienten til udveksling af koden for et adgangstoken og ofte et Opdater token. Webprogrammer er påkrævet for at præsentere deres egne programmet legitimationsoplysninger til token slutpunktet, så tilladelsen serveren kan godkende klienten.

[OAuth2 implicit give](https://tools.ietf.org/html/rfc6749#section-1.3.2) er en variant, så en klient til at hente en adgangstoken (og id_token i forbindelse med [OpenId forbinde](http://openid.net/specs/openid-connect-core-1_0.html)) direkte fra godkendelse slutpunktet, uden at kontakte token slutpunktet eller godkendelse af klientprogrammet. Denne variant blev udviklet specielt for JavaScript-baserede programmer, der kører i en webbrowser: i den oprindelige OAuth2 specifikation, der skal returneres tokens i et URI-fragment. Der gør de token bit tilgængeligt tilgængeligt for JavaScript-kode i klienten, men det garanterer de ikke medtages i omdirigeringer mod serveren. Returnerer tokens via browseren omdirigerer direkte fra godkendelse slutpunkt. Der er også fordelen ved at fjerne alle krav til på tværs af origin opkald, som er nødvendige, hvis JavaScript-programmet, der kræves til at kontakte token slutpunktet.

En vigtige egenskab ved OAuth2 implicit ydelse er fakultet, Sådan flyder aldrig returnerede Opdater tokens til klienten. Som vi vil se i næste afsnit, der ikke rigtig nødvendig og faktisk der ville være et sikkerhedsproblem.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Passende scenarier, hvor OAuth2 implicit ydelse

Som specifikationen OAuth2 selve erklærer, har den implicit Giv er udviklet for at aktivere brugeragent programmer – det vil sige, JavaScript programmer afspillet i en browser. Angive egenskab af disse programmer er, JavaScript-kode, der bruges til at få adgang til serverressourcer (normalt en Web API) og for at opdatere programmet UX i overensstemmelse hermed. Tænk på programmer som Gmail eller Outlook Web Access: Når du vælger en meddelelse fra din Indbakke, ændres kun meddelelse visualisering panelet for at få vist den nye markering, mens resten af siden forbliver uændret. Dette er udtryk traditionelle Omdiriger-baserede webapps, hvor alle brugerinput resulterer i en hel side tilbagesendelse og en hel sidegengivelse af den nye svar fra serveren.

Programmer, der er JavaScript baseret tilgangen til dens særligt kaldes enkelt side programmer eller kursteder: formålet er, at disse programmer fungerer kun en indledende HTML-side og tilknyttede JavaScript, med alle efterfølgende interaktioner, der drives af Web API-kald udføres via JavaScript. Dog hybrid fremgangsmåder, hvor programmet er hovedsageligt tilbagesendelse indsatsbaseret men udfører lejlighedsvise JS opkald, er ikke usædvanlige – diskussion om implicit flow brugen er relevant for dem, samt.

Omdiriger-baserede programmer sikker typisk deres anmodninger via cookies, men, metode ikke fungerer samt for JavaScript-programmer. Cookies virker kun forhold til domænet, de er genereret for, mens JavaScript opkald kan omdirigeres mod andre domæner. Faktisk, der ofte vil være tilfældet: Tænk over aktivering af Microsoft Graph API, Office API Azure API – alle bosiddende uden for domænet fra hvor programmet er served-programmer. En stigende tendens til JavaScript-programmer er at have nogen back-end overhovedet, stole 100% på 3 part Web API'er til at implementere deres business-funktion.

I øjeblikket, er den foretrukne metode til at beskytte opkald til en Web API til at bruge den OAuth2 bæreren token metode, hvor hvert opkald leveres med et OAuth2 adgangstoken. Web API undersøger indgående adgangstoken, og hvis der findes i den nødvendige områder, den giver adgang til den ønskede handling. Implicit strømmen indeholder en praktisk metode til JavaScript-programmer til at hente access tokens for en Web API giver adskillige fordele i forbindelse med cookies:

- Tokens kan fås pålideligt uden brug af på tværs af origin opkald – obligatorisk registrering af Omdiriger URI som tokens er returtypen garanterer, at der ikke er erstattet tokens
- JavaScript-programmer kan få så mange access tokens, som de skal bruge til så mange Web API'er de målrette – med uden begrænsning på domæner
- HTML5 funktioner som session eller lokale lager give fuld kontrol over token cachelagre og levetid management, mens cookies management er uigennemsigtig til appen
- Access tokens ikke udsatte for på tværs af websteder anmodning forfalskning (CSRF) angreb

Implicit Giv flowet udstede ikke Opdater tokens, men ellers primært af sikkerhedsmæssige årsager. En opdatering token fastsat ikke som under som access tokens, give langt flere power derfor inflicting langt flere skader, i tilfælde af, at den er lækket. Tokens leveres i URL-adressen i implicit strømmen, det vil sige risikoen for aflytning er højere end i godkendelse kode Giv.

Bemærk, at et JavaScript-program har en anden metode til rådighed for forny access tokens uden at spørge brugeren om legitimationsoplysninger flere gange. Programmet kan bruge en skjult iframe til at udføre nye token anmodninger mod Azure AD godkendelse slutpunkt: som browseren har stadig en aktiv session (Læs: har en sessionscookie) forhold til Azure AD-domænet anmodningen om godkendelse korrekt kan opstå uden brug af brugerinput. 

Denne model giver programmet JavaScript mulighed for at forny uafhængigt access tokens og endda få fat nye til en ny API (forudsat at brugeren tidligere har accepteret for dem. Derved undgår den tilføjede arbejdsbyrde indhente, vedligeholde og beskytte en høj værdi genstand som et Opdater token. Den genstand, hvilket gør det muligt, uovervåget fornyelsen Azure AD sessionscookie, administreres uden for programmet. En anden fordelen ved denne fremgangsmåde er en bruger kan logge fra Azure AD, ved hjælp af de programmer, der er logget på Azure AD, kører i en af fanerne browser. Dette resulterer i sletningen af Azure AD sessionscookie, og programmet JavaScript mister automatisk muligheden for at forny tokens for den bruger, der er signeret ud.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Er implicit Giv passer til min app?

Implicit Giv præsenterer flere risici end andre giver. De områder, skal du være opmærksom på, er også dokumenteret (se f.eks [Misbrug af Access Token til at repræsentere ressource ejer i Implicit dataflow] [ OAuth2-Spec-Implicit-Misuse] og [OAuth 2.0 truslen Model og overvejelser om sikkerheden][OAuth2-Threat-Model-And-Security-Implications]). Profilen højere risikoen er dog stort set skyldes, at det er beregnet til at aktivere programmer, udfører aktive kode, served af en ekstern ressource til en browser. Hvis du planlægger en SPA arkitektur har ingen back end-komponenter eller at kalde en Web API via JavaScript, det anbefales at bruge med implicit forløb for sikkerhedstoken acquisition.

Hvis dit program er en oprindelig klient, ikke implicit strømmen er et godt Tilpas. Fravær af Azure AD sessionscookie i konteksten af en oprindelig klient, mangler de dit program fra middelværdierne af vedligeholdelse af en lang lived session. Hvilket betyder, at dit program spørger flere gange brugeren hentning af access tokens for nye ressourcer.

Hvis du udvikler et webprogram, der indeholder en back-end og forbrug en API fra dens back end-kode, er implicit strømmen heller ikke passer. Andre giver giver dig langt mere strøm. For eksempel giver OAuth2 klient legitimationsoplysninger Giv mulighed for at få tokens, så de afspejler de tilladelser, der er tildelt til selve, programmet i modsætning til brugerdelegeringer. Det betyder klienten har mulighed for at bevare programmeringsmæssig adgang til ressourcer, der er lige, når en bruger ikke aktivt driver i en session, og så videre. Ikke alene, men disse give højere sikkerhed garantier. For eksempel access tokens forsendelse aldrig via webbrowseren bruger, de ikke offentliggjort gemmes i browseren oversigten, og så videre. Klientprogrammet kan også udføre effektiv godkendelse, når der anmodes om en token.

## <a name="next-steps"></a>Næste trin

- En komplet liste over Udviklerressourcer, herunder oplysninger om de protokoller og OAuth2 godkendelse give flyder understøttelse af Azure AD, referere til [Azure AD Developers vejledning][AAD-Developers-Guide]
- Se, [hvordan du integrerer et program med Azure AD]  [ ACOM-How-To-Integrate] for yderligere dybde på integration ansøgningsprocedure.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16 
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819


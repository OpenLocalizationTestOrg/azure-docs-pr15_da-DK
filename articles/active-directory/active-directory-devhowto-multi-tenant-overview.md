<properties
   pageTitle="Sådan oprettes et program, der kan logge på en hvilken som helst Azure Active Directory-brugeren | Microsoft Azure"
   description="Step by step kan instruktioner til oprettelse af et program, der logge på en bruger fra en hvilken som helst Azure Active Directory-lejeren, også kaldet et med flere arkitekturer til computeren."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="skwan;bryanla"/>

# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Hvordan du logger på en hvilken som helst Azure Active Directory (AD) bruger, der bruger flere lejer programmet mønster
Hvis du tilbyder en Software som et tjenesteprogram mange organisationer, kan du konfigurere dit program tilladelse til at acceptere logon fra en hvilken som helst Azure AD-lejer.  Dette kaldes at gøre dit program med flere arkitekturer i Azure AD.  Brugere i en hvilken som helst Azure AD-lejer vil kunne logge på dit program efter accepterer til at bruge deres konto med dit program.  

Hvis du har et eksisterende program, der har sit eget konto system eller understøtter andre slags Log på fra andre skyen udbydere, tilføje Azure AD-logon fra en hvilken som helst lejeradministration er så enkelt som registrering af din app, tilføje log i kode via OAuth2, OpenID forbindelse eller SAML og lægge en log på med Microsoft-knappen på dit program. Klik på knappen for at få flere oplysninger om branding dit program.

[! [Logge på knappen] [AAD-logon]][AAD-App-Branding]


I denne artikel antages det, du allerede kender oprettelsen af et enkelt lejer program til Azure AD.  Hvis du ikke, gå tilbage til [udvikler vejledning hjemmesiden] [ AAD-Dev-Guide] og prøv en af vores Hurtig start!

Der er fire enkle trin til at konvertere dit program til en Azure AD med flere lejer app:

1.  Opdatere din programmet registrering for at være med flere lejer
2.  Opdatere din kode for at sende anmodninger til /common slutpunkt 
3.  Opdatere din kode for at håndtere flere udsteder værdier
4.  Forstå bruger og administrator samtykke og foretage ændringer af relevante kode

Lad os se på hvert trin i detaljer. Du kan også gå direkte til [denne liste over flere lejer eksempler][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Opdater registrering skal være flere lejer
Som standard er web app/API registreringer i Azure AD enkelt lejer.  Du kan gøre din tilmelding med flere lejer ved at søge efter "Programmet er flere lejer" Skift på konfigurationssiden af dit program registrering i [Azure klassisk portal] [ AZURE-classic-portal] og angive den til "Ja".

Bemærk: Før et program kan gøres med flere arkitekturer, kræver Azure AD App ID URI af programmet skal være globalt entydig. App-ID URI er et af de måder, hvorpå et program er beskrevet i protocol meddelelser.  Det er tilstrækkelige til App-ID URI skal være entydige i pågældende lejer til en enkelt lejer-app.  Om et program med flere lejer, skal den være globalt entydige så Azure AD finder programmet på tværs af alle lejere.  Global entydig gennemtvinges ved at kræve App ID URI skal have et værtsnavn, der svarer til en bekræftet domænet for Azure AD-lejer.  Eksempelvis hvis navnet på din lejer er contoso.onmicrosoft.com derefter en gyldig App ID URI ville være `https://contoso.onmicrosoft.com/myapp`.  Hvis din lejer havde en bekræftet domænet for `contoso.com`, og derefter en gyldig App-ID URI vil også være `https://contoso.com/myapp`.  Angive et program som flere lejer mislykkes, hvis den App-ID URI ikke følge dette mønster.

Oprindelig klientregistreringer er flere lejer som standard.  Du behøver ikke at foretage dig noget for at sikre en indbygget klient programmet registrering med flere lejer.

## <a name="update-your-code-to-send-requests-to-common"></a>Opdatere din kode for at sende anmodninger til /common
Log på anmodninger sendes i et enkelt lejer til computeren, til lejerens logon slutpunkt.   For eksempel for contoso.onmicrosoft.com ville slutpunktet være:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Anmodninger sendes til en lejer slutpunkt kan logge på brugere (eller gæster) i lejeren til programmer i lejeren.  Med et program med flere arkitekturer ved programmet ikke, på forhånd hvilke lejer, brugeren er fra, så du ikke kan sende anmodninger om til en lejer slutpunkt.  I stedet, der sendes en anmodning til et slutpunkt, der multiplexes på tværs af alle Azure AD-lejere:

    https://login.microsoftonline.com/common

Når Azure AD modtager en anmodning på /common slutpunkt, den logger brugeren, og derfor opdager hvilke lejer, brugeren er fra.  Den/almindelige slutpunkt fungerer med alle de godkendelsesprotokoller, der understøttes af Azure AD: OpenID forbinde, OAuth 2.0, SAML 2.0 og WS-sammenslutning.

Log på svar til programmet derefter indeholder et token, som repræsenterer brugeren.  Værdien udsteder i tokenet fortæller et program, hvilke lejer, brugeren er fra.  Når et svar returnerer fra /common slutpunkt, udsteder værdien i tokenet svarer til brugerens lejer.  Det er vigtigt at være opmærksom på /common slutpunkt er ikke en lejer og er ikke en udsteder, er det kun en multiplekser.  Når du bruger /common, skal opdateres for at tage hensyn til logik i dit program til at validere tokens. 

Flere lejer programmer bør også give en ensartet logonoplevelse for brugere, følge programmet Azure AD branding retningslinjer, som tidligere nævnt. Klik på knappen for at få flere oplysninger om branding dit program.

[! [Logge på knappen] [AAD-logon]][AAD-App-Branding]

Lad os se nærmere på brug af /common slutpunkt og implementeringen kode mere detaljeret.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Opdatere din kode for at håndtere flere udsteder værdier
Webprogrammer og web API'er modtager og validere tokens ud fra Azure AD.  

> [AZURE.NOTE] Mens oprindelige klientprogrammer anmode om og modtage tokens fra Azure AD, gør de det for at sende dem til API'er, hvor de er godkendt.  Oprindelige programmer validere ikke tokens og skal behandle dem som uigennemsigtig.

Lad os se på, hvordan et program valideret tokens den modtager fra Azure AD.  Et enkelt lejer program tager normalt et slutpunkt værdi som:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

og bruge det til at oprette en metadata URL-adresse (i dette tilfælde OpenID forbinde) som:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

hente to kritiske filer af oplysninger, der bruges til at validere tokens: lejeren signering nøgler og udsteder værdi.  Hver Azure AD-lejer har en entydig udsteder værdi i formularen:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

hvor er værdien GUID Omdøb sikker version af lejeren lejer ID.  Hvis du klikker på linket metadata over for `contoso.onmicrosoft.com`, kan du se denne udsteder værdi i dokumentet.

Når et enkelt lejer program har valideret et token, kontrollerer signaturen for tokenet mod tasterne signerende fra Metadatadokumentet, og sikrer, at værdien udsteder i tokenet svarer til det, der blev fundet i dokumentets metadata.

Siden /common slutpunkt ikke svarer til en lejer og er ikke en udsteder, når du undersøge værdien udsteder i metadataene for/almindelige den har en skabelonbaseret URL-adresse i stedet for en faktisk værdi:

    https://sts.windows.net/{tenantid}/

Derfor et program med mange brugere kan ikke validere tokens lige ved at sammenligne værdien udsteder i metadata med den `issuer` værdi i tokenet.  Et program med flere arkitekturer skal logik beslutte, hvilke udsteder værdier er gyldige, og hvilke er ikke, baseret på lejeren ID del af værdien udsteder.  

Eksempelvis hvis et program med mange brugere kan kun logon fra bestemte lejere, der har tilmeldt sig til deres tjeneste, skal den skal du kontrollere værdien udsteder eller `tid` gøre krav på værdien i tokenet at sikre, at lejeren er i sin liste over abonnenter.  Hvis et program med flere lejer kun omhandler enkeltpersoner og ikke gøre en hvilken som helst access beslutninger baseret på lejere, kan det kan du ignorere værdien udsteder helt.

I de med flere lejer prøver, finder du i afsnittet [Relateret indhold](#related-content) i slutningen af denne artikel, deaktiveres udsteder validering for at aktivere en hvilken som helst Azure AD-lejer til at logge på.

Nu Lad os se på brugeroplevelsen for brugere, der logget på med flere lejer programmer.

## <a name="understanding-user-and-admin-consent"></a>Forstå bruger og administrator samtykke
For en bruger til at logge på et program i Azure AD, skal programmet gengives i brugerens lejer.  Dette giver mulighed for organisationen for at udføre ting ud til at anvende entydige politikker, når brugere fra deres lejer logger på programmet.  For en enkelt lejer program er denne registrering enkelt; det er den, sker der, når du har registreret programmet i [Azure klassisk portal][AZURE-classic-portal].

Programmer med flere lejer findes den første registrering for programmet i den Azure AD-lejer, der bruges af udvikleren.  Når en bruger fra en anden lejer logger ind på programmet for første gang, beder Azure AD dem tilladelse til de tilladelser, der er blevet anmodet af programmet.  Hvis de samtykke, derefter en repræsentation af det program, kaldet en *tjeneste vigtigste* oprettes i brugerens lejer, og kan fortsætte med at logge på. En delegering oprettes der også i den mappe, der registrerer brugerens samtykke til programmet. Se [programmet på computeren og Service hovedstolen objekter] [ AAD-App-SP-Objects] yderligere oplysninger om programmets programmet og ServicePrincipal objekter, og hvordan de relaterer til hinanden.

![Samtykke til enkelt lag app][Consent-Single-Tier] 

Denne samtykke oplevelse påvirkes af de tilladelser, der er blevet anmodet af programmet.  Azure AD understøtter to typer af tilladelser, kun app og delegeret:

- En delegeret tilladelse giver mulighed for at fungere som en logget bruger til et undersæt af vigtigste brugeren kan gøre programmet.  For eksempel kan du tildele et program tilladelse til at læse signerede i brugerens kalender.
- En app kun tilladelse er tildelt direkte til identiteten af programmet.  For eksempel kan du tildele et program kun app tilladelse til at læse på listen over brugere i en lejer, og det kan gøres uanset der er logget på programmet.

Nogle tilladelser kan være accepteret at af en almindelig bruger, mens andre kræver en lejeradministrator samtykke. 

### <a name="admin-consent"></a>Administrator samtykke
Kun App tilladelser altid at kræve en lejeradministrator samtykke.  Hvis dit program anmoder om en app kun tilladelse og en normal bruger forsøger at logge på programmet, får en fejlmeddelelse om brugeren kan ikke samtykke dit program.

Visse delegeret tilladelser kræver også en lejeradministrator samtykke.  For eksempel kræver muligheden for at skrive tilbage til Azure AD som den bruger, der er logget på en lejeradministrator samtykke.  Hvis en almindelig bruger forsøger at logge på et program, der anmoder om en delegeret tilladelse, der kræver administratorsamtykke, som kun app-tilladelser modtager programmet en fejl.  Hvorvidt en tilladelse kræver administrator samtykke bestemmes af den udvikler, udgives ressourcen, og du kan finde i dokumentationen til ressourcen.  Links til emner, der beskriver de tilgængelige tilladelser til Azure AD Graph API og Microsoft Graph API er i afsnittet [Relateret indhold](#related-content) i denne artikel.

Hvis dit program bruger tilladelser, der kræver administrator samtykke, skal du have en bevægelse i dit program som en knap eller et link, hvor administratoren kan starte handlingen.  Anmodningen dit program sender for denne handling er et normalt OAuth2/OpenID forbinde anmodningen om godkendelse, men der også indeholder den `prompt=admin_consent` forespørgsel strengparameter.  Når administratoren har accepteret og service hovedstolen er oprettet i kundens lejer, efterfølgende logon anmodninger ikke skal bruge den `prompt=admin_consent` parameter.   Da administratoren har besluttet dig for de nødvendige tilladelser kan accepteres, bliver ingen andre brugere i lejeren spurgt, om samtykke fra punktet.

Den `prompt=admin_consent` parameter kan også bruges af programmer, der anmoder om tilladelser, der kræver ikke administrator samtykke, men vil give en oplevelse, hvor administratoren lejer "tilmelder sig" for programmet én gang, og ingen andre brugere bliver bedt om samtykke fra det pågældende tidspunkt på.

Hvis et program kræver administratorsamtykke, og administratoren logger ind på programmet, men den `prompt=admin_consent` parameter sendes ikke kan administratoren vil kunne korrekt samtykke til programmet, men de kan kun samtykke for deres brugerkonto.  Almindelige brugere vil stadig ikke kunne logge på og samtykke til programmet.  Dette er nyttigt, hvis du vil give lejeradministratoren muligheden for at udforske dit program før tillade andre brugere adgang.

Lejeradministrator kan deaktivere muligheden for almindelige brugere tilladelse til programmer.  Hvis denne funktion er deaktiveret, er administrator samtykke er altid påkrævet til programmet konfigureres i lejeren.  Hvis du vil teste dit program med almindelig bruger samtykke deaktiveret, kan du finde parameteren konfiguration i Azure AD-lejer i konfigurationsafsnittet af [Azure klassisk portal][AZURE-classic-portal].

> [AZURE.NOTE] Nogle programmer vil en oplevelse, hvor almindeligt brugerne kan samtykke først og senere programmet kan omfatte de administrator og anmodning om tilladelser, der kræver administrator samtykke.  Der findes ingen måde at gøre dette med et enkelt program registrering i Azure AD i dag.  Den kommende Azure AD v2 slutpunkt tillader programmer for at anmode om tilladelser på kørselstidspunktet, i stedet for på registrering af tid, hvilket gør det muligt dette scenario.  Yderligere oplysninger finder du i [Azure AD App Model v2 Developer Guide][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Samtykke og med flere lag programmer
Programmet kan være flere lag, hver repræsenteret af sin egen registrering i Azure AD.  For eksempel en oprindelige program, der kalder en web API eller et webprogram, der ringer op til en web API.  I begge af disse tilfælde klienten (Oprindelig app eller WebApp) anmoder om tilladelser til at ringe til ressourcen (World Wide web API).  Alle de ressourcer, der anmodes om tilladelser skal for klienten til at være korrekt accepteret på en kundes lejer, allerede findes i kundens lejer.  Hvis problemet ikke opfyldes, vil Azure AD returnere en fejl, ressourcen skal tilføjes først.

Det kan være et problem, hvis dit logiske program består af to eller flere programmer registreringer, for eksempel en separat klient og ressource.  Hvordan får du ressourcen på kundens lejer første?  Azure AD dækker dette tilfælde ved at aktivere klienten og ressourcer for at være accepteret i et enkelt trin, hvor brugeren ser summen af de tilladelser, der er blevet anmodet af både klient- og ressourcer på siden samtykke.  Hvis du vil aktivere denne funktionsmåde, ressourcens programmet registrering skal indeholde kundens App-ID som en `knownClientApplications` i dets progammanifest.  Eksempel:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Denne egenskab kan blive opdateret via ressource [programmets manifest][AAD-App-Manifest], og der demonstreres i en oprindelig med flere lag-klient, der ringer web API eksempel i sektionen [Relaterede indhold](#related-content) i slutningen af denne artikel. Diagrammet nedenfor giver et overblik over samtykke til en med flere lag app:

![Samtykke til kendte klient med flere lag app][Consent-Multi-Tier-Known-Client] 

En lignende sag sker der, hvis de forskellige niveauer af et program er registreret i forskellige lejere.  For eksempel overveje store og små bogstaver i at bygge et native client-program, der ringer op til Office 365 Exchange Online-API'EN.  Hvis du vil udvikle oprindelig program eller nyere til det oprindelige program til at køre i en kundes lejer, skal Exchange Online service hovedstolen findes.  I dette tilfælde er kunden til at købe Exchange Online i tjenesten vigtigste skal have oprettet i deres lejer.  Hvis det er en API, der er oprettet af en organisation end Microsoft, skal udvikleren af API ikke en metode til kunderne til samtykke deres programmet på computeren på en kunde lejer, for eksempel en webside, der styrer samtykke ved hjælp af funktioner, der er beskrevet i denne artikel.  Når tjenesten hovedstolen er oprettet i lejeren, kan det oprindelige program hente tokens til API.

Diagrammet nedenfor giver et overblik over samtykke til en med flere lag-app, der er registreret i forskellige lejere:

![Samtykke til med flere lag med flere deltagere app][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Tilbagekalde samtykke
Brugere og administratorer kan ophæve samtykke til dit program når som helst:

- Brugere tilbagekalde adgang til individuelle programmer ved at fjerne dem fra deres [Få adgang til Panel programmer] [ AAD-Access-Panel] liste.
- Administratorer tilbagekalde adgang til programmer ved at fjerne dem fra Azure AD ved hjælp af afsnittet Azure AD administration af [Azure klassisk portal][AZURE-classic-portal].

Hvis en administrator giver sit samtykke til programmet for alle brugere i en lejer, kan ikke brugere fjerne adgangen enkeltvis.  Kun administratoren kan tilbagekalde adgang, og kun for hele programmet.

### <a name="consent-and-protocol-support"></a>Samtykke og Protocol Support
Samtykke understøttes i Azure AD via OAuth, OpenID oprette forbindelse, WS-Federation og SAML protokoller.  SAML og WS-Federation protokoller ikke understøtter den `prompt=admin_consent` parameter, så administrator samtykke er kun muligt via OAuth og OpenID forbindelse.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Flere lejer programmer og cachelagring Access Tokens
Flere lejer programmer kan også få adgangstokens til at ringe til API'er, der er beskyttet af Azure AD.  En almindelig fejl, når ved hjælp af Active Directory Authentication Library (ADAL) med et program med flere lejer er først anmode om et token for en bruger, der bruger /common, har modtaget et svar, og derefter anmode om et efterfølgende token for den pågældende samme bruger også bruger /common.  Da svaret fra Azure AD kommer fra en lejer, ikke/almindelige, ADAL lagrer tokenet som værende fra lejeren. Efterfølgende opkaldet til /common for at hente et adgangstoken til brugeren går glip af den cachelagrede post, og brugeren bliver bedt om at logge på igen.  Kontrollér, at efterfølgende opkald for en bruger, der allerede er logget på er foretaget i lejerens slutpunkt for at undgå manglende cachen.

## <a name="related-content"></a>Relateret indhold

- [Eksempler på programmet på computeren med flere lejer][AAD-Samples-MT]
- [Branding retningslinjer for programmer][AAD-App-Branding]
- [Azure AD Developer's Guide][AAD-Dev-Guide]
- [Programmet og Service hovedstolen objekter][AAD-App-SP-Objects]
- [Integration af programmer med Azure Active Directory][AAD-Integrating-Apps]
- [Oversigt over samtykke Framework][AAD-Consent-Overview]
- [Microsoft Graph API tilladelse områder][MSFT-Graph-AAD]
- [Azure AD Graph API tilladelse områder][AAD-Graph-Perm-Scopes]

Brug Disqus sektionen kommentarer nedenfor til at give feedback og Hjælp os med at tilpasse og forme vores indhold.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken















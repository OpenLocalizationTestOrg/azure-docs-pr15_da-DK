<properties 
    pageTitle="CENC med flere DRM og adgangskontrol: en Reference Design og implementering på Azure og Azure Media Services | Microsoft Azure" 
    description="Få mere at vide om, hvordan til Microsoft® udglattede Streaming klient konvertere Kit-licenser." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan"  
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="willzhan;kilroyh;yanmf;juliako"/>

#<a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC med flere DRM og adgangskontrol: en Reference Design og implementering på Azure og Azure Media Services

##<a name="key-words"></a>Nøgleord
 
Azure Active Directory, Azure Media Services, Azure Media Player, dynamisk kryptering licens levering PlayReady Widevine, FairPlay, almindelige Encryption(CENC), flere DRM, Axinom, bindestreg, EME, MSE, JSON Web Token (JWT), krav, moderne browsere, nøgle overgang, symmetriske nøgle, asymmetrisk nøgle, og OpenID opretter forbindelse, X509 certifikat. 

##<a name="in-this-article"></a>I denne artikel

De følgende emner behandles i denne artikel:

- [Introduktion](media-services-cenc-with-multidrm-access-control.md#introduction)
    - [Oversigt over i denne artikel](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [En reference design](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [Tilknytte design til teknologi til implementering](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [Implementering](media-services-cenc-with-multidrm-access-control.md#implementation)
    - [Implementering procedurer](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
    - [Nogle faktorer i implementering](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [Yderligere emner for implementering](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
    - [HTTP- eller HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
    - [Azure Active Directory logge vigtige overgang](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
    - [Hvor er Access-Token?](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
    - [Hvad med Live Streaming?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
    - [Hvad med licensservere uden for Azure Media Services?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
    - [Hvad nu, hvis jeg gerne vil bruge en brugerdefineret STS?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [Færdige system og test](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
    - [Brugerens logon](media-services-cenc-with-multidrm-access-control.md#user-login)
    - [Ved hjælp af krypterede medier udvidelser til PlayReady](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
    - [Ved hjælp af EME til Widevine](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
    - [Ikke berettigede brugere](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
    - [Køre brugerdefineret Secure Token Service](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [Oversigt](media-services-cenc-with-multidrm-access-control.md#summary)

##<a name="introduction"></a>Introduktion

Det er godt kendt, at det er en kompleks opgave at designe og opbygge et DRM undersystem for en OTT eller online streaming løsning. Og det er en almindelig praksis for operatorer/online video udbydere at udlicitere denne del til specialiserede DRM tjenesteudbydere. Formålet med dette dokument er at præsentere en reference design og implementering af til slut DRM undersystem i OTT eller online streaming løsning.

Målrettede læsere af dette dokument er ingeniører, der arbejder i DRM undersystem af OTT eller online streaming/MFA-screen løsninger eller en hvilken som helst interesseret i DRM undersystem læsere. Antagelse er, at læserne fortrolig med mindst én af DRM teknologier på markedet, som PlayReady, Widevine, FairPlay eller Adobe adgang.

Ved DRM indeholde vi også CENC (almindelige kryptering) med flere DRM. En overordnede tendens i online streaming og OTT branche er at bruge CENC med MFA-native-DRM på forskellige klientplatforme, som er et skift fra forrige tendensen for med en enkelt DRM og dens klient-SDK for forskellige klientplatforme. Når du bruger CENC med MFA-native-DRM, krypteres både PlayReady og Widevine per specifikationen [Almindelige kryptering (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Fordelene ved CENC med flere DRM er som følger:

1. Reducerer kryptering omkostninger, da en enkelt kryptering behandling bruges målretning af forskellige platforme med dens oprindelige DRMs
1. Reducerer omkostningerne til administration af krypteret aktiver, da der er brug for en enkelt kopi af krypteret Aktiver;
1. Fjerner DRM klient licensering omkostninger, da den oprindelige DRM klient er som regel gratis på dens oprindelige platform.

Microsoft har været en aktive projektleder af bindestreg og CENC sammen med nogle overordnede branche-afspillere. Microsoft Azure Media Services, er blevet at yde support af bindestreg og CENC. Seneste meddelelser, skal du se Mingfeis blogge: [præsentation af Google Widevine tjenester til levering af licens i Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/), og [Azure Media Services tilføjer Google Widevine emballagen til at levere flere DRM stream](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Oversigt over i denne artikel

Formålet med denne artikel indeholder følgende:

1. Indeholder en reference designet af DRM undersystem ved hjælp af CENC med flere-DRM;
1. Indeholder en referenceimplementering på Microsoft Azure/Azure Media Services-platform;
1. I denne artikel beskrives nogle design og implementering emner.

I artiklen omhandler "multi-DRM" følgende:

1. Microsoft PlayReady
1. Google Widevine
1. Apple FairPlay (endnu ikke understøttes af Azure Media Services)

Den følgende tabel opsummerer de oprindelige platform/oprindelig app, og browsere, der understøttes af hver DRM.

**Klient Platform**|**Oprindelig DRM Support**|**Browser/App**|**Streaming formater**
----|------|----|----
**Smart tv, operator STB'er, OTT STB'er**|PlayReady primært, og/eller Widevine og/eller andre|Linux, Opera, WebKit, andre|Forskellige formater
**Windows 10-enheder (PC med Windows, Windows-Tablets, Windows Phone, Xbox)**|PlayReady|MS kant/IE11/EME<br/><br/><br/>UWP|TANKESTREG (For HLS, PlayReady understøttes ikke)<br/><br/>TANKESTREG, bløde Streaming (For HLS, PlayReady understøttes ikke) 
**Android-enheder (telefon, Tablet TV)**|Widevine|Chrome/EME|BINDESTREG
**iOS (iPhone, iPad), OS X-klienter og Apple TV**|FairPlay|Safari 8 +/ EME|HLS
**Plug-in'en: Adobe Primetime**|Primetime Access|Browser-plug-in|HD'ER, HLS

I betragtning den aktuelle status for installation af for hver DRM vil typisk en tjeneste til at implementere 2 eller 3 DRMs at sikre, at du tale om alle typerne slutpunkter i den bedste måde.

Der findes en fordeling af tjenesten logik kompleksitet og -kompleksitet på klientsiden til at oprette forbindelse til et bestemt niveau af brugeroplevelsen på de forskellige klienter.

Hvis dit valg, skal du huske disse oplysninger:

- PlayReady er oprindeligt implementeret i alle Windows-enhed på nogle Android-enheder, og gennem software SDK'er på næsten alle platforme
- Widevine er oprindeligt implementeret i hver Android-enhed, i Chrome og i nogle andre enheder
- FairPlay findes kun på iOS- og Mac OS-klienter eller via iTunes.

Så vil det være en typisk multi-DRM:

- Mulighed 1: PlayReady og Widevine
- Mulighed 2: PlayReady, Widevine og FairPlay


## <a name="a-reference-design"></a>En reference design

I dette afsnit viser vi en reference design, som er uafhængig teknologier, der bruges til at implementere den.

Et DRM undersystem kan indeholde følgende komponenter:

1. Key management
1. DRM emballagen
1. DRM licens levering
1. Ret afkrydsningsfelt
1. Godkendelse/godkendelse
1. Player
1. Origin/CDN

I følgende diagram vises på højt niveau interaktion mellem komponenterne i et DRM undersystem.

![DRM undersystem med CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
Der er tre grundlæggende "lag" i designet:

1. Back-office lag (med sort), ikke vises eksternt, f.eks.
1. "DMZ" lag (blå), der indeholder alle de slutpunkter modstående offentlig.
1. Offentlige internetlaget (lyseblå) med CDN og afspillere med trafik offentlige internettet.
 
Der skal være et værktøj til styring af webindhold for at styre DRM beskyttelse, uanset hvilket er det en fast eller dynamisk kryptering. Input til DRM kryptering skal omfatter:

1. MBR videoindhold;
1. Indhold nøgle.
1. Licens acquisition URL-adresser.

Under afspilningstid er på højt niveau flow:

1. Brugeren er godkendt;
1. Godkendelse token er oprettet for brugeren
1. DRM-beskyttet indhold (manifest) skal overføres til afspiller.
1. Player sender licens acquisition anmodning om licensservere sammen med vigtige-ID og godkendelse token.

Inden du går til det næste emne, et par ord om designet af key management.

**Aktiv – ContentKey**|**Scenarie**
------|---------------------------
1-til-1|Den nemmeste sag. Det giver det bedste kontrolelement. Men dette giver generelt højeste licens levering omkostninger. Anmodning om er påkrævet for hver beskyttet aktiv på mindst én licens.
1-til-mange|Du kan bruge den samme indhold nøgle til flere aktiver. For alle aktiverne i en logisk gruppe som en genre eller et undersæt af genre (eller film gener), kan du bruge en enkelt indhold nøgle.
Mange-til-1|Flere indhold taster er behov for hver aktiv. <br/><br/>Hvis du har brug at anvende dynamisk CENC beskyttelse med flere-DRM for MPEG-STREG og dynamiske AES-128 kryptering for HLS, skal du eksempelvis to separate indhold nøgler, hver med sin egen ContentKeyType. (For den indhold nøgle, der bruges til dynamisk CENC beskyttelse, ContentKeyType.CommonEncryption skal bruges, mens til tasten indhold, der bruges til dynamisk AES-128 kryptering, ContentKeyType.EnvelopeEncryption skal bruges.)<br/><br/>Et andet eksempel i CENC beskyttelse af TANKESTREG indhold, i teorien, en indhold nøgle kan bruges til at beskytte video-stream og et andet indhold for at beskytte lydstreamet. 
Mange-til - mange|Kombination af de ovenfor to scenarier: et sæt af indhold taster bruges for hver af de flere aktiver i det samme aktiv "gruppe".


En anden vigtige faktor skal du tænke på, er brugen af fast og ikke-permanente licenser.

Hvorfor er disse overvejelser i forbindelse med vigtige? 

De har direkte indvirkning licens levering omkostninger, hvis du bruger offentlige skyen til levering af licens. Lad os se på de følgende to forskellige design tilfælde til at illustrere:



1. Månedligt abonnement: Brug af fast licens og 1-til-mange indhold nøgle-aktiv tilknytning. F.eks. for alle børn film kan bruge vi en enkelt indhold nøgle til kryptering. I dette tilfælde: 

    Samlet # licenser, der anmodes om til alle børn film/enhed = 1

1. Månedligt abonnement: Brug af ikke-permanente licens og 1-1-tilknytning mellem indhold nøgle og aktiv. I dette tilfælde:

    Samlet # licenser, der anmodes om til alle børn film/enhed = [# film set] x [# sessioner]

Som du nemt kan se resulterer to forskellige design i meget forskelligt licens anmodning mønstre derfor licens levering omkostninger, hvis licens leveringstjenesten modtages fra en offentlig sky som Azure Media Services.

## <a name="mapping-design-to-technology-for-implementation"></a>Tilknytte design til teknologi til implementering

Dernæst skal knytte vi vores generisk design til teknologier på Microsoft Azure/Azure Media Services platformen, ved at angive, hvilke teknologi, der skal bruges for hver dokumentkomponent.

Følgende tabel viser tilknytningen:

**Dokumentkomponent**|**Teknologi**
------|-------
**Player**|[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/)
**Identitetsudbyder (IDP)**|Azure Active Directory
**Secure Token Service (STS)**|Azure Active Directory
**DRM beskyttelse arbejdsproces**|Azure Media Services dynamisk beskyttelse
**DRM licens levering**|1. azure Media Services licens levering (PlayReady, Widevine, FairPlay), eller <br/>2. Axinom licensserveren <br/>3. brugerdefinerede PlayReady licensserveren
**Origin**|Azure Media Services Streaming slutpunkt
**Key Management**|Ikke er nødvendigt for referenceimplementering
**Styring af webindhold**|Et C# console-program

Det vil sige, bliver både identitet udbyder (IDP) og Secure Token STS (Service) Azure AD. Vi bruger [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/)til videoafspillersiden. Både Azure Media Services og Azure Media Player understøtter TANKESTREG og CENC med flere DRM.

I det følgende diagram viser den overordnede struktur og forløb med det ovenfor teknologi tilknytning.

![CENC på AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

For at konfigurere dynamiske CENC kryptering, anvender værktøjet til styring af webindhold følgende oplysninger:

1. Åbn indhold.
1. Indhold nøgle fra nøgle generering af/management;
1. URL-adresser til licens acquisition;
1. En liste over oplysninger fra Azure AD.

Output fra værktøjet til styring af webindhold kan:

1. ContentKeyAuthorizationPolicy, der indeholder specifikationen på hvordan licens levering kontrollerer et JWT token og DRM licens specifikationer
1. AssetDeliveryPolicy, der indeholder specifikationer på streaming format, DRM beskyttelse og licens acquisition URL-adresser.

Under kørslen, er strømmen som nedenfor:

1. På brugergodkendelse genereres en JWT token;
1. En af de krav, der er indeholdt i JWT Tokenet er "grupper" krav, der indeholder gruppe objekt ID "EntitledUserGroup". Dette krav der skal bruges til at overføre "ret Kontrollér".
1. Player overførsler klient manifest for en CENC beskyttet indhold og "ser" følgende:
    1. primære-ID 
    1. indholdet er beskyttet, CENC
    1. Licens acquisition URL-adresser.

1. Player gør en licens acquisition anmodning, der er baseret på den browser/DRM, understøttes. I licens acquisition anmodningen, de primære ID og JWT tokenet sendes også. Licens leveringstjenesten kontrollere JWT token og krav indeholdt før udstedelse den nødvendige licens.

##<a name="implementation"></a>Implementering

###<a name="implementation-procedures"></a>Implementering procedurer

Implementeringen omfatter følgende trin:

1. Forberede der er test: kode/pakke en testvideo til flere bithastighed fragmenteret MP4 i Azure Media Services. Dette aktiv er ikke DRM beskyttet. DRM beskyttelse foretages ved dynamisk beskyttelse senere.
1. Oprette vigtige-ID og indhold vigtige (du kan også fra vigtige frø). Til vores formål vigtige administrationssystem ikke er nødvendigt da vi arbejder med kun en enkelt række-ID og indhold nøgle til nogle af test Aktiver
1. Brug AMS API til at konfigurere tjenester til levering af multi-DRM licens for aktivet test. Hvis du bruger brugerdefinerede licensservere af din virksomhed eller virksomhedens leverandører i stedet for licens tjenester i Azure Media Services, kan du springe dette trin over og angive licens acquisition URL-adresser i trin til konfiguration af levering af licens. AMS API skal bruges til at angive nogle detaljerede konfigurationer, såsom godkendelse politik begrænsning, licens svar skabeloner til forskellige DRM licens tjenester osv. På nuværende tidspunkt giver portalen Azure endnu ikke de nødvendige brugergrænseflade til denne konfiguration. Du kan finde API niveau oplysninger, og lyt til kode i Julia Kornich dokument: [ved hjælp af PlayReady og/eller Widevine dynamiske almindelige kryptering](media-services-protect-with-drm.md). 
1. Brug AMS API til at konfigurere aktiv levering politik for aktivet test. Du kan finde API niveau oplysninger, og lyt til kode i Julia Kornich dokument: [ved hjælp af PlayReady og/eller Widevine dynamiske almindelige kryptering](media-services-protect-with-drm.md).
1. Oprette og konfigurere en Azure Active Directory-lejer i Azure;
1. Oprette et par brugerkonti og grupper i din Azure Active Directory-lejer: Du skal oprette mindst "EntitledUser" gruppere og tilføje en bruger i denne gruppe. Brugere i denne gruppe overfører ret afkrydsningsfelt i af licenser og brugere ikke i denne gruppe kan ikke overføre godkendelse afkrydsningsfelt og vil ikke kunne hente en licens. Være medlem af gruppen "EntitledUser" er påkrævet "grupper" krav i JWT tokenet udstedt af Azure AD. Dette krav krav skal angives i konfiguration af multi-DRM licens levering tjenester, trin.
1. Oprette en ASP.NET MVC app, vil være vært for din videoafspiller. Denne ASP.NET-app overførslen er beskyttet med brugergodkendelse mod Azure Active Directory-lejer. Stort krav medtages i access tokens der fås efter brugergodkendelse. OpenID forbinde API anbefales til dette trin. Du har brug at installere følgende NuGet-pakker:
    - Installer-pakke Microsoft.Azure.ActiveDirectory.GraphClient
    - Installer-pakke Microsoft.Owin.Security.OpenIdConnect
    - Installer-pakke Microsoft.Owin.Security.Cookies
    - Installer-pakke Microsoft.Owin.Host.SystemWeb
    - Installer-pakke Microsoft.IdentityModel.Clients.ActiveDirectory
1. Oprette en afspiller ved hjælp af [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). [Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) giver dig mulighed at angive, hvilke DRM-teknologi, der skal bruges på forskellige DRM platform.
1. Testmatrix:

**DRM**|**Browser**|**Resultat for berettigede bruger**|**Resultat for ophævelse berettigede bruger**
---|---|-----|---------
**PlayReady**|MS kant eller IE11 i Windows 10|Lykkes|Mislykkes
**Widevine**|Chrome i Windows 10|Lykkes|Mislykkes
**FairPlay** |TBD||

George Trifonov af Azure Media Services-teamet har skrevet en blog, give detaljeret vejledning i at konfigurere Azure Active Directory for en ASP.NET MVC player app: [integrere Azure Media Services OWIN MVC baseret app med Azure Active Directory og begrænse baseret på JWT krav vigtige levering af indhold](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Jens har også skrevet en blog på [JWT token godkendelse i Azure Media Services og dynamiske kryptering](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). Og her er sin [Eksempel på Azure AD-integration med Azure Media Services vigtige levering](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Oplysninger om Azure Active Directory:

- Du kan finde oplysninger om developer i [Azure Active Directory Developer's Guide](../active-directory/active-directory-developers-guide.md).
- Du kan finde Administratoroplysninger i [Administrere dine Azure AD Directory](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Nogle faktorer i implementering

Der er nogle "faktorer" i implementeringen. Følgende liste over "faktorer" kan forhåbentlig hjælpe dig med at foretage fejlfinding i tilfælde af, at der opstår problemer.

1. **Udsteder** URL-adresse skal slutte med **"/"**.  

    **Publikum** skal være player application klient-ID, og du bør også tilføje **"/"** i slutningen af udsteder URL-adressen.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

    I [JWT dekoder](http://jwt.calebb.net/), skal du se afsnittet **aud** og **iss** som under i JWT tokenet:
    
    ![1st gotcha](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. Føj tilladelser til programmet i AAD (på Konfigurer fanen af programmet). Dette er påkrævet for hvert program (lokale og udløst versioner).

    ![2. gotcha](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. Brug den rigtige udsteder i at konfigurere dynamisk CENC beskyttelse:

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    
    Følgende virker ikke:
    
        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    
    GUID er AAD lejer ID. GUID kan findes i slutpunkter viste Azure-portalen.

4. Giv gruppemedlemskab krav rettigheder. Kontrollér, at i AAD programmet manifestfilen, vi har følgende

    "groupMembershipClaims": "Alle", (Standardværdien er null)

5. Angive stort TokenType, når du opretter begrænsning krav.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Siden tilføje understøttelse af JWT (AAD) foruden SWT (ACS), er standard TokenType TokenType.JWT. Hvis du bruger SWT/ACS, skal du angive til TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Yderligere emner for implementering
Vi vil næste DISKONTO uss nogle flere emner i vores design og implementering.

###<a name="http-or-https"></a>HTTP- eller HTTPS?

ASP.NET MVC player application vi indbygget skal understøtte følgende:

1. Brugergodkendelse via Azure AD som skal være under HTTPS.
1. JWT token exchange mellem klient og Azure AD som skal være under HTTPS.
1. DRM licenser af klienten som skal være under HTTPS, hvis licens levering modtages fra Azure Media Services. PlayReady produkt pakke naturligvis ikke kræver HTTPS til levering af licens. Hvis din PlayReady licensserveren er uden for Azure Media Services, kunne enten HTTP eller HTTPS bruges.

Derfor, at ASP.NET player programmet bruger HTTPS som en bedste fremgangsmåde. Dette betyder Azure Media Player vil være på en side under HTTPS. Dog til streaming vi foretrækker HTTP, derfor vi skal overveje blandet indhold problem.

1. Browser tillader ikke blandet indhold. Men plug-ins som Silverlight og OSMF-plug-in til jævne og TANKESTREG tilladelse. Blandet indhold er en sikkerhedsrisiko – dette er på grund af truslen om muligheden for at indsætte skadelig JS, hvilket kan medføre kundedata skal være på ansvar.  Browsere blokere dette som standard, og indtil nu er den eneste måde til at løse det på serveren (origin), så alle domæner (uanset https eller http). Dette er sandsynligvis ikke en god ide enten.
1. Vi bør undgå blandet indhold: enten begge Brug HTTP eller begge Brug HTTPS. Når du afspiller blandet indhold, kræver silverlightSS tech rydde en blandet indhold advarsel. flashSS tech håndterer blandet indhold uden blandet indhold advarsel.
1. Hvis din streaming slutpunkt blev oprettet før August 2014, understøtter den ikke HTTPS. I dette tilfælde skal du oprette og bruge et nyt streaming slutpunkt til HTTPS.

I referenceimplementering ved DRM-beskyttet indhold, bliver programmet og streaming under HTTTPS. For åbne indholdet behøver afspilleren ikke godkendelse eller den licens, så du har mulighed for at bruge enten HTTP eller HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory logge vigtige overgang

Dette er en vigtig ting at tage højde for implementeringen. Hvis du ikke finder det i implementeringen, stoppe det færdige system til sidst arbejde helt inden højst 6 uger.

Azure AD benytter branchestandard til at oprette tillid mellem selve og ved hjælp af Azure AD-programmer. Nærmere betegnet kan anvender Azure AD en signerende nøgle, der består af et offentlige og private nøgler par. Når Azure AD opretter et sikkerhedstoken, der indeholder oplysninger om brugeren, er dette token signeret af Azure AD ved hjælp af dens privat nøgle, før det sendes tilbage til programmet. For at bekræfte, at Tokenet er gyldige og faktisk tilhørende fra Azure AD, skal programmet validere det token signatur ved hjælp af den offentlige nøgle, der vises af Azure AD, der er indeholdt i lejerens sammenslutning metadata dokument. Denne offentlig nøgle – og tasten signerende hvorfra det henter – er det samme, som bruges til alle lejere i Azure AD.

Du kan finde detaljerede oplysninger om Azure AD vigtige overgang i dokumentet: [Vigtige oplysninger om signering nøgle overgang i Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Mellem [offentlige og private nøgler par](https://login.windows.net/common/discovery/keys/) 

- Privat nøgle bruges af Azure Active Directory til at oprette et JWT token;
- Offentlig nøgle bruges af et program som DRM licens levering af tjenester i AMS til at kontrollere JWT tokenet;
 
Azure Active Directory roterer til sikkerhed formål dette certifikat med jævne mellemrum (hver 6 uger). I tilfælde af sikkerhedsbrist, vigtige overgang kan blive vist som helst. Derfor skal licens levering af tjenester i AMS opdatere den offentlige nøgle, der bruges som Azure AD roterer parret nøgle, ellers token godkendelse i AMS mislykkes og ingen licens der udstedes. 

Dette opnås ved at angive TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument, når du konfigurerer tjenester til levering af DRM licens.

JWT token strømmen er som nedenfor:

1.  Azure AD udsender JWT tokenet med den aktuelle private nøgle for en godkendt bruger
2.  Når en afspiller registrerer en CENC med flere-DRM beskyttet indhold, finder tokenet JWT udstedt af Azure AD først.
3.  Afspilleren sender licens acquisition anmodning med JWT tokenet til tjenester til levering af licens i AMS;
4.  Licens levering af tjenester i AMS anvender den aktuelle/gyldig offentlig nøgle fra Azure AD for at bekræfte JWT tokenet, før der udstedes licenser.

Tjenester til levering af DRM licens skal altid kontrollerer, om den aktuelle/gyldig offentlig nøgle fra Azure AD. Den offentlige nøgle præsenteret af Azure AD bliver tasten bruges til bekræftelse af et JWT token udstedt af Azure AD.

Hvad nu, hvis de vigtigste overgang sker der, efter AAD genererer en JWT token, men før JWT token sendes ved afspillere til DRM licens levering af tjenester i AMS til godkendelse? 

Fordi en nøgle kan rulles på et tidspunkt, er der altid mere end én gyldig offentlig nøgle tilgængelig i dokumentets sammenslutning metadata. Azure Media Services licens levering kan bruge en af de taster, der er angivet i dokumentet, da én tast kan rulles snart, en anden kan være dens erstatning, og osv..

### <a name="where-is-the-access-token"></a>Hvor er Access-Token?

Hvis du ser på hvordan en WebApp kalder en API app under [Program-id med OAuth 2.0 klient legitimationsoplysninger Giv](active-directory-authentication-scenarios.md#web-application-to-web-api), godkendelse strømmen er som nedenfor:

1.  En bruger er logget på Azure AD i webprogrammet (se [Webprogram webbrowser](active-directory-authentication-scenarios.md#web-browser-to-web-application).
2.  Azure AD godkendelse slutpunktet omdirigerer brugeragenten tilbage til klientprogrammet med en tilladelse kode. Brugeragenten returnerer godkendelseskode til den klientprogrammet redirect URI.
3.  Webprogrammet skal anskaffe et adgangstoken, så den kan godkende på World Wide web API og hente den ønskede ressource. Det giver en anmodning til Azure AD token slutpunkt, give legitimationsoplysninger, klient-ID og -API'EN webprogrammet URI-ID. Der vises godkendelse koden for at bevise, at brugeren har accepteret.
4.  Azure AD godkender programmet og returnerer en JWT adgangstoken, der bruges til at ringe til World Wide web API.
5.  Over HTTPS bruges webprogrammet returnerede JWT adgangstoken til at tilføje JWT strengen med en "Bæreren" angivelse i overskriften godkendelse af anmodning på World Wide web API. World Wide web API derefter valideret JWT tokenet, og hvis validering lykkes, returnerer den ønskede ressource.

I dette "program-id" flow web API har tillid til, at webprogrammet godkendes brugeren. Derfor hedder dette mønster et undersystem, der er tillid til. [Diagram på denne side](http://msdn.microsoft.com/library/azure/dn645542.aspx/) beskrives, hvordan godkendelseskode give flow fungerer.

I med token begrænsning af licenser følger vi det samme der er tillid til undersystem mønster. Og leveringstjenesten licens i Azure Media Services er World Wide web API ressource, "back end-ressourcen" et webprogram, der skal have adgang til. Hvor er så adgangstoken?

Vi faktisk henter adgangstoken fra Azure AD. Efter vellykket brugergodkendelse returneres Godkendelseskode. Godkendelseskoden derefter bruges, sammen med klient-ID og app nøgle til exchange til adgangstoken. Og adgangstoken har brug for at få adgang til en "markøren" programmet pege eller repræsenterer Azure Media Services licens leveringstjenesten.

Vi har brug at registrere og konfigurere appen "markøren" på Azure AD ved at benytte følgende fremgangsmåde:

1.  I Azure AD-lejer

    - tilføje et program (ressource) med enkeltlogon URL-adresse: 

    https://[resource_name].azurewebsites.NET/ og 

    - App-ID URL-adresse: 
    
    https://[aad_tenant_name].onmicrosoft.com/[resource_name]; 
2.  Tilføje en ny nøgle for appen ressource;
3.  Opdatere manifestfilen app, så egenskaben groupMembershipClaims har følgende værdi: "groupMembershipClaims": "Alle"  
4.  Tilføje appen ressource, som blev tilføjet i trin 1 ovenfor i afsnittet "tilladelser til andre programmer," i den Azure AD-app, pege på player WebApp. Se "Access [resource_name]" markering under "delegerede tilladelser". Det giver web app-tilladelse til at oprette adgangstokens for at få adgang til appen ressource. Du skal gøre dette for både lokale og installerede version af WebApp, hvis du udvikler med Visual Studio og Azure WebApp.
    
JWT tokenet udstedt af Azure AD er derfor faktisk adgangstoken for at få adgang til denne ressource "markøren".

### <a name="what-about-live-streaming"></a>Hvad med Live Streaming?

I ovenstående har vores diskussion fokuserer på Aktiver efter behov. Hvad med live streaming?

Det positive er, at du kan bruge præcis det samme design og implementering til beskyttelse af direkte streaming i Azure Media Services, ved at behandle de aktiver, der er knyttet til et program som en "VOD aktiv".

Det er især velkendte, hvis du vil gøre direkte streaming i Azure Media Services, skal du oprette en kanal, og klik derefter et program under kanalen. Hvis du vil oprette programmet, skal du oprette et aktiv, som skal indeholde det direkte arkiv til programmet. For at give CENC flere DRM beskyttelse af live indhold, du skal gøre, er at anvende den samme konfiguration/behandling til aktivet som om den var en "VOD aktiv", før du starter programmet.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>Hvad med licensservere uden for Azure Media Services?

Kunder kan ofte har investeret i licens serverfarm enten i deres egne data centrere eller hostet ved DRM tjenesteudbydere. Heldigvis Azure Media Services indholdsbeskyttelse gør det muligt at betjene i hybridtilstand: indholdet hostet og dynamisk beskyttet i Azure Media Services, mens DRM licenser leveres af servere uden for Azure Media Services. Der er i dette tilfælde overvejelser i forbindelse med følgende ændringer:

1. Secure Token Service skal udstede tokens, som er acceptable og kan være godkendt af serverfarmen licens. Eksempelvis Widevine licensservere, der leveres af Axinom kræver et bestemt JWT-token, der indeholder "ret meddelelsen". Derfor skal du have en STS at udstede sådanne JWT token. Forfatterne har fuldført disse implementering og du kan finde oplysninger i følgende dokument i [Azure dokumentation Center](https://azure.microsoft.com/documentation/): [Ved hjælp af Axinom til at levere Widevine licenser til Azure Media Services](media-services-axinom-integration.md). 
1. Du ikke længere skal bruge til at konfigurere tjenesten til levering af licens (ContentKeyAuthorizationPolicy) i Azure Media Services. Hvad du skal gøre er at tilvejebringe licensen acquisition URL-adresser (for PlayReady, Widevine og FairPlay) når du konfigurerer AssetDeliveryPolicy i at konfigurere CENC med flere DRM.
 
### <a name="what-if-i-want-to-use-a-custom-sts"></a>Hvad nu, hvis jeg gerne vil bruge en brugerdefineret STS?

Der kan være en kunde kan vælge at bruge en brugerdefineret STS (Secure Token Service) til at give JWT tokens på et par mulige årsager. Nogle af dem er:

1.  Identitet udbyder (IDP) bruges af kunden understøtter ikke STS. I dette tilfælde kan en brugerdefineret STS være en indstilling.
2.  Kunden skal muligvis mere fleksibel og tættere kontrolelement i integration STS med kundens abonnement fakturering system. For eksempel kan en MVPD operator indeholde flere OTT abonnement-pakker som premium, grundlæggende, sport, osv. Operatoren måske passer krav i et token med et abonnement pakke, så kun indholdet i den rigtige pakke er gjort tilgængelige. I dette tilfælde en brugerdefineret STS giver den nødvendige fleksibilitet og kontrol.

To ændringer skal udføres, når du bruger en brugerdefineret STS:

1.  Når du konfigurerer tjenesten til levering af licens for et aktiv, skal du angive nøglen bruges til bekræftelse af de brugerdefinerede STS (flere detaljer nedenfor) i stedet for den aktuelle nøgle fra Azure Active Directory.
2.  Når der oprettes et JTW token, en security key er angivet i stedet for den private nøgle i den aktuelle X509 certifikat i Azure Active Directory.

Der er to typer af sikkerhedsnøgler:

1.  Symmetriske nøgle: den samme nøgle bruges til både generering og bekræfte et JWT token;
2.  Asymmetrisk nøgle: et offentlige og private nøgler par i et certifikat bruges sammen med privat nøgle til at kryptere/generere et JWT token og den offentlige nøgle for at bekræfte tokenet X509.

####<a name="tech-note"></a>Bemærkning til tekniske

Hvis du bruger .NET Framework / C# som din development platform på X509 certifikat, der bruges til asymmetrisk sikkerhedsnøgle have vigtige længde mindst 2048. Dette er et krav i klassen System.IdentityModel.Tokens.X509AsymmetricSecurityKey i .NET Framework. Ellers kan være udløst følgende undtagelse:

IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' til signering må ikke være mindre end '2048' bit. 

## <a name="the-completed-system-and-test"></a>Færdige system og test

Vi vil gennemgå nogle scenarier i færdige til slut-systemet, læsere kan have et basic "billede" af funktionsmåden før du går logon-konto.

Webprogrammet player og dens login kan findes [her](https://openidconnectweb.azurewebsites.net/).

Hvis du har brug for er "ikke-integreret" scenarie: video Aktiver hostet i Azure Media Services, som er enten ubeskyttet eller DRM beskyttede, men uden token godkendelse (udstedelse af en licens til den person anmoder om det), kan du teste det uden login (ved at skifte til HTTP, hvis din videostreaming via HTTP).

Hvis du har brug for til slut integreret scenarie: video Aktiver er under dynamiske DRM beskyttelse i Azure Media Services med token godkendelse og JWT token der genereres af Azure AD, skal du logge på.

### <a name="user-login"></a>Brugerens logon

For at teste til slut integreret DRM-systemet, skal du have en "konto" oprettet eller tilføjet. 

Hvilken konto?

Selvom Azure tilladt oprindeligt access af brugere, Microsoft-konto, kan det nu adgang ved brugere fra begge systemer. Dette blev gjort ved at få alle Azure egenskaber tilliden Azure AD til godkendelse, har du Azure AD godkende organisatoriske brugere, og ved at oprette en sammenslutning relation hvor Azure AD har tillid til Microsoft-konto consumer identitetssystem til at godkende consumer brugere. Azure AD er derfor kunne godkendes "gæst" Microsoft-konti samt "Oprindelig" Azure AD-konti.

Da Azure AD har tillid til Microsoft-konto (MSA) domæne, kan du tilføje alle konti fra nogen af de følgende domæner til den brugerdefinerede Azure AD lejer og bruge kontoen til logon:

**Domænenavn**|**Domæne**
-----------|----------
**Brugerdefineret Azure AD-lejer domæne**|somename.onmicrosoft.com
**Virksomhedens domæne**|Microsoft.com
**Microsoft-konto (MSA) domæne**|Outlook.com, live.com, hotmail.com

Du kan kontakte nogen af forfatterne, der har en konto, der er oprettet eller tilføjet for dig. 

Nedenfor vises skærmbilleder af andre logonsider, der anvendes af forskellige domænekonti.

**Brugerdefinerede Azure AD-lejer domænekonto**: I dette tilfælde skal du se den tilpassede logonside brugerdefinerede Azure AD-lejer domæne.

![Brugerdefineret Azure AD-lejer domænekonto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft domænekonto med chipkort**: I dette tilfælde du vist siden login defineret af Microsoft virksomhedens IT med to-faktor-godkendelse.

![Brugerdefineret Azure AD-lejer domænekonto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-konto (MSA)**: I dette tilfælde skal du se siden login i Microsoft-Account til forbrugere.

![Brugerdefineret Azure AD-lejer domænekonto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Ved hjælp af krypterede medier udvidelser til PlayReady

På en moderne browser med krypterede medier udvidelser (EME) til understøttelse af PlayReady som Internet Explorer 11 i Windows 8.1 og op og Microsoft Edge-browseren på Windows 10, bliver PlayReady den underliggende DRM for EME.

![Ved hjælp af EME til PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Området mørk player er skyldes, at PlayReady beskyttelse forhindrer en i at foretage skærmbillede af beskyttet video. 

Følgende skærmbillede viser den player-plug-ins og MSE/EME support.

![Ved hjælp af EME til PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME i Microsoft Edge og Internet Explorer 11 på Windows 10 giver mulighed for aktivering af [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) på Windows 10-enheder, der understøtter den. PlayReady SL3000 låser strømmen af forbedrede premium indhold (4K, HDR, osv.) og nye levering af indhold modeller (tidlig vindue til udvidet indhold).

Fokuser på Windows-enheder: PlayReady er den eneste DRM i den hardware, der er tilgængelig på Windows-enheder (PlayReady SL3000). En streaming tjeneste kan bruge PlayReady via EME eller via et UWP til computeren og tilbyder en højere videokvalitet ved hjælp af PlayReady SL3000 end en anden DRM. 2K indhold, typisk flyder gennem Chrome eller Firefox og 4K indhold via Microsoft Edge/IE11 eller et UWP program på den samme enhed (afhængigt af Tjenesteindstillinger og implementering).


#### <a name="using-eme-for-widevine"></a>Ved hjælp af EME til Widevine

På en moderne browser med EME/Widevine support, som Chrome 41 + på Windows 10, Windows 8.1, Mac OSX Yosemite og Chrome på Android 4.4.4 er Google Widevine DRM bag EME.

![Ved hjælp af EME til Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Bemærk, at Widevine ikke forhindrer en i at foretage skærmbillede af beskyttet video.

![Ved hjælp af EME til Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Ikke berettigede brugere

Hvis en bruger ikke er medlem af gruppen "Berettiger brugere", brugeren vil ikke kunne overføre "ret Kontrollér" og tjenesten multi-DRM licens kan nægte at udstede den nødvendige licens, som vist nedenfor. En detaljeret beskrivelse er "licens hente mislykkedes", som er efter hensigten.

![Ophævelse berettigede brugere](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Køre brugerdefineret Secure Token Service

For dette scenario ved at køre brugerdefineret Service STS (Secure Token) der JWT tokenet udstedes af de brugerdefinerede STS ved hjælp af symmetriske eller asymmetrisk nøgle. 

Er tilfældet med ved hjælp af symmetriske nøgle (med Chrome):

![Køre brugerdefineret STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Er tilfældet med ved hjælp af asymmetrisk nøgle via en X509 af certifikat (ved hjælp af Microsoft moderne browser).

![Køre brugerdefineret STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

I begge af ovennævnte tilfælde forbliver brugergodkendelse på samme måde – via Azure AD. Den eneste forskel er, at JWT tokens er udstedt af de brugerdefinerede STS i stedet for Azure AD. Naturligvis, når du konfigurerer dynamiske CENC beskyttelse, begrænsning af licens leveringstjenesten angiver typen af JWT token symmetriske eller asymmetrisk nøgle.

## <a name="summary"></a>Oversigt

I dette dokument, vi gennemgås CENC med MFA-native-DRM og adgangskontrol via token godkendelse: dens design og dens med Azure, Azure Media Services og Azure Media Player installation.

- En reference design præsenteres som indeholder alle de nødvendige komponenter i et DRM/CENC undersystem;
- En referenceimplementering på Azure, Azure Media Services og Azure Media Player.
- Nogle emner, der er direkte involveret i design og implementering gennemgås også.


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Bekræftelse 

William Zhang Mingfei Yan Roland Le Franc, Kilroy Hughes, og Julia Kornich

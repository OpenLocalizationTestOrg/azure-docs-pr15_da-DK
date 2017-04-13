<properties 
    pageTitle="Bedste fremgangsmåder for sikkerhed til brug af Azure MFA"
    description="Dette dokument indeholder bedste fremgangsmåder ved hjælp af Azure MFA med Azure-konti"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Bedste fremgangsmåder for sikkerhed for Azure Multi-Factor Authentication med Azure AD-konti

Hvis du vil forbedre din godkendelsesprocessen, er det foretrukne valg i de fleste organisationer Multi-Factor authentication. Azure Multi-Factor Authentication (MFA) gør det muligt for virksomheder at opfylde deres sikkerhed og overholdelse krav samtidig en simpel logonoplevelse for deres brugere. I denne artikel beskrives nogle af de bedste fremgangsmåder, du bør overveje, når du planlægger for indføring af Azure MFA.

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>Bedste fremgangsmåder til Azure Multi-Factor Authentication i skyen
For at give alle dine brugere Multi-Factor authentication og tage fordelene ved de udvidede funktioner, der indeholder Azure Multi-Factor Authentication, skal du aktivere Azure Multi-Factor Authentication på alle dine brugere.  Dette opnås ved hjælp af en af følgende:

- Azure AD-Premium- eller Enterprise mobilitet pakke
- Multi-Factor Auth udbyder

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Azure AD Premium/Enterprise mobilitet pakke

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Det første anbefalede trin til ind Azure MFA i skyen ved hjælp af Azure AD Premium- eller Enterprise mobilitet pakke er at tildele licenser til brugerne.  Azure Multi-Factor Authentication er en del af disse pakker, og som din organisation har brug for ikke noget yderligere at udvide Multi-Factor authentication muligheden for alle brugere.

Når du konfigurerer Multi-Factor Authentication overveje følgende:

- Du behøver ikke at oprette en multi-Factor Auth udbyder.  Azure AD-Premium og Enterprise mobilitet pakke leveres med Azure Multi-Factor Authentication.  Hvis du opretter en Auth-udbyder, kan du få dobbelt faktureret.
- Azure AD-forbindelse er kun et krav, hvis du synkroniserer dit lokale Active Directory-miljø med en Azure AD-mappe. Hvis du kun bruger en Azure AD-mappe, der ikke er synkroniseret med en lokal forekomst af Active Directory, behøver du ikke Azure AD-forbindelse.


### <a name="multi-factor-auth-provider"></a>Multi-Factor Auth udbyder

![Multi-Factor Auth udbyder](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Hvis du ikke har Azure AD Premium- eller Enterprise mobilitet pakke er det første anbefalede trin til ind Azure MFA i skyen til at oprette en MFA Auth udbyder. Selvom MFA er som standard tilgængelig for globale administratorer, der har Azure Active Directory, når du installerer MFA til din organisation skal du udvide Multi-Factor authentication muligheden for at alle brugere og for at gøre, at du har brug for en multi-Factor Auth udbyder.
Når du vælger Auth-udbyder, skal du vælge en mappe og skal du overveje følgende:

- Du behøver ikke en Azure AD-mappe til at oprette en multi-Factor Auth udbyder.
- Du skal tilknyttes en Azure AD-directory Multi-Factor Auth udbyder, hvis du vil udvide Multi-Factor authentication til alle dine brugere og/eller vil dine globale administratorer skal kunne udnytte funktioner som management-portalen, brugerdefinerede hilsener og rapporter.
- DirSync eller AAD Sync er kun et krav, hvis du synkroniserer dit lokale Active Directory-miljø med en Azure AD-mappe. Hvis du kun bruger en Azure AD-mappe, der ikke er synkroniseret med en lokal forekomst af Active Directory, behøver du ikke DirSync eller AAD Sync.
- Hvis du har Azure AD Premium- eller Enterprise mobilitet pakke, behøver du ikke at oprette en multi-Factor Auth-udbyder. Du skal kun tildele en licens til en bruger, og derefter kan du begynde at slå MFA for brugere.
- Sørg for at vælge den rette Brug model til din virksomhed (per auth eller per aktiveret bruger), når du har valgt brugen modellen ikke kan du ændre den.

### <a name="user-account"></a>Brugerkonto
Når du aktiverer MFA for dine brugere, brugerkonti kan være i en af tre core tilstande: deaktiveret, aktiveret eller træder i kraft.
Brug nedenstående retningslinjer for at sikre, at du bruger indstillingen passer bedst til din installation:

- Når brugerens status er angivet til deaktiveret, er brugeren ikke bruger Multi-Factor godkendelse. Dette er standardtilstanden.
- Når brugerens status er angivet til aktiveret, betyder det, at brugeren er aktiveret, men ikke er fuldført registreringsprocessen. De bliver bedt om at fuldføre processen på næste logon. Denne indstilling påvirker ikke ikke browserapps. Alle apps fortsat fungerer, før registreringsprocessen er fuldført.
- Når brugerens status er angivet til tvungen, betyder det, at brugeren kan eller ikke har fuldført registrering. Hvis de har afsluttet registreringsprocessen derefter bruger de godkendelse i flere niveauer. Ellers kan brugeren bliver bedt om at fuldføre registreringsprocessen ved næste logon. Denne indstilling påvirker ikke browserapps. Disse apps fungerer ikke, før appadgangskoder oprettes og bruges.
- Bruge bruger meddelelse skabelonen tilgængelig i artiklen [Introduktion til Azure Multi-Factor Authentication i skyen](multi-factor-authentication-get-started-cloud.md) til at sende en mail til brugerne om MFA indføring.

### <a name="supportability"></a>Supportability

Da størstedelen af brugerne er vant til at bruge kun adgangskoder til at godkende, er det vigtigt, at din virksomhed få tilstedeværelse for alle brugere om denne proces. Denne tilstedeværelse kan reducere sandsynligheden for, at brugere ringer din helpdesk for mindre problemer i forbindelse med MFA.
Der er dog visse scenarier, hvor det er nødvendigt at midlertidigt deaktivere MFA. Brug nedenstående retningslinjer for at forstå, hvordan du skal håndtere disse scenarier:

- Kontrollér, at din supportmedarbejderen modtager oplæring håndtaget scenarier, hvor mobilapp eller telefon ikke er modtager en besked eller telefonopkald og derfor brugeren kan ikke logge på. De kan aktivere en enkeltstående Spring indstilling for at tillade en bruger til at godkende en enkelt gang ved at "springe" Multi-Factor authentication. Spring er midlertidige og udløber efter det angivne antal sekunder.
- Hvis det er nødvendigt, kan du udnytte muligheden for der er tillid til IP'er i Azure MFA. Denne funktion kan administratorer af en administreret eller medlem af organisationsnetværket lejer mulighed for at omgå Multi-Factor godkendelse for brugere, der logget ind fra firmaets lokalt intranet. Funktionerne er tilgængelige for Azure AD-lejere, der har Azure AD Premium, Enterprise mobilitet pakke eller Azure Multi-Factor Authentication licenser.


## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>Bedste fremgangsmåder til lokale Azure Multi-Factor Authentication
Hvis din virksomhed besluttede dig for at udnytte sin egen infrastruktur for at aktivere MFA, vil det være nødvendigt at installere en Azure Multi-Factor Authentication Server i det lokale miljø. MFA Server-komponenter vises i diagrammet nedenfor:

![Multi-Factor Auth udbyder](./media/multi-factor-authentication-security-best-practices/server.png)
*som standard ikke installeret ** installeret, men ikke aktiveret som standard


Azure Multi-Factor Authentication Server kan bruges til at beskytte skyen ressourcer og lokale ressourcer, der åbnes ved Azure AD-konti.  Men dette kan kun udføres ved hjælp af sammenslutning.  Det vil sige, skal du have AD FS og har den knyttet til din Azure AD-lejer.
Når du konfigurerer Multi-Factor Authentication Server overveje følgende:

- Hvis du er sikring af Azure AD ressourcer ved hjælp af Active Directory Federation Services, og klik derefter på 1st faktor godkendelse udføres lokale ved hjælp af AD FS og 2. faktor er udført lokalt ved honoring kravet.
- Det er ikke et krav, Azure Multi-Factor Authentication serveren være installeret på din AD FS sammenslutningsserver, men Multi-Factor Authentication kortet til AD FS skal være installeret på Windows Server 2012 R2, der kører AD FS. Du kan installere serveren på en anden computer, som det er en understøttet version og installere AD FS-adapter separat på AD FS sammenslutning-serveren. Se fremgangsmåden nedenfor for at få instruktioner til installation kortet separat.
- Installationsguiden af Multi-Factor Authentication AD FS-Adapter opretter en sikkerhedsgruppe, kaldet PhoneFactor Admins i Active Directory og lægger derefter din sammenslutning tjeneste AD FS servicekonto til denne gruppe. Det anbefales, at du kontrollerer på domænecontrolleren, gruppen PhoneFactor Admins faktisk er oprettet, og at AD FS-konto er medlem af denne gruppe. Hvis det er nødvendigt, du manuelt føje tjenestekonto AD FS til gruppen PhoneFactor Admins på domænecontrolleren.

### <a name="user-portal"></a>User Portal
Denne portal kører i et Internet Information Server (IIS)-websted, som gør det muligt for selvbetjeningsfunktionerne og giver et komplet sæt af funktioner til administration af brugeren. Brug nedenstående retningslinjer til at konfigurere denne komponent:

- IIS 6 eller nyere er påkrævet
- ASP.NET v2.0.507207 skal være installeret og registreret
- Denne server kan anvendes i en afskærmede netværk.



### <a name="app-passwords"></a>Appadgangskoder
Hvis din organisation er sammenkædet med Azure AD SSO, og du skal bruge Azure MFA, skal være opmærksom på følgende når du bruger appadgangskoder (Husk, at dette kun gælder for organisationsnetværket (SSO) bruges):

- Appadgangskoden er godkendt af Azure AD og derfor tilsidesætte sammenslutning. Sammenslutning bruges kun, når du konfigurerer Appadgangskode.
- Samlet (SSO) gemmes brugere adgangskoder i feltet organisations-id. Hvis brugeren forlader virksomheden, har disse oplysninger til dataflow til organisations-id med DirSync i realtid. Konto Deaktiver/sletningen kan tage op til 3 timer at synkronisere, forsinke Deaktiver/sletning af Appadgangskode i Azure AD.
- Lokalt klient adgangskontrol indstillinger ikke er accepteret af Appadgangskode
- Ingen lokale godkendelse logføring / overvågning egenskab er tilgængelig for Appadgangskode
- Flere slutbrugerlicensaftale uddannelse er påkrævet for Microsoft Lync 2013-klient.
- Visse avancerede arkitektonisk designs kan kræve ved hjælp af en kombination af organisatoriske brugernavn og adgangskode og appadgangskoder, når du bruger Multi-Factor authentication med klienter, afhængigt af hvor de godkende. For kunder, der godkender mod en lokal infrastruktur, vil du bruge en organisatoriske brugernavn og din adgangskode. For kunder, der godkender mod Azure AD, vil du bruge appadgangskoden.
- Brugere kan ikke oprette appadgangskoder, hvis din virksomhed kræver, eller hvis du vil tillade brugere at oprette appadgangskode i visse scenarier Sørg for, at indstillingen Tillad brugere at oprette appadgangskoder at logge på ikke-browser-programmer er valgt som standard.

## <a name="additional-considerations"></a>Yderligere overvejelser
Brug listen nedenfor til at forstå nogle yderligere overvejelser og bedste praksis for hver komponent, der skal være installeret lokalt:

Metode|Beskrivelse
:------------- | :------------- |
[Active Directory Federation-tjenesten](multi-factor-authentication-get-started-adfs.md)|Oplysninger om konfiguration af Azure Multi-Factor Authentication med AD FS.
[RADIUS Authenticaton](multi-factor-authentication-get-started-server-radius.md)|  Oplysninger om installation og konfiguration af Azure MFA Server med RADIUS.
[IIS-godkendelse](multi-factor-authentication-get-started-server-iis.md)|Oplysninger om installation og konfiguration af Azure MFA Server med IIS.
[Windows Authenticaton](multi-factor-authentication-get-started-server-windows.md)|  Oplysninger om installation og konfiguration af Azure MFA Server med Windows-godkendelse.
[LDAP-godkendelse](multi-factor-authentication-get-started-server-ldap.md)|Oplysninger om installation og konfiguration af Azure MFA Server med LDAP-godkendelse.
[Remote Desktop Gateway og Azure Multi-Factor Authentication Server ved hjælp af RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Oplysninger om installation og konfiguration af Azure MFA Server med Remote Desktop Gateway ved hjælp af RADIUS.
[Synkroniser med Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)|Oplysninger om installation og konfiguration af synkronisering mellem Active Directory og Azure MFA-serveren.
[Implementere Azure Multi-Factor Authentication Server Mobile-App-webtjeneste](multi-factor-authentication-get-started-server-webservice.md)|Oplysninger om installation og konfiguration af webtjenesten Azure MFA server.
[Avancerede VPN-konfiguration med Azure Multi-Factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md)|Oplysninger om konfiguration af Cisco ASA, Citrix Netscaler og Juniper/Pulse Secure VPN anvendelser ved hjælp af LDAP- eller RADIUS.


## <a name="additional-resources"></a>Yderligere ressourcer
I denne artikel fremhæves nogle af de bedste fremgangsmåder til Azure MFA, men der er andre ressourcer, som du kan også bruge planlægningen MFA installationen. På listen herunder har nogle vigtige artikler, der kan hjælpe dig under denne proces:

- [Rapporter i Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
- [Oplevelse af installationsprogrammet til Azure Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md)
- [Ofte stillede spørgsmål om Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)

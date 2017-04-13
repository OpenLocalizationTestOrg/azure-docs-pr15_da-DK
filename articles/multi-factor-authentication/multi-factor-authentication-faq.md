<properties
    pageTitle="Ofte stillede spørgsmål om Azure Multi-Factor Authentication"
    description="Oversigt over ofte stillede spørgsmål og svar, der er relateret til Azure Multi-Factor Authentication. Multi-Factor Authentication er en metode til bekræftelse af en bruger-id, der kræver mere end et brugernavn og adgangskode. Den indeholder en ekstra sikkerhedslag brugerlogon og transaktioner."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="azure-multi-factor-authentication-faq"></a>Ofte stillede spørgsmål om Azure Multi-Factor Authentication


Disse ofte stillede spørgsmål besvares nogle almindelige spørgsmål om Azure Multi-Factor Authentication og bruger tjenesten Multi-Factor Authentication, herunder spørgsmål om fakturering modellen og brugervenligheden.

## <a name="general"></a>Generelt

**Sp: Hvordan håndterer Azure Multi-Factor Authentication Server brugerdata?**

Med Multi-Factor Authentication Server gemmes brugerdata kun på de lokale servere. Ingen fast brugerdata er gemt i skyen. Når brugeren foretager totrinsbekræftelse, sender Multi-Factor Authentication serveren data til Azure Multi-Factor Authentication skybaseret tjeneste til godkendelse. Kommunikation mellem Multi-Factor Authentication Server og skytjenesten Multi-Factor Authentication bruger Secure Sockets Layer (SSL) eller sikkerhed TLS (Transport Layer) over port 443 udgående.

Når anmodninger om godkendelse sendes til skybaseret tjeneste, der indsamles data for godkendelse og brugen af rapporter. Datafelter, der er inkluderet i to-trins bekræftelse logfiler er som følger:

- **Entydigt ID** (enten bruger navn eller en lokal Multi-Factor Authentication Server-ID)
- **Fornavn og efternavn** (valgfrit)
- **Mailadresse** (valgfrit)
- **Telefonnummer** (når du bruger et taleopkald eller SMS godkendelse)
- **Enhed Token** (når du bruger mobilapp godkendelse)
- **Godkendelsestilstand**
- **Godkendelse resultat**
- **Multi-Factor Authentication servernavn**
- **Multi-Factor Authentication Server IP-adresse**
- **Klient IP** (hvis relevant)

De valgfrie felter kan konfigureres i Multi-Factor Authentication Server.

Bekræftelse resultatet (succes eller afvisning) og årsagen til, hvis den blev afvist, er gemt med Godkendelsesdataene, og er tilgængelig i godkendelse og brugsrapporter.


## <a name="billing"></a>Fakturering

De fleste fakturering spørgsmål kan besvares ved at referere til [multi-Factor Authentication priser side](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

**Sp: min organisation betale for telefonopkald eller tekstmeddelelser bruges til at godkende mine brugere?**

Organisationer er ikke betale for individuelle telefonopkald placeret eller tekst meddelelser, der sendes af brugere via Azure Multi-Factor Authentication. Telefon ejere kan betale for telefonopkald eller tekstmeddelelser, de modtager, efter deres personlige telefontjeneste.

**Sp: bruges til hver bruger fakturering model gebyr baseret på antallet af brugere, der er konfigureret til at bruge Multi-Factor Authentication eller antallet af brugere, der udfører kontrol?**

Fakturering er baseret på antallet af brugere, der er konfigureret til at bruge Multi-Factor Authentication.

**Sp: hvordan fungerer Multi-Factor Authentication fakturering?**

Når du bruger den "per bruger" eller "per godkendelse" modellen, Azure MFA er en forbrug-baserede ressource. En hvilken som helst gebyrer faktureres til organisationens Azure abonnement ligesom virtuelle maskiner, websteder osv.

Når du bruger licens modellen, er Azure Multi-Factor Authentication licenser købt og derefter tildeles til brugere, ligesom til Office 365 og andre abonnement produkter.

**Sp: er der en gratis version af Azure Multi-Factor Authentication for administratorer?**

I nogle tilfælde Ja. Multi-Factor Authentication til Azure administratorer indeholder et undersæt af Azure MFA funktioner uden omkostninger. Dette tilbud gælder for medlemmer af gruppen Azure globale administratorer i Azure Active Directory-forekomster, der ikke er knyttet til en udbyder af forbrug-baserede Azure Multi-Factor Authentication. Ved hjælp af en udbyder af Multi-Factor Authentication opgraderer alle administratorer og brugere i kataloget, der er konfigureret til at bruge Multi-Factor Authentication til den fulde version af Azure Multi-Factor Authentication.

**Sp: er der en gratis version af Azure Multi-Factor Authentication for Office 365-brugere?**

I nogle tilfælde Ja. Multi-Factor Authentication til Office 365 indeholder et undersæt af Azure MFA funktioner uden omkostninger. Dette tilbud gælder for brugere, der har en Office 365-licens, der er tildelt, når en forbrug-baserede Azure Multi-Factor Authentication udbyder ikke er knyttet til den tilsvarende forekomst af Azure Active Directory. Ved hjælp af Multi-Factor Authentication provider opgraderer alle administratorer og brugere i kataloget, der er konfigureret til at bruge Multi-Factor Authentication til den fulde version af Azure Multi-Factor Authentication.

**Sp: kan min organisation skifte mellem hver bruger og godkendelse forbrug fakturering modeller til enhver tid?**

Din organisation vælger en fakturering model, når der oprettes en ressource. Du kan ikke ændre en fakturering model, når ressourcen, der er klargjort. Du kan dog oprette en anden Multi-Factor Authentication ressource for at erstatte oprindeligt. Brugerindstillinger og -indstillinger kan ikke overføres til den nye ressource.

**Sp: kan min organisation skifte mellem forbrug fakturering og licensmodel til enhver tid?**

Når licenser er føjet til en mappe, der allerede har en udbyder af Azure Multi-Factor Authentication hver bruger, er forbrug-baserede fakturering reduceres med antallet af licenser, der er ejet. Hvis alle brugere, der er konfigureret til at bruge Multi-Factor Authentication har licenser, der er tildelt, kan administratoren slette provideren Azure Multi-Factor Authentication.

Du kan ikke blande per godkendelse forbrug fakturering med en licensmodel. Når en udbyder af Multi-Factor Authentication per godkendelse er knyttet til en mappe, faktureret organisationen for alle Multi-Factor Authentication bekræftelse anmodninger, uanset eventuelle ejes licenser.

**Sp: organisationen har til at bruge og synkronisere identiteter for at bruge Azure Multi-Factor Authentication?**

Når en organisation bruger en forbrug-baserede fakturering model, er Azure Active Directory er ikke påkrævet. Sammenkæde en udbyder af Multi-Factor Authentication til en mappe er valgfrit. Hvis din organisation ikke er knyttet til en mappe, kan det Installer Azure Multi-Factor Authentication Server eller de Azure Multi-Factor Authentication SDK i det lokale miljø.

Azure Active Directory er påkrævet for licens modellen, fordi licenser føjes til mappen, når du køber og tildele dem til brugere i kataloget.


## <a name="usability"></a>Brugervenligheden

**Sp: Hvad gør en bruger, hvis de ikke har modtaget et svar på deres telefon, eller hvis telefonen ikke er tilgængelige for brugeren?**

Hvis brugeren har konfigureret en sikkerhedskopi telefon, skal de prøv igen og vælge telefonen, når du bliver bedt om på siden Log på. Hvis brugeren ikke har en anden metode, der er konfigureret, kan organisationens administrator opdatere det tal, der er tildelt til brugerens primære telefon.


**Sp: Hvad gør administratoren, hvis en bruger kontakter administratoren om en konto, der kan ikke længere få adgang til brugeren?**

Administratoren kan nulstille brugerens konto ved at bede dem om at gennemgå registreringsprocessen igen. Få mere at vide om [Administration af brugeren og Enhedsindstillinger med Azure Multi-Factor Authentication i skyen](multi-factor-authentication-manage-users-and-devices.md).

**Sp: Hvad gør en administrator, hvis en bruger telefon, der bruger appadgangskoder går tabt eller stjæles?**

Administratoren kan slette alle brugerens appadgangskoder for at forhindre uautoriseret adgang. Når brugeren har en erstatnings-enhed, kan brugeren Genopret adgangskoderne. Få mere at vide om [Administration af brugeren og Enhedsindstillinger med Azure Multi-Factor Authentication i skyen](multi-factor-authentication-manage-users-and-devices.md).

**Sp: Hvad nu, hvis brugeren ikke kan logge på ikke-browserapps?**

En bruger, der er konfigureret til at bruge Multi-Factor Authentication kræver en appadgangskode til at logge på visse ikke-browser-apps. En bruger skal rydde (slette) logonoplysninger, genstarte appen og logge på ved hjælp af deres bruger navn og app-adgangskode.

Få mere at vide om oprettelse af appadgangskoder og andre [hjælpe dig med appadgangskoder](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Moderne godkendelse til Office 2013-klienter
>
> Office 2013-klienter (herunder Outlook) understøtter nye godkendelsesprotokoller. Du kan konfigurere Office 2013 for at understøtte Multi-Factor Authentication. Når du konfigurerer Office 2013, er appadgangskoder er ikke påkrævet til Office 2013-klienter. Du kan finde yderligere oplysninger finder [Office 2013 moderne godkendelse prøveversionen meddelelse](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**Sp: Hvad gør en bruger, hvis brugeren ikke modtager en SMS-besked, eller hvis brugeren besvarer en tovejs SMS-besked, men bekræftelsen udløber?**

Levere af SMS-beskeder, og er ingen garanti for modtagelse af svar i tovejs-SMS fordi der er ukontrolleret faktorer, der kan påvirke pålideligheden af tjenesten. Disse faktorer omfatter destinationsland, mobiltelefon carrier og signalstyrken.

Brugere, der oplever problemer med at modtage tekstmeddelelser pålideligt bør i stedet vælge metoden for mobile-app eller et telefonopkald. Mobilappen kan modtage beskeder både over mobilnetværk og Wi-Fi-forbindelser. Desuden kan mobilappen generere bekræftelse koder, selv når enheden har ingen signal overhovedet. Microsoft Authenticator app er tilgængelig til [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)og [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Hvis du skal bruge SMS-beskeder, anbefaler vi bruger envejs SMS i stedet for tovejs SMS, når det er muligt. Envejs SMS er mere pålidelig og det forhindrer brugerne i at foretage globale SMS betaling fra besvarelse af en SMS-besked, der blev sendt fra et andet land.


**Sp: kan jeg bruge hardwaretokens med Azure Multi-Factor Authentication Server?**

Hvis du bruger Azure Multi-Factor Authentication Server, kan du importere fra tredjepart Åbn godkendelse (ed) tidsbaserede, enkeltstående adgangskode (TOTP) tokens og bruge dem til totrinsbekræftelse.

Du kan bruge ActiveIdentity tokens, der er ed TOTP tokens, hvis du placerer hemmeligt vigtige fil i en CSV-fil og importere til Azure Multi-Factor Authentication Server. Du kan bruge ed tokens med Active Directory Federation Services (ADFS), Remote godkendelse telefonmøder bruger Service (RADIUS) når klientsystemet kan behandle access udfordring svar, og Internet Information Server (IIS) formularbaseret godkendelse.

Du kan importere fra tredjepart ed TOTP tokens med følgende formater:  
- Bærbare symmetriske vigtige objektbeholder (PSKC)  
- CSV, hvis filen indeholder et serienummer, en hemmeligt nøgle i Base 32-format og et tidsinterval  

**Sp: kan jeg bruge Azure Multi-Factor Authentication Server til at sikre Terminal Services?**

Ja, men hvis du bruger Windows Server 2012 R2 eller nyere, kun ved hjælp af Remote Desktop Gateway (RD Gateway).

Sikkerhedsændringer i Windows Server 2012 R2 er ændret, som Azure Multi-Factor Authentication Server opretter forbindelse til den lokale sikkerhed nøglecenter (LSA) sikkerhedspakke i Windows Server 2012 og tidligere versioner. I versioner af Terminal Services i Windows Server 2012 eller tidligere kan du [secure et program med Windows-godkendelse](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Hvis du bruger Windows Server 2012 R2, skal du RD Gateway.

**Sp: Hvorfor ville en bruger modtage et multi-Factor Authentication opkald fra en anonym opkalds efter konfigurering af opkalds-ID?**

Når Multi-Factor Authentication opkald placeres gennem offentlige telefonnetværket, dirigeres nogle gange de gennem en carrier, som ikke understøtter opkalds-ID. På grund af dette, er opkalds-ID ikke sikkert, selvom Multi-Factor Authentication systemet altid sender den.


## <a name="errors"></a>Fejl

**Sp: Hvad skal brugerne gøre, hvis de se fejlmeddelelsen "anmodningen om godkendelse er ikke for en aktiveret konto", når du bruger mobilapp meddelelser?**

Bede dem om at følge denne fremgangsmåde for at fjerne deres konto fra mobilappen og derefter tilføje den igen:

1. Gå til [din Azure portalen profil](https://account.activedirectory.windowsazure.com/profile/) , og log på med din virksomhedskonto.
2. Markér **ekstra sikkerhed bekræftelse**.
4. Fjern den eksisterende konto fra mobilappen.
5. Klik på **Konfigurer**, og følg derefter vejledningen for at omkonfigurere mobilappen.


**Sp: Hvad skal brugerne gøre, hvis de får vist en 0x800434D4L fejlmeddelelse, når du logger på en ikke-browser-program?**

I øjeblikket, kan en bruger bruge ekstra sikkerhed bekræftelse kun med programmer og tjenester, som brugeren kan få adgang til via en browser. Ikke-browser-programmer (også kaldet *RTF-klientprogrammer*), der er installeret på en lokal computer, som Windows PowerShell, fungerer ikke med konti, der kræver ekstra sikkerhed bekræftelse. I dette tilfælde kan brugeren se programmet genererer en 0x800434D4L fejl.

En løsning til dette er har separat bruger konti admin-relaterede og ikke-administrator handlinger. Senere, kan du sammenkæde postkasser mellem din administratorkonto og ikke-administratorkonto, så du kan logge på Outlook ved hjælp af din ikke-administratorkonto. Få mere at vide om dette Lær, hvordan du kan [give en administrator mulighed for at åbne og få vist indholdet af en brugers postkasse](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Næste trin

Hvis dit spørgsmål ikke besvaret her, skal du lade det i kommentarerne nederst på siden. Eller her er nogle flere indstillinger til at få hjælp:


**Sp: Hvordan kan jeg få hjælp til Azure Multi-Factor Authentication?**

- Søge i [Microsoft Support Knowledge Base](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) efter løsninger på almindelige tekniske problemer.

- Søge efter og gennemse tekniske spørgsmål og svar fra gruppen, eller dine egne spørgsmål i [Azure Active Directory-fora](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

- Hvis du er en ældre PhoneFactor-kunde, og du har spørgsmål eller har brug for hjælp nulstille en adgangskode, kan du bruge linket [til nulstilling af adgangskode](mailto:phonefactorsupport@microsoft.com) til at åbne en understøttelse af store og små bogstaver.

- Kontakte en supportmedarbejder via [understøttelse af Azure Multi-Factor Authentication Server (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Ved at kontakte os, er det nyttigt, hvis du kan medtage så mange oplysninger om problemet som muligt. Du kan angive oplysninger omfatter den side, hvor du fik vist fejlen, fejlkoden, bestemt session-ID og ID'ET for den bruger, der fik vist fejlen.

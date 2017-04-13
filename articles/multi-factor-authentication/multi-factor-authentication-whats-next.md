<properties
    pageTitle="Azure Multi-Factor Authentication - Hvad skal der ske nu"
    description="Dette er den Azure Multi-Factor authentication side, der beskriver, hvad du skal gøre med MFA.  Dette omfatter rapporter, bedrageri besked, enkeltstående Spring, brugerdefinerede talebeskeder, cachelagring, der er tillid til IP-adresser og app adgangskoder."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="kgremban"/>

# <a name="configuring-azure-multi-factor-authentication"></a>Konfiguration af Azure Multi-Factor Authentication

I denne artikel hjælper dig med at administrere Azure Multi-Factor Authentication nu, hvor du befinder dig op at køre.  Det dækker en lang række emner, der hjælper dig med at få mest muligt ud af Azure Multi-Factor Authentication.  Ikke alle disse funktioner er tilgængelige i hver version af Azure Multi-Factor Authentication.

Konfigurationen for nogle af funktionerne nedenfor findes på portalen Azure Multi-Factor Authentication administration. Der findes to forskellige måder, som du kan få adgang til portalen MFA administration, som er begge færdig via Azure-portalen. Først er ved at administrere en multi-Factor Auth udbyder, hvis bruger forbrug-baserede MFA. Andet er via MFA-Tjenesteindstillinger. Den anden mulighed kræver en udbyder af Multi-Factor Auth eller licensen til Azure MFA, Azure AD Premium eller Enterprise mobilitet pakke.

Log på portalen Azure som administrator for at få adgang til portalen MFA Management via en Azure Multi-Factor Auth-udbyder, og vælg indstillingen Active Directory. Klik på fanen **Multi-Factor Auth udbydere** , og derefter vælge mappen og klikke på knappen **Administrer** nederst.

Log på portalen Azure som administrator for at få adgang til portalen MFA Management via siden MFA-Tjenesteindstillinger, og vælg indstillingen Active Directory. Klik på mappen, og klik derefter på fanen **Konfigurer** . Vælg **Administrer Tjenesteindstillinger**under afsnittet Multi-Factor authentication. Klik på linket **Gå til portalen** nederst på siden MFA-Tjenesteindstillinger.


Funktion| Beskrivelse| Hvad er dækket
:------------- | :------------- | :------------- |
[Svindel besked](#fraud-alert)|Svindel besked kan konfigureres og konfigurere, så brugerne kan rapportere falske forsøg på at få adgang til deres ressourcer.|Hvordan du konfigurerer, konfigurere og rapportere svindel?
[Enkeltstående Spring](#one-time-bypass) |En enkeltstående Spring gør det muligt for en bruger til at godkende en enkelt gang ved at "springe" Multi-Factor authentication.|Hvordan du kan indstille og konfigurere en enkeltstående Spring
[Brugerdefineret talebeskeder](#custom-voice-messages) |Brugerdefineret talebeskeder gør det muligt at bruge din egen optagelser eller hilsener med Multi-Factor authentication. |Hvordan du kan indstille og konfigurere brugerdefinerede hilsener og meddelelser
[Cachelagring](#caching-in-azure-multi-factor-authentication)|Cachelagring, kan du angive et bestemt tidspunkt periode, så efterfølgende godkendelse lykkes automatisk. |Hvordan du kan indstille og konfigurere godkendelse cachelagring.
[Der er tillid til IP'er](#trusted-ips)|Der er tillid til IP-adresser er en funktion af godkendelse i flere niveauer, der tillader administratorer af en administreret eller medlem af organisationsnetværket lejer mulighed for at omgå Multi-Factor godkendelse for brugere, der logget ind fra firmaets lokalt intranet.|Konfigurere og konfigurere IP-adresser, der er undtaget til multi-Factor authentication
[Appadgangskoder](#app-passwords)|En appadgangskode kan et program, der ikke er MFA-aware ignorere Multi-Factor authentication og fortsætte med at arbejde.|Oplysninger om appadgangskoder.
[Husk Multi-Factor Authentication til husket enheder og browsere](#remember-multi-factor-authentication-for-devices-users-trust)|Gør det muligt at huske enheder til et fastsat antal dage, efter at en bruger har logget på ved hjælp af MFA.|Oplysninger om at aktivere denne funktion og konfiguration af antallet dage.
[Kan vælges bekræftelse metoder](#selectable-verification-methods)|Kan du vælge de godkendelsesmetoder, der er tilgængelige for brugerne at benytte.|Oplysninger om at aktivere eller deaktivere bestemte godkendelsesmetoder som opkald eller SMS.



## <a name="fraud-alert"></a>Svindel besked
Svindel besked kan konfigureres og konfigurere, så brugerne kan rapportere falske forsøg på at få adgang til deres ressourcer.  Brugere kan rapportere svindel med mobilappen eller via deres telefon.

### <a name="to-set-up-and-configure-fraud-alert"></a>Til at konfigurere og konfigurere svindel besked

1.  Logge på http://azure.microsoft.com
2.  Gå til portalen MFA Management ifølge instruktionerne øverst på denne side.
3.  Klik på indstillinger under afsnittet konfiguration på portalen Azure Multi-Factor Authentication administration.
4.  Markér afkrydsningsfeltet Tillad brugere at sende advarsler om svindel afkrydsningsfelt under afsnittet svindel besked på siden Indstillinger.
5.  Hvis du ønsker at blive blokeret, når svindel rapporteres brugere, Marker blokere bruger når svindel rapporteres.
6.  Angiv en kode, der kan bruges under opkald bekræftelse i tekstfeltet **Kode til rapport svindel under indledende hilsen** . Hvis en bruger indtaster denne kode plus # i stedet for blot tegnet #, rapporteres en svindel besked.
7.  Til bunden, og klik på Gem.

>[AZURE.NOTE]
>Microsofts standard stemme hilsener Fortæl brugerne trykker på 0# til at sende en besked om svindel. Hvis du vil bruge en kode end 0, skal du registrere og overføre dine egne brugerdefinerede stemme hilsener relevante instruktioner.


![Skyen](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>Til rapport svindel besked
Svindel besked kan rapporteres to måder.  Enten via mobilappen eller via telefonen.  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>Til rapport svindel besked med mobilappen



1. Når der sendes en bekræftelse på din telefon skal du vælge den til at starte Microsoft Authenticator app.
2. Rapportere svindel, skal du klikke på Annuller og rapport svindel. Dette viser et felt, som står organisationens IT supportpersonale får besked.
3. Klik på rapporten svindel.
4. APP'en, klik på Luk.

![Skyen](./media/multi-factor-authentication-whats-next/report1.png)


![Skyen](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>Til rapport svindel besked med telefonen

1. Når bekræftelsen kommer et opkald til din telefon, besvare den.  
2. Rapportere svindel, skal du angive den kode, der er blevet konfigureret til at svare med rapportering svindel via telefonen og derefter tegnet #. Du får besked, en svindel besked er blevet sendt.
3. Afslutte opkaldet.

### <a name="to-view-the-fraud-report"></a>Sådan får du vist rapporten svindel?

1. Log på [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Vælg Active Directory i venstre side.
3. Vælg Multi-Factor Auth udbydere øverst. Dette viser en liste over dine Multi-Factor Auth udbydere.
4. Hvis du har mere end én Multi-Factor Auth udbyder, skal du vælge det, du ønsker at få vist rapporten svindel besked, og klik på Administrer nederst på siden. Hvis du har kun én, skal du klikke på Administrer. Dette åbner Azure Multi-Factor Authentication Management Portal.
5. Klik på svindel besked på Azure Multi-Factor Authentication Management-portalen, i venstre side under View A-rapport.
6. Angiv det datointerval, du vil have vist i rapporten. Du kan også angive en bestemt brugernavne, telefonnumre og brugerens status.
7. Klik på Kør. Dette viser en rapport, der minder om den nedenfor. Du kan også klikke på Eksportér til csv-, hvis du vil eksportere rapporten.

## <a name="one-time-bypass"></a>Enkeltstående Spring

En enkeltstående Spring gør det muligt for en bruger til at godkende en enkelt gang ved at "springe" Multi-Factor authentication. Spring er midlertidige og udløber efter det angivne antal sekunder.  Så du kan aktivere en enkeltstående Spring i situationer, hvor mobilapp eller telefon ikke er modtager en besked eller opkald, så brugeren kan få adgang til den ønskede ressource.

### <a name="to-create-a-one-time-bypass"></a>Oprette en enkeltstående Spring

1.  Logge på http://azure.microsoft.com
2.  Gå til portalen MFA Management ifølge instruktionerne øverst på denne side.
3.  I Azure Multi-Factor Authentication Management portalen, hvis du ser navnet på din lejer eller Azure MFA udbyder til venstre med en + ud for det, skal du klikke på den + se forskellige MFA Server gentagelse grupper og gruppen Azure standard. Klik på den relevante gruppe.
4.  Klik på **One-Time Spring**under Administration af brugeren.
![Skyen](./media/multi-factor-authentication-whats-next/create1.png)
5.  Klik på **Ny One-Time Spring**på siden One-Time Spring.
6.  Skriv antallet sekunder, der findes på Spring, årsagen til Spring brugerens brugernavn, og klik på **igennem**.
![Skyen](./media/multi-factor-authentication-whats-next/create2.png)
7.  På dette tidspunkt, skal brugeren logge på, før den enkeltstående Spring udløber.



### <a name="to-view-the-one-time-bypass-report"></a>For at få vist igennem den enkeltstående rapport

1. Log på [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Vælg Active Directory i venstre side.
3. Vælg Multi-Factor Auth udbydere øverst. Dette viser en liste over dine Multi-Factor Auth udbydere.
4. Hvis du har mere end én Multi-Factor Auth udbyder, skal du vælge det, du ønsker at få vist rapporten svindel besked, og klik på Administrer nederst på siden. Hvis du har kun én, skal du klikke på Administrer. Dette åbner Azure Multi-Factor Authentication Management Portal.
5. Klik på One-Time Spring på Azure Multi-Factor Authentication Management-portalen, i venstre side under View A-rapport.
6. Angiv det datointerval, du vil have vist i rapporten. Du kan også angive en bestemt brugernavne, telefonnumre og brugerens status.
7. Klik på Kør. Dette viser en rapport, der minder om den nedenfor. Du kan også klikke på Eksportér til csv-, hvis du vil eksportere rapporten.

<center>![Skyen](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>Brugerdefineret talebeskeder

Brugerdefineret talebeskeder gør det muligt at bruge din egen optagelser eller hilsener med Multi-Factor authentication.  Disse kan bruges ud over eller til at erstatte Microsoft-poster.

Før du begynder at være opmærksom på følgende:

- De aktuelle understøttede filformater er .wav og .mp3.
- Grænsen for filstørrelsen er 5 MB.
- Det anbefales, at til godkendelse af meddelelser, som det ikke være længere end 20 sekunder. Noget, der er større end dette kan medføre bekræftelsen mislykkes, fordi brugeren, ikke reagerer, før meddelelsen afsluttes og bekræftelsen timeout.



### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>Konfigurere brugerdefinerede talebeskeder i Azure Multi-Factor Authentication
1.  Oprette en brugerdefineret talebesked ved hjælp af en af de understøttede filformater.
2.  Logge på http://azure.microsoft.com
3.  Gå til portalen MFA Management ifølge instruktionerne øverst på denne side.
4.  Klik på talebeskeder under afsnittet konfiguration på portalen Azure Multi-Factor Authentication administration.
5.  Klik på **Ny talebesked**under afsnittet talebeskeder.
![Skyen](./media/multi-factor-authentication-whats-next/custom1.png)
6.  I feltet Konfigurer: nye talebeskeder på siden skal du klikke på **Administrer lyd filer**.
![Skyen](./media/multi-factor-authentication-whats-next/custom2.png)
7.  I feltet Konfigurer: lyde siden filer skal du klikke på **Overfør lydfil**.
![Skyen](./media/multi-factor-authentication-whats-next/custom3.png)
8.  I feltet Konfigurer: overføre lydfil, klik på **Gennemse** og gå til din talebesked, klik på **Åbn**.
![Skyen](./media/multi-factor-authentication-whats-next/custom4.png)
9.  Tilføje en beskrivelse, og klik på Overfør.
10. Når dette er fuldført, bekræfter en meddelelse, du har overført filen.
11. Klik på talebeskeder i venstre side.
12. Klik på Ny talebesked under afsnittet talebeskeder.
13. Vælg et sprog på rullelisten sprog.
14. Hvis denne meddelelse er til et bestemt program skal du angive det i feltet programmet.
15. Vælg den meddelelse fra typen meddelelse, skal tilsidesættes med vores nye brugerdefinerede meddelelse.
16. Vælg din lydfil fra rullelisten lydfil.
17. Klik på **Opret**. En meddelelse bekræfter, at du har oprettet en talebesked.
![Skyen](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## <a name="caching-in-azure-multi-factor-authentication"></a>Cachelagring i Azure Multi-Factor Authentication

Cachelagring, kan du angive et bestemt tidspunkt periode, så efterfølgende godkendelse lykkes automatisk. Dette er primært bruges, når lokale systemer som VPN sender flere anmodninger om godkendelse, mens den første anmodning er stadig er i gang. Dette giver mulighed for de efterfølgende anmodninger om kan udføres automatisk, når brugeren er oprettet bekræftelsen i gang. Bemærk, at cachelagring ikke er beregnet til bruges til logon til Azure AD.


### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>Konfigurere cachelager i Azure Multi-Factor Authentication

1.  Logge på http://azure.microsoft.com
2.  Gå til portalen MFA Management ifølge instruktionerne øverst på denne side.
3.  Klik på cachelagring under afsnittet konfiguration på portalen Azure Multi-Factor Authentication administration.
4.  Klik på ny Cache på afkrydsningsfeltet Konfigurer cachelagring af siden
5.  Vælg typen Cache og cache-sekunder. Klik på Opret.

<center>![Skyen](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>Der er tillid til IP'er

Der er tillid til IP-adresser er en funktion af godkendelse i flere niveauer, der tillader administratorer af en administreret eller medlem af organisationsnetværket lejer mulighed for at omgå Multi-Factor godkendelse for brugere, der logget ind fra firmaets lokalt intranet. Funktionerne er tilgængelige for Azure AD-lejere, der har Azure AD Premium, Enterprise mobilitet pakke eller Azure Multi-Factor Authentication licenser.


Type af Azure AD-lejer| Tilgængelige der er tillid til IP-indstillinger
:------------- | :------------- |
Administrerede|Bestemte IP-adresseområder – administratorer kan angive et område af IP-adresser, der kan tilsidesætte Multi-Factor godkendelse for brugere, der logget ind fra firmaets intranet.
Del af et organisationsnetværk|<li>Alle brugere af Federated - alle organisationsnetværket brugere, der er logge på fra i organisationen, der springer Multi-Factor authentication ved hjælp af et krav, der er udstedt af AD FS.</li><li>Bestemte IP-adresseområder – administratorer kan angive et område af IP-adresser, der kan tilsidesætte Multi-Factor godkendelse for brugere, der logget ind fra firmaets intranet.

Dette tilsidesættelse kun fungerer fra i en virksomheds intranet. Så for eksempel, hvis du kun har markeret alle organisationsnetværket brugere, og en bruger logger ind skal fra uden for virksomhedens intranet, brugeren godkende ved hjælp af godkendelse i flere niveauer, selvom brugeren præsenterer et AD FS krav. I følgende tabel beskrives når Multi-Factor authentication og app der kræves adgangskoder i din corpnet og uden for din corpnet når der er tillid til IP-adresser er aktiveret.


|Der er tillid til IP'er aktiveret| Der er tillid til IP'er deaktiveret
:------------- | :------------- | :------------- |
Indvendig corpnet|For browser flyder Multi-Factor authentication ikke påkrævet.|For browser flyder Multi-Factor godkendelse kræves
|Til omfattende klient-apps fungerer normalt adgangskoder, hvis brugeren ikke har oprettet en hvilken som helst appadgangskoder. Når en appadgangskode er blevet oprettet, er appadgangskoder påkrævet.|Til omfattende klient apps appadgangskoder påkrævet
Eksterne corpnet|For browser flyder Multi-Factor godkendelse er påkrævet.|For browser flyder Multi-Factor godkendelse er påkrævet.
|Til omfattende klient-apps, der er påkrævet appadgangskoder.|Til omfattende klient-apps, der er påkrævet appadgangskoder.

### <a name="to-enable-trusted-ips"></a>Aktivere IP'er, der er tillid til

1. Log på portalen Azure klassisk.
2. Klik på Active Directory i venstre side.
3. Directory under Klik på den mappe, du vil konfigurere der er tillid til IPsing på.
4. Klik på den mappe, du har valgt, Konfigurer.
5. Klik på Administrer Tjenesteindstillinger i afsnittet Multi-Factor authentication.
6. På siden Tjenesteindstillinger under der er tillid til IP'er enten vælge:

    - For anmodninger fra samlet brugere, der kommer fra min intranet – alle samlet brugere, der logget ind fra virksomhedens netværk, der springer Multi-Factor authentication ved hjælp af et krav, der er udstedt af AD FS.
    - Angiv IP-adresserne i felterne, der vises ved hjælp af CIDR notation for anmodninger fra et bestemt interval af offentlige IP'er –. For eksempel: xxx.xxx.xxx.0/24 til IP-adresser i området xxx.xxx.xxx.1 – xxx.xxx.xxx.254 eller xxx.xxx.xxx.xxx/32 for en enkelt IP-adresse. Du kan angive op til 50 IP-adresseområder.

7. Klik på Gem.
8. Klik på Luk, når opdateringerne, der er anvendt.



![Der er tillid til IP'er](./media/multi-factor-authentication-whats-next/trustedips3.png)




## <a name="app-passwords"></a>Appadgangskoder

Du kan ikke bruge Multi-Factor authentication i nogle apps, som Office 2010 eller ældre og Apple Mail.  Hvis du vil bruge disse apps, skal du bruge "appadgangskoder" i stedet for din traditionelle adgangskode.  Appadgangskoden betyder, at springe Multi-Factor authentication og fortsætte med at arbejde.

>[AZURE.NOTE] Moderne godkendelse til Office 2013-klienter
>
> Office 2013-klienter (herunder Outlook) understøtter nye godkendelsesprotokoller nu og kan aktiveres til at understøtte Multi-Factor Authentication.  Det betyder, at når aktiveret, appadgangskoder ikke er påkrævet til brug sammen med Office 2013-klienter.  Få vist [Office 2013 moderne godkendelse offentlige eksempel offentliggjort](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)kan finde flere oplysninger.



### <a name="important-things-to-know-about-app-passwords"></a>Vigtige ting at vide om appadgangskoder

Følgende er en vigtig liste over de ting, du bør vide om appadgangskoder.

- Brugere kan have flere appadgangskoder, hvilket øger området surface for identitetstyveri. Da appadgangskoder er svært at huske, kan den opfordre folk til at skrive dette ned. Det anbefales ikke og skal være ikke bør fordi kun én faktor er påkrævet for at logon med appadgangskode.
- Apps som cachen adgangskoder og brug den lokale scenarier starter muligvis ned, da appadgangskoden ikke kendes uden for dit organisations-id. Et eksempel er Exchange e-mails, der er i det lokale miljø, men arkiverede e-mailen er i skyen. Den samme adgangskode virker ikke.
- Den aktuelle adgangskode genereres automatisk, og der ikke er angivet af brugeren. Dette er, fordi den automatisk oprettede adgangskode er sværere for en hacker at gætte og er mere sikker.
- I øjeblikket er der en grænse på 40 adgangskoder per bruger. Du bliver bedt om at slette et af dine eksisterende appadgangskoder for at oprette en ny.
- Når Multi-Factor authentication er aktiveret på en brugers konto, appadgangskoder kan bruges sammen med de fleste ikke-browser-klienter som Outlook og Lync, men ikke kan udføres administrative handlinger med appadgangskoder gennem ikke browser programmer som Windows PowerShell, også selvom brugeren har en administratorkonto.  Sikre, at du opretter en tjenestekonto med en stærk adgangskode til at køre PowerShell-scripts, og Aktivér ikke kontoen til multi-Factor authentication.

>[AZURE.WARNING]  Appadgangskoder virker ikke i hybridmiljøer hvor klienter kommunikerer med både lokalt og autodiscover slutpunkter i skyen. Dette skyldes, at domæneadgangskoder skal godkendes i det lokale miljø og appadgangskoder skal godkende med skyen.


### <a name="naming-guidance-for-app-passwords"></a>Navngive vejledning til Appadgangskoder
Det anbefales, at app adgangskode navne skal afspejle den enhed, som de anvendes. Eksempelvis hvis du har en bærbar computer, der indeholder ikke-browserapps som Outlook, Word og Excel, skal du kun oprette en appadgangskode med navnet bærbare og bruge denne appadgangskode i alle disse programmer. Selvom du kan oprette separate adgangskoder for alle disse programmer, er det ikke anbefales. Anbefalet er måde at bruge en appadgangskode per enhed.


<center>![Skyen](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>Samlet (SSO) Appadgangskoder
Azure AD understøtter sammenslutning med lokale Windows Server Active Directory Domain Services (AD DS). Hvis din organisation er federated(SSO) med Azure AD, og du skal bruge Azure Multi-Factor Authentication, så er følgende vigtige oplysninger, skal du være opmærksom på, når du bruger appadgangskoder. Dette gælder kun for federated(SSO) kunder.

- Appadgangskoden er godkendt af Azure AD og dermed tilsidesætte sammenslutning. Sammenslutning bruges kun aktivt, når du konfigurerer Appadgangskode.
- For federated(SSO) brugere gå vi aldrig til den identitetsudbyder (IdP) i modsætning til passive strømmen. Adgangskoderne er gemt i dit organisations-id. Hvis brugeren forlader virksomheden, har disse oplysninger til dataflow til organisations-id med DirSync i realtid. Konto Deaktiver/sletningen kan tage op til tre timer at synkronisere, forsinke Deaktiver/sletning af Appadgangskode i Azure AD.
- Lokalt klient adgangskontrol indstillinger ikke er accepteret af Appadgangskode
- Ingen lokale godkendelse logføring / overvågning egenskab er tilgængelig for Appadgangskode
- Flere slutbrugerlicensaftale uddannelse er påkrævet for Microsoft Lync 2013-klient. Se, hvordan du ændrer adgangskoden i din mail til appadgangskoden for de nødvendige trin.
- Visse avancerede arkitektonisk designs kan kræve ved hjælp af en kombination af organisatoriske brugernavn og adgangskode og appadgangskoder, når du bruger Multi-Factor authentication med klienter, afhængigt af hvor de godkende. For kunder, der godkender mod en lokal infrastruktur, vil du bruge en organisatoriske brugernavn og din adgangskode. For kunder, der godkender mod Azure AD, vil du bruge appadgangskoden.

Lad os antage, at du har en arkitektur, som består af følgende:

- Du en sammenslutning din lokalt forekomst af Active Directory med Azure AD
- Du bruger Exchange online
- Du bruger Lync, der er specielt lokalt
- Du bruger Azure Multi-Factor Authentication


![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

 I disse tilfælde skal du gøre følgende:

- Når du logger på Lync, kan du bruge din organisationer brugernavn og din adgangskode.
- Når du forsøger at få adgang til adressekartoteket via en Outlook-klient, der opretter forbindelse til Exchange online, kan du bruge en appadgangskode.

### <a name="allowing-app-password-creation"></a>Tillade app oprettelse af adgangskode
Brugere kan ikke oprette appadgangskoder som standard.  Denne funktion skal være aktiveret.  Hvis du vil give brugere mulighed for at oprette appadgangskoder, skal du benytte følgende fremgangsmåde.

#### <a name="to-enable-users-to-create-app-passwords"></a>Aktivere brugere til at oprette appadgangskoder



1. Log på portalen Azure klassisk.
2. Klik på Active Directory i venstre side.
3. Directory under Klik på mappen for den bruger, du vil aktivere.
4. Klik på brugere er placeret øverst.
5. Klik på Administrer Multi-Factor Auth. nederst på siden  
6. Øverst på siden Multi-Factor godkendelse, skal du klikke på Tjenesteindstillinger.
7. Sørg for, at alternativknappen ud for Tillad brugere at oprette appadgangskoder at logge på ikke-browser-programmer er markeret.


![Oprette Appadgangskoder](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>Oprette appadgangskoder
Brugere kan oprette appadgangskoder under deres første registrering.  De får en indstilling i slutningen af registreringsprocessen, så de kan oprettes.

Desuden kan brugere også oprette appadgangskoder senere ved at ændre deres indstillinger i Azure-portalen på Office 365-portalen eller ved at

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Du kan oprette appadgangskoder i Office 365-portalen
--------------------------------------------------------------------------------


1. Log på Office 365-portalen
2. I øverste højre hjørne skal du vælge indstillinger widget
3. Vælg yderligere sikkerhed kontrol i venstre side
4. Vælg **opdatere mine telefonnumre, der bruges til kontosikkerhed** i højre side
5. På siden proofup øverst skal du vælge appadgangskoder
6. Klik på **Opret**
7. Angiv et navn til appadgangskoden og klikke på **Næste**
8. Kopiere appadgangskoden til Udklipsholder og sætte det ind i din app.

<center>![Skyen](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Du kan oprette appadgangskoder i portalen Azure
--------------------------------------------------------------------------------
1. Log på portalen Azure klassisk.
3. Højreklik på dit brugernavn øverst, og vælg ekstra sikkerhed bekræftelse.
5. På siden proofup øverst skal du vælge appadgangskoder
6. Klik på **Opret**
7. Angiv et navn til appadgangskoden og klikke på **Næste**
8. Kopiere appadgangskoden til Udklipsholder og sætte det ind i din app.


![Appadgangskoder](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>Oprette appadgangskoder, hvis du ikke har et abonnement til Office 365 eller Azure
--------------------------------------------------------------------------------
1. Log på [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Øverst skal du vælge profil.
3. Klik på dit brugernavn, og vælg ekstra sikkerhed bekræftelse.
5. På siden proofup øverst skal du vælge appadgangskoder
6. Klik på **Opret**
7. Angiv et navn til appadgangskoden og klikke på **Næste**
8. Kopiere appadgangskoden til Udklipsholder og sætte det ind i din app.

![Appadgangskoder](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>Husk Multi-Factor Authentication til enheder brugere Hav tillid til

Huske Multi-Factor Authentication af enheder og browsere, brugere sikkerhed og rettighedsadministration er en gratis funktion for alle MFA-brugere.  Det gør det muligt at give brugerne mulighed for at bypass MFA for et fastsat antal dage, når du har udført en vellykket logon ved hjælp af MFA. Dette kan forbedre brugervenligheden for dine brugere.

Da brugerne, der har tilladelse til at huske MFA for enheder, der er tillid til, kan denne funktion kan reducere kontosikkerhed. For at sikre kontosikkerhed, skal du gendanne Multi-Factor Authentication for deres enheder til en af følgende scenarier:

- Hvis deres virksomhedskonto er blevet kompromitteret
- Hvis en husket enhed går tabt eller stjæles

> [AZURE.NOTE] Denne funktion er implementeret som en browser cookie cache. Den virker ikke, hvis din browsercookies ikke er aktiveret.

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>Hvordan du kan aktivere/deaktivere Husk Multi-Factor authentication

1. Log på portalen Azure klassisk.
2. Klik på Active Directory i venstre side.
3. Klik på den mappe, du vil konfigurere huske Multi-Factor Authentication til enheder under Active Directory.
4. Klik på den mappe, du har valgt, Konfigurer.
5. Klik på Administrer Tjenesteindstillinger i afsnittet Multi-Factor authentication.
6. På siden Tjenesteindstillinger under administrere bruger Enhedsindstillinger, Vælg/fjerne markeringen af **Tillad brugere at huske Multi-Factor authentication på enheder, de har tillid til**.
![Husk enheder](./media/multi-factor-authentication-whats-next/remember.png)
8. Angiv antallet dage, som du vil tillade annullering. Standard er 14 dage.
9. Klik på Gem.
10. Klik på Luk.


## <a name="selectable-verification-methods"></a>Kan vælges bekræftelse metoder
I både skyen og lokale versioner, kan du vælge, hvilke bekræftelse metoder er tilgængelige for dine brugere. Tabellen nedenfor indeholder en kort oversigt over hver enkelt metode.

Når brugerne tilmelde deres konti til MFA, vælge de deres foretrukne metode til bekræftelse af de indstillinger, du har aktiveret. Vejledning til deres tilmeldingsprocessen dækkes i [Konfigurere min konto for totrinsbekræftelse](multi-factor-authentication-end-user-first-time.md)

Metode|Beskrivelse
:------------- | :------------- |
Ringe til telefon |  Placerer et automatiseret taleopkald til godkendelse telefonen. Brugeren besvarer opkaldet og trykker på # i telefon tastatur til at godkende. Dette telefonnummer er ikke synkroniseret til lokale Active Directory.
SMS-besked til telefon | Sender en SMS-besked, der indeholder en bekræftelseskode til brugeren. Brugeren bliver bedt om at et svar til tekstmeddelelsen med verifikationskoden eller indtast verifikationskoden i grænsefladen logon.
Besked om via-mobilappen | I denne tilstand appen Microsoft Authenticator forhindrer uautoriseret adgang til konti og holder op med at falske transaktioner. Dette gøres ved hjælp af en opslagsnål meddelelse til din telefon eller registrerede enhed. Blot se meddelelsen, og hvis det er gyldige du trykker på Bekræft. Ellers kan du vælge Afvis eller vælge at nægte og rapportere meddelelsen til falsk. Se, hvordan du bruger funktionen Afvis og rapport svindel til multi-Factor Authentication oplysninger om rapportering falske meddelelser.</br></br>Microsoft Authenticator app er tilgængelig til [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)og [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).|
Verifikationskoden fra-mobilappen | I denne tilstand, kan appen Microsoft Authenticator bruges som en software-token til at oprette en ed verifikationskoden. Denne verifikationskoden kan derefter angives sammen med det brugernavn og adgangskode, der giver den anden form for godkendelse.</li><br><p> Microsoft Authenticator app er tilgængelig til [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)og [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

### <a name="how-to-enabledisable-authentication-methods"></a>Hvordan du kan aktivere/deaktivere godkendelsesmetoder

1. Log på portalen Azure klassisk.
2. Klik på Active Directory i venstre side.
3. Klik på den mappe, du vil aktivere eller deaktivere godkendelsesmetoder under Active Directory.
4. Klik på den mappe, du har valgt, Konfigurer.
5. Klik på Administrer Tjenesteindstillinger i afsnittet Multi-Factor authentication.
6. På siden Indstillinger under bekræftelsesindstillinger for Vælg/Fjern markeringen af de indstillinger, du vil bruge.</br></br>
![Indstillinger for godkendelse](./media/multi-factor-authentication-whats-next/authmethods.png)
9. Klik på Gem.
10. Klik på Luk.

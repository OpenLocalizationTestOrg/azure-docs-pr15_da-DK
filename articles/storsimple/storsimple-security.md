<properties 
   pageTitle="StorSimple sikkerhed | Microsoft Azure" 
   description="I denne artikel beskrives de funktioner til sikkerhed og beskyttelse af personlige oplysninger, der beskytter dine StorSimple service, enhed og data lokalt og i skyen." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="05/03/2016"
   ms.author="v-sharos"/>

# <a name="storsimple-security-and-data-protection"></a>StorSimple sikkerhed og databeskyttelse

## <a name="overview"></a>Oversigt

Sikkerhed er et større problem for alle, der anvender en ny teknologi, især hvis teknologien, der blev brugt med fortrolige eller beskyttede data. Når du evaluerer forskellige teknologier, skal du overveje øget risiko og omkostninger for databeskyttelse. Microsoft Azure StorSimple indeholder både en sikkerhed og beskyttelse af personlige oplysninger løsning for databeskyttelse, hvilket sikrer: 

- **Fortrolighed** – kun autoriserede objekter kan se dine data. 
- **Integritet** – kun godkendt objekter kan ændre eller slette dine data.

Microsoft Azure StorSimple løsningen består af fire primære komponenter, der interagerer med hinanden:

- **StorSimple Manager service vært Microsoft Azure** -tjenesten management, du bruger til at konfigurere og klargøre StorSimple enhed.
- **StorSimple enhed** – en fysisk enhed, der er installeret i dit datacenter. Alle værter og klienter, der opretter data kan du oprette forbindelse til StorSimple enheden, og enheden administrerer dataene og flytter den til Azure skyen efter behov.
- **Klienter/værter har forbindelse til enheden** -klienter i infrastrukturen, oprette forbindelse til StorSimple enheden og generere data, der skal være beskyttet.
- **Cloud storage** – placeringen i Azure skyen hvor data er gemt.

I følgende afsnit beskrives de StorSimple sikkerhedsfunktioner, der beskytter hver af disse komponenter og de data, der er gemt på dem. Den indeholder også en liste over spørgsmål, som du muligvis om Microsoft Azure StorSimple sikkerhed og de tilsvarende svar.

## <a name="storsimple-manager-service-protection"></a>StorSimple Manager service beskyttelse

Tjenesten StorSimple Manager er en management-tjenesten Microsoft Azure som vært og bruges til at administrere alle StorSimple enheder, som din organisation har udtaget. Du kan få adgang til tjenesten StorSimple Manager ved hjælp af dine egne legitimationsoplysninger til at logge på Azure klassisk portalen via en webbrowser. 

Adgang til tjenesten StorSimple Manager kræver, at din organisation har et Azure abonnement, som indeholder StorSimple. Dit abonnement styrer de funktioner, som du kan få adgang til på portalen Azure klassisk. Hvis din organisation har ikke et Azure-abonnement, og du vil vide mere om dem, kan du se [tilmelde sig Azure som en organisation](../active-directory/sign-up-organization.md). 

Da tjenesten StorSimple Manager er hostet i Azure, er det beskyttet af Azure sikkerhedsfunktionerne. Gå til [Microsoft Azure Sikkerhedscenter](https://azure.microsoft.com/support/trust-center/security/)kan finde flere oplysninger om de sikkerhedsfunktioner, der leveres af Microsoft Azure.

## <a name="storsimple-device-protection"></a>StorSimple enhed beskyttelse

StorSimple enheden er en lokal hybrid lagerplads enhed, der indeholder dækkende tilstand SSD'er () og harddiske (harddiske) sammen med overflødige enheder og automatisk failoveregenskaber. Enheder Administrer lagerplads overgang switchmiljø, placere aktuelt åbnede (eller genvejstaster) data på lokale lager (i afsnittet StorSimple enhed eller en lokal servers), mens du flytter mindre ofte anvendte data til skyen.

Kun tilladelse StorSimple enheder har tilladelse til at deltage i tjenesten StorSimple Manager, du oprettede i abonnementet Azure. Hvis du vil godkende en enhed, skal du registrere den med tjenesten StorSimple Manager ved at indsende tasten service registrering. Tasten service registrering er en 128-bit tilfældige nøgle genereres i portalen Azure klassisk. 

![Tjenesten registreringsnøgle](./media/storsimple-security/ServiceRegistrationKey.png)

For at lære hvordan få en tjeneste registrering tast, skal du gå til [trin 2: hente service registrering nøglen](storsimple-deployment-walkthrough.md#step-2-get-the-service-registration-key).

Tasten service registrering er en lang nøgle, der indeholder 100 + tegn. Du kan kopiere nøglen og gemme den i en tekstfil et sikkert sted, så du kan bruge til at tillade flere enheder efter behov. Hvis tasten service registrering går tabt, når du har registreret dit første enhed, kan du oprette en ny nøgle fra tjenesten StorSimple Manager. Dette påvirker ikke handlingen eksisterende enheder. 

Når en enhed er registreret, bruger symboler til at kommunikere med Microsoft Azure. Tasten service registrering bruges ikke efter enhed registrering.

> [AZURE.NOTE] Vi anbefaler, at genoprette nøglen service registrering efter hver anvendelse.

## <a name="protect-your-storsimple-solution-via-passwords"></a>Beskytte din StorSimple løsning via adgangskoder

Adgangskoder er et vigtigt aspekt af sikkerhed på en computer, og der anvendes i stort omfang i StorSimple løsningen for at sikre, at dine data er tilgængelige for autoriserede brugere. StorSimple kan du konfigurere følgende adgangskoder:

- StorSimple enhed administratoradgangskode
- Udfordring CHAP Handshake Authentication Protocol () afsender og mål adgangskoder
- StorSimple øjebliksbillede Manager-adgangskode

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell til StorSimple og StorSimple enhed administratoradgangskode

Windows PowerShell til StorSimple er en kommandolinjen, som du kan bruge til at administrere StorSimple enhed. Windows PowerShell til StorSimple har funktioner, der gør det muligt at registrere din enhed, konfigurerer netværksgrænsefladen på din enhed, installere visse typer opdateringer, udføre fejlfinding på enheden ved at få adgang til support-session, og ændre enhedens tilstand. Du kan få adgang til Windows PowerShell til StorSimple ved at oprette forbindelse til seriel konsollen på enheden eller ved at bruge Windows PowerShell remoting.

PowerShell remoting kan udføres over HTTPS eller HTTP. Hvis fjernadministration over HTTPS er aktiveret, skal du downloade certifikatet fjernadministration fra enheden og installere den på remote klienten. Gå til [oprette forbindelse fra en fjernplacering til enheden StorSimple](storsimple-remote-connect.md)kan finde flere oplysninger om PowerShell remoting.

Når du bruger Windows PowerShell til StorSimple til at oprette forbindelse til enheden, skal du angive administratoradgangskode enhed til at logge på enheden.

![Enhed administratoradgangskode](./media/storsimple-security/DeviceAdminPW.png)

Huske følgende anbefalede fremgangsmåder:

- Fjernadministration er som standard slået fra. Du kan bruge tjenesten StorSimple Manager til at aktivere den. Som en sikkerhedsmæssige årsager fjernadgang skal aktiveres kun i den tidsperiode, som det er faktisk er nødvendigt.
- Hvis du ændrer adgangskoden, skal du sørge for at informere alle fjernadgang til brugere, så de ikke opleve en uventede connectivity tab.
- Tjenesten StorSimple Manager kan ikke hentes eksisterende adgangskoder: det kan kun nulstille dem. Vi anbefaler, at du gemmer alle adgangskoder i et sikkert sted, så du ikke behøver at nulstille en adgangskode, hvis det er glemt. Hvis du har brug for at nulstille en adgangskode, skal du sørge for at informere alle brugere, før du nulstille den. 

Du kan få adgang til Windows PowerShell-grænseflade ved hjælp af en seriel forbindelsen til enheden. Du kan også få adgang til den fra en fjernplacering ved hjælp af HTTP eller HTTPS, som understøtter ekstra sikkerhed. HTTPS giver et højere niveau af sikkerhed end et serienummer eller HTTP-forbindelse. Men hvis du vil bruge HTTPS, skal du først installere et certifikat på klientcomputeren, der skal have adgang til enheden. Du kan hente fjernadgang certifikat fra konfigurationssiden af enhed i tjenesten StorSimple Manager. Hvis certifikatet til fjernadgang går tabt, skal du hente et nyt certifikat og sprede til alle klienter, der er autoriseret til at bruge fjernadministration.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Udfordring CHAP Handshake Authentication Protocol () afsender og mål adgangskoder

CHAP er en godkendelse farveskema, der bruges af StorSimple-enhed til at validere identiteten remote-klienter. Bekræftelsen er baseret på en delt adgangskode. CHAP kan være envejs (envejs) eller gensidig (tovejs). Med envejs CHAP godkender mål (StorSimple-enhed) en afsender (host). Fælles eller omvendt CHAP kræver, at destinationen godkende afsenderen og derefter godkende afsenderen destinationen. Din StorSimple kan være konfigureret til at bruge begge metoder.

Vær opmærksom på følgende, når du konfigurerer CHAP:

- Brugernavnet CHAP skal indeholde færre end 233 tegn.
- CHAP adgangskoden skal være mellem 12 og 16 tegn. Forsøger at bruge en længere brugernavn eller adgangskode medfører en fejl ved godkendelse på Windows-vært.
- Du kan ikke bruge den samme adgangskode til både CHAP afsenderen og CHAP destinationen.
- Når du har angivet adgangskoden, den kan ændres, men den kan ikke hentes. Hvis adgangskoden er ændret, skal du sørge for at informere alle fjernadgang til brugere, så de kan oprette forbindelse til StorSimple enheden.

Gå til [Konfigurere CHAP for enheden StorSimple](storsimple-configure-chap.md)kan finde flere oplysninger om CHAP, og hvordan du konfigurere det til din StorSimple løsning.

### <a name="storsimple-snapshot-manager-password"></a>StorSimple øjebliksbillede Manager-adgangskode

StorSimple øjebliksbillede Manager er en snap-in til Microsoft Management Console (MMC), der bruger lydstyrken grupper og tjenesten Windows lydstyrken øjebliksbillede til at generere program-ensartet sikkerhedskopier. Desuden kan du bruge StorSimple øjebliksbillede Manager til at oprette ekstra tidsplaner og Klon eller gendanne enheder.

Når du konfigurerer en enhed for at bruge StorSimple øjebliksbillede Manager, skal du angive adgangskoden til StorSimple øjebliksbillede Manager. Denne adgangskode angives først i Windows PowerShell til StorSimple under registrering. Adgangskoden kan også angive og ændres fra tjenesten StorSimple Manager. Denne adgangskode godkender enhed med StorSimple øjebliksbillede Manager.

![StorSimple øjebliksbillede Manager-adgangskode](./media/storsimple-security/SnapshotMgrPassword.png)

Den StorSimple øjebliksbillede Manager-adgangskode skal være 14 til 15 tegn og skal indeholde 3 eller flere af en kombination af store bogstaver, små bogstaver, numeriske og særlige tegn. Når du har angivet den StorSimple øjebliksbillede Manager-adgangskode, den kan ændres, men den kan ikke hentes. Hvis du ændrer adgangskoden, skal du sørge for at informere alle eksterne brugere.

Gå til for at få mere at vide om StorSimple øjebliksbillede Manager [Hvad er StorSimple øjebliksbillede Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Bedste fremgangsmåder for adgangskoder

Vi anbefaler, at du bruger følgende retningslinjer for at sikre, at StorSimple adgangskoder er stærke og ordentligt beskyttet:

- Ændre adgangskoderne alle tre måneder. Ændre adgangskoderne gennemtvinges en gang om året.
- Brug stærke adgangskoder. Gå til [oprette stærkere adgangskoder og beskytte dem](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/)kan finde flere oplysninger.
- Brug altid forskellige adgangskoder til forskellige til netværksadgang; hver af de adgangskoder, du angiver skal være entydig.
- Adgangskoder kan ikke dele med alle, der ikke har tilladelse til at få adgang til StorSimple enheden.
- Ikke tale om en adgangskode foran andre eller tip på formatet af en adgangskode.
- Hvis du har mistanke om, at et firma eller en adgangskode er blevet afsløret, kan du rapportere hændelsen til din oplysninger sikkerhed afdeling.
- Behandle alle adgangskoder som følsomme, fortrolige oplysninger. 

## <a name="storsimple-data-protection"></a>StorSimple databeskyttelse

I dette afsnit beskrives de StorSimple sikkerhedsfunktioner, der beskytter data undervejs og lagrede data.

Som beskrevet i andre sektioner, anvendes adgangskoder til at godkende og godkende brugere, før de kan få adgang til din StorSimple løsning. En anden sikkerhed overvejelser beskytte data fra uautoriserede brugere, mens den overføres mellem lagerplads systemer, og når den gemmes. I følgende afsnit beskrives funktionerne til beskyttelse, der følger med StorSimple.

> [AZURE.NOTE] Deduplication giver yderligere beskyttelse af data, der er gemt på enheden StorSimple og i Microsoft Azure-lager. Når dataene er deduplicated, gemmes dataobjekter adskilt fra de metadata, der bruges til at tilknytte og få adgang til dem: der er ingen tilgængelig lagerpladsen kontekst rekonstruere de data, der er baseret på lydstyrke struktur, filsystemet eller filnavn.

## <a name="protect-data-flowing-through-the-service"></a>Beskytte data, der flyder gennem tjenesten

Det primære formål med tjenesten StorSimple Manager er at administrere og konfigurere StorSimple enheden. Tjenesten StorSimple Manager kører i Microsoft Azure. Du bruger Azure klassisk portalen til at indtaste data til konfiguration af enhed, og derefter Microsoft Azure bruger tjenesten StorSimple Manager til at sende dataene til enheden. StorSimple bruger et system af asymmetrisk nøgle par for at sikre, at en sammensat til Azure-tjenesten ikke resulterer i en sammensat af lagrede data. 

![Kryptering af data i flight](./media/storsimple-security/DataEncryption.png)

Asymmetrisk vigtige systemet hjælper med at beskytte de data, der bevæger sig gennem tjenesten på følgende måde:

1. Et certifikat til kryptering af data, der bruger en asymmetrisk offentlige og private nøgle par genereres på enheden og bruges til at beskytte data. Tasterne genereres, når den første enhed er registreret. 
2. Tasterne data kryptering certifikat eksporteres til en Personal Information Exchange (.pfx)-fil, der er beskyttet af tjenesten data krypteringsnøglen, som er en stærk 128-bit-nøgle, der oprettes tilfældigt af den første enhed under registrering.
3. Offentlig nøgle for certifikatet, der sikkert stilles til rådighed for tjenesten StorSimple Manager, og den private nøgle forbliver med enheden.
4. Data i tjenesten er krypteret med offentlig nøgle og dekrypteres ved hjælp af den private nøgle, der er gemt på enheden, at sikre, at tjenesten Azure ikke dekryptere de data, der flyder på enheden.

Tjenesten data krypteringsnøglen genereres på kun den første enhed, der er registreret hos tjenesten. Alle efterfølgende enheder, der er registreret med tjenesten skal bruge den samme service data krypteringsnøgle. 

> [AZURE.IMPORTANT] 
> 
> Det er meget vigtigt at oprette en kopi af krypteringsnøglen service data og gemme den i et sikkert sted. En kopi af krypteringsnøglen service data skal gemmes i en sådan måde, at den kan åbnes af en autoriseret person og nemt kan kommunikeres til administratoren enhed.
>
> Hvis krypteringsnøglen service data går tabt, kan en person fra Microsoft support kan hjælpe dig med at hente det, hvis du har mindst én enhed i onlinetilstand. Vi anbefaler, at du ændrer krypteringsnøglen service data, når det er hentet. Finde en vejledning, gå til [ændre krypteringsnøglen service data](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

Du kan ændre krypteringsnøglen service data og det tilsvarende data krypteringscertifikat ved at vælge indstillingen **ændre service data krypteringsnøgle** på dashboardet til tjenesten. For at sikre, at datasikkerhed ikke afsløret, skal du bruge en fysisk StorSimple-enhed til at ændre krypteringsnøglen service data. Ændre tasterne kryptering kræver, at alle enheder opdateres med den nye nøgle. Vi anbefaler derfor, at du ændrer nøglen, når alle enheder er online. Hvis enheder er offline, kan ændres deres taster på et andet tidspunkt. Enheder med forældede taster vil stadig kunne køre sikkerhedskopier, men de vil ikke kunne gendanne data, indtil nøglen opdateres. Gå til [Brug dashboardet StorSimple Manager service](storsimple-service-dashboard.md)kan finde flere oplysninger.

Tjenesten data krypteringsnøglen og krypteringscertifikat data udløber ikke. Dog anbefaler vi, at du ændrer service data krypteringsnøglen en gang om året for at forhindre sammensat nøgle.

## <a name="protect-data-at-rest"></a>Beskytte data på resten

StorSimple enheden administrerer data ved at gemme den i niveauer, lokalt og i skyen, afhængigt af hyppighed af brug. Alle host-computere, der er forbundet til enheden sender data til enheden, som flytter data i skyen, efter behov. Der overføres data fra enheden til skyen sikkert via internettet. Hver enhed har en iSCSI-destinationen, overflader alle delte enheder på enheden. Alle data er krypteret, før det sendes til skyen lagerplads. 

![Cloud storage krypteringsnøglen](./media/storsimple-security/CloudStorageEncryption.png)

For at sikre sikkerheden og integriteten af data, der flyttes til skyen, StorSimple giver dig mulighed at definere cloud storage kryptering taster på følgende måde:

- Du angiver krypteringsnøglen skyen lagerplads, når du opretter en objektbeholder lydstyrken. Tasten kan ikke ændres eller tilføjes senere. 
- Alle enheder i en lydstyrken objektbeholder dele den samme krypteringsnøgle. Hvis du vil have en anden slags kryptering for et bestemt drev, anbefaler vi, at du opretter en ny objektbeholder lydstyrken Hvis du vil placere lydstyrken.
- Når du angiver krypteringsnøglen cloud storage i tjenesten StorSimple Manager, krypteres tasten ved hjælp af den offentlige del af krypteringsnøglen service data og derefter sendes til enheden.
- Cloud storage krypteringsnøglen gemmes ikke et vilkårligt sted i tjenesten og kendes kun til enheden.
- Angive en cloud storage krypteringsnøgle er valgfrit. Du kan sende data, der er krypteret på værten til enheden.

### <a name="additional-security-best-practices"></a>Bedste fremgangsmåder for ekstra sikkerhed

- Opdele trafik: isolere din iSCSI SAN fra bruger trafik på en virksomhedens LAN ved at implementere et helt adskilt netværk og bruge VLAN'er hvor fysisk isolationsniveauet ikke er en indstilling. En dedikeret netværk for iSCSI-storage garanterer sikkerhed og ydeevnen af dine vigtige data. Blanding af lager og bruger trafik via en virksomhedens LAN kan anbefales ikke og øge ventetid og medføre netværksfejl.

- Brug host side netværk sikkerhed, netværksgrænseflader, der understøtter TCP/IP-Offload Engine (TOE). TOE reducerer CPU-belastning ved at behandle TCP på netværkskortet.

## <a name="protect-data-via-storage-accounts"></a>Beskytte data via lagerplads konti

Hvert Microsoft Azure-abonnement kan oprette en eller flere lagerplads konti. (En lagerplads konto giver et entydigt navneområde til at arbejde med data, der er gemt i skyen, Azure). Adgang til en lagerplads konto styres af tasterne abonnement og access, der er knyttet til den pågældende lagerplads konto. 

Når du opretter en konto med lagerplads, genererer Microsoft Azure to 512-bit lagerplads adgangstaster, hvoraf den ene bruges til godkendelse, når enheden StorSimple får adgang til kontoen lagerplads. Bemærk, at kun én af disse taster er i brug. De andre tast holdes i reserver, så du kan rotere de pågældende taster med jævne mellemrum. For at rotere taster, skal du aktivere den sekundære nøgle, og derefter slette den primære nøgle. Du kan derefter oprette en ny nøgle til brug under næste rotationen. (Af sikkerhedsmæssige årsager kræver mange datacentre vigtige rotation.) 

Vi anbefaler, at du følger disse bedste fremgangsmåder til vigtige rotation:

- Du skal rotere lagerplads konto taster jævnligt for at sikre, at kontoen lagerplads ikke er tilgængelig for uautoriserede brugere.
- Administratoren Azure skal med jævne mellemrum, ændre eller genoprette nøglen primær eller sekundær ved hjælp af afsnittet lager i portalen Azure klassisk direkte adgang til kontoen lagerplads.


## <a name="protect-data-via-encryption"></a>Beskytte data ved hjælp af kryptering

StorSimple bruger de følgende krypteringsalgoritmer til at beskytte data, der er gemt i eller rejse mellem komponenterne i din StorSimple løsning.

| Algoritme | Vigtige længde | Protokoller/programmer/kommentarer |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | RSA PKCS 1 v 1.5 bruges af portalen Azure klassisk til at kryptere konfigurationsdata, der sendes til enheden: eksempelvis lager legitimationsoplysninger, StorSimple enhedskonfiguration, og lagerplads kryptering taster i skyen. |
| AES       | 256        | AES med CBC bruges til at kryptere den offentlige del af krypteringsnøglen service data, før de sendes til portalen Azure klassisk fra StorSimple enheden. Det er også bruges af StorSimple-enhed til at kryptere data, inden data, der sendes til skyen lagerplads konto. |


## <a name="storsimple-virtual-device-security"></a>StorSimple virtuel enhedssikkerhed

[AZURE.INCLUDE [storsimple virtual device security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Ofte stillede spørgsmål (FAQ)

Følgende er nogle spørgsmål og svar om sikkerhed og Microsoft Azure StorSimple.

**Q:** Min tjeneste er beskadiget. Hvad skal min næste trin?

**A:** Med det samme skal du ændre krypteringsnøglen service data og lagerplads konto taster til kontoen lagerplads, der bruges til overgang switchmiljø data. Finde en vejledning, gå til: 

- [Ændre krypteringsnøglen service data](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Vigtige rotationen af lagerplads konti](storsimple-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:** Jeg har en ny StorSimple enhed, der beder om tasten service registrering. Hvordan får jeg fat i den?

**A:** Denne tast blev oprettet, da du oprettede først tjenesten StorSimple Manager. Når du bruger tjenesten StorSimple Manager til at oprette forbindelse til enheden, kan du bruge siden Startvejledning til at få vist eller genoprette nøglen service registrering. Oprettelse af nye service registrering nøgler påvirker ikke de eksisterende registrerede enheder. Finde en vejledning, gå til:

- [Få vist eller genoprette nøglen service registrering](storsimple-service-dashboard.md#view-or-regenerate-the-service-registration-key)

**Q:** Jeg har mistet min tjeneste data krypteringsnøgle. Hvad gør jeg?

**A:** Kontakt Microsoft Support. De kan logge på en session med support på din enhed og Hjælp du hente den pågældende nøgle (hvis mindst én enheden er online). Med det samme, når du har anskaffet krypteringsnøglen service data, skal du ændre det for at sikre, at den nye nøgle kendes kun for dig. Finde en vejledning, gå til:

- [Ændre krypteringsnøglen service data](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**Q:**  Jeg godkendt en enhed til en tjeneste data kryptering vigtige ændring, men startede ikke tasten Skift processen. Hvad skal jeg gøre?

**A:** Hvis timeout-perioden er udløbet, skal du godkende igen enhed til tjenesten data kryptering vigtige ændringen og starte processen igen.

**Q:**  Jeg har ændret krypteringsnøglen service data, men jeg er ikke muligt at opdatere de andre enheder inden for 4 timer. Skal jeg anvende for at starte igen?

**A:** 4-timers periode er kun til start af ændringen. Når du starter opdateringsprocessen på autoriserede StorSimple enheden, gælder tilladelsen, indtil alle enheder er opdateret.

**Q:** Vores StorSimple administrator har forladt virksomheden. Hvad skal jeg gøre?

**A:** Ændre og nulstille adgangskoder, der giver adgang til StorSimple enheden, og Skift krypteringsnøglen service data for at sikre, at de nye oplysninger ikke kendes til uautoriserede personer. Finde en vejledning, gå til:

- [Bruge tjenesten StorSimple Manager til at ændre dine storsimple adgangskoder](storsimple-change-passwords.md)
- [Ændre krypteringsnøglen service data](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Konfigurere CHAP til enheden StorSimple](storsimple-configure-chap.md)

**Q:** Jeg vil angive den StorSimple øjebliksbillede Manager-adgangskode til en udbyder, som opretter forbindelse til StorSimple enheden, men adgangskoden er ikke tilgængelig. Hvad kan jeg gøre?

**A:** Hvis du har glemt adgangskoden, skal du oprette en ny. Derefter skal du sørge for at fortælle alle eksisterende brugere, at adgangskoden er blevet ændret, og at de skal opdaterer deres klienter til at bruge den nye adgangskode. Finde en vejledning, gå til:

- [Ændre adgangskoden StorSimple øjebliksbillede Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)
- [Godkende en enhed](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:** Certifikat til fjernadgang til Windows PowerShell til StorSimple er blevet ændret på enheden. Hvordan opdaterer jeg mine fjernadgang kunder?

**A:** Du kan hente det nye certifikat fra tjenesten StorSimple Manager, og derefter angive det skal være installeret i certifikat store dine fjernadgang til kunder. Finde en vejledning, gå til:

- [Importer certifikat cmdlet](https://technet.microsoft.com/library/hh848630.aspx)

**Q:** Er mine data beskyttet Hvis StorSimple leder tjenesten er skadet?

**A:** Data fra webtjeneste konfiguration er altid krypteret med dit offentlig nøgle, når du får det vist i en webbrowser. Da tjenesten ikke har adgang til den private nøgle, kan tjenesten kan ikke se nogen data. Hvis tjenesten StorSimple Manager afsløret, er der ingen virkning, som der er ingen nøgler, der er gemt i tjenesten StorSimple Manager.

**Q:** Hvis nogen får adgang til data krypteringscertifikat, bliver dataene være kompromitteret?

**A:** Microsoft Azure gemmer kundens data krypteringsnøgle (.pfx-fil) i et krypteret format. Da .pfx-filen er krypteret og tjenesten StorSimple ikke har den tjeneste data krypteringsnøgle til at dekryptere .pfx-fil, viser blot få adgang til .pfx-fil ikke en hvilken som helst hemmeligheder.

**Q:** Hvad sker der, hvis en offentlige enhed beder om Microsoft til mine data?

**A:** Da alle dataene er krypteret på tjenesten, og der bevares på privat nøgle med enheden, skal den offentlige enhed bede kunden for dataene. 

## <a name="next-steps"></a>Næste trin

[Implementer enheden StorSimple](storsimple-deployment-walkthrough.md).
 

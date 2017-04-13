<properties
    pageTitle="Ofte stillede spørgsmål: Administration af Azure AD-adgangskoder | Microsoft Azure"
    description="Ofte stillede spørgsmål (FAQ) om administration af adgangskoder i Azure AD, nulstilling herunder af adgangskode, registrering, rapporter og tilbageførsel til lokale Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="password-management-frequently-asked-questions"></a>Ofte stillede spørgsmål om administration af adgangskoder

> [AZURE.IMPORTANT] **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).

Følgende er nogle ofte stillede spørgsmål til alle de ting, der er relateret til administration af adgangskoder.

Hvis du kan finde dig selv med et spørgsmål, som du ikke kender svaret på eller søger efter hjælp til et bestemt problem, du står over, kan du læse videre nedenfor til at se, hvis vi har dækket den allerede.  Hvis vi ikke allerede har gjort det, ikke bekymre dig! Velkommen til at stille et spørgsmål, du har, ikke er omfattet her i [Azure AD-fora](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) og vi vil vende tilbage til dig, så snart vi kan.

Disse ofte stillede spørgsmål er opdelt i de følgende afsnit:

- [**Spørgsmål om registrering til nulstilling af adgangskode**](#password-reset-registration)
- [**Spørgsmål om nulstilling af adgangskode**](#password-reset)
- [**Spørgsmål om adgangskode Management rapporter**](#password-management-reports)
- [**Spørgsmål om adgangskode tilbageførsel**](#password-writeback)

## <a name="password-reset-registration"></a>Registrering til nulstilling af adgangskode
 - **Sp: kan min brugerne registrere deres egne data til nulstilling af adgangskode?**

 > **A:** Ja, så længe nulstilling af adgangskode er aktiveret, og de er givet i licens, kan de gå til portalen adgangskode nulstille registrering på http://aka.ms/ssprsetup til at registrere deres godkendelsesoplysninger skal bruges sammen med nulstilling af adgangskode. Brugere kan også registrere ved at gå til panelet adgang på http://myapps.microsoft.com, klikke på fanen profil og klikke på tilmelding til nulstilling af adgangskode indstilling. Lær mere om, hvordan du få dine brugere, der er konfigureret til ved at læse, hvordan du kan få brugere, der er konfigureret til nulstilling af adgangskode til nulstilling af adgangskode.

 - **Sp: kan jeg angive data til nulstilling af adgangskode på vegne af mine brugere?**

 > **A:** Ja, kan du gøre det med DirSync eller PowerShell eller via [Azure Management Portal](https://manage.windowsazure.com) eller Office Admin-portalen. Lær mere om denne funktion på blogindlægget forbedret beskyttelse af personlige oplysninger for Azure AD MFA og adgangskode nulstille telefonnumre og ved at læse Lær, hvordan data bruges af adgangskode er nulstillet.

 - **Sp: kan jeg synkronisere data til spørgsmål om sikkerhed fra lokalt?**

 > **A:** Nej, det er ikke muligt i dag, men vi overvejer den.

 - **Sp: kan registrere Mine brugere data i en sådan måde, andre brugere ikke kan se disse data?**

 > **A:** Ja, når brugerne registrere data ved hjælp af portalen registrering af adgangskode nulstille den bliver gemt i private godkendelse felter, der kun er synlige af globale administratorer og brugeren sig selv. Lær mere om denne funktion på blogindlægget forbedret beskyttelse af personlige oplysninger for Azure AD MFA og adgangskode nulstille telefonnumre og ved at læse Lær, hvordan data bruges af adgangskode er nulstillet.

 - **Sp: min brugere skal være registreret, før de kan bruge nulstilling af adgangskode?**

 > **A:** Nej, hvis du definerer nok godkendelsesoplysninger på deres vegne, brugere vil ikke have til at registrere. Nulstilling af adgangskode fungerer fint, som du har korrekt formateret data gemt i de relevante felter i kataloget. Få mere at vide mere om ved at læse Lær, hvordan data bruges af nulstilling af adgangskode.

 - **Sp: kan jeg synkronisere eller angive godkendelse telefon, godkendelse mail eller alternative godkendelse telefon felter på vegne af mine brugere?**

 > **A:** Ikke i øjeblikket, men vi overvejer at aktivere denne funktion.

 - **Sp: Hvordan ved portalen registrering, hvilke indstillinger for at få vist mine brugere?**

 > **A:** Portalen adgangskode Nulstil registrering viser kun indstillingerne, du har aktiveret for dine brugere under sektionen bruger adgangskodepolitik nulstille din directory Konfigurer under fanen. Det betyder, at hvis du ikke aktiverer, siger, spørgsmål om sikkerhed, derefter brugere ikke vil kunne registrere til indstillingen.

 - **Sp: Når en bruger betragtes som registreret?**

 > **A:** En bruger betragtes som registreret, når han eller hun har mindst N dele af godkendelse oplysninger, der er defineret, hvor N er antallet af godkendelse metoder obligatorisk, du har angivet i [Azure Management Portal](https://manage.windowsazure.com). Se tilpasse bruger nulstille adgangskodepolitik for at få mere for at vide.


## <a name="password-reset"></a>Nulstilling af adgangskode

 - **Sp: hvor længe skal jeg vente til at modtage en mail, SMS eller telefonopkald fra nulstilling af adgangskode?**

 > **A:** Mail, SMS-beskeder og telefonopkald skal ender i under 1 minut med normal kassen der 5-20 sekunder. Hvis du ikke modtager meddelelsen i denne periode, kontrollere mappen uønsket, antallet / blive kontaktet af mail er det, som du forventer, og at Godkendelsesdataene i mappen, er formateret korrekt. Hvis du vil vide mere om formatering af telefonnumre og e-mail-adresser til brug med nulstilling af adgangskoder, skal du se få mere at vide, hvordan data bruges af nulstilling af adgangskode.

 - **Sp: hvilke sprog der understøttes af nulstilling af adgangskode?**

 > **A:** Brugergrænsefladen til nulstilling af adgangskode SMS-beskeder, og taleopkald er lokaliseret i de samme 40 sprog, der understøttes i Office 365. Du har nu: arabisk, bulgarsk, kinesisk (forenklet), traditionelt kinesisk, kroatisk, tjekkisk, dansk, nederlandsk, engelsk, estisk, finsk, fransk, tysk, græsk, hebraisk, Hindi, ungarsk, indonesisk, italiensk, japansk, Kasakhisk, koreansk, lettisk, litauisk, malaysisk (Malaysia), norsk (Bokmål), polsk, portugisisk (Brasilien), portugisisk (Portugal), rumænsk, russisk, serbisk (latinsk), slovakisk, slovensk, spansk, svensk, Thai, tyrkisk, ukrainsk, og vietnamesisk.

 - **Sp: hvilke dele af den adgangskode Nulstil oplevelse få brandet, når jeg angiver organisationsdiagram mærkning i min directory er konfigurere fane?**

 > **A:** Nulstil adgangskode portalen, vises dit organisatoriske logo og også mulighed for at konfigurere kontakten din administrator link til at pege på en brugerdefineret mail eller URL-adresse. En hvilken som helst mail, der bliver sendt af nulstilling af adgangskode indeholder organisationens logo, farver (i dette tilfælde rød), navn i brødteksten i mailen, og tilpasset fra navn. Få vist et eksempel med alle de mærkede elementer nedenfor. Læs nulstilling af adgangskode tilpasse udseende og funktionalitet for at få mere for at vide.

  ![][001]

 - **Sp: Hvordan kan jeg min gør brugerne bevidste om her skal du gå til at nulstille sin adgangskode?**

 > **A:** Du kan sende dine brugere til https://passwordreset.microsoftonline.com direkte, eller du kan bede dem om at klikke på den kan ikke få adgang til din konto for, findes på en hvilken som helst skole eller arbejde ID logonskærm. Du kan Velkommen til at publicere disse links (eller oprette URL-adressen omdirigeringer til dem) på en hvilken som helst sted, der er nem adgang til dine brugere.

 - **Sp: kan jeg bruge denne side fra en mobilenhed?**

 > **A:** Ja, fungerer på denne side på mobile enheder.

 - **Sp: du understøtter låse lokale active directory-konti, når brugere nulstille sin adgangskode?**

 > **A:** Ja, når en bruger nulstiller sin adgangskode og adgangskode tilbageførsel er blevet installeret med alle versioner af Azure AD-forbindelse eller versioner af Azure Active Directory-synkronisering 1.0.0485.0222 eller nyere, og derefter bliver automatisk ulåste pågældende brugers konto, når brugeren nulstiller sin adgangskode.

 - **Sp: Hvordan kan jeg ikke integrere direkte i min bruger computeren logonoplevelse til nulstilling af adgangskode?**

 > **A:** Dette er ikke muligt i dag. Men hvis du har brug for denne egenskab absolut og er Azure AD Premium-kunde, kan du installere Microsoft identitet Manager uden ekstra omkostninger og installere løsningen lokale adgangskode Nulstil fundet her til at løse dette krav.

 - **Sp: kan jeg ikke angive forskellige sikkerhedsspørgsmål til forskellige landestandarder?**

 > **A:** Nej, det er ikke muligt i dag, men vi overvejer den.

 - **Sp: hvor mange spørgsmål kan vi konfigurere indstillingen for godkendelse sikkerhedsspørgsmål?**

 > **A:** Du kan konfigurere op til 20 brugerdefineret sikkerhedsspørgsmål på [Azure Management Portal](https://manage.windowsazure.com).

 - **Sp: hvor lang tid muligvis sikkerhedsspørgsmål?**

 > **A:** Spørgsmål om sikkerhed muligvis mellem 3 og 200 tegn.

 - **Sp: hvor lang tid muligvis svar på spørgsmål om sikkerhed?**

 > **A:** Svar kan bestå af 3-40 tegn.

 - **Sp: er dublerede svar på spørgsmål om sikkerhed afvist?**

 > **A:** Ja, vi afvise dublerede svar på spørgsmål om sikkerhed.

 - **Sp: kan en bruger registrere mere end én af de samme sikkerhedsspørgsmål?**

 > **A:** Nej, når en bruger registrerer et bestemt spørgsmål, han eller hun kan ikke registrere for det pågældende spørgsmål endnu en gang.

 - **Sp: er det muligt at angive en mindste begrænsning på spørgsmål om sikkerhed for registrering og nulstille?**

 > **A:** Ja, én grænse kan angives til registrering og et andet til Nulstil. 3-5 spørgsmål om sikkerhed kan det være nødvendigt til registrering og 3-5 kan det være nødvendigt til Nulstil.

 - **Sp: Hvis en bruger har registreret mere end det maksimale antal spørgsmål, der er påkrævet for at nulstille, hvordan spørgsmål om sikkerhed markeres under Nulstil?**

 > **A:** Spørgsmål om sikkerhed N vælges tilfældigt af det samlede antal spørgsmål, der er en bruger har registreret for, hvor N er det mindste antal spørgsmål, der kræves til nulstilling af adgangskode. Eksempelvis hvis en bruger har 5 spørgsmål om sikkerhed registreret, men kun 3 er påkrævet for at nulstille, er 3 af disse 5 tilfældigt udvalgte og vises for brugeren på tidspunktet for Nulstil. Hvis brugeren får svar på spørgsmålene galt, opstår markeringsprocessen igen for at undgå spørgsmål hammering.

 - **Sp: du forhindre brugere i forsøg på mange gange i en kort tidsperiode til nulstilling af adgangskode?**

 > **A:** Ja, der er flere sikkerhedsfunktioner, der er indbygget i nulstilling af adgangskode. Brugere kan kun forsøge 5 forsøg til nulstilling af adgangskode inden for en time før bliver låst ude til 24 timer. Brugere kan kun forsøger at validere et telefonnummer 5 gange inden for en time før bliver låst ude til 24 timer. Brugere kan kun prøve en enkelt godkendelsesmetode 5 gange inden for en time før bliver låst ude til 24 timer.

 - **Sp: hvor længe være mails og SMS engangsadgangskode gældende i?**

 > **A:** Session levetiden for nulstilling af adgangskode er 105 minutter. Det betyder, at nulstille handling fra begyndelsen af adgangskoden, har brugeren 105 minutter til at nulstille sin adgangskode. Mail og SMS engangsadgangskode er ugyldige, når denne periode udløber.


## <a name="password-management-reports"></a>Administration af adgangskoder rapporter

 - **Sp: hvor lang tid tager det for data, der skal vises på adgangskode management rapporter?**

 > **A:** Data skal vises i adgangskode management rapporter efter 5-10 minutter. Det visse tilfælde, der kan gå op til en time skal vises.

 - **Sp: Hvordan kan jeg filtrere adgangskode management rapporter?**

 > **A:** Du kan filtrere adgangskode management rapporter ved at klikke på den lille forstørrelsesglasset til yderste højre for kolonnenavne øverst i rapporten (se skærmbillede). Hvis du vil gøre mere omfattende filtrering, kan du hente rapporten til excel og Opret en pivottabel.

  ![][002]

 - **Sp: Hvad er det maksimale antal hændelser, der er gemt i adgangskode management rapporter?**

 > **A:** Nulstil eller adgangskode Nulstil registrering hændelser gemmes op til 1.000 adgangskoden i adgangskode management rapporter.  Vi arbejder på for at udvide dette nummer for at medtage flere begivenheder.

 - **Sp: hvor langt tilbage går adgangskode management rapporter?**

 > **A:** Adgangskode management rapporter viser handlinger, der udføres i de seneste 30 dage. Vi undersøger i øjeblikket hvordan du gør dette en længere periode. Nu, hvis du vil arkivere disse data, kan du hente rapporterne med jævne mellemrum og gemme dem i et andet sted.

 - **Sp: er der et maksimalt antal rækker, der kan vises på adgangskode management rapporter?**

 > **A:** Ja, maksimalt 1.000 rækker vises på en af rapporterne, administration af adgangskoder, uanset om de der vises i Brugergrænsefladen eller der hentes. Vi undersøger Sådan øge denne grænse i øjeblikket.

 - **Sp: er der en API til nulstilling af adgangskode eller registrering rapporteringsdata?**

 > **A:** Ja, skal du se følgende dokumentation til at få mere at vide, hvordan du kan få adgang til den rapportering datastream nulstilling af adgangskode.  [Få mere at vide om at åbne adgangskode nulstille reporting begivenheder fra et program](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

## <a name="password-writeback"></a>Adgangskode tilbageførsel
 - **Sp: hvordan fungerer adgangskode der ikke er gemt i baggrunden?**

 > **A:** Se, [hvordan adgangskode tilbageførsel fungerer](active-directory-passwords-learn-more.md#how-password-writeback-works) for en detaljeret beskrivelse af, hvad sker der, når du aktiverer adgangskode tilbageførsel, samt hvordan data bevæger sig gennem systemet tilbage til dit lokale miljø. Se [adgangskode tilbageførsel sikkerhedsmodel](active-directory-passwords-learn-more.md#password-writeback-security-model) i hvordan adgangskode tilbageførsel fungerer for at få mere for at vide, hvordan vi sikre adgangskode tilbageførsel er en meget sikre tjeneste.

 - **Sp: hvor lang tid adgangskode tilbageførsel tager til at fungere sammen?  Er der en synkronisering forsinkelse som med synkronisering af adgangskoder hash?**

 > **A:** Adgangskode tilbageførsel er Chat. Det er en synkron rørledning, der fungerer grundlæggende anderledes end synkronisering af adgangskoder hash. Adgangskode tilbageførsel kan brugerne få realtid feedback om en vellykket deres nulstilling af adgangskode eller ændre operation. Den gennemsnitlige tid for en vellykket tilbageførsel af en adgangskode er under 500 ms.

 - **Sp: hvilke typer konti fungerer adgangskode tilbageførsel?**

 > **A:** Adgangskode tilbageførsel fungerer til Federated og synkronisering af adgangskoder Hash'd brugere.

 - **Sp: gennemtvinger adgangskode tilbageførsel adgangskodepolitikker mit domæne?**

 > **A:** Ja, håndhæves adgangskode tilbageførsel adgangskode alder, historik, kompleksitet, filtre og andre begrænsninger du kan anbringe sted på adgangskoder i din lokale domæne.

 - **Sp: ikke er sikker adgangskode tilbageførsel?  Hvordan kan jeg være sikker på, at jeg ikke få hacket?**

 > **A:** Ja, adgangskode tilbageførsel er meget sikker. Vil læse mere om de 4 sikkerhedslag implementeret af tjenesten adgangskode tilbageførsel, tjekke [adgangskode tilbageførsel sikkerhedsmodel](active-directory-passwords-learn-more.md#password-writeback-security-model) i hvordan adgangskode tilbageførsel fungerer.




## <a name="links-to-password-reset-documentation"></a>Links til adgangskode nulstille dokumentation
Nedenfor finder du links til alle siderne nulstilling af adgangskode til Azure AD dokumentation:

* **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).
* [**Sådan fungerer det**](active-directory-passwords-how-it-works.md) – få mere at vide om de seks forskellige komponenter i den tjeneste, og hvad hver betyder
* [**Kom godt i gang**](active-directory-passwords-getting-started.md) - Lær, hvordan du giver dig mulighed for brugerne at nulstille og ændre deres skyen eller en lokal adgangskoder
* [**Tilpas**](active-directory-passwords-customize.md) - Lær at tilpasse udseende og funktionalitet og funktionsmåde for tjenesten til din organisations behov
* [**Bedste fremgangsmåder**](active-directory-passwords-best-practices.md) - Lær at implementere hurtigt og effektivt administrere adgangskoder i din organisation
* [**Få indsigt**](active-directory-passwords-get-insights.md) - få mere at vide om vores integrerede rapporteringsfunktioner
* [**Fejlfinding i forbindelse med**](active-directory-passwords-troubleshoot.md) – Lær, hvordan du hurtigt foretage fejlfinding af problemer med tjenesten
* [**Få mere at vide**](active-directory-passwords-learn-more.md) - gå deep til de tekniske detaljer i Sådan fungerer service


[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"

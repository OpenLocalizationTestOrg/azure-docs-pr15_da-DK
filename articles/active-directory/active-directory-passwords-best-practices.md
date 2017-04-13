<properties
    pageTitle="Bedste fremgangsmåde: Azure AD-adgangskode Management | Microsoft Azure"
    description="Installation og brug bedste fremgangsmåder, eksempel slutbrugerlicensaftale dokumentation og uddannelse vejledninger til administration af adgangskoder i Azure Active Directory."
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

# <a name="deploying-password-management-and-training-users-to-use-it"></a>Implementere administration af adgangskoder og brugere at bruge det til uddannelse

> [AZURE.IMPORTANT] **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).

Efter aktivering adgangskode er nulstillet, er det næste trin, du skal gennemføre at få brugere ved hjælp af tjenesten i din organisation. For at gøre dette, skal du kontrollere, at brugerne er konfigureret til at bruge tjenesten korrekt og også, at brugerne har den uddannelse, de skal være vellykket med at administrere deres egne adgangskoder. I denne artikel forklares følgende begreber for dig:

* [**Sådan får du dine brugere, der er konfigureret til administration af adgangskoder**](#how-to-get-users-configured-for-password-reset)
  * [Hvad gør en konto, der er konfigureret til nulstilling af adgangskode](#what-makes-an-account-configured)
  * [Hvordan du kan udfylde godkendelsesdata dig selv](#ways-to-populate-authentication-data)
* [**De bedste måder at udrulle for din organisation til nulstilling af adgangskode**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [E-mail-baseret udrulning af + eksempel mail kommunikation](#email-based-rollout)
  * [Oprette din egen brugerdefinerede adgangskode management portal for dine brugere](#creating-your-own-password-portal)
  * [Sådan bruger du tvungen registrering tvinge brugere til at registrere på Log på](#using-enforced-registration)
  * [Sådan overfører du godkendelsesdata til brugerkonti](#uploading-data-yourself)
* [**Eksempel på bruger og support kursusmaterialer (kommer snart!)**](#sample-training-materials)

## <a name="how-to-get-users-configured-for-password-reset"></a>Sådan henter du brugere, der er konfigureret til nulstilling af adgangskode
I dette afsnit beskrives for dig forskellige metoder, som du kan sikre, at alle brugere i organisationen kan bruge selvbetjening til nulstilling af adgangskode effektivt i tilfælde af, at de glemmer sin adgangskode.

### <a name="what-makes-an-account-configured"></a>Hvad gør en konto, der er konfigureret
Før en bruger kan bruge nulstilling af adgangskode, skal **alle** af følgende betingelser være opfyldt:

1.  Nulstilling af adgangskode skal være aktiveret i kataloget.  Lær, hvordan du aktiverer ved at læse [brugerne mulighed for at nulstille deres Azure AD-adgangskoder](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) eller [brugerne mulighed for at nulstille eller ændre deres AD adgangskoder](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) til nulstilling af adgangskode
2.  Brugeren skal være licens.
 - For brugere i skyen, skal brugeren have **en hvilken som helst betalt Office 365-licens**, eller en **AAD grundlæggende** eller tildelt **AAD Premium licens** .
 - For lokalt brugere (samlet eller hash synkroniseret), brugeren **skal have tildelt en AAD Premium licens**.
3.  Brugeren skal have **minimum af godkendelsesdata, der er defineret** i overensstemmelse med den aktuelle adgangskode Nulstil politik.
 - Godkendelsesdata betragtes som defineret, hvis det tilsvarende felt i mappen indeholder korrekt udformet data.
 - Et minimum af godkendelsesdata er defineret som på **mindst én** af godkendelsesindstillingerne aktiveret, hvis en politik for én port er konfigureret eller i **mindst to** af godkendelsesindstillingerne for aktiveret, hvis en politik for to port er konfigureret.
4.  Hvis brugeren bruger en konto i det lokale miljø, skal derefter [Adgangskode tilbageførsel](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) være aktiveret og slået til

### <a name="ways-to-populate-authentication-data"></a>Måder til at udfylde godkendelsesdata
Du har flere muligheder at angive data for brugere i organisationen skal bruges til nulstilling af adgangskode.

- Redigere brugere i [Azure Management Portal](https://manage.windowsazure.com) eller [Office 365-administrationsportalen](https://portal.microsoftonline.com)
- Bruge Azure Active Directory-synkronisering til at synkronisere brugeregenskaber til Azure AD fra et lokalt Active Directory-domæne
- Bruge Windows PowerShell til at redigere egenskaber for brugere ved at [følge trinnene her](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).
- Tillad brugere at registrere deres egne data ved at lede dem til portalen registrering på [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Kræve, at brugere til at registrere til adgangskodenulstilling, når de logger på deres Azure AD-konto ved at angive det [**brugerne til at registrere, når du logger på?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) konfigurationsindstillingen til **Ja**.

Brugere skal ikke registrere til nulstilling af adgangskode for systemet til at arbejde.  Eksempelvis hvis du har eksisterende Mobil eller office telefonnumre i dit lokale katalog, kan du synkronisere dem på Azure AD og vi vil bruge dem til automatisk til nulstilling af adgangskode.

Du kan også læse mere om [hvordan data bruges af adgangskode nulstille](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) og [hvordan du kan udfylde individuelle godkendelse felter med PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## <a name="what-is-the-best-way-to-roll-out-password-reset-for-users"></a>Hvad er den bedste måde at udrulle nulstilling af adgangskode for brugere?
Følgende er generelle udrulning af fremgangsmåden til nulstilling af adgangskode:

1.  Aktivere i mappen ved at gå til fanen **Konfigurer** [Azure Management Portal](https://manage.windowsazure.com) og vælge **Ja** til indstillingen **Brugere aktiveret til nulstilling af adgangskode** til nulstilling af adgangskode.
2.  Tildele de relevante licenser til de enkelte brugere, som du gerne vil tilbyde adgangskodenulstilling i den ved at gå til fanen **licenser** i [Azure Management Portal](https://manage.windowsazure.com).
3.  Du kan også begrænse til en gruppe af brugere at udrulle funktionen langsomt over tid ved at **Begrænse adgang til nulstilling af adgangskode** indstillingen til **Ja** og vælge en sikkerhedsgruppe aktivere til nulstilling af adgangskode (Bemærk disse brugere skal alle have fået tildelt licenser) til nulstilling af adgangskode.
4.  Bed brugerne om at bruge ved enten at sende dem en mail instruktioner om dem til at registrere, til nulstilling af adgangskode aktivere tvungne registrering i panelet adgang eller ved at uploade den relevante godkendelsesdata for disse brugere selv via DirSync, PowerShell eller [Azure Management Portal](https://manage.windowsazure.com).  Flere oplysninger om dette er angivet nedenfor.
5.  Gennemse brugere registrering ved at gå til fanen rapporter og få vist rapporten [**Adgangskode nulstille registrering aktivitet**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) i tidens løb.
6.  Når en god antal brugere har registreret, skal du se dem, bruge ved at gå til fanen rapporter og få vist rapporten [**Adgangskode nulstille aktivitet**](active-directory-passwords-get-insights.md#view-password-reset-activity) til nulstilling af adgangskode.

Der er flere måder at underrette brugerne, at de kan registrere til og bruge i din organisation til nulstilling af adgangskode.  De er beskrevet nedenfor.

### <a name="email-based-rollout"></a>E-mail-baseret udrulning af
Måske er den nemmeste metode til at underrette brugerne om at tilmelde dig eller bruge adgangskode til nulstilling af ved at sende dem en mail, instruktioner om dem at gøre dette.  Nedenfor er en skabelon, du kan bruge til at gøre dette.  Velkommen til at erstatte farverne / logoer med de af dine egne, hvis du vælger at tilpasse den efter dine behov.

  ![][001]

Du kan [hente mailskabelon her](http://1drv.ms/1xWFtQM).

### <a name="creating-your-own-password-portal"></a>Oprette din egen adgangskode-portal
En strategi, der fungerer godt til store kunder implementering af funktioner til administration af adgangskode er at oprette en enkelt "adgangskode portal", som brugerne kan bruge til at administrere alle ting drejer sig om deres adgangskoder på ét sted.  

Mange af vores største kunder Vælg for at oprette en rod DNS-post, som https://passwords.contoso.com med links til Azure AD-adgangskode nulstille portal, registrering portal til nulstilling af adgangskode, og din adgangskode ændre sider.  Denne måde i en hvilken som helst mailkommunikation eller løbesedler, du sender, kan du medtage en enkelt, at huske, URL-adresse, som brugere kan gå til, når de har et sekund at komme i gang med tjenesten.

For at komme i gang her, vi har oprettet en enkelt side, der bruger de nyeste svarede Brugergrænsefladen design paradigmer og fungerer på alle browsere og mobilenheder.

  ![][007]

Du kan [hente skabelonen websted her](https://github.com/kenhoff/password-reset-page).  Vi anbefaler, at tilpasse logo og farver til brug af din organisation.

### <a name="using-enforced-registration"></a>Brug af tvungen registrering
Hvis du vil dine brugere at tilmelde sig til nulstilling af adgangskode, kan du også gennemtvinge dem til at registrere, når de logger på panelet adgang på [http://myapps.microsoft.com](http://myapps.microsoft.com).  Du kan aktivere denne indstilling fra dit bibliotek **Konfigurer** fanen ved at aktivere indstillingen **Kræv brugere til Register, når du logger på panelet adgang** .  

Du kan også vælge at angive uanset om de vil blive bedt om at registrere igen efter en konfigurerbar tidsrum ved at ændre **antallet af dage før brugerne skal bekræfte deres kontaktdata** mulighed for at være en værdi ikke er nul. Du kan få flere oplysninger i [Tilpasse bruger adgangskode Management funktionsmåde](active-directory-passwords-customize.md#password-management-behavior) .

  ![][002]

Når du har aktiveret denne indstilling, når brugere logger på panelet adgang, vises der et pop op-vindue, der oplyser dem, der kræves deres administrator dem om at bekræfte deres kontaktoplysninger. De kan bruge det til at nulstille adgangskoden, hvis de skulle miste adgang til deres konto.

  ![][003]

Klik på **Kontrollér nu** bringer dem til **nulstilling af adgangskode registrering portal** på [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) og kræver dem til at registrere.  Registrering via denne metode kan være afvist ved at klikke på knappen **Annuller** eller lukke vinduet, men brugere er mindet, hver gang de logger på, hvis de ikke registrerer.

  ![][004]

### <a name="uploading-data-yourself"></a>Overførsel af data dig selv
Hvis du vil overføre godkendelsesdata dig selv, skal derefter brugere ikke registrere til adgangskodenulstilling, før du kan nulstille sin adgangskode.  Så længe brugere har de godkendelsesdata, der er defineret i deres konto, der opfylder adgangskoden nulstille politik, du har angivet, og derefter disse brugere vil kunne nulstille sin adgangskode.

Se, [hvilke data der bruges af nulstilling af adgangskode](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)for at få mere for at vide, hvilke egenskaber, du kan angive via AAD kontakt eller Windows PowerShell.

Du kan overføre godkendelsesdata via [Azure Management-portalen](https://manage.windowsazure.com) ved at benytte følgende fremgangsmåde:

1.  Gå til mappen i **Active Directory lokalnummer** [Azure Management Portal](https://manage.windowsazure.com).
2.  Klik på fanen **brugere** .
3.  Vælg den bruger, du er interesseret i, på listen.
4.  På den første fane, kan du finde **Alternative mail**, som kan bruges som en egenskab til at aktivere nulstilling af adgangskode.

    ![][005]

5.  Klik på fanen **Arbejdsoplysninger** .
6.  På denne side, kan du finde **Arbejdstelefon**, **mobiltelefon**, **Godkendelse telefon**og **Godkendelse mail**.  Disse egenskaber kan også angive at tillade brugere at nulstille sin adgangskode.

    ![][006]

Se, [hvilke data der bruges af nulstilling af adgangskode](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) til at se, hvordan hver af disse egenskaber kan bruges.

Se, [hvordan du kan få adgang til adgangskode nulstille data for dine brugere fra PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) til at se, hvordan du kan læse og angive disse data med PowerShell.

## <a name="sample-training-materials"></a>Eksempel-uddannelsesmaterialer
Vi arbejder på eksempel-uddannelsesmaterialer, som du kan bruge til at få din IT-organisation og dine brugere på omgangshøjde hurtigt om, hvordan du installerer og bruge nulstilling af adgangskode.  Vær klar!


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Links til adgangskode nulstille dokumentation
Nedenfor finder du links til alle siderne nulstilling af adgangskode til Azure AD dokumentation:

* **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).
* [**Sådan fungerer det**](active-directory-passwords-how-it-works.md) – få mere at vide om de seks forskellige komponenter i den tjeneste, og hvad hver betyder
* [**Kom godt i gang**](active-directory-passwords-getting-started.md) - Lær, hvordan du giver dig mulighed for brugerne at nulstille og ændre deres skyen eller en lokal adgangskoder
* [**Tilpas**](active-directory-passwords-customize.md) - Lær at tilpasse udseende og funktionalitet og funktionsmåde for tjenesten til din organisations behov
* [**Få indsigt**](active-directory-passwords-get-insights.md) - få mere at vide om vores integrerede rapporteringsfunktioner
* [**Ofte stillede spørgsmål**](active-directory-passwords-faq.md) – få svar på ofte stillede spørgsmål
* [**Fejlfinding i forbindelse med**](active-directory-passwords-troubleshoot.md) – Lær, hvordan du hurtigt foretage fejlfinding af problemer med tjenesten
* [**Få mere at vide**](active-directory-passwords-learn-more.md) - gå deep til de tekniske detaljer i Sådan fungerer service



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"

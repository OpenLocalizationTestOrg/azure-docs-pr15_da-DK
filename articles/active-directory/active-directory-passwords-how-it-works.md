<properties
    pageTitle="Sådan fungerer det: Azure AD adgangskode Management | Microsoft Azure"
    description="Få mere at vide om de forskellige komponenter i administration af Azure AD adgangskoder, herunder hvor brugere register, nulstille og ændre deres adgangskode, og hvor administratorer konfigurere, rapportere på, og Aktivér administration af lokale Active Directory adgangskoder."
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

# <a name="how-password-management-works"></a>Hvordan fungerer administration af adgangskoder

> [AZURE.IMPORTANT] **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).

Administration af adgangskoder i Azure Active Directory består af flere logiske komponenter, der er beskrevet nedenfor.  Klik på hvert link mere at vide om komponenten.

- [**Adgangskode Management konfiguration Portal**](#password-management-configuration-portal) – administratorer styrer forskellige aspekter af hvordan adgangskoder administreres i deres lejere ved at navigere til deres directory Konfigurer fane i [Azure Management Portal](https://manage.windowsazure.com).
- [**Bruger registrering Portal**](#user-registration-portal) – brugerne kan registrere sig til adgangskodenulstilling via denne webportal.
- [**User adgangskode Nulstil Portal**](#user-password-reset-portal) -brugere kan nulstille deres egne adgangskoder ved hjælp af en række forskellige udfordringer i overensstemmelse med administrator-kontrolleres Nulstil adgangskodepolitik
- [**User adgangskode ændre Portal**](#user-password-change-portal) -brugere kan ændre deres egne adgangskoder når som helst ved at indtaste deres gamle adgangskode og vælge en ny adgangskode ved hjælp af denne webportalen
- [**Adgangskode Management rapporter**](#password-management-reports) – administratorer kan få vist og analysere adgangskode Nulstil og registrering aktivitet i deres lejer ved at gå til afsnittet "aktivitetsrapporter" i deres mappe under fanen "Rapporter" i [Azure Management Portal](https://manage.windowsazure.com)
- [**Adgangskode tilbageførsel komponent i Azure AD-forbindelse**](#password-writeback-component-of-azure-ad-connect) -administratorer kan du kan også aktivere funktionen adgangskode tilbageførsel, når du installerer Azure AD-forbindelse for at aktivere styring af samlet eller adgangskode synkroniseret brugeradgangskoder fra skyen.

## <a name="password-management-configuration-portal"></a>Adgangskode Management konfiguration Portal
Du kan konfigurere politikker for administration af adgangskode for en bestemt mappe ved hjælp af [Azure Management-portalen](https://manage.windowsazure.com) ved at gå til afsnittet **Bruger adgangskodepolitik Nulstil** mappens **Konfigurer** under fanen.  Denne konfigurationssiden, kan du administrere mange aspekter af hvordan adgangskoder administreres i din organisation, herunder:

- Aktivering og deaktivering af nulstilling af adgangskode for alle brugere i en mappe
- Konfigurer antallet af udfordringer (enten én eller to) en bruger skal gennemgå til at nulstille sin adgangskode
- Indstille bestemte typer af udfordringer, du vil aktivere for brugere i organisationen fra valgmulighederne nedenfor:
 - Mobiltelefon (en bekræftelseskode via tekst eller et taleopkald)
 - Arbejdstelefon (et taleopkald)
 - Alternative mail (en bekræftelseskode via mail)
 - Spørgsmål om sikkerhed (knowledge-baseret godkendelse)
- Konfigurer antallet af spørgsmål en bruger skal registrere for at bruge den sikkerhed spørgsmål godkendelsesmetode (kun synlig, hvis spørgsmål om sikkerhed er aktiveret)
- Konfigurer antallet af spørgsmål en bruger skal angive under Nulstil for at bruge den sikkerhed spørgsmål godkendelsesmetode (kun synlig, hvis spørgsmål om sikkerhed er aktiveret)
- Brug af foruddefinerede dåsefoder, oversatte, sikkerhedsspørgsmål, som en bruger kan vælge at bruge ved registrering for adgangskode nulstille (kun synlig, hvis spørgsmål om sikkerhed er aktiveret)
- Definition af brugerdefineret sikkerhed spørgsmålene, som en bruger kan vælge at bruge, når registrering for adgangskode nulstille (kun synlig, hvis spørgsmål om sikkerhed er aktiveret)
- Kræve brugere til at registrere til adgangskodenulstilling, når de går til programmet Access Panel på [http://myapps.microsoft.com](http://myapps.microsoft.com).
- Der kræver brugere at igen bekræfte deres tidligere registrerede data efter en konfigurerbar antallet af dage er gået (kun synlig, hvis tvungen registrering er aktiveret)
- Give en brugerdefineret helpdesk-mail eller URL-adresse, der vises for brugerne, i tilfælde af, at de har problemer med nulstilling af deres adgangskoder
- Aktivere eller deaktivere muligheden for adgangskode tilbageførsel (når adgangskode tilbageførsel er blevet installeret med AAD Opret forbindelse)
- Få vist status for adgangskode tilbageførsel agent (når adgangskode tilbageførsel er blevet installeret med AAD Opret forbindelse)
- Aktivere e-mail-beskeder til brugerne, når deres egen adgangskode er nulstillet (findes i afsnittet **meddelelser** i [Azure Management Portal](https://manage.windowsazure.com))
- Aktivere mailbeskeder for administratorer, når andre administratorer nulstille deres egne adgangskoder (findes i afsnittet **meddelelser** i [Azure Management Portal](https://manage.windowsazure.com)
- Branding brugeradgangskode nulstille portal og adgangskode nulstille mails med organisationens logo og et navn ved hjælp af lejeren branding funktionen til tilpasning af (findes i afsnittet **Egenskaber for mappe** i [Azure Management Portal](https://manage.windowsazure.com)

Du kan få flere oplysninger om konfiguration af administration af adgangskoder i din organisation, [Introduktion: Administration af Azure AD adgangskoder](active-directory-passwords-getting-started.md).

##<a name="user-registration-portal"></a>Bruger registrering Portal
Før brugere er aktiveret til nulstilling af adgangskode, skal deres skyen brugerkonti opdateres med de korrekte godkendelsesdata til at sikre, at de kan passerer gennem det ønskede antal adgangskode Nulstil udfordringer defineret af deres administrator.  Administratorer kan også angive denne godkendelsesoplysninger på vegne af deres bruger ved hjælp af Azure eller Office webportaler, DirSync / Azure AD-forbindelse eller Windows PowerShell.

Men hvis du hellere vil have brugerne registrere deres egne data, giver vi også en webside, som brugere kan gå til for at angive disse oplysninger.  Denne side, så brugerne angiver oplysninger til godkendelse i overensstemmelse med adgangskoden nulstille politikker, der er aktiveret i deres organisation.  Når disse data er bekræftet, gemmes den i deres skyen brugerkonto, der skal bruges til kontogendannelse på et senere tidspunkt. Her er, hvordan den registrering portalen ser ud:

  ![][001]

Kan finde flere oplysninger under [Introduktion: Administration af Azure AD adgangskoder](active-directory-passwords-getting-started.md) og [bedste praksis: Administration af Azure AD adgangskoder](active-directory-passwords-best-practices.md).

##<a name="user-password-reset-portal"></a>User adgangskode Nulstil Portal
Når du har aktiveret selvbetjening nulstille, konfigurere din organisations selvbetjening Nulstil adgangskodepolitik og sikres, at brugerne har den relevante kontaktdata i kataloget, brugerne i organisationen kan nulstille deres egne adgangskoder automatisk fra en webside, som bruger en konto til arbejde eller skole til logon (såsom [portal.microsoftonline.com](https://portal.microsoftonline.com)). På sider som disse, brugerne får vist en **kan ikke få adgang til din konto?** link.

  ![][002]

At klikke på dette link åbne selvbetjening Nulstil portal.

  ![][003]

Du kan få mere at vide om, hvordan brugere kan nulstille deres egne adgangskoder, [Introduktion: Administration af Azure AD adgangskoder](active-directory-passwords-getting-started.md).

##<a name="user-password-change-portal"></a>User adgangskode Skift Portal
Hvis brugere vil ændre deres egne adgangskoder, kan brugeren kan gøre det ved hjælp af portalen adgangskode Skift når som helst.  Brugere kan få adgang til portalen adgangskode Skift via siden Access Panel profil eller klikker på linket "ændre adgangskode" fra i Office 365-programmer.  I tilfælde når deres adgangskoder udløber, brugere vil også blive bedt om at ændre dem automatisk, når du logger på.

  ![][004]

I begge af disse tilfælde, hvis adgangskode tilbageførsel er blevet aktiveret, og brugeren er enten knyttet eller synkronisering af adgangskoder ville gøre, skrives disse ændrede adgangskoder tilbage til din lokale Active Directory. Her er, hvad adgangskode Skift portalen ser sådan:

  ![][005]

Hvis du vil vide mere om, hvordan brugere kan ændre deres egne lokale Active Directory adgangskoder, se [Introduktion: Administration af Azure AD adgangskoder](active-directory-passwords-getting-started.md).

##<a name="password-management-reports"></a>Administration af adgangskoder rapporter
Ved at gå til fanen **rapporter** og kigge under afsnittet **Aktivitetslogfiler** , du får vist to administration af adgangskoder rapporter: **aktivitet til nulstilling af adgangskode** og **registrering aktivitet til nulstilling af adgangskode**.  Brug af disse to rapporter, kan du få et overblik over brugere registrering til og bruge i din organisation til nulstilling af adgangskode. Her er, hvordan disse rapporter ser ud i [Azure Management portalen](https://manage.windowsazure.com):

  ![][006]

Du kan finde flere oplysninger, se [få indsigt: Administration af Azure AD adgangskoder rapporter](active-directory-passwords-get-insights.md).

##<a name="password-writeback-component-of-azure-ad-connect"></a>Adgangskode tilbageførsel komponent i Azure AD-forbindelse
Hvis adgangskoder for brugere i organisationen stammer fra dit lokale miljø (enten via sammenslutning eller synkronisering af adgangskoder), kan du installere den nyeste version af Azure AD-forbindelse til at aktivere opdatering af disse adgangskoder direkte fra skyen.  Det betyder, når brugerne glemmer eller vil ændre adgangskoden for AD, brugeren kan gøre det direkte fra internettet.  Her er, hvor du kan finde adgangskode der ikke er gemt i installationsguiden Azure AD-forbindelse:

  ![][007]

Finde flere oplysninger om Azure AD-forbindelse, [Introduktion: Azure AD-forbindelse](active-directory-aadconnect.md). Finde flere oplysninger om adgangskode tilbageførsel [Introduktion: Administration af Azure AD adgangskoder](active-directory-passwords-getting-started.md).


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Links til adgangskode nulstille dokumentation
Nedenfor finder du links til alle siderne nulstilling af adgangskode til Azure AD dokumentation:

* **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).
* [**Kom godt i gang**](active-directory-passwords-getting-started.md) - Lær, hvordan du giver dig mulighed for brugerne at nulstille og ændre deres skyen eller en lokal adgangskoder
* [**Tilpas**](active-directory-passwords-customize.md) - Lær at tilpasse udseende og funktionalitet og funktionsmåde for tjenesten til din organisations behov
* [**Bedste fremgangsmåder**](active-directory-passwords-best-practices.md) - Lær at implementere hurtigt og effektivt administrere adgangskoder i din organisation
* [**Få indsigt**](active-directory-passwords-get-insights.md) - få mere at vide om vores integrerede rapporteringsfunktioner
* [**Ofte stillede spørgsmål**](active-directory-passwords-faq.md) – få svar på ofte stillede spørgsmål
* [**Fejlfinding i forbindelse med**](active-directory-passwords-troubleshoot.md) – Lær, hvordan du hurtigt foretage fejlfinding af problemer med tjenesten
* [**Få mere at vide**](active-directory-passwords-learn-more.md) - gå deep til de tekniske detaljer i Sådan fungerer service



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

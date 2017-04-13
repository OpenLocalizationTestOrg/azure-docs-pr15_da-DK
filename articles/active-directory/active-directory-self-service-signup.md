<properties
    pageTitle="Hvad er selvbetjening tilmelding til Azure? | Microsoft Azure"
    description="En oversigt over selvbetjening tilmelding til Azure, hvordan du administrerer tilmeldingsprocessen, og hvordan du overtage en DNS-domænenavn."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>


# <a name="what-is-self-service-signup-for-azure"></a>Hvad er selvbetjening tilmelding til Azure?

Dette emne forklares selvbetjening tilmeldingsprocessen og hvordan du kan tage over et DNS-domænenavn.  

## <a name="why-use-self-service-signup"></a>Hvorfor bruge selvbetjening tilmelding?

- Få kunder til tjenester, de vil hurtigere.
- Oprette e-mail-baseret tilbud til en tjeneste.
- Oprette e-mail-baseret tilmelding flyder som hurtigt tillade brugere at oprette identiteter ved hjælp af deres arbejde, der er nem at huske mailaliasser.
- Ikke-administreret Azure mapper kan ændres til administrerede kataloger og genbruges til andre tjenester.

## <a name="terms-and-definitions"></a>Begreber og definitioner

+ **Selvbetjening tilmelde**: Dette er den metode, som en bruger tilmelder sig til en skybaseret tjeneste og har en identitet, oprettes automatisk efter dem i Azure Active Directory (Azure AD), der er baseret på deres maildomæne.
+ **Ikke-administreret Azure directory**: Dette er den mappe, hvor der oprettes, identitet. En ikke-administreret mappe er en mappe, der har ingen global administrator.
+ **Mail-godkendt bruger**: Dette er en type brugerkonto i Azure AD. En bruger, der har en identitet, der er oprettet automatisk, når du tilmelder dig et nyt tilbud, der kendes som en mail-godkendt bruger. En mail-godkendt bruger er et almindelige medlem af en mappe, der er mærket med creationmethod = EmailVerified.

## <a name="user-experience"></a>Brugeroplevelsen

For eksempel Antag, at en bruger, hvis mailen findes Dan@BellowsCollege.com modtager følsomme filer via mail. Filerne, der er beskyttet af Azure Rights Management (Azure RMS). Men dans organisation, bælge Universitetsstuderende, ikke har tilmeldt sig til Azure RMS og har den installeret Active Directory RMS. I dette tilfælde kan Dan tilmelde dig en gratis abonnement på RMS for personer for at læse de beskyttede filer.

Hvis Dan er den første bruger med en e-mailadresse fra BellowsCollege.com til at tilmelde sig denne selvbetjening med tilbud, og klik derefter et ikke-administreret directory oprettes for BellowsCollege.com i Azure AD. Hvis andre brugere fra BellowsCollege.com domænet tilmelde sig til dette tilbud eller en lignende selvbetjening tilbud, skal de også har mail-bekræftet brugerkonti, der er oprettet i den samme ikke-administrerede mappe i Azure.

## <a name="admin-experience"></a>Administrator-oplevelse

En administrator der ejer DNS-domænenavnet på en ikke-administreret Azure directory kan overtage eller flette mappen efter beviser ejerskab. I næste afsnit beskrives administrator oplevelsen mere detaljeret, men her er en oversigt:

- Når du tager over en ikke-administreret Azure directory, bliver du blot den globale administrator af mappen ikke-administreret. Dette kaldes også en intern overtagelse.
- Når du fletter en ikke-administreret Azure mappe, du føjer DNS-domænenavnet på mappen ikke-administreret til adresselisten administrerede Azure og tilknytningen af brugere til ressourcer oprettes så brugere kan fortsætte med at få adgang til tjenester uden afbrydelser. Dette kaldes også en ekstern overtagelse.

## <a name="what-gets-created-in-azure-active-directory"></a>Hvad bliver oprettet i Azure Active Directory?

#### <a name="directory"></a>Directory

- En Azure Active Directory-katalogtjeneste for domænet, der oprettes, én mappe per domæne.
- Azure AD-mappe har ingen global administrator.

#### <a name="users"></a>Brugere

- For hver bruger, der tilmelder sig, oprettes et brugerobjekt i Azure AD-mappe.
- Hver brugerobjekt er markeret som eksterne.
- Hver bruger får adgang til tjenesten, som de har tilmeldt sig.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Hvordan gør jeg krav på en selvbetjening Azure AD mappe for et domæne, jeg ejer?

Du kan gøre krav på en selvbetjening Azure AD mappe ved at udføre domæne validering. Domæne validering beviser, at du ejer domænet ved at oprette DNS-poster.

Der er to måder at udføre en DNS-overtagelse af en Azure AD-mappe:

- interne overtagelse (Admin opdager et ikke-administreret Azure directory og ønsker at ændre til en administreret mappe)
- eksterne overtagelse (administrator forsøger at tilføje et nyt domæne til deres administrerede Azure mappe)

Du måske være interesseret i validering af, at du ejer et domæne, fordi du tager over et ikke-administreret directory, når en bruger har udført selvbetjening tilmelding, eller du kan tilføje et nyt domæne til en eksisterende administrerede mappe. Du har et domæne med navnet contoso.com f.eks, og du vil tilføje et nyt domæne med navnet contoso.co.uk eller contoso.uk.

## <a name="what-is-domain-takeover"></a>Hvad er domæne overtagelse?  

Dette afsnit beskrives, hvordan du kontrollere, at du ejer et domæne

### <a name="what-is-domain-validation-and-why-is-it-used"></a>Hvad er domæne datavalidering, og hvorfor bruges det?

Azure AD kræver, at du validerer ejerskabet af DNS-domænet for at udføre handlinger på en mappe.  Validering på domænet, kan du gøre krav på mappen, og enten hæve den selvbetjening mappe til en administreret mappe eller flette mappen selvbetjening til en eksisterende administrerede mappe.

## <a name="examples-of-domain-validation"></a>Eksempler på domæne validering

Der er to måder at udføre en DNS-overtagelse af en mappe:

+ interne overtagelse (For eksempel administrator opdager en selvbetjening, ikke-administreret mappe og ønsker at lave til administrerede directory)
+ eksterne overtagelse (For eksempel en administrator forsøger at tilføje et nyt domæne til en administreret mappe)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Interne overtagelse - hæve en selvbetjening, ikke-administreret mappe for at være en administreret mappe

Når du gør interne overtagelse, bliver mappen konverteret fra en ikke-administreret mappe til en administreret mappe. Du skal fuldføre DNS domæne navn validering, hvor du opretter en MX-post eller en TXT-post i DNS-zonen. Denne handling:

+ Kontrollerer, at du ejer domænet
+ Gør den mappe, der administreres
+ Gør du den globale administrator af mappen

Antag, at en IT-administrator fra bælge Universitetsstuderende opdager, at brugere fra skolen har tilmeldt sig til selvbetjening tilbud. Mens registrerede ejeren af DNS navngive BellowsCollege.com, kan IT-administratoren validere ejerskabet af DNS-navnet i Azure og derefter tage over ikke-administreret kataloget. Mappen bliver derefter en administreret mappe, og IT-administratoren er tildelt den globale administratorrolle for mappen BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Eksterne overtagelse - flette en selvbetjening mappe til en eksisterende administrerede mappe

I en ekstern overtagelse, du har allerede en administreret mappe og alle brugere og grupper fra en ikke-administreret adresseliste til at deltage i administrerede mappen i stedet for egen to separate mapper.

Som administrator af en administreret mappe, du har tilføjet et domæne, og domænet, sker der med har en ikke-administreret mappe, der er knyttet til den.

For eksempel Antag, at du er en IT-administrator, og du allerede har en administreret mappe til Contoso.com, et domænenavn, der er registreret til organisationen. Du opdager, at brugere fra din organisation er udført selvbetjeningsfunktionen for tilmelding op til et tilbud via mail domænenavn user@contoso.co.uk, som er et andet domænenavn, der ejer af din organisation. Disse brugere har i øjeblikket konti i en ikke-administreret mappe til contoso.co.uk.

Du vil ikke administrere to separate kataloger, så du flette mappen ikke-administreret for contoso.co.uk til din eksisterende IT-administrerede mappe til contoso.com.

Eksterne overtagelse følger samme DNS valideringsprocessen som interne overtagelse.  Forskel stå: brugere og tjenester er tilknyttet den IT-administrerede mappe.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>Hvad er effekten af at udføre en ekstern overtagelse?

Med en ekstern overtagelse oprettes en tilknytning af brugere til ressourcer, så brugerne kan fortsætte med at få adgang til tjenester uden afbrydelser. Mange programmer, herunder RMS for personer, håndtere tilknytningen af brugere til ressourcer godt, og brugerne kan fortsætte med at få adgang til disse tjenester uden at ændre. Hvis et program ikke håndtere tilknytningen af brugere til ressourcer effektivt, kan eksterne overtagelse blokeres eksplicit for at forhindre brugere i en dårlig oplevelse.

#### <a name="directory-takeover-support-by-service"></a>understøttelse af Directory overtagelse af tjenesten

Følgende tjenester understøtter overtagelse:

- RMS


Følgende tjenester vil snart understøttende overtagelse:

- PowerBI

Følgende ikke og kræver ekstra administrationshandling til at overflytte brugerdata efter en ekstern overtagelse.

- SharePoint/OneDrive


## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Hvordan du udfører en DNS-domæne navn overtagelse

Har du et par muligheder at udføre en domæne validering (og gøre en overtagelse, hvis du vil):

1.  Azure Management-portalen

    En overtagelse udløses ved at gøre tilføjelsen af en domæne.  Hvis der findes allerede en mappe for domænet, har du mulighed for at udføre en ekstern overtagelse.

    Log på portalen Azure ved hjælp af dine legitimationsoplysninger.  Gå til din eksisterende mappe, og klik derefter på **Tilføj domæne**.

2.  Office 365

    Du kan bruge indstillingerne på siden [Administrer domæner](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) i Office 365 til at arbejde med dine domæner og DNS-poster. Se [bekræfte dit domæne i Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).

3.  Windows PowerShell

    Følgende trin er påkrævet til at udføre en validering ved hjælp af Windows PowerShell.

    Trin    |   -Cmdlet til brug
    ------- | -------------
    Oprette et legitimationsoplysninger objekt | Get-legitimationsoplysninger
    Oprette forbindelse til Azure AD | Oprette forbindelse MsolService
    få en liste over domæner   | Get-MsolDomain
    Oprette en udfordring  | Get-MsolDomainVerificationDns
    Oprette DNS-post   | Gøre dette på din DNS-server
    Kontrollér udfordringen    | Bekræft MsolEmailVerifiedDomain

Eksempel:

1. Oprette forbindelse til Azure AD ved hjælp af de legitimationsoplysninger, der blev brugt til at besvare den selvbetjening teknologi: Importér modul MSOnline $msolcred = get-legitimationsoplysninger forbinde-msolservice-legitimationsoplysninger $msolcred

2. Få en liste over domæner:

    Get-MsolDomain

3. Kør derefter cmdlet'en Get-MsolDomainVerificationDns til for at oprette en udfordring:

    Get-MsolDomainVerificationDns – DomainName *your_domain_name* – tilstand DnsTxtRecord

    Eksempel:

    Get-MsolDomainVerificationDns – DomainName contoso.com – tilstand DnsTxtRecord

4. Kopiere værdien (udfordringen), der returneres fra denne kommando.

    Eksempel:

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. Oprette en DNS-txt-post, der indeholder den værdi, du kopierede i det forrige trin i din offentlige DNS-navneområdet.

    Navn for denne post er navnet på det overordnede domæne, så hvis du opretter denne ressourcepost ved hjælp af rollen DNS fra Windows Server, Lad post navn tomt og kun indsætte værdien i feltet tekst

6. Kør cmdlet for at bekræfte udfordringen Bekræft MsolDomain:

    Bekræft MsolEmailVerifiedDomain - DomainName *your_domain_name*

    Eksempel:

    Bekræft MsolEmailVerifiedDomain - DomainName contoso.com

En vellykket udfordring vender tilbage til prompten uden en fejl.

## <a name="how-do-i-control-self-service-settings"></a>Hvordan styrer jeg selvbetjening indstillinger?

Administratorer have to selvbetjening kontrolelementer i dag. De kan styre:

- Uanset om brugere kan deltage i mappen via mail.
- Brugere kan hvorvidt licensere selv til programmer og tjenester.


### <a name="how-can-i-control-these-capabilities"></a>Hvordan kan jeg så styre disse funktioner?

En administrator kan konfigurere disse funktioner ved hjælp af disse Azure AD cmdlet'en Set-MsolCompanySettings parametre:

+ **AllowEmailVerifiedUsers** kontrollerer, om en bruger kan oprette eller deltage i et ikke-administreret directory. Hvis du indstiller denne parameter til $false, kan ingen mail-bekræftet brugere kan deltage i kataloget.
+ **AllowAdHocSubscriptions** kontrollerer, om brugere til at udføre selvbetjeningsfunktionen for tilmelding op. Hvis du indstiller denne parameter til $false, kan ingen brugere kan udføre selvbetjening tilmelding.


### <a name="how-do-the-controls-work-together"></a>Hvordan kontrolelementerne arbejder sammen?

Disse to parametre kan bruges sammen til at definere mere nøjagtig kontrol over selvbetjeningsfunktionen for tilmelding op. For eksempel følgende kommando vil tillade brugere at udføre selvbetjeningsfunktionen for tilmelding op, men kun hvis disse brugere har allerede en konto i Azure AD (det vil sige, brugere, der har brug for en bekræftet mail-konto skal have oprettet kan ikke udføre selvbetjeningsfunktionen for tilmelding op):

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

Følgende rutediagram beskriver de forskellige kombinationer for disse parametre og de deraf følgende betingelser for mappen, og selvbetjeningsfunktionen for tilmelding op.

![][1]

Flere oplysninger og eksempler på, hvordan du bruger disse parametre, under [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## <a name="see-also"></a>Se også

-  [Hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)

-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Reference til Azure Cmdlet](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

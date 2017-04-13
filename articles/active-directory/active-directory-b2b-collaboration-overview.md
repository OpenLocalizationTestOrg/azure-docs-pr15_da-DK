<properties
   pageTitle="Samarbejde med Azure Active Directory-B2B | Microsoft Azure"
   description="Samarbejde med Azure Active Directory-B2B gør det muligt for forretningsforbindelser med hver af de brugere, der er repræsenteret af en enkelt Azure AD-konto at få adgang til dine virksomhedens programmer"
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

# <a name="azure-active-directory-b2b-collaboration"></a>Azure Active Directory-B2B samarbejde

Azure Active Directory (Azure AD) B2B samarbejde kan du aktivere adgang til dine virksomhedens programmer fra partner-administrerede identiteter. Du kan oprette relationer i tværs af firmaer ved at invitere og godkende brugere fra partnervirksomheder at få adgang til dine ressourcer. Kompleksitet er reduceret, fordi hver virksomhed federates én gang med Azure Active Directory og hver bruger er repræsenteret af en enkelt Azure AD-konto. Sikkerhed forøges, hvis din forretningsforbindelser administrere deres konti i Azure AD, fordi adgangstilladelse når partner brugere er afsluttet fra deres organisationer, og forhindres utilsigtet adgang via medlemskab interne mapper. For business-partnere, der ikke allerede har Azure AD, har B2B samarbejde en strømlinet tilmelding oplevelse at levere Azure AD-konti til din forretningsforbindelser.

-   Din forretningsforbindelser bruge deres egne logonoplysninger, som slipper du fra administration af en ekstern partner directory og fra er nødvendigheden at fjerne adgang, når brugere forlader organisationen partner.

-   Du kan administrere adgangen til dine apps uafhængigt af din business partner konto livscyklus. Det betyder f.eks, at du kan fjerne adgangen uden at spørge IT-afdelingen i din business partner til at gøre noget.

## <a name="capabilities"></a>Funktioner

Samarbejde med B2B forenkler administration og forbedrer sikkerheden for partneradgang til virksomhedsressourcer, herunder SaaS apps, som Office 365, Salesforce, Azure Services og alle mobile i skyen og lokale krav-aware programmet. B2B samarbejde gør det muligt for partnere administrere deres egne konti og virksomheder kan anvende sikkerhedspolitikker på partneradgang.

Azure Active Directory B2B samarbejde er nemt at konfigurere med forenklet tilmelding til partnere af alle størrelser, selvom de ikke har deres egne Azure Active Directory via en mail-bekræftet proces. Det er også nem at vedligeholde med nogen eksterne mapper eller per partner sammenslutning konfigurationer.

## <a name="b2b-collaboration-process"></a>B2B samarbejde proces

1. Azure AD B2B samarbejde gør det muligt for en virksomhedsadministrator invitere og Godkend en række eksterne brugere ved at uploade filen kommaseparerede værdier (CSV) med linjer, der ikke mere end 2000 til portalen B2B samarbejde.

  ![Overfør dialogboks i CSV-fil](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. På portalen sender e-mail-invitationer til de eksterne brugere.

3. Den inviterede bruger kan enten logge på en eksisterende arbejdskonto med Microsoft (administreres i Azure AD), eller få en ny arbejds-konto i Azure AD.

4. Når du er logget, bliver brugeren omdirigeret til den app, der blev delt med dem.

Invitationer til forbrugere mailadresser (for eksempel Gmail eller [*comcast.net*](http://comcast.net/)) understøttes ikke i øjeblikket.

Få mere at vide om, hvordan B2B samarbejde fungerer, se [denne video](http://aka.ms/aadshowb2b).

## <a name="next-steps"></a>Næste trin
Gennemse vores andre artikler på Azure AD B2B samarbejde.

- [Hvad er Azure AD B2B samarbejde?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Sådan fungerer det](active-directory-b2b-how-it-works.md)
- [Detaljeret gennemgang](active-directory-b2b-detailed-walkthrough.md)
- [Reference til CSV-fil-format](active-directory-b2b-references-csv-file-format.md)
- [Eksterne brugere token format](active-directory-b2b-references-external-user-token-format.md)
- [Eksterne brugere objekt attributændringer](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Aktuelle preview-begrænsninger](active-directory-b2b-current-preview-limitations.md)
- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

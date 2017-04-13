<properties
   pageTitle="Azure AD B2B samarbejde preview: hvordan det fungerer | Microsoft Azure"
   description="I denne artikel beskrives, hvordan Azure Active Directory B2B samarbejde ved at aktivere forretningsforbindelser selektivt adgang til din virksomhedens programmer understøtter din relationer i tværs af firmaer"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-how-it-works"></a>Azure AD B2B samarbejde preview: Sådan fungerer det
Azure AD B2B samarbejde er baseret på en invitation og indløse model. Du angiver mailadresserne på de parter, du vil arbejde med, sammen med de programmer, du vil bruge. Azure AD får tilsendt en mail Inviter med et link. Partner brugeren følger linket og bliver bedt om at logge på med deres Azure AD-konto eller Log til en ny Azure AD-konto.

1. Din administrator inviterer partner brugere ved at uploade [en strukturerede .csv-fil](active-directory-b2b-references-csv-file-format.md) ved hjælp af portalen Azure.
2. Portalen sender invitere mails til disse partner brugere.
3. Partner-brugere skal du klikke på linket i mailen, og bliver bedt om at logge på med deres arbejde legitimationsoplysninger (hvis de er allerede i Azure AD) eller for at tilmelde dig som bruger en Azure AD B2B samarbejde.
4. Partner brugere omdirigeres til det program, de er blevet inviteret til, hvor de har nu adgang.

## <a name="directory-operations"></a>Directory-handlinger
Partner brugerne findes i din Azure AD som eksterne brugere. Denne betyder, at din administrator kan tildele licenser, tildele gruppemedlemskab og yderligere give adgang til virksomhedens apps via portalen Azure eller ved hjælp af PowerShell Azure præcis som for brugere i virksomheden.

Når et betalt Azure AD abonnement (Basic eller Premium) er ikke nødvendigt at bruge Azure AD B2B, lejere, som har et betalt Azure AD-abonnement (Basic eller Premium) får vist følgende yderligere fordele:

 - Administratorer kan tildele grupper til apps, give for nemmere administration af inviterede brugeradgang.
 - Administrator-lejer branding bruges til at skabe et brand invitation mails og indløsningskurs oplevelse, give mere kontekst til inviterede partner brugere.

## <a name="related-articles"></a>Relaterede artikler
 Gennemse vores andre artikler på Azure AD B2B samarbejde

 - [Hvad er Azure AD B2B samarbejde?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Detaljeret gennemgang](active-directory-b2b-detailed-walkthrough.md)
 - [Reference til CSV-fil-format](active-directory-b2b-references-csv-file-format.md)
 - [Eksterne brugere token format](active-directory-b2b-references-external-user-token-format.md)
 - [Eksterne brugere objekt attributændringer](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Aktuelle preview-begrænsninger](active-directory-b2b-current-preview-limitations.md)
 - [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

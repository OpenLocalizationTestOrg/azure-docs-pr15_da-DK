<properties
   pageTitle="Aktuelle preview begrænsninger til Azure Active Directory B2B samarbejde | Microsoft Azure"
   description="Azure Active Directory-B2B understøtter din tværs af firmaer relationer ved at aktivere forretningsforbindelser selektivt adgang til din virksomhedens programmer"
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

# <a name="azure-ad-b2b-collaboration-preview-current-preview-limitations"></a>Azure AD B2B samarbejde preview: aktuelle Gennemse begrænsninger

- Multi-Factor authentication (MFA) understøttes ikke på eksterne brugere. Eksempelvis hvis Contoso har MFA, men Partner organisation ikke er tilfældet, derefter Partner Org brugere kan ikke tildeles MFA gennem B2B samarbejde.
- Invitationer er mulige kun via CSV; individuelle invitationer og API-adgang understøttes ikke.
- Azure AD globale administratorer kan kun overføre .csv-filer.
- Invitationer til forbrugere mailadresser (som hotmail.com, Gmail.com eller comcast.net) understøttes ikke i øjeblikket.
- Eksterne brugeradgang til lokale programmer ikke testet.
- Eksterne brugere ryddes ikke automatisk op, når den pågældende bruger slettes fra deres mappe.
- Invitationer til distributionslister understøttes ikke.
- Maksimalt 2.000 poster kan overføres via CSV.

## <a name="related-articles"></a>Relaterede artikler
Gennemse vores andre artikler på Azure AD B2B samarbejde:

- [Hvad er Azure AD B2B samarbejde?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Sådan fungerer det](active-directory-b2b-how-it-works.md)
- [Detaljeret gennemgang](active-directory-b2b-detailed-walkthrough.md)
- [Reference til CSV-fil-format](active-directory-b2b-references-csv-file-format.md)
- [Eksterne brugere token format](active-directory-b2b-references-external-user-token-format.md)
- [Eksterne brugere objekt attributændringer](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

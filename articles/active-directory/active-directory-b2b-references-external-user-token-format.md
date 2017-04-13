<properties
   pageTitle="Eksterne brugere token format til Azure Active Directory B2B samarbejde preview | Microsoft Azure"
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
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-external-user-token-format"></a>Azure AD B2B samarbejde preview: ekstern bruger token format

Tjenesten krav til et almindeligt Azure AD token er beskrevet i artiklen [understøttes Token og typer af gør krav](active-directory-token-and-claims.md) på azure.microsoft.com.

De krav, der er forskellige en godkendt B2B samarbejde med eksterne brugere er som følger:<br/>
- **Objekt-id:** objekt-ID'ET fra de ressource<br/>
- **TID**: lejer ID fra de ressource<br/>
- **Udsteder**: Dette er den ressource lejer<br/>
- **IDP**: Dette er den private lejer på brugeren<br/>
- **AltSecId**: Dette er alternativ sikkerheds-ID, som er uigennemsigtig til dig<br/>

## <a name="related-articles"></a>Relaterede artikler
Gennemse vores andre artikler på Azure AD B2B samarbejde:

- [Hvad er Azure AD B2B samarbejde?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Sådan fungerer det](active-directory-b2b-how-it-works.md)
- [Detaljeret gennemgang](active-directory-b2b-detailed-walkthrough.md)
- [Reference til CSV-fil-format](active-directory-b2b-references-csv-file-format.md)
- [Eksterne brugere objekt attributændringer](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Aktuelle preview-begrænsninger](active-directory-b2b-current-preview-limitations.md)
- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

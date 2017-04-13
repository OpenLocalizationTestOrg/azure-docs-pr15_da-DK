<properties
    pageTitle="Azure AD-forbindelse: Synkronisere forekomster af tjenesten | Microsoft Azure"
    description="Særlige overvejelser i forbindelse med Azure AD forekomster af dokumenter til denne side."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD-forbindelse: Særlige overvejelser i forbindelse med forekomster
Azure AD-forbindelse er mest almindeligt brugte for den verdensplan forekomst af Azure AD og Office 365. Men der er også andre forekomster, og disse har forskellige krav til URL-adresser og andre særlige overvejelser.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Tyskland
[Microsoft Cloud Tyskland](http://www.microsoft.de/cloud-deutschland) er en suveræne sky, som drives af en person tysk data.

Denne skyen er i øjeblikket i Vis udskrift. Mange af de scenarier, som du normalt kan gøre ved dig selv, f.eks Bekræft domæner, skal udføres af operatoren. Kontakt din lokale Microsoft-repræsentant for flere oplysninger om at deltage i eksemplet.

URL-adresser til at åbne i proxy-server |
--- |
\*. microsoftonline.de |
\*. windows.net |
+ Lister over tilbagekaldte certifikater af certifikat |

Når du logger på din Azure AD-mappe skal du bruge en konto i onmicrosoft.de domænet.

Funktioner, der er i øjeblikket ikke til stede i Microsoft Cloud-Tyskland:

- Azure AD forbinde tilstand er ikke tilgængelig.
- Automatiske opdateringer er ikke tilgængelig.
- Adgangskode tilbageførsel er ikke tilgængelig.

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure Government cloud
[Microsoft Azure Government cloud](https://azure.microsoft.com/features/gov/) er en skybaseret til den amerikanske regering.

Denne skyen er blevet understøttes af tidligere versioner af DirSync. Oprettelse af skyen understøttes fra build 1.1.180 af Azure AD-forbindelse på Næste. Denne oprettelse anvendes kun USA-baseret slutpunkter og have en anden liste over URL-adresser til at åbne i din proxyserver.

URL-adresser til at åbne i proxy-server |
--- |
\*. microsoftonline.com |
\*. gov.us.microsoftonline.com |
+ Lister over tilbagekaldte certifikater af certifikat |

Azure AD-forbindelse kan ikke automatisk registrerer, at din Azure AD-mappe er placeret i skyen Government. I stedet skal du udføre følgende handlinger, når du installerer Azure AD-forbindelse.

1. Start installationen af Azure AD-forbindelse.
2. Så snart du kan se den første side, hvor du skal acceptere den slutbrugerlicensaftale (EULA), skal du ikke fortsætte, men lade installationsguiden kører.
3. Start regedit og ændre nøglen i registreringsdatabasen `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` til værdien `2`.
4. Gå tilbage til installationsguiden af Azure AD-forbindelse, acceptere den slutbrugerlicensaftale (EULA) og fortsætte. Sørg for at bruge den **brugerdefinerede konfiguration** installationssti (og ikke standardinstallation) under installationen. Fortsæt derefter installationen på sædvanlig vis.

Funktioner, der er i øjeblikket ikke til stede i Microsoft Azure Government skyen:

- Azure AD forbinde tilstand er ikke tilgængelig.
- Automatiske opdateringer er ikke tilgængelig.
- Adgangskode tilbageførsel er ikke tilgængelig.

## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).

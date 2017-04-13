<properties
    pageTitle="Azure AD Connect i Microsoft Cloud Tyskland"
    description="Azure AD-forbindelse integrere dine lokale mapper med Azure Active Directory. Dette kan du give en fælles identitet for Office 365, Azure og SaaS programmer, der er integreret med Azure AD."
    keywords="Introduktion til Azure AD-forbindelse, Azure AD-forbindelse oversigt over, hvad er Azure AD-forbindelse, installere active directory, Tyskland, sort skov"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/08/2016"
    ms.author="billmath"/>

#<a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect i Microsoft Cloud Tyskland - prøveversionen

## <a name="introduction"></a>Introduktion
Azure AD-forbindelse indeholder synkroniseringen mellem lokale Active Directory og Azure Active Directory.
Mange af scenarierne i [Microsoft Cloud Tyskland](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) udføres i øjeblikket af operatoren. Når du bruger Microsoft Cloud Tyskland, skal du være opmærksom på følgende:


- De følgende URL-adresser skal åbnes på en proxyserver til synkronisering skal udføres korrekt:
    - *. microsoftonline.de
    - *. windows.net
    - + Lister over tilbagekaldte

- Når du logger på din Azure AD-mappe, skal du bruge en konto i onmicrosoft.de domænet.
- Følgende funktioner er ikke tilgængelige:
    - Azure AD Connect tilstand
    - Automatiske opdateringer
    - Adgangskode tilbageførsel

## <a name="download"></a>Download
Du kan hente Azure AD-forbindelse fra bladet Azure AD-forbindelse i portalen.  Brug vejledningen nedenfor for at finde bladet Azure AD-forbindelse.

### <a name="the-azure-ad-connect-blade"></a>Azure AD Connect Blade

Når du har logget på portalen Azure skal du gøre følgende:

1. Gå til Gennemse
2.  Vælg Azure Active Directory
3.  Vælg derefter Azure AD-forbindelse

Du burde se følgende:

![Azure AD Connect Blade](media\active-directory-aadconnect-germany\germany1.png)

 
I følgende tabel beskrives de funktioner, der vises i bladet.


Titel|Beskrivelse|
----- | ----- |
STATUS FOR SYNKRONISERING|Lad os vide du om synkronisering er aktiveret eller deaktiveret.|
SENESTE SYNKRONISERING|Sidste gang en vellykket synkronisering fuldført.|
DOMÆNER|Viser antallet af domæner konfigureret i øjeblikket.|


## <a name="installation"></a>Installation
Hvis du vil installere Azure AD-forbindelse, kan du bruge dokumentationen [her](active-directory-aadconnect.md#install-azure-ad-connect).

## <a name="advanced-features-and-additional-information"></a>Avancerede funktioner og yderligere oplysninger
For yderligere oplysninger og retningslinjer for brugerdefinerede indstillinger eller avancerede konfigurationer, start med [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).  Denne side indeholder oplysninger og links til yderligere vejledning.

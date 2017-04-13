<properties
    pageTitle="Azure AD og programmer: lede udviklere | Microsoft Azure"
    description="Skrevet for IT-fagfolk, får i denne artikel du retningslinjer for at integrere Azure-programmer med Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-and-applications-develop-line-of-business-apps"></a>Azure AD og programmer: udvikle line of business apps

Denne vejledning giver et overblik over udviklingsprogrammer line of business (LoB) til Azure Active Directory (AD). Målgruppen er global Active Directory/Office 365-administratorer.

## <a name="overview"></a>Oversigt

Lave integreret med Azure AD-programmer giver brugerne i din organisation single sign-on – med Office 365. Har programmet i Azure AD giver dig kontrol over politikken godkendelse for programmet. Se [konfiguration af adgangsregler](active-directory-conditional-access-azuread-connected-apps.md)for at få flere oplysninger om betinget adgang og hvordan du beskytter apps med Multi-Factor authentication (MFA).

Registrere dit program tilladelse til at bruge Azure Active Directory. Registrering af programmet betyder, at din udviklere kan bruge Azure AD til at godkende brugere og anmode om adgang til ressourcer, der bruger som mail, kalender og dokumenter.

Et medlem af mappen (ikke gæster) kan registrere et program, også kendt som *oprettelse af et objekt i programmet*.

Registrering af et program en hvilken som helst giver brugeren mulighed at gøre følgende:

- Få en identitet til deres program, der Azure AD genkender
- Få en eller flere hemmeligheder/taster, programmet kan bruges til at godkende sig selv AD
- Tilpasse programmet i Azure-portalen med et brugerdefineret navn, logo, osv.
- Anvende Azure AD godkendelse funktioner for deres Apps, herunder:
  - Rollebaseret adgangskontrol (RBAC)
  - Azure Active Directory som oAuth godkendelse server (secure en API, der vises af programmet)

- Erklære nødvendige tilladelser det er nødvendigt for programmet kan fungere som forventet, herunder:-App-tilladelser (kun globale administratorer). For eksempel: rollemedlemskab af en anden Azure AD-program eller rolle medlemskab i forhold til en Azure ressource, ressourcegruppe, eller et andet abonnement - delegeret tilladelser (alle brugere). For eksempel: Azure AD-logon, og Læs profil


> [AZURE.NOTE]Alle medlemmer kan registrere et program som standard. Hvis du vil lære at begrænse tilladelser til at registrere programmer til bestemte medlemmer, skal du se [hvordan programmer føjes til Azure AD](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Her er, hvad du som global administrator skal gøre for at hjælpe udviklere med at foretage deres program er du klar til fremstilling:

- Konfigurere adgangsregler (access politik/MFA)
- Konfigurere app'en for at kræve tildelingen bruger og tildele brugere
- Skjule standard samtykke brugeroplevelsen

## <a name="configure-access-rules"></a>Konfigurere adgangsregler

Konfigurere adgangsregler for hver programmet til dine SaaS apps. Du kan for eksempel kræver MFA eller kun tillader adgang til brugere på netværk, der er tillid til. Oplysninger om dette er tilgængelige i dokumentet [konfiguration af adgangsregler](active-directory-conditional-access-azuread-connected-apps.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfigurere app'en for at kræve tildelingen bruger og tildele brugere

Brugere har adgang til programmer uden at være tildelt som standard. Hvis programmet Fremviser roller, eller hvis du vil programmet vises på en brugers adgang panel, bør du kræver tildelingen bruger.

[Kræve bruger tildeling](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Hvis du har en Azure AD Premium- eller Enterprise mobilitet pakke (EMS) abonnement, anbefales det ved hjælp af grupper. Tildele grupper til programmet, kan du uddelegere administration for at få vedvarende adgang at ejeren af gruppen. Du kan oprette en gruppe eller Spørg ansvarlig deltagere i din organisation til at oprette gruppen ved hjælp af din gruppe management facilitet.

[Tildele brugere til et program](active-directory-applications-guiding-developers-assigning-users.md)  
[Tildele grupper til et program](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Skjule bruger samtykke

Som standard gennemgår hver bruger en samtykke oplevelse til at logge på. Den samtykke oplevelse bede brugere om at give tilladelser til et program, kan være disconcerting for brugere, der ikke har kendskab til at foretage sådanne beslutninger.

For programmer, som du har tillid til, kan du forenkle brugeroplevelsen ved accepterer vilkår for anvendelse på vegne af din organisation.

Du kan finde flere oplysninger om bruger samtykke og samtykke oplevelse i Azure [Integrering af programmer med Azure Active Directory](active-directory-integrating-applications.md).

##<a name="related-articles"></a>Relaterede artikler

- [Aktivere sikker fjernadgang til lokale programmer med Azure AD-proxyen](active-directory-application-proxy-get-started.md)
- [Azure betinget Access Preview til SaaS Apps](active-directory-conditional-access-azuread-connected-apps.md)
- [Adgangskontrol til apps med Azure AD](active-directory-managing-access-to-apps.md)
- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

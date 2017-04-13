<properties
   pageTitle="Integration af Azure Active Directory med programmer få Introduktion til |  Microsoft Azure"
   description="I denne artikel er en introduktion få vejledning til integration af Azure Active Directory (AD) med lokale programmer og skyen programmer."
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="femila"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="inhenk"/>

# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integration af Azure Active Directory med programmer få Introduktion til
## <a name="overview"></a>Oversigt
Dette emne er beregnet til at give dig en vejledning til integration af programmer med Azure Active Directory (AD). Hver af de nedenstående afsnit indeholder en kort oversigt over et mere detaljeret emne, så du kan identificere, hvilke dele af denne introduktion få vejledning, der er relevante for dig.  Du kan følge links til en dybere få på hvert emne.

## <a name="before-you-begin-take-inventory"></a>Før du begynder, oprette en oversigt
Før du springe i til integrering af programmer med Azure AD, er det vigtigt at vide, hvor du befinder dig, og hvor du vil gå.  Følgende spørgsmål er beregnet til at hjælpe dig med at synes om projektet Azure AD-program integration.

### <a name="application-inventory"></a>Programmet lager
- Hvor er alle dine programmer? Hvem ejer dem?
- Hvilken type godkendelse kræver dine programmer?
- Hvem skal have adgang til hvilke programmer?
- Vil du installere en ny program?
  - Vil du oprette den interne og installere den på en Azure Beregn forekomst?
  - Skal du bruge en, der er tilgængelige i galleriet Azure-program?

### <a name="user-and-group-inventory"></a>Bruger- og lager
- Hvor bor dine brugerkonti?
 - Lokale Active Directory
 - Azure AD
 - I en separat programdatabase, du ejer
 - I unsanctioned programmer
 - Alle ovenstående
- Hvilke tilladelser og rolletildelinger individuelle brugere har i øjeblikket? Har du brug for at gennemse deres adgang eller er du sikker på, at brugeren adgang og rolle tildelingerne er relevante nu?
- Grupper allerede oprettes i din lokale Active Directory?
 - Hvordan organiseres dine grupper?
 - Hvem er medlemmerne i gruppen?
 - Hvilke tilladelser/rolletildelinger grupperne har i øjeblikket?
- Vil du brug at rydde op i bruger/gruppe databaser før integrering af?  (Dette er et ret vigtige spørgsmål. Garbage i garbage ud).

### <a name="access-management-inventory"></a>Access management lager
- Hvordan du aktuelt administrerer brugeradgang til programmer? Skal, ændre?  Har du overvejet andre måder at administrere adgang, f.eks, med [RBAC](role-based-access-control-configure.md) for eksempel?
- Hvem skal have adgang til hvad?

Måske du har ikke svarene på alle disse spørgsmål på forhånd, men det er OK.  Denne vejledning kan hjælpe dig med at besvare nogle af disse spørgsmål og foretage nogle rigtige beslutninger.

## <a name="prerequisites"></a>Forudsætninger
- Et Azure-abonnement og en Azure Active Directory-katalogtjeneste.  Hvis du ikke allerede har et Azure-abonnement, kan du prøve Azure gratis i 30 dage. [Prøv det!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Programmet integration med Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Finde unsanctioned skyen programmer med skyen App registrering
Som nævnt ovenfor, kan der være programmer, der ikke har været administreres af din organisation indtil nu.  Det er muligt at finde unsanctioned skyen programmer som en del af processen lager. Se [finde unsanctioned skyen programmer med skyen App registrering](active-directory-cloudappdiscovery-whatis.md).

### <a name="authentication-types"></a>Godkendelsestyper
Hver af dine programmer kan have forskellige godkendelseskrav. Med Azure AD kan logge certifikater bruges med programmer, der bruger SAML 2.0, WS-sammenslutning, eller OpenID forbinde protokoller samt adgangskode Single Sign-On. Finde flere oplysninger om programmet godkendelsestyper til brug sammen med Azure AD [Administrere certifikater for samlet Single Sign-On i Azure Active Directory](active-directory-sso-certs.md) og [adgangskode baseret enkelt Log på](active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Aktivere SSO med Azure AD App-Proxy
Med Microsoft Azure AD-proxyen, kan du give adgang til programmer, der findes i dit private netværk sikkert, hvor som helst og på en hvilken som helst enhed. Når du har installeret et program proxy forbindelse i dit miljø, kan det nemt konfigureret med Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Integration af programmer med Azure AD
De følgende artikler omhandler de forskellige måder programmer integrere med Azure AD, og nogle vejlede.

- [Afgøre, hvilke Active Directory for at bruge](active-directory-administer.md)
- [Brug af programmer i galleriet Azure-program](active-directory-appssoaccess-whatis.md)
- [Integrering af listen over SaaS programmer selvstudier](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Adgangskontrol til programmer
I følgende artikler beskrives måder, du kan administrere adgang til programmer, når de er blevet integreret med Azure AD ved hjælp af Azure AD-forbindelser og Azure AD.

- [Adgangskontrol til ved hjælp af Azure AD-apps](active-directory-managing-access-to-apps.md)
- [Automatisere med Azure AD-forbindelser](active-directory-saas-app-provisioning.md)
- [Tildele brugere til et program](active-directory-applications-guiding-developers-assigning-users.md)
- [Tildele grupper til et program](active-directory-applications-guiding-developers-assigning-groups.md)
- [Deling af konti](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Integrering af brugerdefinerede programmer
Hvis du skriver et nyt program og vil hjælpe udviklere med at udnytte power Azure AD, skal du se [Guiding udviklere](active-directory-applications-guiding-developers-for-lob-applications.md).

Hvis du vil tilføje dit brugerdefinerede program til galleriet Azure-program, skal du se ["Tage dine egne app" med Azure AD selvbetjening SAML konfiguration](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

## <a name="see-also"></a>Se også

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

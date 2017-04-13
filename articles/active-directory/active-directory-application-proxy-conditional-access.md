<properties
    pageTitle="Betinget adgang til programmer, der publiceres med Azure AD-proxyen"
    description="Omhandler, hvordan du konfigurerer betinget adgang til programmer, du publicerer for at få adgang til fra en fjernplacering via Azure AD-proxyen."
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
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-conditional-access"></a>Arbejde med betinget adgang

Du kan konfigurere adgangsregler for at give betinget adgang til programmer, der er publiceret ved hjælp af proxyen. Dette kan du:

- Kræve godkendelse i flere niveauer i programmet på computeren
- Kræve godkendelse i flere niveauer, kun, når brugerne ikke er på arbejde
- Blokere brugere får adgang til programmet, når de ikke er på arbejde

Disse regler kan anvendes til alle brugere og grupper eller kun til bestemte brugere og grupper. Som standard gælder reglen for alle brugere, der har adgang til programmet. Reglen kan dog også være begrænset til brugere, der er medlemmer af angivne sikkerhedsgrupper.  

Adgangsregler evalueres, når en bruger har adgang til en samlet program, der bruger OAuth 2.0, OpenID Opret forbindelse, SAML eller WS-sammenslutning. Desuden evalueres adgangsregler med OAuth 2.0 og OpenID oprette forbindelse, når en opdatering token bruges til at få fat på et adgangstoken.

## <a name="conditional-access-prerequisites"></a>Forudsætninger, der er betinget adgang

- Azure Active Directory Premium-abonnement
- Et medlem af organisationsnetværk eller administreret Azure Active Directory-lejer
- Samlet lejere kræver, at Multi-Factor godkendelse (MFA) være aktiveret  
    ![Konfigurere adgangsregler - kræver godkendelse i flere niveauer](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Konfigurere per programmet Multi-Factor authentication
1. Log på som administrator på portalen Azure klassisk.
2. Gå til Active Directory, og vælg den mappe, hvor du vil aktivere proxyen.
3. Klik på **programmer** , og Rul ned til sektionen **Adgangsregler** . Sektionen adgang til regler vises kun for programmer, der er publiceret ved hjælp af proxyen, der bruger samlet godkendelse.
4. Aktivér reglen ved at vælge **Aktivér adgangsregler** til **til**.
5. Angive de brugere og grupper, som reglerne, der skal gælde. Brug knappen **Tilføj gruppe** for at vælge en eller flere grupper, access reglen skal gælde for. Denne dialogboks kan også bruges til at fjerne markerede grupper.  Når reglerne, der er valgt til at anvende til grupper, gennemtvinges på adgangsregler kun for brugere, der tilhører en af de angivne sikkerhedsgrupper.  

  - Kontrollere **undtagen** for at udelade eksplicit sikkerhedsgrupper fra reglen, og Angiv en eller flere grupper. Brugere, der er medlemmer af en gruppe på listen Except skal ikke at udføre Multi-Factor godkendelse.  

  - Hvis en bruger blev konfigureret ved hjælp af funktionen hver bruger godkendelse i flere niveauer, kan denne indstilling tilsidesætter programmet Multi-Factor authentication regler. Det betyder, at en bruger, der er blevet konfigureret til hver bruger Multi-Factor authentication blive bedt om at udføre Multi-Factor authentication, selvom de er blevet fritaget programmets Multi-Factor authentication regler. Lær mere om [multi-Factor authentication og indstillinger for hver bruger](../multi-factor-authentication/multi-factor-authentication.md).

6. Vælg den access-regel, du vil angive:
    - **Kræver Multi-Factor authentication**: brugere, som access regler i kraft vil blive bedt om at fuldført Multi-Factor authentication før at få adgang til det program, som reglen gælder.
    - **Kræver Multi-Factor authentication når de ikke er på arbejde**: brugere forsøger at få adgang til programmet fra en pålidelig IP-adresse ikke forpligtet til at udføre Multi-Factor authentication. Der er tillid til IP-adresseområder kan konfigureres på siden Multi-Factor authentication indstillinger.
    - **Bloker adgang, når de ikke er på arbejde**: brugere forsøger at få adgang til programmet fra uden for virksomhedens netværk vil ikke kunne få adgang til programmet.


## <a name="configuring-mfa-for-federation-services"></a>Konfigurere MFA til federation services
Multi-Factor authentication (MFA) til medlem af organisationsnetværket lejere kan udføres af Azure Active Directory eller af lokale AD FS server. Som standard opstår MFA på en side, der er hostet af Azure Active Directory. For at konfigurere MFA lokalt skal køre Windows PowerShell og bruge egenskaben – SupportsMFA til at angive Azure AD-modulet.

I følgende eksempel viser, hvordan du aktiverer lokale MFA ved hjælp af [sæt MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) på contoso.com-lejeren:`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Ud over at angive dette flag, skal være konfigureret organisationsnetværket lejer AD FS forekomst for at udføre Multi-Factor authentication. Følg instruktionerne til [installation af Microsoft Azure Multi-Factor godkendelse i det lokale miljø](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).


## <a name="see-also"></a>Se også

- [Arbejde med krav opmærksom på programmer](active-directory-application-proxy-claims-aware-apps.md)
- [Udgive programmer med proxyen](active-directory-application-proxy-publish.md)
- [Aktivere enkelt Log på](active-directory-application-proxy-sso-using-kcd.md)
- [Publicere programmer, der bruger dit eget domænenavn](active-directory-application-proxy-custom-domains.md)

Se [proxyen blog](http://blogs.technet.com/b/applicationproxyblog/) for de seneste nyheder og opdateringer

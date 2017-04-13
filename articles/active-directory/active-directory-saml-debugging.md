<properties 
    pageTitle="Sådan foretages fejlfinding SAML-baseret single sign-on til programmer i Azure Active Directory | Microsoft Azure" 
    description="Lær, hvordan du udfører fejlfinding SAML-baseret single sign-on til programmer i Azure Active Directory " 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Sådan foretages fejlfinding SAML-baseret single sign-on til programmer i Azure Active Directory

Det er ofte praktisk at bruge et værktøj som [Fiddler](http://www.telerik.com/fiddler) til at se SAML anmodningen, SAML svaret og faktisk SAML tokenet, der er udstedt til programmet, når du foretager fejlfinding en SAML-baseret programintegration. Ved at undersøge SAML tokenet, kan du sikre dig, at alle de nødvendige attributter, brugernavn i emnet SAML og udsteder URI kommer, som forventet.

![][1]

Svar fra Azure AD, der indeholder SAML Tokenet er typisk den, der sker, når en HTTP 302 omdirigering fra https://login.windows.net, og der sendes til konfigurerede **Svar URL-adressen** på programmet. 
 
Du kan få vist SAML tokenet ved at vælge linjen og derefter vælge den **inspektioner > WebForms** fane i højre panel. Højreklik på værdien **SAMLResponse** derfra, og vælg **Send til TextWizard**. Vælg derefter **Fra Base64** i menuen **transformere** at afkode tokenet og se indholdet.
 
**Bemærk**: Hvis du vil se indholdet af denne HTTP-anmodning, Fiddler bede dig om at konfigurere dekryptering af HTTPS-trafikken, som du har brug for at gøre.

## <a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Konfiguration af single sign-on til programmer, der ikke er i galleriet Azure Active Directory-program](active-directory-saas-custom-apps.md)
- [Hvordan du tilpasser krav udstedt i SAML Token for allerede integrerede Apps](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png

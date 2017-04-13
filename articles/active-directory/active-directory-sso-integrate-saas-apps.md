<properties
    pageTitle="Integrere Azure Active Directory single sign-on – med SaaS apps |  Microsoft Azure"
    description="Aktivere enkelt sign-on – godkendelse og bruger klargøring centralt access styring af SaaS apps i Azure Active Directory. En oversigt over, hvordan du integrerer Azure Active Directory til SaaS apps."
    services="active-directory"
      keywords="integrere Azure AD med SaaS apps"
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
    ms.date="09/30/2016"
    ms.author="curtand"/>

# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Integrere Azure Active Directory single sign-on – med SaaS apps  

> [AZURE.SELECTOR]
- [Azure-portalen](active-directory-enterprise-apps-manage-sso.md)
- [Azure klassisk portal](active-directory-sso-integrate-saas-apps.md)

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

For at komme i gang at konfigurere single sign-on til en app, som du bringer i din organisation, kan du bruge en eksisterende mappe i Azure Active Directory (Azure AD). Du kan bruge en Azure AD-mappe, der får du via Microsoft Azure-, Office 365- eller Windows Intune. Hvis du har to eller flere af disse, kan du se [administrere din Azure AD-mappe](active-directory-administer.md) til at bestemme, hvilket der skal bruges.

## <a name="authentication"></a>Godkendelse

For programmer, der understøtter SAML 2.0, WS-sammenslutning, eller OpenID forbinde protokoller, Azure Active Directory bruger logge certifikater til at etablere relationer sikkerhed og rettighedsadministration. Du kan finde flere oplysninger om dette, [administrere certifikater til medlem af organisationsnetværket single sign-on](active-directory-sso-certs.md).

For programmer, der understøtter kun HTML formularbaseret logon, Azure Active Directory anvender 'adgangskode større' til at etablere relationer sikkerhed og rettighedsadministration. Dette gør det muligt for brugerne i organisationen at være automatisk logget på et SaaS program ved Azure AD ved hjælp af brugerkontooplysninger fra programmet SaaS. Azure AD indsamler og gemmer sikkert oplysninger om brugerkonto og relaterede adgangskoden. Se [adgangskode-baserede single sign-on –](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)du kan finde flere oplysninger.

## <a name="authorization"></a>Godkendelse

En klargjort konto kan en bruger have tilladelse til at bruge et program, når de har godkendt via enkeltlogon. Brugerklargøring af kan udføres manuelt eller i nogle tilfælde kan du tilføje og fjerne brugeroplysninger fra SaaS app baseret på ændringer i Azure Active Directory. Du kan finde flere oplysninger om brug af eksisterende Azure AD-forbindelser til klargøring af automatiseret, [automatiseret bruger klargøring og deaktivere klargøring til SaaS programmer](active-directory-saas-app-provisioning.md).

Ellers skal kan du manuelt føje brugeroplysninger til en app eller bruge andre klargøring løsninger, der er tilgængelige på markedet.

## <a name="access"></a>Access

Azure AD kan tilpasses på flere måder at implementere programmer til slutbrugerne i organisationen. Du er ikke låst til en bestemt installation eller access-løsning. Du kan bruge [løsningen, der bedst opfylder dine behov](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Yderligere overvejelser i forbindelse med programmer allerede brug i

Konfiguration af enkelt Log på i et program, der bruges i din organisation allerede er en anden proces fra processen med at oprette nye konti for et nyt program. Der er et par foreløbige trin, herunder: tilknytning bruger-id'er i programmet til Azure AD identiteter og forstå, hvordan brugerne opleve logføring i et program, når det er integreret.

> [AZURE.NOTE] Hvis du vil konfigurere SSO til et eksisterende program, du vil have globale administratorrettigheder i begge Azure AD og SaaS programmet på computeren.

### <a name="mapping-user-accounts"></a>Tilknytning af brugerkonti

En bruger-id har normalt et entydigt id, der kan være en mailadresse eller brugerens hovednavn (UPN). Du skal link (kort) hver bruger program-id til deres respektive Azure AD identitet. Der er nogle metoder til at udføre dette afhængigt af hvordan kravet om godkendelse af programmet.

Se [tilpasse krav udstedt i SAML tokenet](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) og [tilpasse attributtilknytninger til klargøring af](active-directory-saas-customizing-attribute-mappings.md)kan finde flere oplysninger om tilknytning programmet identiteter med Azure AD identiteter.

### <a name="understanding-the-users-log-in-experience"></a>Forstå oplevelse brugerens logon

Når du integrerer SSO for et program, der allerede er i brug, er det vigtigt at opdager, at brugeroplevelsen påvirkes. For alle programmer, kan brugerne begynder at bruge deres Azure AD-legitimationsoplysninger til at logge på. Det kan også være, at de skal bruge en anden portal til at få adgang til programmerne.

SSO for visse programmer kan udføres på programmets log i brugergrænsefladen, men til andre programmer, får brugeren til at gennemgå en central portal som ([Mine Apps](http://myapps.microsoft.com) eller [Office365](http://portal.office.com/myapps)) til at logge på. Lær mere om de forskellige typer SSO og deres brugeroplevelser i [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

En anden værdifuld ressource er *Suppressing bruger samtykke* i artiklen [Guiding udviklere](active-directory-applications-guiding-developers-for-lob-applications.md) .

## <a name="next-steps"></a>Næste trin


Til SaaS apps, som du kan finde i galleriet App, indeholder Azure AD en række [selvstudier om, hvordan du integrerer SaaS apps](active-directory-saas-tutorial-list.md).

Hvis app ikke er i App-galleri, kan du [føje den til galleriet Azure AD App som et brugerdefineret program](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Der er meget mere detaljeret på alle disse problemer i biblioteket Azure.com begynder med [Hvad er adgang til programmer og single sign-on med Azure Active Directory.](active-directory-appssoaccess-whatis.md).

## <a name="see-also"></a>Se også

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

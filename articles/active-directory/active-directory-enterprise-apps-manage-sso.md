<properties
    pageTitle="Single sign-on – administration for enterprise-apps i Azure Active Directory preview | Microsoft Azure"
    description="Lær, hvordan du administrere enkelt Log på for enterprise apps ved hjælp af Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="asmalser"/>

# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>Eksempel: Administrere single sign-on til virksomhedsapps i den nye Azure portal

> [AZURE.SELECTOR]
- [Azure-portalen](active-directory-enterprise-apps-manage-sso.md)
- [Azure klassisk portal](active-directory-sso-integrate-saas-apps.md)

I denne artikel beskrives, hvordan du bruger [Azure portal](https://portal.azure.com) til at administrere enkelt enkeltlogon-indstillingerne for programmer, især dem, der er føjet fra [Azure Active Directory (Azure AD) programmet galleriet](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Azure AD management oplevelse til single sign-on er i øjeblikket i prøveversionen, og i denne artikel beskrives de nye funktioner samt nogle midlertidige begrænsninger, der vil være placeret kun i perioden, preview. [Hvad er i preview?](active-directory-preview-explainer.md)

##<a name="finding-your-apps-in-the-new-portal"></a>Finde dine apps i den nye portal

Og September 2016, kan alle programmer, der er konfigureret for enkelt enkeltlogon i en mappe, som administrator af en mappe ved hjælp af [Azure Active Directory-program galleriet](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) i [Azure klassisk portal](https://manage.windowsazure.com), nu vises og administreres i portalen Azure.

Disse programmer kan findes i afsnittet **Virksomhedens programmer** i portalen Azure, et link, kan findes på listen **Flere tjenester** i [portalen](https://portal.azure.com). Virksomhedsapps er apps, der er implementeret og bliver brugt af brugere i organisationen.

![Virksomhedens programmer blade][1]

Vælg **alle programmer** til at få vist en liste over alle apps, der er konfigureret, herunder apps, der er blevet tilføjet i galleriet. Hvis du vælger en app indlæser bladet ressource for den app, hvor rapporter kan ses for denne app og en række indstillinger kan administreres.

For at administrere enkelt sign-on – indstillinger, skal du vælge **Single sign-on**.

![Programmet ressource blade][2]


##<a name="single-sign-on-modes"></a>Enkelt sign-on – funktionsmåder

Bladet **Single sign-on** begynder med en **tilstand** -menu, der gør det muligt for enkelt sign-on – tilstanden skal konfigureres. De tilgængelige indstillinger omfatter:

* **SAML-baseret sign-on** – denne indstilling er tilgængelig, hvis programmet, der understøtter fuld organisationsnetværket single sign-on – med Azure Active Directory ved hjælp af SAML 2.0-protokollen.

* **Adgangskode-baserede sign-on** – denne indstilling er tilgængelig, hvis Azure AD understøtter adgangskode formular udfylde for dette program.

* **Sammenkædet Log på** - tidligere kendt som "Eksisterende single sign-on", denne indstilling giver administratorer mulighed for at placere et link til dette program i deres brugerens Azure AD Access Panel eller Office 365-program appstarteren.

Finde flere oplysninger om disse tilstande [hvordan single sign-on – med Azure Active Directory arbejde](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).


##<a name="saml-based-sign-on"></a>SAML-baseret sign-on

Indstillingen **SAML-baseret sign-on** viser en blade, der er inddelt i fire sektioner:

###<a name="domains-and-urls"></a>Domæner og URL-adresser
Dette er, hvor alle oplysninger om programmets domæne og URL-adresser er føjet til din Azure AD-mappe. Alle input, der kræves for at gøre enkelt sign-on – arbejde app vises direkte på skærmen, mens alle valgfrit input kan ses ved at markere afkrydsningsfeltet **Vis avancerede indstillinger for URL-adresse** . Den komplette liste over understøttede input indeholder:

* **Log på URL-adressen** – som hvor brugeren går til logon til dette program. Hvis programmet er konfigureret til at udføre service provider-definerede enkelt Log på, derefter, når en bruger navigerer til denne URL-adresse, understøtter udbyderen af de nødvendige omdirigering til Azure AD til at godkende og logonskærm brugeren. Hvis dette felt er udfyldt, derefter Azure AD skal bruge denne URL-adresse for at starte programmet fra Office 365 og panelet Azure AD-adgang. Hvis dette felt udelades, er Azure AD i stedet udfører identitetsudbyder-startet sign-on når appen startes fra Office 365, panelet Azure AD adgang eller fra Azure AD single sign-on – URL-adresse.

* **Id** - denne URI skal entydigt identificerer programmet, for hvilke enkelt logge er konfigureret. Dette er den værdi, der sender Azure AD tilbage til anvendelse som parameteren målgruppe af SAML tokenet, og der forventes programmet til at validere det. Denne værdi vises også som enheds-ID i en hvilken som helst SAML metadata, der leveres af programmet.

* **Svar URL** - svar URL-adressen er, hvor programmet forventer at modtage SAML tokenet. Dette kaldes også program Consumer ACS (Service) URL-adressen. Når disse er indsat, skal du klikke på Næste for at gå videre til næste skærmbillede. Dette skærmbillede indeholder oplysninger om, hvad der skal konfigureres i programmet side til at aktivere den til at acceptere et SAML token fra Azure AD.

* **Relay tilstand** - relay-tilstand er en valgfri parameter, der kan hjælpe dig med at se programmet, til at omdirigere brugeren efter godkendelse er fuldført. Typisk værdien er en gyldig URL-adresse på programmet, men nogle programmer bruger dette felt anderledes (se den app enkelt Log på dokumentation få mere at vide). Muligheden for at angive relay tilstanden er en ny funktion, der er entydige for den nye Azure portal.

###<a name="user-attributes"></a>Brugerattributter
Dette er hvor administratorer kan få vist og redigere de attributter, der sendes i SAML tokenet, som Azure AD udsteder til programmet hver gang brugerne logge på.

For første preview-versionen er attributten kun redigerbare understøttes attributten **Bruger-id** . Værdien af denne attribut er feltet i Azure AD, der entydigt identificerer hver enkelt bruger i programmet. Eksempelvis hvis appen er installeret ved hjælp af "mail-adresse" som det brugernavn og entydigt id, skal derefter værdien angives i feltet "user.mail" i Azure AD.

Redigering af yderligere attributter der understøttes i et efterfølgende eksempel.

###<a name="saml-signing-certificate"></a>SAML signeringscertifikat
I dette afsnit vises detaljerne for det certifikat, der Azure AD bruger til at signere SAML tokens, der er udstedt til det program, hver gang brugeren godkender. Det kan egenskaberne for det aktuelle certifikat undersøges, herunder udløbsdatoen.

Muligheden for at overgang certifikatet og administrere flere certifikat indstillinger vil være understøttet i en efterfølgende preview-versionen. Bemærk, at fuld administration af certifikater stadig kan udføres i [Azure klassisk portal](active-directory-sso-certs.md).

###<a name="application-configuration"></a>Programkonfiguration
Den endelige sektion indeholder dokumentation og/eller kontrolelementer, der kræves for at konfigurere selve programmet bruge Azure Active Directory som identitetsudbyder.

**Konfigurere programmets** pop op-menu indeholder en ny præcis, integrerede vejledning til konfiguration af programmet. Dette er en anden ny funktion, der er entydige for den nye Azure portal.

> [AZURE.NOTE] Du kan finde en komplet eksempel på integreret dokumentation Salesforce.com programmet. Dokumentation til flere apps er føjes kontinuerligt under Vis udskrift.

![Integrerede dokumenter][3]

##<a name="password-based-sign-on"></a>Adgangskode-baserede sign-on
Hvis understøttes for programmet, konfigurerer at vælge tilstanden adgangskode-baserede SSO og vælge **Gem** med det samme den for at gøre adgangskode-baserede SSO. Finde flere oplysninger om installation af adgangskode-baserede SSO [hvordan single sign-on – med Azure Active Directory arbejde](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Adgangskode-baserede sign-on][4]


##<a name="linked-sign-on"></a>Sammenkædede sign-on
Hvis understøttes for programmet, kan du skal vælge den sammenkædede SSO-tilstand du angive URL-adresse, du vil Azure AD Access Panel eller Office 365 for at omdirigere til, når brugerne klikker på denne app. Finde flere oplysninger om sammenkædede SSO (tidligere kaldet "eksisterende SSO"), [hvordan single sign-on – med Azure Active Directory arbejde](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Sammenkædede enkeltlogon][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG

<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Facebook på arbejdet | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Facebook på arbejdet."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-facebook-at-work"></a>Selvstudium: Azure Active Directory-integration med Facebook på arbejde

Formålet med dette selvstudium er at se, hvordan du integrerer Facebook på arbejdet med Azure Active Directory (Azure AD).

Integrere Facebook på arbejdet med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, der har adgang til Facebook på arbejde 
- Du kan automatisk klargøre konto for brugere, der har fået adgang til Facebook på arbejde
- Du kan aktivere dine brugere til automatisk få logget på Facebook på arbejdet (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted 

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Forudsætninger 

Hvis du vil konfigurere Azure AD-integration med CS stjerner, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Facebook på arbejdet med single sign-on aktiveret

Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 


## <a name="adding-facebook-at-work-from-the-gallery"></a>Tilføje Facebook på arbejde fra galleriet
Hvis du vil konfigurere integration af Facebook på arbejdet i Azure AD, skal du tilføje Facebook på arbejde fra galleriet til listen over administrerede SaaS apps.

**For at tilføje Facebook på arbejde fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.
    
    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Facebook på arbejdet**i søgefeltet.

7. Vælg **Facebook på arbejdet**i resultatruden, og klik derefter på **udført** for at tilføje programmet.


### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Dette afsnit beskrives, hvordan du aktiverer brugere skal godkendes til Facebook på arbejdet med deres konto i Azure Active Directory, ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

**Hvis du vil konfigurere Azure AD single sign-on med Facebook på arbejdet, kan du udføre følgende trin:**

1.  Når du har tilføjet Facebook på arbejdet i portalen Azure klassisk, skal du klikke på **Konfigurere Single Sign-On**.

2.  På skærmbilledet **Konfigurer App URL-adresse** Angiv URL-adressen, hvor brugerne kan logge på din Facebook på arbejde programmet. Dette er din Facebook på arbejde lejer URL-adresse (eksempel: https://example.facebook.com/). Når færdig, skal du klikke på **Næste**.

3.  Log på din Facebook på arbejde virksomheds websted som en administrator i et andet browservindue.

4. Følg vejledningen på følgende URL-adressen for at konfigurere Facebook på at kunne bruge Azure AD som identitetsudbyder: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.  Når færdige, vende tilbage til de browservinduer, der viser Azure klassisk portalen, klik på afkrydsningsfeltet for at bekræfte, at du har fuldført proceduren og derefter klikke på **Næste** og **færdigt**.


## <a name="automatically-provisioning-users-to-facebook-at-work"></a>Automatisk klargøring af brugere til Facebook på arbejde

Azure AD understøtter muligheden for at automatisk synchonize kontooplysninger over tildelte brugere til Facebook på arbejdet. Denne automatisk sychronization aktiverer Facebook på at kunne hente de data, der skal godkende brugere adgang, på forhånd for dem forsøger at logge på for første gang. Det også Klargør deaktivere brugere fra Facebook på arbejdet når access er tilbagekaldt i Azure AD.

Kan konfigureres automatisk klargøring ved at klikke på **Konfigurer konto klargøring** i Azure klassisk portalen vinduet.

Få mere at vide om, hvordan du konfigurerer automatisk klargøring se [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## <a name="assigning-users-to-facebook-at-work"></a>Tildele brugere til Facebook på arbejde

For klargjort AAD brugere skal kunne se Facebook på arbejdet på deres Access Panel, skal de være tildelt adgang i portalen Azure klassisk.

**Tildele brugere til Facebook på arbejdet:**

1.  Klik på Startside til Facebook på arbejdet i portalen Azure klassisk, **Tildel konti**.

2.  Vælg, om du vil tildele en bruger eller en gruppe til Facebook på arbejdet, og klik på knappen markering i menuen **Vis** .

3.  Vælg de brugere eller gruppe, som du vil tildele Facebook på arbejdet på den viste liste.

4.  I sidefoden, skal du klikke på knappen **tildele** .


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png





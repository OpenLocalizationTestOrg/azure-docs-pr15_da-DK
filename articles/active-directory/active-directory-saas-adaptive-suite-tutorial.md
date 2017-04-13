<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med tilpasset pakke | Microsoft Azure"
    description="Lær, hvordan du bruger tilpasset pakke med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Selvstudium: Azure Active Directory-integration med tilpasset pakke

Formålet med dette selvstudium er at vise integration af Azure og tilpasset pakke.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En tilpasset pakke lejer

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til tilpasset pakke kunne enkelt Log på programmet på din tilpassede pakke virksomheds websted (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen tilpasset pakke
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Scenarie")
##<a name="enabling-the-application-integration-for-adaptive-suite"></a>Aktivere programmet integrationen tilpasset pakke

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen tilpasset pakke.

###<a name="to-enable-the-application-integration-for-adaptive-suite-perform-the-following-steps"></a>For at aktivere programmet integrationen tilpasset pakke, skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Tilpasset pakke**.

    ![Galleri med programmet] (./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Galleri med programmet")

7.  Vælg **Tilpasset pakke**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Tilpasset pakke] (./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Tilpasset pakke")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere Sådan brugerne mulighed for at godkende til tilpasset pakke med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfigurere Enkeltlogon tilpasset pakke, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Tilpasset pakke** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge tilpasset pakke** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Svar URL-adressen** på siden **Konfigurer App indstillinger** "*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE =*", og klik derefter på **Næste**.

    >[AZURE.NOTE] Du kan få denne værdi fra den tilpassede pakke **SAML SSO** indstillingssiden.

    ![Konfigurere indstillinger for] (./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Konfigurere indstillinger for")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på tilpasset pakke** , og derefter gemme certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Konfigurere Single Sign-On")

5.  Log på din tilpassede pakke virksomheds websted som en administrator i et andet browservindue.

6.  Gå til **administrator**.

    ![Administrator] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrator")

7.  Klik på **Administrer SAML SSO indstillinger**i sektionen **brugere og roller** .

    ![Administrere indstillinger for SAML SSO] (./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Administrere indstillinger for SAML SSO")

8.  På siden **Indstillinger for SAML SSO** du udføre følgende trin:

    ![SAML SSO-indstillinger] (./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO-indstillinger")

    1.  Skriv et navn til din konfiguration i tekstboksen **navn på identitet udbyder** .
    2.  Kopiér værdien **Enheds-ID** i Azure klassisk-portalen på **Konfigurer single sign-on – på tilpasset pakke** dialogboksen siden, og Indsæt det i tekstfeltet **identitetsudbyder enheds-ID** .
    3.  Kopiér værdien **SAML SSO URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på tilpasset pakke** dialogboksen siden, og Indsæt det i tekstfeltet **identitetsudbyder SSO URL-adresse** .
    4.  Kopiér værdien **SAML SSO URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på tilpasset pakke** dialogboksen siden, og Indsæt det i tekstfeltet **brugerdefineret log URL-adresse** .
    5.  Hvis du vil overføre hentede certifikatet, skal du klikke på **Vælg fil**.
    6.  Vælg **brugerens tilpasset indsigt brugernavn**som **SAML bruger-id**.
    7.  Vælg **bruger-id i NameID af emnet**som **SAML bruger-id placering**.
    8.  Vælg **e-mailadresse**, som **SAML NameID format**.
    9.  **Aktivere SAML**, vælge **Tillad SAML SSO og direkte tilpasset indsigt login**.
    10. Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på tilpasset pakke, skal de klargøres til tilpasset pakke.  
Hvis det er tilpasset-pakken er klargøring en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på din **Tilpassede pakke** virksomheds websted som administrator.

2.  Gå til **administrator**.

    ![Administrator] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrator")

3.  Klik på **Tilføj bruger**i sektionen **brugere og roller** .

    ![Tilføj bruger] (./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Tilføj bruger")

4.  I sektionen **Nye bruger** skal du udføre følgende trin:

    ![Sende] (./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Sende")

    1.  Skriv **navn**, **logon**, **mail**, **din adgangskode** til en gyldig Azure Active Directory-bruger, du vil klargøre i den relaterede tekstbokse.
    2.  Vælg en **rolle**.
    3.  Klik på **Send**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden tilpasset pakke bruger konto værktøjer til oprettelse eller API'er leveres af tilpassede pakke til klargøring AAD-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-adaptive-suite-perform-the-following-steps"></a>Hvis du vil tildele brugere til tilpasset pakke, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Tilpasset pakke **application integration.

    ![Tildele brugere] (./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

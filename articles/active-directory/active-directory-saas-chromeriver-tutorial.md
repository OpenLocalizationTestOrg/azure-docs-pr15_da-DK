<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Chromeriver | Microsoft Azure" 
    description="Lær, hvordan du bruger Chromeriver med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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


#<a name="tutorial-azure-active-directory-integration-with-chromeriver"></a>Selvstudium: Azure Active Directory-integration med Chromeriver

Formålet med dette selvstudium er at vise integration af Azure og Chromeriver.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Chromeriver single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Chromeriver kunne enkelt Log på programmet på webstedet Chromeriver virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Chromeriver
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Scenarie")
##<a name="enabling-the-application-integration-for-chromeriver"></a>Aktivere programmet integrationen for Chromeriver

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Chromeriver.

###<a name="to-enable-the-application-integration-for-chromeriver-perform-the-following-steps"></a>For at aktivere programmet integration for Chromeriver skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Chromeriver**.

    ![Galleri med programmet] (./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Galleri med programmet")

7.  Vælg **Chromeriver**i resultatruden, og klik derefter på **udført** for at tilføje programmet.
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Chromeriver med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Chromeriver** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Chromeriver** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Konfigurere Single Sign-On")

3.  På siden **Konfigurer App indstillinger** ved at udføre følgende trin:

    ![Konfigurere indstillinger for] (./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Konfigurere indstillinger for")

    1.  Skriv din Chromeriver **AssertionConsumerService URL-adressen** i tekstfeltet **Svar URL-adresse** (f.eks.: *https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*).  

        >[AZURE.NOTE] Du kan få denne værdi fra supportteamet Chromeriver.

    2.  Klik på **Næste**

4.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på Chromeriver** , og Gem derefter Metadatafilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Konfigurere Single Sign-On")

5.  Sende metadatafilen hentede til supportteamet Chromeriver.

    >[AZURE.NOTE]Supportteamet Chromeriver har at gøre den faktiske SSO-konfiguration.  
    Du får en meddelelse, når SSO er blevet aktiveret for dit abonnement.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Chromeriver, skal de klargøres til Chromeriver.  
I forbindelse med Chromeriver skal brugerkontiene oprettes ved at supportteamet Chromeriver.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Chromeriver bruger konto værktøjer til oprettelse eller API'er, der leveres af Chromeriver at blive klargjort Azure Active Directory-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-chromeriver-perform-the-following-steps"></a>Hvis du vil tildele brugere til Chromeriver, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Chromeriver **application integration.

    ![Tildele brugere] (./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

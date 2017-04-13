<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med hjørnestenene anmodet arbejdsproces | Microsoft Azure" 
    description="Lær, hvordan du bruger hjørnestenene anmodet arbejdsproces med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Selvstudium: Azure Active Directory-integration med hjørnestenene anmodet arbejdsproces

Formålet med dette selvstudium er at vise integration af Azure og hjørnestenene anmodet arbejdsproces.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En anmodet arbejdsproces hjørnestenene lejer

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til hjørnestenene anmodet arbejdsproces kunne enkelt Log på programmet på webstedet hjørnestenene anmodet arbejdsproces virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for hjørnestenene anmodet arbejdsproces
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scenarie")
##<a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>Aktivere programmet integrationen for hjørnestenene anmodet arbejdsproces

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for hjørnestenene anmodet arbejdsproces.

###<a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>For at aktivere programmet integration for hjørnestenene anmodet arbejdsproces skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **hjørnestenene anmodet arbejdsproces**.

    ![Galleri med programmet] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Galleri med programmet")

7.  Vælg **Hjørnestenene anmodet arbejdsproces**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Hjørnestenene anmodet arbejdsproces] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Hjørnestenene anmodet arbejdsproces")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende hjørnestenene anmodet arbejdsproces med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Hjørnestenene anmodet arbejdsproces** application integration.

    ![Aktivere Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Aktivere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge hjørnestenene anmodet arbejdsproces** , og klik derefter på **Næste**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*http://company.csod.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Hjørnestenene anmodet arbejdsproces log i URL-adresse** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på hjørnestenene anmodet arbejdsproces** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Konfigurere Single Sign-On")

5.  Send følgende elementer til den hjørnestenene anmodet arbejdsproces supportteam:

    1.  Det hentede certifikat
    2.  Værdien **Remote Login URL-adresse**
    3.  Værdien **Remote log URL-adresse** .

    >[AZURE.NOTE] Skal konfigureres af supportteam hjørnestenene anmodet arbejdsproces Single Sign-On.
Du får en meddelelse fra supportteamet, når konfigurationen er fuldført.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på hjørnestenene anmodet arbejdsproces, skal de klargøres til hjørnestenene anmodet arbejdsproces.  
Klargøring af er hjørnestenene anmodet arbejdsproces, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Sende oplysningerne (f.eks.: navn, Emial) om den Azure AD-bruger, du vil klargøre til den hjørnestenene anmodet arbejdsproces dit supportteam.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden hjørnestenene anmodet arbejdsproces bruger konto værktøjer til oprettelse eller API'er, som hjørnestenene anmodet arbejdsproces til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>Hvis du vil tildele brugere til hjørnestenene anmodet arbejdsproces, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Hjørnestenene anmodet arbejdsproces **application integration.

    ![Tildele brugere] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Tildele brugere] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Tildele brugere")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

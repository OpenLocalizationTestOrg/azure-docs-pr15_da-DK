<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Qualtrics | Microsoft Azure" 
    description="Lær, hvordan du bruger Qualtrics med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Selvstudium: Azure Active Directory-integration med Qualtrics
  
Formålet med dette selvstudium er at vise integration af Azure og Qualtrics.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Qualtrics single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Qualtrics kunne enkelt Log på programmet på webstedet Qualtrics virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Qualtrics
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Scenarie")
##<a name="enabling-the-application-integration-for-qualtrics"></a>Aktivere programmet integrationen for Qualtrics
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Qualtrics.

###<a name="to-enable-the-application-integration-for-qualtrics-perform-the-following-steps"></a>For at aktivere programmet integration for Qualtrics skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-qualtrics-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-qualtrics-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Qualtrics**.

    ![Galleri med programmet] (./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Galleri med programmet")

7.  Vælg **Qualtrics**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Qualtrics] (./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Qualtrics med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Qualtrics** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Qualtrics** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Konfigurere Single Sign-On")

3.  Skriv URL-adressen i tekstfeltet **Qualtrics Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://ssotest2ut1.qualtrics.com*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-qualtrics-tutorial/IC789547.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på Qualtrics** , og Gem derefter Metadatafilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Konfigurere Single Sign-On")

5.  Sende metadatafile til Qualtrics-supportteam.

    >[AZURE.NOTE]Konfigurationen af enkelt sign-on – der skal udføres af Qualtrics supportteam. Du får en meddelelse, så snart konfigurationen er fuldført.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til Qualtrics.  
Når en tildelt brugeren forsøger at logge på ved hjælp af panelet access Qualtrics, kontrollerer Qualtrics om brugeren findes.  
Hvis der findes ingen brugerkonto endnu, oprettes den automatisk ved Qualtrics.
##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-qualtrics-perform-the-following-steps"></a>Hvis du vil tildele brugere til Qualtrics, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Qualtrics **application integration.

    ![Tildele brugere] (./media/active-directory-saas-qualtrics-tutorial/IC789550.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
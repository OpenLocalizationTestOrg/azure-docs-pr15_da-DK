<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Lucidchart | Microsoft Azure" 
    description="Lær, hvordan du bruger Lucidchart med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Selvstudium: Azure Active Directory-integration med Lucidchart
  
Formålet med dette selvstudium er at vise integration af Azure og Lucidchart.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Lucidchart single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Lucidchart kunne enkelt Log på programmet på webstedet Lucidchart virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Lucidchart
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scenarie")
##<a name="enabling-the-application-integration-for-lucidchart"></a>Aktivere programmet integrationen for Lucidchart
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Lucidchart.

###<a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>For at aktivere programmet integration for Lucidchart skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Lucidchart**.

    ![Galleri med programmet] (./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Galleri med programmet")

7.  Vælg **Lucidchart**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Lucidchart] (./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Lucidchart med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Lucidchart** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Lucidchart** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Konfigurere Single Sign-On")

3.  Skriv URL-adressen bruges af dine brugere til at logge din Lucidchart-program i tekstfeltet **Lucidchart Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://chart2.office.lucidchart.com/saml/sso/azure*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på Lucidchart** , og Gem derefter datafilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Konfigurere Single Sign-On")

5.  Log på webstedet Lucidchart virksomhed som administrator i et andet browservindue.

6.  Klik på **gruppe**i menuen øverst.

    ![Team] (./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")

7.  Klik på **programmet \> administrere SAML**.

    ![Administrere SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Administrere SAML")

8.  På siden **Indstillinger for proxygodkendelse SAML** dialogboksen du udføre følgende trin:

    1.  Vælg **Enable SAML Authentication**, og klik derefter på **valgfrit**.
        ![SAML godkendelsesindstillinger] (./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML godkendelsesindstillinger")
    2.  Skriv dit domæne i tekstfeltet **domæne** , og klik derefter på **Skift certifikat**.
        ![Skifte certifikat] (./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Skifte certifikat")
    3.  Åbn metadatafilen hentede, kopiere indholdet, og Indsæt det i tekstfeltet **Overføre Metadata** .
        ![Overføre Metadata] (./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Overføre Metadata")
    4.  Vælg **automatisk Tilføj ny bruger til gruppen**, og klik derefter på **Gem ændringer**.
        ![Gemme ændringer] (./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Gemme ændringer")

9.  Vælge enkelt sign-on – konfiguration bekræftelsen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til Lucidchart.  
Når en tildelt brugeren forsøger at logge på ved hjælp af panelet access Lucidchart, kontrollerer Lucidchart om brugeren findes.  
Hvis der findes ingen brugerkonto endnu, oprettes den automatisk ved Lucidchart.
##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>Hvis du vil tildele brugere til Lucidchart, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Lucidchart **application integration.

    ![Tildele brugere] (./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
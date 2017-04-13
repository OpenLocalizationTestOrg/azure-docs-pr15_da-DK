<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med SCC livscyklus | Microsoft Azure" 
    description="Lær, hvordan du bruger SCC livscyklus med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Selvstudium: Azure Active Directory-integration med SCC livscyklus
  
Formålet med dette selvstudium er at vise integration af Azure og SCC livscyklus.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En SCC livscyklus single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, kan de Azure AD-brugere, du har tildelt til SCC supportlivscyklus enkelt Log på programmet på webstedet SCC livscyklus virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for SCC livscyklus
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Scenarie")
##<a name="enabling-the-application-integration-for-scc-lifecycle"></a>Aktivere programmet integrationen for SCC livscyklus
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for SCC livscyklus.

###<a name="to-enable-the-application-integration-for-scc-lifecycle-perform-the-following-steps"></a>For at aktivere programmet integration for SCC livscyklus skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **SCC livscyklus**.

    ![Galleri med programmet] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Galleri med programmet")

7.  Vælg **SCC livscyklus**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![SCC livscyklus] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC livscyklus")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende SCC livscyklus med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **SCC livscyklus** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge SCC livscyklus** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Konfigurere Single Sign-On")

3.  Skriv URL-adressen bruges af dine brugere til at logge din SCC livscyklus-program ved hjælp af følgende mønster "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på SCC livscyklus** , og Gem derefter metadatafilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Konfigurere Single Sign-On")

5.  Videresend metadatafilen til SCC livscyklus supportteam.

    >[AZURE.NOTE]Enkeltlogon skal aktiveres af supportteam SCC livscyklus.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på SCC livscyklus, skal de klargøres i SCC livscyklus.
  
Der er ingen handlingspunkt for dig at konfigurere brugerklargøring til SCC livscyklus.  
Når en tildelt brugeren forsøger at logge på SCC livscyklus, oprettes en SCC livscyklus konto automatisk, hvis det er nødvendigt.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden SCC livscyklus bruger konto værktøjer til oprettelse eller API'er, som SCC livscyklus til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-scc-lifecycle-perform-the-following-steps"></a>Hvis du vil tildele brugere til SCC livscyklus, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **SCC livscyklus **application integration.

    ![Tildele brugere] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
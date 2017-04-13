<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med SimpleNexus | Microsoft Azure" 
    description="Lær, hvordan du bruger SimpleNexus med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>Selvstudium: Azure Active Directory-integration med SimpleNexus
  
Formålet med dette selvstudium er at vise integration af Azure og SimpleNexus.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En SimpleNexus single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til SimpleNexus kunne enkelt Log på programmet på webstedet SimpleNexus virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for SimpleNexus
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Scenarie")
##<a name="enabling-the-application-integration-for-simplenexus"></a>Aktivere programmet integrationen for SimpleNexus
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for SimpleNexus.

###<a name="to-enable-the-application-integration-for-simplenexus-perform-the-following-steps"></a>For at aktivere programmet integration for SimpleNexus skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **enkle nexus**.

    ![Galleri med programmet] (./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Galleri med programmet")

7.  Vælg **SimpleNexus**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Enkel Nexus] (./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Enkel Nexus")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende SimpleNexus med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **SimpleNexus** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge SimpleNexus** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **SimpleNexus log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://simplenexus.com/CompanyName\_login*", og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på SimpleNexus** , og derefter videresende metadatafilen til SimpleNexus-supportteam.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Konfigurere Single Sign-On")

    >[AZURE.NOTE] Skal være aktiveret af supportteam SimpleNexus enkeltlogon.

5.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på SimpleNexus, skal de klargøres til SimpleNexus.  
Klargøring af er SimpleNexus, en manuel opgave er udført af lejeradministratoren.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden SimpleNexus bruger konto værktøjer til oprettelse eller API'er, som SimpleNexus til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-simplenexus-perform-the-following-steps"></a>Hvis du vil tildele brugere til SimpleNexus, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **SimpleNexus **application integration.

    ![Tildele brugere] (./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med LogicMonitor | Microsoft Azure" 
    description="Lær, hvordan du bruger LogicMonitor med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Selvstudium: Azure Active Directory-integration med LogicMonitor
  
Formålet med dette selvstudium er at vise integration af Azure og LogicMonitor.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En LogicMonitor lejer
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for LogicMonitor
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scenarie")
##<a name="enabling-the-application-integration-for-logicmonitor"></a>Aktivere programmet integrationen for LogicMonitor
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for LogicMonitor.

###<a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>For at aktivere programmet integration for LogicMonitor skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **logicmonitor**.

    ![Galleri med programmet] (./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Galleri med programmet")

7.  Vælg **LogicMonitor**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![LogicMonitor] (./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende LogicMonitor med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **LogicMonitor **application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge LogicMonitor** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse, der bruges af dine brugere til at logge LogicMonitor i tekstfeltet **Log på URL-adressen** på siden **Konfigurer App URL-adressen** \(e, g,: "*http://company.logicmonitor.com*"\), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på LogicMonitor** , og derefter gemme den på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Konfigurere Single Sign-On")

5.  Log på webstedet **LogicMonitor** virksomhed som administrator.

6.  Klik på **Indstillinger**i menuen øverst.

    ![Indstillinger] (./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Indstillinger")

7.  I navigation bat i venstre side, skal du klikke på **Single Sign-On**

    ![Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")

8.  I sektionen **Indstillinger for Single Sign-on (SSO)** skal du udføre følgende trin:

    ![Enkelt Sign-On-indstillinger] (./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Enkelt Sign-On-indstillinger")

    1.  Vælg **Aktiver Single Sign-on**.
    2.  Som **Standard rolletildeling**, Vælg **skrivebeskyttet**.
    3.  Åbn metadatafilen hentede i Notesblok, og derefter indsætte indholdet af filen i tekstfeltet **Identitet udbyder Metadata** .
    4.  Klik på **Gem ændringer**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For AAD brugere skal kunne logge på, skal de klargøres til programmet LogicMonitor ved hjælp af deres Azure Active Directory-brugernavne.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet LogicMonitor virksomhed som administrator.

2.  Klik på **Indstillinger**i menuen øverst, og klik derefter på **roller og brugere**.

    ![Roller og brugere] (./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roller og brugere")

3.  Klik på **Tilføj**.

4.  I sektionen **Tilføj en konto** skal du udføre følgende trin:

    ![Tilføj en konto] (./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Tilføj en konto")

    1.  Skriv **brugernavn**, **mail**, **din adgangskode** og **Skriv adgangskode igen** værdierne for den Azure Active Directory-bruger, du vil klargøre i den relaterede tekstbokse.
    2.  Vælg **roller**, **vise tilladelser** og **Status**.
    3.  Klik på **Send**.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden LogicMonitor bruger konto værktøjer til oprettelse eller API'er, der leveres af LogicMonitor at blive klargjort Azure Active Directory-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>Hvis du vil tildele brugere til LogicMonitor, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **LogicMonitor** application integration.

    ![Tildele brugere] (./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.





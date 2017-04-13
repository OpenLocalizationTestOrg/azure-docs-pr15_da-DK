<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Thirdlight | Microsoft Azure" 
    description="Lær, hvordan du bruger Thirdlight med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Selvstudium: Azure Active Directory-Integration med Thirdlight
  
Formålet med dette selvstudium er at vise integration af Azure og Thirdlight.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Thirdlight single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Thirdlight kunne enkelt Log på programmet på webstedet Thirdlight virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Thirdlight
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Scenarie")

##<a name="enabling-the-application-integration-for-thirdlight"></a>Aktivere programmet integrationen for Thirdlight
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Thirdlight.

###<a name="to-enable-the-application-integration-for-thirdlight-perform-the-following-steps"></a>For at aktivere programmet integration for Thirdlight skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Thirdlight**.

    ![Galleri med programmet] (./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Galleri med programmet")

7.  Vælg **Thirdlight**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![ThirdLight] (./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Thirdlight med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Thirdlight, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Thirdlight** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Thirdlight** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse, der bruges af dine brugere til at logge din Thirdlight-program i tekstfeltet **Thirdlight log i URL-adresse** på siden **Konfigurer App URL-adresse** (f.eks.: "*http://azuresso2.thirdlight.com/*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på Thirdlight** , og Gem derefter metadatafilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Konfigurere Single Sign-On")

5.  Log på webstedet Thirdlight virksomhed som administrator i et andet browservindue.

6.  Gå til **konfiguration \> System Administration**, og klik derefter på **SAML2**.

    ![Systemadministration af] (./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Systemadministration af")

7.  I afsnittet SAML2 konfiguration skal du udføre følgende trin:

    ![SAML Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML Single Sign-On")

    1.  Vælg **Aktiver SAML2 Single Sign-On**.
    2.  Vælg **Indlæs IdP Metadata fra XML**som **kilde til IdP Metadata**.
    3.  Åbn metadatafilen hentede, kopiere indholdet og derefter indsætte det i tekstfeltet **IdP Metadata XML** .
    4.  Klik på **Gem SAML2 indstillinger**.

8.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Thirdlight, skal de klargøres til Thirdlight.  
Klargøring af er Thirdlight, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet **Thirdlight** virksomhed som administrator.

2.  Gå til fanen **brugere** .

3.  Vælg **brugere og grupper**.

4.  Klik på knappen **Tilføj ny bruger** .

5.  Angiv **brugernavn, navn eller beskrivelse, mail, Vælg en forudindstillet eller gruppe af nye medlemmer** af en gyldig AAD-konto, du ønsker at blive klargjort.

6.  Klik på **Opret**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Thirdlight bruger konto værktøjer til oprettelse eller API'er, som Thirdlight til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-thirdlight-perform-the-following-steps"></a>Hvis du vil tildele brugere til Thirdlight, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Thirdlight **application integration.

    ![Tildele brugere] (./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
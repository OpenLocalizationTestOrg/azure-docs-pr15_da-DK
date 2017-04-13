<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Brightspace ved Desire2Learn | Microsoft Azure" 
    description="Lær, hvordan du bruger Brightspace ved Desire2Learn med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Selvstudium: Azure Active Directory-integration med Brightspace ved Desire2Learn

Formålet med dette selvstudium er at vise integration af Azure og Brightspace ved Desire2Learn.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Brightspace ved Desire2Learn single sign-on – aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Brightspace ved Desire2Learn kunne enkelt Log på programmet på din Brightspace ved Desire2Learn virksomheds websted (service provider startet sign-on) eller bruge [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Brightspace ved Desire2Learn
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scenarie")
##<a name="enabling-the-application-integration-for-brightspace-by-desire2learn"></a>Aktivere programmet integrationen for Brightspace ved Desire2Learn

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Brightspace ved Desire2Learn.

###<a name="to-enable-the-application-integration-for-brightspace-by-desire2learn-perform-the-following-steps"></a>For at aktivere programmet integration for Brightspace ved Desire2Learn skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Brightspace ved Desire2Learn**.

    ![Apllication galleri] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Apllication galleri")

7.  Vælg **Brightspace ved Desire2Learn**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Brightspace ved Desire2Learn] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace ved Desire2Learn")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Brightspace ved Desire2Learn med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Brightspace ved Desire2Learn** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Brightspace ved Desire2Learn** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Konfigurere Single Sign-On")

3.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Konfigurere App URL-adresse")

    1.  Skriv URL-adressen bruges af dine brugere til at logge på din **Brightspace ved Desire2Learn** i tekstfeltet **Log på URL-adresse** (f.eks.: *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
    2.  Klik på **Næste**

4.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på Brightspace ved Desire2Learn** , og Gem derefter metadata på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Konfigurere Single Sign-On")

5.  Sende metadatafilen hentede til din Brightspace ved Desire2Learn supportteam.

    >[AZURE.NOTE] Din Brightspace af Desire2Learn supportteam har at gøre den faktiske SSO-konfiguration.
Du får en meddelelse, når SSO er blevet aktiveret for dit abonnement.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Brightspace ved Desire2Learn, skal være de klargjort i Brightspace ved Desire2Learn.  
I forbindelse med Brightspace ved Desire2Learn skal brugerkonti være oprettet af din Brightspace af Desire2Learn supportteam.

>[AZURE.NOTE] Du kan bruge andre Brightspace ved værktøjer til oprettelse af Desire2Learn bruger konto eller API'er, der leveres af Brightspace ved Desire2Learn at blive klargjort Azure Active Directory-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-brightspace-by-desire2learn-perform-the-following-steps"></a>Hvis du vil tildele brugere til Brightspace ved Desire2Learn, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Brightspace ved Desire2Learn **application integration.

    ![Tildele brugere] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

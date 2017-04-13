<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Aha! | Microsoft Azure" 
    description="Lær at bruge Aha! med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-aha"></a>Selvstudium: Azure Active Directory-integration med Aha!

Formålet med dette selvstudium er at vise integration af Azure og Aha!  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Aha! single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, Azure AD-brugere du har tildelt til Aha! vil kunne enkelt Log på programmet på din Aha! virksomhed-websted (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Aha!
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-aha-tutorial/IC798944.png "Scenarie")
##<a name="enabling-the-application-integration-for-aha"></a>Aktivere programmet integrationen for Aha!

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Aha!.

###<a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>At aktivere programmet integration for Aha!, udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-aha-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-aha-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-aha-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Aha!**.

    ![Galleri med programmet] (./media/active-directory-saas-aha-tutorial/IC798945.png "Galleri med programmet")

7.  Vælg i resultatruden **Aha!**, og klik derefter på **udført** for at tilføje programmet.

    ![Aha!] (./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er at oprette en disposition for at aktivere brugere til at godkende Aha! med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  I Azure klassisk-portalen på den **Aha!** integration af programmet på computeren skal du klikke på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798946.png "Konfigurere Single Sign-On")

2.  På den **hvordan vil du brugere til at logge Aha!** siden, Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798947.png "Konfigurere Single Sign-On")

3.  På siden **Konfigurer App URL-adresse** i **Aha! Log på URL-adressen** tekstboks skal du skrive URL-adressen bruges af dine brugere til at logge på din Aha! Program (f.eks.: "*https://company.aha.io/session/new*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-aha-tutorial/IC798948.png "Konfigurere App URL-adresse")

4.  På den **konfigurere single sign-on på Aha!** siden til at hente metadatafilen, klik på **Hent metadata**og derefter gemme metadatafilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798949.png "Konfigurere Single Sign-On")

5.  Log på din Aha i et andet browservindue! virksomhed-websted som en administrator.

6.  Klik på **Indstillinger**i menuen øverst.

    ![Indstillinger] (./media/active-directory-saas-aha-tutorial/IC798950.png "Indstillinger")

7.  Klik på **konto**.

    ![Profil] (./media/active-directory-saas-aha-tutorial/IC798951.png "Profil")

8.  Klik på **sikkerhed og single sign-on**.

    ![Sikkerhed og single sign-on] (./media/active-directory-saas-aha-tutorial/IC798952.png "Sikkerhed og single sign-on")

9.  Vælg **SAML2.0**som **Identitetsudbyder**, i sektionen **Single Sign-On** .

    ![Sikkerhed og single sign-on] (./media/active-directory-saas-aha-tutorial/IC798953.png "Sikkerhed og single sign-on")

10. På konfigurationssiden **Single Sign-On** du udføre følgende trin:

    ![Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798954.png "Single Sign-On")

    1.  I tekstfeltet **navn** skal du skrive et navn til din konfiguration.
    2.  Vælg **Metadata fil**for at **konfigurere ved hjælp af**.
    3.  Klik på **Gennemse**for at overføre metadatafilen hentede.
    4.  Klik på **Opdater**.

11. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798955.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Aha!, de skal klargøres til Aha!.  
Hvis det er Aha!, klargøring er en automatiseret opgave.  
Der er ingen handlingspunkt for dig.
  
Brugere oprettes automatisk eventuelt under det første enkelte enkeltlogon forsøg.

>[AZURE.NOTE] Du kan bruge andre Aha! værktøjer til oprettelse af bruger konto eller API'er, der leveres af Aha! til at klargøre AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-aha-perform-the-following-steps"></a>Tildele brugere til Aha!, udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  På den **Aha!** integration af programmet på computeren skal du klikke på **Tildel brugere**.

    ![Tildele brugere] (./media/active-directory-saas-aha-tutorial/IC798956.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-aha-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

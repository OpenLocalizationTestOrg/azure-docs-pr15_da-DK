<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Overdrive bøger | Microsoft Azure" 
    description="Lær, hvordan du bruger Overdrive bøger med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Selvstudium: Azure Active Directory-integration med Overdrive bøger
  
Formålet med dette selvstudium er at vise integration af Azure og OverDrive.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En OverDrive single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til OverDrive kunne enkelt Log på programmet på webstedet OverDrive virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for OverDrive
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scenarie")
##<a name="enabling-the-application-integration-for-overdrive"></a>Aktivere programmet integrationen for OverDrive
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for OverDrive.

###<a name="to-enable-the-application-integration-for-overdrive-perform-the-following-steps"></a>For at aktivere programmet integration for OverDrive skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **OverDrive**.

    ![Galleri med programmet] (./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Galleri med programmet")

7.  Vælg **OverDrive**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![OverDrive] (./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende OverDrive med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **OverDrive** application integration.

    ![Aktivere enkeltlogon] (./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Aktivere enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge OverDrive** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*http://mslibrarytest.libraryreserve.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **OverDrive log i URL-adresse** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Konfigurere App URL-adresse")

4.  På **Konfigurer single sign-on – på OverDrive** siden til at hente metadatafilen, og send den til OverDrive supportteam.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Konfigurere Enkeltlogon")

    >[AZURE.NOTE]Supportteam OverDrive konfigurerer Enkeltlogon for dig og sender en besked, når konfigurationen er fuldført.

5.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Der er ingen handlingspunkt for dig at konfigurere brugerklargøring til OverDrive.  
Når en tildelt brugeren forsøger at logge på OverDrive, oprettes en OverDrive konto automatisk, hvis det er nødvendigt.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden OverDrive bruger konto værktøjer til oprettelse eller API'er, som OverDrive til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-overdrive-perform-the-following-steps"></a>Hvis du vil tildele brugere til OverDrive, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **OverDrive **application integration.

    ![Tildele brugere] (./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
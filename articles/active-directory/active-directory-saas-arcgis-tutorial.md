<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med ArcGIS | Microsoft Azure" 
    description="Lær, hvordan du bruger ArcGIS med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-arcgis"></a>Selvstudium: Azure Active Directory-Integration med ArcGIS

Formålet med dette selvstudium er at vise integration af Azure og ArcGIS. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En ArcGIS single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til ArcGIS kunne enkelt Log på programmet på webstedet ArcGIS virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for ArcGIS
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scenarie")
##<a name="enabling-the-application-integration-for-arcgis"></a>Aktivere programmet integrationen for ArcGIS

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for ArcGIS.

###<a name="to-enable-the-application-integration-for-arcgis-perform-the-following-steps"></a>For at aktivere programmet integration for ArcGIS skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-arcgis-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-arcgis-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-arcgis-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **ArcGIS**.

    ![Applcation galleri] (./media/active-directory-saas-arcgis-tutorial/IC784736.png "Applcation galleri")

7.  Vælg **ArcGIS**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![ArcGIS] (./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende ArcGIS med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **ArcGIS** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784738.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge ArcGIS** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784739.png "Konfigurere Single Sign-On")

3.  På siden **Konfigurer App URL-adressen** i tekstfeltet **ArcGIS log i URL-adresse** , Skriv URL-adressen bruges af dine brugere til at logge på ved hjælp af følgende mønster "*https://company.maps.arcgis.com*", og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-arcgis-tutorial/IC784740.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på ArcGIS** , og Gem derefter metadatafilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784741.png "Konfigurere Single Sign-On")

5.  Log på webstedet ArcGIS virksomhed som administrator i et andet browservindue.

6.  Klik på **Rediger indstillinger**.

    ![Redigere indstillinger for] (./media/active-directory-saas-arcgis-tutorial/IC784742.png "Redigere indstillinger for")

7.  Klik på **sikkerhed**.

    ![Sikkerhed] (./media/active-directory-saas-arcgis-tutorial/IC784743.png "Sikkerhed")

8.  Klik på **Angiv identitetsudbyder**under **Enterprise logon**.

    ![Virksomhed-logon] (./media/active-directory-saas-arcgis-tutorial/IC784744.png "Virksomhed-logon")

9.  På siden **Angiv identitetsudbyder** konfiguration skal du udføre følgende trin:

    ![Angive identitetsudbyder] (./media/active-directory-saas-arcgis-tutorial/IC784745.png "Angive identitetsudbyder")

    1.  Skriv organisationens navn i tekstfeltet navn.
    2.  Vælg **En fil**til **metadataene for Enterprise-identitetsudbyder der skal angives ved hjælp af**.
    3.  For at overføre metadatafilen hentede, skal du klikke på **Vælg fil**.
    4.  Klik på **Angiv identitetsudbyder**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784746.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på ArcGIS, skal de klargøres til ArcGIS.  
Klargøring af er ArcGIS, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på din **ArcGIS** lejer.

2.  Klik på **Inviter medlemmer**.

    ![Inviter medlemmer] (./media/active-directory-saas-arcgis-tutorial/IC784747.png "Inviter medlemmer")

3.  Vælg **Tilføj medlemmer automatisk uden at sende en mail**, og klik derefter på **Næste**.

    ![Tilføje medlemmer automatisk] (./media/active-directory-saas-arcgis-tutorial/IC784748.png "Tilføje medlemmer automatisk")

4.  På siden **medlemmer** dialogboksen skal du udføre følgende trin:

    ![Tilføj og gennemsyn] (./media/active-directory-saas-arcgis-tutorial/IC784749.png "Tilføj og gennemsyn")

    1.  Angiv **Fornavn**, **Efternavn** og **mail** med en gyldig AAD-konto, du ønsker at blive klargjort.
    2.  Klik på **Tilføj og gennemse**.

5.  Gennemse de data, du har angivet, og klik derefter på **Tilføj medlemmer**.

    ![Tilføj medlem] (./media/active-directory-saas-arcgis-tutorial/IC784750.png "Tilføj medlem")

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden ArcGIS bruger konto værktøjer til oprettelse eller API'er, som ArcGIS til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-arcgis-perform-the-following-steps"></a>Hvis du vil tildele brugere til ArcGIS, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **ArcGIS **application integration.

    ![Tildele brugere] (./media/active-directory-saas-arcgis-tutorial/IC784751.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-arcgis-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

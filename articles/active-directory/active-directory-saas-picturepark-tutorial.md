<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Picturepark | Microsoft Azure" 
    description="Lær, hvordan du bruger Picturepark med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Selvstudium: Azure Active Directory-Integration med Picturepark
  
Formålet med dette selvstudium er at vise integration af Azure og Picturepark.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Picturepark lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Picturepark kunne enkelt Log på programmet på webstedet Picturepark virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Picturepark
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scenarie")

##<a name="enabling-the-application-integration-for-picturepark"></a>Aktivere programmet integrationen for Picturepark
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Picturepark.

###<a name="to-enable-the-application-integration-for-picturepark-perform-the-following-steps"></a>For at aktivere programmet integration for Picturepark skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-picturepark-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-picturepark-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-picturepark-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Picturepark**.

    ![Galleri med programmet] (./media/active-directory-saas-picturepark-tutorial/IC795056.png "Galleri med programmet")

7.  Vælg **Picturepark**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Picturepark] (./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Picturepark med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Picturepark, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)..

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Picturepark** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795058.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Picturepark** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795059.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*http://company.picturepark.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Picturepark Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-picturepark-tutorial/IC795060.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Picturepark** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795061.png "Konfigurere Single Sign-On")

5.  Log på webstedet Picturepark virksomhed som administrator i et andet browservindue.

6.  Klik på **Administration**på værktøjslinjen i øverste, og klik derefter på **Management Console**.

    ![Management Console] (./media/active-directory-saas-picturepark-tutorial/IC795062.png "Management Console")

7.  Klik på **godkendelse**, og klik derefter på **id-udbydere**.

    ![Godkendelse] (./media/active-directory-saas-picturepark-tutorial/IC795063.png "Godkendelse")

8.  I afsnittet **identitet udbyder konfiguration** skal du udføre følgende trin:

    ![Konfiguration af identitet] (./media/active-directory-saas-picturepark-tutorial/IC795064.png "Konfiguration af identitet")

    1.  Klik på **Tilføj**.
    2.  Skriv et navn til din konfiguration.
    3.  Vælg **Angiv som standard**.
    4.  Kopiér værdien **SAML SSO URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Picturepark** dialogboksen siden, og Indsæt det i tekstfeltet **Udsteder URI** .
    5.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **Der er tillid til udsteder rulleboks Udskriv** .  

        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    6.  Klik på **JoinDefaultUsersGroup**.
    7.  For at angive egenskaben **mailadresse** i tekstfeltet **krav** , skal du skrive **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
        ![Konfiguration] (./media/active-directory-saas-picturepark-tutorial/IC795065.png "Konfiguration")
    8.  Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795066.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Picturepark, skal de klargøres til Picturepark.  
Klargøring af er Picturepark, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **Picturepark** lejer.

2.  Klik på **Administration**på værktøjslinjen øverst, og klik derefter på **brugere**.

    ![Brugere] (./media/active-directory-saas-picturepark-tutorial/IC795067.png "Brugere")

3.  Klik på **Ny**under fanen **Oversigt over brugere** .

    ![Brugeradministration] (./media/active-directory-saas-picturepark-tutorial/IC795068.png "Brugeradministration")

4.  I dialogboksen **Opret bruger** skal du udføre følgende trin:

    ![Oprette bruger] (./media/active-directory-saas-picturepark-tutorial/IC795069.png "Oprette bruger")

    1.  Skriv den: **e-mail-adresse**, **adgangskode**, **Bekræft adgangskode**, **Fornavn**, **Efternavn**, **virksomhed**, **land**, **ZIP**og **By** med en gyldig Azure Active Directory-bruger, du vil have objekttype klargøring til den relaterede tekstbokse.
    2.  Vælg et **sprog**.
    3.  Klik på **Opret**.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden Picturepark bruger konto værktøjer til oprettelse eller API'er, som Picturepark til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-picturepark-perform-the-following-steps"></a>Hvis du vil tildele brugere til Picturepark, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Picturepark **application integration.

    ![Tildele brugere] (./media/active-directory-saas-picturepark-tutorial/IC795070.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-picturepark-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
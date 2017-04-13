<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med ScreenSteps | Microsoft Azure" 
    description="Lær, hvordan du bruger ScreenSteps med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Selvstudium: Azure Active Directory-integration med ScreenSteps
  
Formålet med dette selvstudium er at vise integration af Azure og ScreenSteps.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En ScreenSteps lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til ScreenSteps kunne enkelt Log på programmet på webstedet ScreenSteps virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for ScreenSteps
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scenarie")
##<a name="enabling-the-application-integration-for-screensteps"></a>Aktivere programmet integrationen for ScreenSteps
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for ScreenSteps.

###<a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>For at aktivere programmet integration for ScreenSteps skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-screensteps-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-screensteps-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-screensteps-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **ScreenSteps**.

    ![Galleri med programmet] (./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galleri med programmet")

7.  Vælg **ScreenSteps**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![ScreenSteps] (./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende ScreenSteps med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **ScreenSteps** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-screensteps-tutorial/IC778519.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge ScreenSteps** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-screensteps-tutorial/IC778520.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **ScreenSteps log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. ScreenSteps.com*", og klik derefter på **Næste**.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-screensteps-tutorial/IC778521.png "Konfigurere app URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på ScreenSteps** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-screensteps-tutorial/IC778522.png "Konfigurere Enkeltlogon")

5.  Log på webstedet ScreenSteps virksomhed som administrator i et andet browservindue.

6.  Klik på **Kontoadministration af**.

    ![Kontoadministration] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Kontoadministration")

7.  Klik på **Remote godkendelse**.

    ![Remote godkendelse] (./media/active-directory-saas-screensteps-tutorial/IC778524.png "Remote godkendelse")

8.  Klik på **Opret godkendelse slutpunkt**.

    ![Remote godkendelse] (./media/active-directory-saas-screensteps-tutorial/IC778525.png "Remote godkendelse")

9.  I sektionen **Opret godkendelse ark** skal du udføre følgende trin:

    ![Opret et slutpunkt for godkendelse] (./media/active-directory-saas-screensteps-tutorial/IC778526.png "Opret et slutpunkt for godkendelse")

    1.  Skriv en titel i tekstfeltet **Titel** .
    2.  Vælg **SAML**på listen **tilstand** .
    3.  Klik på **Opret**.

10. I sektionen **Remote godkendelse slutpunkt** skal du udføre følgende trin:

    ![Remote godkendelse slutpunkt] (./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote godkendelse slutpunkt")

    1.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på siden **Konfigurer single sign-on – på ScreenSteps** , og Indsæt det i tekstfeltet **Remote Login URL-adresse** .
    2.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på siden **Konfigurer single sign-on – på ScreenSteps** , og Indsæt det i tekstfeltet **logge af URL-adresse** .
    3.  Klik på **Vælg en fil**, og overfør derefter det certifikat, der er hentet.
    4.  Klik på **Opdater**.

11. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-screensteps-tutorial/IC778542.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på **ScreenSteps**, skal de klargøres til **ScreenSteps**.  
Klargøring af er **ScreenSteps**, en manuel opgave.

###<a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>Hvis du vil tildele en brugerkonto til ScreenSteps, skal du udføre følgende trin:

1.  Log på din **ScreenSteps** lejer.

2.  Klik på **Kontoadministration af**.

    ![Kontoadministration] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Kontoadministration")

3.  Klik på **brugere**.

    ![Brugere] (./media/active-directory-saas-screensteps-tutorial/IC778544.png "Brugere")

4.  Klik på **Opret en bruger**.

    ![Alle brugere] (./media/active-directory-saas-screensteps-tutorial/IC778545.png "Alle brugere")

5.  Vælg en rolle til din bruger fra listen **Brugerrolle** .

6.  Skriv**"Fornavn**, **Efternavn**, **mail**, **logon**, **adgangskode** og **Adgangskode, du angav"**af en gyldig AAD-konto, du vil klargøre i den relaterede tekstfelter i sektionen brugerrolle.

    ![Ny bruger] (./media/active-directory-saas-screensteps-tutorial/IC778546.png "Ny bruger")

7.  Vælg **Godkendelse gruppe (SAML)**i sektionen grupper, og klik derefter på **Opret bruger**.

    ![Grupper] (./media/active-directory-saas-screensteps-tutorial/IC778547.png "Grupper")

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden ScreenSteps bruger konto værktøjer til oprettelse eller API'er, som ScreenSteps til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>Hvis du vil tildele brugere til ScreenSteps, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **ScreenSteps **application integration.

    ![Tildele brugere] (./media/active-directory-saas-screensteps-tutorial/IC773094.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Tildele brugere] (./media/active-directory-saas-screensteps-tutorial/IC778548.png "Tildele brugere")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
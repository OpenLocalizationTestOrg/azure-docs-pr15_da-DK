<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med TalentLMS | Microsoft Azure" 
    description="Lær, hvordan du bruger TalentLMS med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Selvstudium: Azure Active Directory-Integration med TalentLMS
  
Formålet med dette selvstudium er at vise integration af Azure og TalentLMS.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En TalentLMS lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til TalentLMS kunne enkelt Log på programmet på webstedet TalentLMS virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for TalentLMS
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-talentlms-tutorial/IC777289.png "Scenarie")

##<a name="enabling-the-application-integration-for-talentlms"></a>Aktivere programmet integrationen for TalentLMS
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for TalentLMS.

###<a name="to-enable-the-application-integration-for-talentlms-perform-the-following-steps"></a>For at aktivere programmet integration for TalentLMS skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-talentlms-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-talentlms-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-talentlms-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **TalentLMS**.

    ![Galleri med programmet] (./media/active-directory-saas-talentlms-tutorial/IC777290.png "Galleri med programmet")

7.  Vælg **TalentLMS**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![TalentLMS] (./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende TalentLMS med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen. .  
Konfiguration af single sign-on til TalentLMS, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **TalentLMS** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-talentlms-tutorial/IC777292.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge TalentLMS** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-talentlms-tutorial/IC777293.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **TalentLMS log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. TalentLMSapp.com*", og klik derefter på **Næste**.

    ![Log på URL-adresse] (./media/active-directory-saas-talentlms-tutorial/IC777294.png "Log på URL-adresse")

4.  På siden **Konfigurer single sign-on – på TalentLMS** for at downloade certifikatet, skal du klikke på **Hent certifikat**, og Gem derefter certifikatfil lokalt som **c:\\TalentLMS.cer**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-talentlms-tutorial/IC777295.png "Konfigurere Single Sign-On")

5.  Log på webstedet TalentLMS virksomhed som administrator i et andet browservindue.

6.  Klik på fanen **brugere** i sektionen **konto og indstillinger** .

    ![Konto og indstillinger] (./media/active-directory-saas-talentlms-tutorial/IC777296.png "Konto og indstillinger")

7.  Klik på **Single Sign-On (SSO)**,

8.  I sektionen Single Sign-On skal du udføre følgende trin:

    ![Single Sign-On] (./media/active-directory-saas-talentlms-tutorial/IC777297.png "Single Sign-On")

    1.  Vælg **SAML 2.0**listen **SSO Integrationstype** .
    2.  Kopiér værdien **Identitet Provider-ID** i Azure klassisk-portalen på **Konfigurer single sign-on – på TalentLMS** dialogboksen siden, og Indsæt det i tekstfeltet **identitetsudbyder (IdP)** .
    3.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **Certifikat fingeraftryk** .

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    4.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på TalentLMS** dialogboksen siden, og Indsæt det i tekstfeltet **Remote URL** .
    5.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på TalentLMS** dialogboksen siden, og Indsæt det i tekstfeltet **Remote hvor URL-adresse** .
    6.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name** i tekstfeltet **TargetedID**
    7.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** i tekstfeltet **Fornavn**
    8.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** i tekstfeltet **Efternavn**
    9.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** i tekstfeltet **mail**
    10. Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-talentlms-tutorial/IC777298.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på TalentLMS, skal de klargøres til TalentLMS.  
Klargøring af er TalentLMS, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **TalentLMS** lejer.

2.  Klik på **brugere**, og klik derefter på **Tilføj bruger**.

3.  På siden **Tilføj bruger** dialogboksen skal du udføre følgende trin:

    ![Tilføj bruger] (./media/active-directory-saas-talentlms-tutorial/IC777299.png "Tilføj bruger")

    1.  Skriv relateret attributværdierne for Azure AD-brugerkontoen i følgende tekstfelterne: **Fornavn**, **Efternavn**, **e-mail-adresse**.
    2.  Klik på **Tilføj bruger**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden TalentLMS bruger konto værktøjer til oprettelse eller API'er, som TalentLMS til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-talentlms-perform-the-following-steps"></a>Hvis du vil tildele brugere til TalentLMS, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **TalentLMS **application integration.

    ![Tildele brugere] (./media/active-directory-saas-talentlms-tutorial/IC777300.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-talentlms-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
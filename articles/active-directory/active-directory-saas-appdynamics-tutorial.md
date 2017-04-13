<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med AppDynamics | Microsoft Azure" 
    description="Lær, hvordan du bruger AppDynamics med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Selvstudium: Azure Active Directory-integration med AppDynamics

Formålet med dette selvstudium er at vise integration af Azure og AppDynamics. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En AppDynamics single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til AppDynamics kunne enkelt Log på programmet på webstedet AppDynamics virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for AppDynamics
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenarie")
##<a name="enabling-the-application-integration-for-appdynamics"></a>Aktivere programmet integrationen for AppDynamics

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for AppDynamics.

###<a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>For at aktivere programmet integration for AppDynamics skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **AppDynamics**.

    ![Galleri med programmet] (./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Galleri med programmet")

7.  Vælg **AppDynamics**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![AppDynamics] (./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende AppDynamics med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **AppDynamics** application integration.

    ![Konfigurere enkelt in] (./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Konfigurere enkelt in")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge AppDynamics** , og klik derefter på **Næste**.

    ![Konfigurere enkelt in] (./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Konfigurere enkelt in")

3.  Skriv din URL-adresse, der bruges af dine brugere at sign-on til AppDynamics ("*https://companyname.saas.appdynamics.com*") på siden **Konfigurer App URL-adressen** i tekstfeltet **AppDynamics Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på AppDynamics** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere enkelt in] (./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Konfigurere enkelt in")

5.  Log på webstedet AppDynamics virksomhed som administrator i et andet browservindue.

6.  Klik på **Indstillinger**på værktøjslinjen øverst, og klik derefter på **Administration**.

    ![Administration] (./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")

7.  Klik på fanen **Udbyder af godkendelse** .

    ![Udbyder af godkendelse] (./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Udbyder af godkendelse")

8.  I sektionen **Godkendelsesudbyder** skal du udføre følgende trin:

    ![SAML konfiguration] (./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML konfiguration")

    1.  Vælg **SAML**som **Udbyder af godkendelse**.
    2.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på AppDynamics** dialogboksen siden, og Indsæt det i tekstfeltet **URL-adressen til logon** .
    3.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på AppDynamics** dialogboksen siden, og Indsæt det i tekstfeltet **Log URL-adresse** .
    4.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    5.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **certifikat**
    6.  Klik på **Gem**.
        ![Gemme] (./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Gemme")

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere enkelt in] (./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Konfigurere enkelt in")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på AppDynamics, skal de klargøres til AppDynamics.  
Klargøring af er AppDynamics, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet AppDynamics virksomhed som administrator.

2.  Gå til **brugere**, og klik derefter på **+** til at åbne dialogboksen **Opret bruger** .

    ![Brugere] (./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Brugere")

3.  I sektionen **Create User** skal du udføre følgende trin:

    ![Oprette bruger] (./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Oprette bruger")

    1.  Skriv det **brugernavn**, **navn**, **mail**, **Ny adgangskode**, **Gentag ny adgangskode** til en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden AppDynamics bruger konto værktøjer til oprettelse eller API'er leveres af AppDynamics til at klargøre Azure AD-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Hvis du vil tildele brugere til AppDynamics, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **AppDynamics **application integration.

    ![Tildele brugere] (./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

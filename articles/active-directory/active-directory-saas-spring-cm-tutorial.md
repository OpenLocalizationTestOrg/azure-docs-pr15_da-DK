<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med foråret CM | Microsoft Azure" 
    description="Lær, hvordan du bruger foråret CM med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-spring-cm"></a>Selvstudium: Azure Active Directory-integration med foråret CM
  
Formålet med dette selvstudium er at se, hvordan du konfigurerer enkeltlogon mellem Azure Active Directory og SpringCM.
  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En SpringCM single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure Active Directory-brugere, du har tildelt til SpringCM kunne enkeltlogon ved hjælp af panelet AAD Access.

1.  Aktivere programmet integrationen for SpringCM
2.  Konfiguration af Single Sign-On
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Scenarie")

##<a name="enabling-the-application-integration-for-springcm"></a>Aktivere programmet integrationen for SpringCM
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for SpringCM.

###<a name="to-enable-the-application-integration-for-springcm-perform-the-following-steps"></a>For at aktivere programmet integration for SpringCM skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **SpringCM**.

    ![Galleri med programmet] (./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Galleri med programmet")

7.  Vælg **SpringCM**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![SpringCM] (./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##<a name="configuring-single-sign-on"></a>Konfiguration af Single Sign-On
  
Dette afsnit beskrives, hvordan du aktiverer brugere til at godkende SpringCM med deres konto i Azure Active Directory, ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **SpringCM** application integration.

    ![Konfigurere single Sign-On] (./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Konfigurere single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge SpringCM** , og klik derefter på **Næste**.

    ![Konfigurere single Sign-On] (./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Konfigurere single Sign-On")

3.  Skriv URL-adressen bruges af dine brugere til at logge din SpringCM-program, og klik derefter på **Næste**på siden **Konfigurer App URL-adressen** i tekstfeltet **SpringCM Log på URL-adresse** . 

    App URL-adressen er din SpringCM lejer URL-adresse (f.eks.: *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*):

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på SpringCM** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere enkelt in] (./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Konfigurere enkelt in")

5.  I et andet webbrowser-vindue, du logge webstedet **SpringCM** virksomhed som administrator.

6.  Klik på **Gå til**i menuen øverst, klik på **Indstillinger**, og klik derefter på **SAML SSO**i sektionen **Kontoindstillinger** .

    ![SAML SSO] (./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  I afsnittet identitet udbyder konfiguration skal du udføre følgende trin:

    ![Konfiguration af identitet] (./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Konfiguration af identitet")

    1.  Hvis du vil overføre dit hentede Azure Active Directory-certifikat, skal du klikke på **Vælg udstedercertifikat** eller **Skifte udstedercertifikat**.
    2.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på siden **Konfigurer single sign-on – på SpringCM** , og Indsæt det i tekstfeltet **udsteder** .
    3.  Kopiér værdien **Singel Sign-On URL-adressen** på Azure-klassisk portalen på siden **Konfigurer single sign-on – på SpringCM** , og Indsæt det i tekstfeltet **Service Provider (SP) startet slutpunkt** .
    4.  Som **SAML aktiveret**, kan du vælge **Aktivér**.
    5.  Klik på **Gem**.

8.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere enkelt in] (./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Konfigurere enkelt in")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure Active Directory-brugere at logge på SpringCM, skal de klargøres til SpringCM.  
Klargøring af er SpringCM, en manuel opgave.

>[AZURE.NOTE] Se [oprette og redigere en SpringCM bruger](http://knowledge.springcm.com/create-and-edit-a-springcm-user) kan finde flere oplysninger

###<a name="to-provision-a-user-account-to-springcm-perform-the-following-steps"></a>Hvis du vil tildele en brugerkonto til SpringCM, skal du udføre følgende trin:

1.  Log på webstedet **SpringCM** virksomhed som administrator.

2.  Klik på **Gå til**, og klik derefter på **Adressekartotek**.

    ![Oprette bruger] (./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Oprette bruger")

3.  Klik på **Opret bruger**.

4.  Vælg en **brugerrolle**.

5.  Vælg **Send aktivering mail**.

6.  Skriv fornavn, Efternavn og e-mail-adressen på en gyldig Azure Active Directory brugerkonto, du vil klargøre i den relaterede tekstbokse.

7.  Føje brugeren til en **sikkerhedsgruppe**.

8.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden SpringCM bruger konto værktøjer til oprettelse eller API'er, som SpringCM til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-springcm-perform-the-following-steps"></a>Hvis du vil tildele brugere til SpringCM, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **SpringCM** application integration.

    ![Tildele brugere] (./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.





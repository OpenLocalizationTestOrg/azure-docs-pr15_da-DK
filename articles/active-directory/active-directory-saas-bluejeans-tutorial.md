<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med BlueJeans | Microsoft Azure" 
    description="Lær, hvordan du bruger BlueJeans med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-ad-integration-with-bluejeans"></a>Selvstudium: Azure AD-Integration med BlueJeans

Formålet med dette selvstudium er at vise integration af Azure og BlueJeans.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En BlueJeans single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til BlueJeans kunne enkelt Log på programmet på webstedet BlueJeans virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for BlueJeans
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scenarie")
##<a name="enabling-the-application-integration-for-bluejeans"></a>Aktivere programmet integrationen for BlueJeans

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for BlueJeans.

###<a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>For at aktivere programmet integration for BlueJeans skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **BlueJeans**.

    ![Galleri med programmet] (./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Galleri med programmet")

7.  Vælg **BlueJeans**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![BlueJeans] (./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende BlueJeans med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **BlueJeans** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge BlueJeans** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://company.BlueJeans.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **BlueJeans Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på BlueJeans** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Konfigurere Single Sign-On")

5.  Log på webstedet **BlueJeans** virksomhed som administrator i et andet browservindue.

6.  Gå til **administrator \> gruppeindstillinger \> sikkerhed**.

    ![Administrator] (./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Administrator")

7.  I sektionen **sikkerhed** skal du udføre følgende trin:

    ![SAML Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign-On")

    1.  Vælg **SAML Single Sign-On**.
    2.  Vælg **Aktivér automatisk klargøring**.

8.  Gå videre med følgende trin:

    ![Certifikat sti] (./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Certifikat sti")

    1.  Klik på **Vælg fil**, og overfør derefter det certifikat, der er hentet.
    2.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på BlueJeans** dialogboksen siden, og Indsæt det i tekstfeltet **URL-adressen til logon** .
    3.  Kopiér værdien **Skift adgangskode URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på BlueJeans** dialogboksen siden, og Indsæt det i tekstfeltet **Adgangskode ændre URL-adresse** .
    4.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på BlueJeans** dialogboksen siden, og Indsæt det i tekstfeltet **Log URL-adresse** .

9.  Gå videre med følgende trin:

    ![Gemme ændringer] (./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Gemme ændringer")

    1.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**i tekstfeltet **bruger-id** .
    2.  I tekstfeltet **mail** skal du skrive **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    3.  Klik på **Gem ændringer**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på BlueJeans, skal de klargøres til BlueJeans.  
Klargøring af er BlueJeans, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **BlueJeans** virksomhed som administrator.

2.  Gå til **administrator \> administrere brugere \> Tilføj bruger**.

    ![Administrator] (./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Administrator")

    >[AZURE.IMPORTANT] Fanen **Tilføj bruger** er kun tilgængelig, hvis **fanen Sikkerhed**skal **aktivere automatisk klargøring** er markeret.

3.  I sektionen **Tilføj bruger** skal du udføre følgende trin:

    ![Tilføj bruger] (./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Tilføj bruger")

    1.  Skrive et **BlueJeans brugernavn**, en **e-mail-adresse**, et **BlueJeans møde-ID**, en **Redaktør adgangskode**, et **Fuldt navn**, **virksomhed** af en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Tilføj bruger**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden BlueJeans bruger konto værktøjer til oprettelse eller API'er, som BlueJeans til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Hvis du vil tildele brugere til BlueJeans, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **BlueJeans **application integration.

    ![Tildele brugere] (./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

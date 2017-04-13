<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med NetSuite | Microsoft Azure"
    description="Lær, hvordan du bruger NetSuite med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#<a name="tutorial-how-to-integrate-netsuite-with-azure-active-directory"></a>Selvstudium: Hvordan du integrerer NetSuite med Azure Active Directory

Dette selvstudium viser du, hvordan du knytter dit NetSuite miljø til din Azure Active Directory (Azure AD). Du kan lære, hvordan du konfigurerer single sign-on til NetSuite, hvordan du aktiverer automatiseret brugerklargøring og hvordan du kan tildele brugere skal have adgang til NetSuite. 

##<a name="prerequisites"></a>Forudsætninger

1. For at få adgang til Azure Active Directory via [Azure klassisk portal](https://manage.windowsazure.com), skal du først har et gyldigt Azure-abonnement.

2. Du skal have administratoradgang til et [NetSuite](http://www.netsuite.com/portal/home.shtml) abonnement. Du kan bruge en gratis prøveversion konto til hver tjeneste.

##<a name="step-1-add-netsuite-to-your-directory"></a>Trin 1: Tilføje NetSuite på adresselisten

1. Klik på **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), i den venstre navigationsrude.

    ![Vælg Active Directory i den venstre navigationsrude.][0]

2. Vælg den mappe, som du gerne vil tilføje NetSuite til listen **adresseliste** .

3. Klik på **programmer** i den øverste menu.

    ![Klik på programmer.][1]

4. Klik på **Tilføj** i bunden af siden.

    ![Klik på Tilføj for at tilføje et nyt program.][2]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Klik på Tilføj et program i galleriet.][3]

6. I **søgefeltet**skal du skrive **NetSuite**. Derefter Vælg **NetSuite** fra resultaterne, og klik på **udført** for at tilføje programmet.

    ![Tilføje NetSuite.][4]

7. Du bør nu se siden Hurtig Start for NetSuite:

    ![Netsuites Hurtig Start-siden i Azure AD][5]

##<a name="step-2-enable-single-sign-on"></a>Trin 2: Aktivere Single Sign-On

1. Klik på knappen **konfigurere Enkeltlogon** i Azure AD, på siden Hurtig Start for NetSuite.

    ![Enkelt sign-on – knappen Konfigurer][6]

2. Der åbnes en dialogboks, får du vist et skærmbillede, der beder om "Hvordan vil du brugere til at logge NetSuite?" Vælg **Azure AD Single Sign-On**, og klik derefter på **Næste**.

    ![Vælg Azure AD-Single Sign-On][7]

    > [AZURE.NOTE] At lære mere om de forskellige enkelte sign-on – indstillinger, [skal du klikke her](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. På siden **Konfigurer App indstillinger** for feltet **URL-adresse til svar** , Skriv din NetSuite-lejer URL-adresse, der er ved hjælp af en af følgende formater:
    - `https://<tenant-name>.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.netsuite.com/saml2/acs`
    - `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    ![Skriv din lejer URL-adresse][8]

4. Klik på **Hent metadata**på siden **Konfigurer single sign-on – på NetSuite** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Hent metadata.][9]

5. Åbn en ny fane i browseren, og logge på webstedet Netsuite virksomhed som administrator.

6. Værktøjslinjen øverst på siden skal du klikke på **installationen**, og klik derefter **Setup Manager**.

    ![Gå til konfiguration af Manager][10]

7. Vælg **Integration**fra listen **Konfigurationsopgaver** .

    ![Gå til Integration][11]

8. Klik på **SAML Single Sign-on**i afsnittet **Administrere godkendelse** .

    ![Gå til SAML Single Sign-on][12]

9. På siden **Konfiguration af SAML** skal du udføre følgende trin:

    - Kopiere værdien **Remote Login URL-adressen** i Azure Active Directory, og sæt dem ind i feltet **Identitet udbyder logonside** i NetSuite.

        ![SAML konfigurationssiden.][13]

    - Vælg **Primære godkendelsesmetode**i NetSuite.

    - Vælg **Overfør IDP Metadata fil**for feltet med navnet **SAMLV2 identitet udbyder Metadata**. Klik derefter på **Gennemse** for at overføre metadatafilen, som du har hentet i trin #4.

        ![Overføre metadata][16]

    - Klik på **Send**.

9. Vælg enkelt sign-on – konfiguration bekræftelse for at aktivere det certifikat, du har overført til NetSuite i Azure AD. Klik derefter på **Næste**.

    ![Markér afkrydsningsfeltet bekræftelse][14]

10. Skriv i en e-mailadresse, hvis du vil gerne modtage besked via mail for fejl og advarsler relateret til vedligeholdelse af denne enkelt sign-on – konfiguration på den sidste side i dialogboksen. 

    ![Skriv din mailadresse.][15]

11. Klik på **udført** for at lukke dialogboksen. Klik derefter på **attributter** øverst på siden.

    ![Klik på attributter.][17]

12. Klik på **Tilføj bruger attribut**.

    ![Klik på Tilføj bruger attributten.][18]

13. Skriv i for feltet **Attributnavn** `account`. Feltet **Attributværdien** at skrive i dit NetSuite konto-ID. Oplysninger om, hvordan du finder din konto-ID er medtaget nedenfor.

    ![Tilføje dit NetSuite konto-ID.][19]

    - Klik på **konfiguration** i den øverste navigationsmenu i NetSuite.
    - Klik derefter på under afsnittet **Konfigurationsopgaver** i den venstre navigationsmenu, vælg afsnittet **Integration** , og klik på **Web Services indstillinger**.
    - Kopiér dit NetSuite konto-ID og sætte det ind i feltet **Attributværdien** i Azure AD.

        ![Få din konto-ID][20]

14. Klik på **udført** for at afslutte tilføjelse attributten SAML i Azure AD. Klik derefter på **Anvend ændringer** på nederst i menuen.

    ![Gem dine ændringer.][21]

15. Før brugere kan udføre enkeltlogon til NetSuite, skal de først være tildelt de relevante tilladelser i NetSuite. Følg vejledningen nedenfor for at tildele disse tilladelser.

    - I menuen øverste navigationslinje skal du klikke på **installationen**, og klik derefter **Setup Manager**.

        ![Gå til konfiguration af Manager][10]

    - I venstre navigationsrude i menuen Vælg **Brugere/roller**, og klik derefter på **Administrer roller**.

        ![Gå til at administrere roller][22]

    - Klik på **ny rolle**.

    - Skriv et **navn** til ny rolle, og Markér afkrydsningsfeltet **Enkelt Sign-On kun** .

        ![Navngiv den nye rolle.][23]

    - Klik på **Gem**.

    - Klik på **tilladelser**i menuen øverst. Klik derefter på **installation**.

        ![Gå til tilladelser][24]

    - Vælg **Angiv op Søren Single Sign-on**, og klik derefter på **Tilføj**.

    - Klik på **Gem**.

    - I menuen øverste navigationslinje skal du klikke på **installationen**, og klik derefter **Setup Manager**.

        ![Gå til konfiguration af Manager][10]

    - I venstre navigationsrude i menuen Vælg **Brugere/roller**, og klik derefter på **Administrer brugere**.

        ![Gå til at administrere brugere][25]

    - Vælg en testbruger. Klik derefter på **Rediger**.

        ![Gå til at administrere brugere][26]

    - Vælg den rolle, du har oprettet, og klik på **Tilføj**i dialogboksen roller.

        ![Gå til at administrere brugere][27]

    - Klik på **Gem**.

19. For at teste din konfiguration, skal du se afsnittet nedenfor med titlen [Tildel brugere til NetSuite](#step-4-assign-users-to-netsuite).

##<a name="step-3-enable-automated-user-provisioning"></a>Trin 3: Aktivér automatisk klargøring af bruger

> [AZURE.NOTE] Som standard, klargjort brugerne føjes til rod datterselskaber i dit NetSuite-miljø.

1. Klik på **Konfigurer brugerklargøring**på siden Hurtig Start for NetSuite, i Azure Active Directory.

    ![Konfigurere brugerklargøring af][28]

2. Skrive i dine Administratoroplysninger til NetSuite i den dialogboks, der åbnes, og derefter klikke på **Næste**.

    ![Skriv dine NetSuite Administratoroplysninger.][29]

3. Skriv i din e-mail-adresse til at modtage beskeder om klargøring af mislykkede forsøg på siden confirmation.

    ![Bekræft.][30]

4. Klik på **udført** for at lukke dialogboksen.

##<a name="step-4-assign-users-to-netsuite"></a>Trin 4: Føje brugere til NetSuite

1. For at teste din konfiguration, kan du begynde at oprette en ny testkonto i kataloget.

2. Klik på knappen **Tildel brugere** på siden NetSuite Hurtig Start.

    ![Klik på Tildel brugere][31]

3. Vælg din testbruger, og klik på knappen **tildele** nederst på skærmen:

 - Hvis du ikke har aktivere automatiseret brugerklargøring, og derefter får du vist følgende meddelelse for at bekræfte:

        ![Confirm the assignment.][32]

 - Hvis du har aktiveret Automatiske bruger klargøring, får du vist en meddelelse om at angive, hvilken type rolle brugeren skal have i NetSuite. Nyligt klargjort brugere skal vises i dit miljø, NetSuite efter et par minutter.

4. Åbn panelet Access på [https://myapps.microsoft.com](https://myapps.microsoft.com/), log på kontoen test, og klik på **NetSuite**for at teste dine enkelt sign-on – indstillinger.

##<a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Liste over selvstudier om, hvordan du integrerer SaaS Apps](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png

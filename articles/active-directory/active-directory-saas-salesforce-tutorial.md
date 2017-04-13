<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Salesforce | Microsoft Azure"
    description="Lær, hvordan du bruger Salesforce med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!"
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

#<a name="tutorial-how-to-integrate-salesforce-with-azure-active-directory"></a>Selvstudium: Hvordan du integrerer Salesforce med Azure Active Directory

Dette selvstudium viser du, hvordan du knytter dit Salesforce-miljø til din Azure Active Directory. Du skal lære, hvordan du konfigurerer single sign-on til Salesforce, aktivere automatiseret brugerklargøring og til at tildele brugere skal have adgang til Salesforce.

##<a name="prerequisites"></a>Forudsætninger

1. For at få adgang til Azure Active Directory via [Azure klassisk portal](https://manage.windowsazure.com), skal du først har et gyldigt Azure-abonnement.

2. Du skal have en gyldig lejer i [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Hvis du bruger en **prøveversion** Salesforce.com-konto, vil du derefter ikke kunne konfigurere automatiseret brugerklargøring. Prøveversion konti har ikke de nødvendige API-adgang aktiveret, før de bliver købt.
> 
> Du kan finde rundt i denne begrænsning ved hjælp af en [gratis udvikler konto](https://developer.salesforce.com/signup) til at udføre dette selvstudium.

Hvis du bruger et Salesforce lukket miljø, skal du se [Salesforce sandkassetilstand integration selvstudium](https://go.microsoft.com/fwLink/?LinkID=521879).

##<a name="video-tutorials"></a>Videoselvstudier

Du kan følge dette selvstudium, ved hjælp af de nedenstående videoer.

**Video selvstudium del en: Sådan aktiveres Single Sign-On**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Anden del video Selvstudium: Hvordan du kan automatisere brugerklargøring**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##<a name="step-1-add-salesforce-to-your-directory"></a>Trin 1: Tilføje Salesforce på adresselisten

1. Klik på **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), i den venstre navigationsrude.

    ![Vælg Active Directory i den venstre navigationsrude.][0]

2. Vælg den mappe, som du gerne vil tilføje Salesforce til listen **adresseliste** .

3. Klik på **programmer** i den øverste menu.

    ![Klik på programmer.][1]

4. Klik på **Tilføj** i bunden af siden.

    ![Klik på Tilføj for at tilføje et nyt program.][2]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Klik på Tilføj et program i galleriet.][3]

6. I **søgefeltet**skal du skrive **Salesforce**. Derefter Vælg **Salesforce** resultaterne, og klik på **udført** for at tilføje programmet.

    ![Tilføj Salesforce.][4]

7. Du bør nu se siden Hurtig Start til Salesforce:

    ![Salesforces Hurtig Start-siden i Azure AD][5]

##<a name="step-2-enable-single-sign-on"></a>Trin 2: Aktivere enkeltlogon

1. Før du kan konfigurere single sign-on, skal du konfigurere og implementere et brugerdefineret domæne til dit Salesforce-miljø. Flere oplysninger om, hvordan du gør dette, under [Angive nogle Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. Klik på knappen **Konfigurer enkeltlogon** Salesforces Hurtig Start-siden i Azure AD.

    ![Enkelt sign-on – knappen Konfigurer][6]

3. Der åbnes en dialogboks, får du vist et skærmbillede, der beder om "Hvordan vil du brugere til at logge Salesforce?" Vælg **Azure AD Single Sign-On**, og klik derefter på **Næste**.

    ![Vælg Azure AD-Single Sign-On][7]

    > [AZURE.NOTE] At lære mere om de forskellige enkelte sign-on – indstillinger, [skal du klikke her](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

4. På siden **Konfigurer App indstillinger** ved at udfylde **Log på URL-adresse** ved at skrive i din Salesforce-domæne URL-adresse, der er i følgende format:
 - Virksomhed-konto:`https://<domain>.my.salesforce.com`
 - Udvikler konto:`https://<domain>-dev-ed.my.salesforce.com` 

    ![Skriv din Sign-On URL-adresse][8]

5. Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Salesforce** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Hent certifikat][9]

6. Åbn en ny fane i browseren, og log på din Salesforce-administratorkonto.

7. Klik på **Sikkerhedskontrol** for at udvide sektionen Relaterede under navigationsruden i **Administrator** . Klik derefter på **Indstillinger for enkelt Sign-On**.

    ![Klik på enkelt Sign-On-indstillinger under Sikkerhedskontrolelementer til][10]

8. Klik på knappen **Rediger** på siden **Indstillinger for enkelt Sign-On** .

    ![Klik på knappen Rediger][11]

    > [AZURE.NOTE] Hvis du er i stand til at aktivere Single Sign-On-indstillinger for din Salesforce-konto, skal du muligvis kontakte Salesforce's support for at have funktionen er aktiveret for dig.

9. Vælg **SAML aktiveret**, og klik derefter på **Gem**.

    ![Vælg SAML aktiveret][12]

10. Klik på **Ny**for at konfigurere dine SAML enkelt sign-on – indstillinger.

    ![Vælg SAML aktiveret][13]

11. Foretage følgende konfigurationer på SAML enkelt Sign-On indstilling siden **Rediger** :

    ![Skærmbillede af konfigurationer, skal du sikre][14]

 - Skriv et brugervenligt navn til denne konfiguration for feltet **navn** . Give en værdi for **navn** automatisk at udfylde tekstboksen **API** .

 - Kopiér værdien **Udsteder URL-adressen** i Azure AD, og Indsæt det i feltet **udsteder** i Salesforce.

 - Skriv domænenavnet Salesforce ved hjælp af følgende mønster i **tekstfeltet enheds-Id**:
     - Virksomhed-konto:`https://<domain>.my.salesforce.com`
     - Udvikler konto:`https://<domain>-dev-ed.my.salesforce.com`

 - Klik på **Gennemse** eller **Vælg fil** for at åbne dialogboksen **Vælg fil til overførsel** , Vælg din Salesforce-certifikat, og klik derefter på **Åbn** for at overføre certifikatet.

 - **SAML identitetstype**skal du vælge **program indeholder brugerens salesforce.com brugernavn**.

 - Vælg **identitet er i NameIdentifier elementet i sætningen emne** for **SAML identitet placering**

 - Kopiér værdien **Remote Login URL-adressen** i Azure AD, og Indsæt det i feltet **Identitet udbyder Login URL-adresse** i Salesforce.

 - Vælg **HTTP omdirigering**for **Service Provider startet anmode om indbinding**.

 - Til sidst skal du klikke på **Gem** for at anvende dine SAML enkelt sign-on – indstillinger.

12. Klik på **Domain Management** for at udvide sektionen Relaterede på den venstre navigationsrude i Salesforce, og klik derefter på **Mit domæne**.

    ![Klik på mit domæne][15]

13. Rul ned til sektionen **Konfiguration af godkendelse** , og klik på knappen **Rediger** .

    ![Klik på knappen Rediger][16]

14. Vælg det fulde navn på din SAML SSO-konfiguration i sektionen **Godkendelsestjenesten** , og klik derefter på **Gem**.

    ![Vælg din SSO-konfiguration][17]

    > [AZURE.NOTE] Hvis mere end én godkendelsestjenesten er markeret, derefter når brugerne forsøger at starte single sign-on til dit Salesforce-miljø, de vil blive bedt om at vælge hvilke godkendelsestjenesten, de vil have til at logge på med. Hvis du ikke ønsker dette, skal du **lade alle andre godkendelsestjenester, der er markeret**.

15. Vælg enkelt sign-on – konfiguration bekræftelse for at aktivere det certifikat, du har overført til Salesforce i Azure AD. Klik derefter på **Næste**.

    ![Markér afkrydsningsfeltet bekræftelse][18]

16. Skriv i en e-mailadresse, hvis du vil gerne modtage besked via mail for fejl og advarsler relateret til vedligeholdelse af denne enkelt sign-on – konfiguration på den sidste side i dialogboksen. 

    ![Skriv din mailadresse.][19]

17. Klik på **udført** for at lukke dialogboksen. For at teste din konfiguration, skal du se afsnittet nedenfor med titlen [Tildel brugere til Salesforce](#step-4-assign-users-to-salesforce).

##<a name="step-3-enable-automated-user-provisioning"></a>Trin 3: Aktivér automatisk brugerklargøring af

1. Klik på knappen **Konfigurer brugerklargøring** i siden Azure AD Hurtig Start for Salesforce.

    ![Klik på knappen konfigurere klargøring af bruger][20]

2. Skriv dit Salesforce administrator brugernavn og din adgangskode i dialogboksen **Konfigurer brugerklargøring** .

    ![Skriv i din administrator brugernavn eller adgangskode][21]

    > [AZURE.NOTE] Hvis du konfigurerer et produktionsmiljø, er den bedste måde at oprette en ny administrator i Salesforce specifikt for dette trin. Denne konto skal have tildelt i Salesforce **Systemadministrator** profilen.

3. For at få din Salesforce-sikkerhed token skal du åbne en ny fane og logge ind i den samme Salesforce-administratorkonto. Klik på dit navn i øverste højre hjørne på siden, og klik derefter på **Indstillinger for mit**.

    ![Klik på navn og derefter klikke på Indstillinger for mit][22]

4. Klik på **personlige** at udvide sektionen Relaterede i den venstre navigationsrude, og klik derefter på **Nulstil min sikkerheds-id**.

    ![Klik på navn og derefter klikke på Indstillinger for mit][23]

5. Klik på knappen **Nulstil sikkerhedstoken** på siden **Nulstil min sikkerheds-id** .

    ![Læs advarsler.][24]

6. Markér den Indbakke, der er knyttet til denne administratorkonto. Søg efter en mail fra Salesforce.com, der indeholder det nye sikkerhedstoken.

7. Kopiér tokenet, gå til Azure AD-vinduet, og sæt dem ind i feltet **Bruger sikkerheds-id** . Klik derefter på **Næste**.

    ![Sæt ind i det pågældende sikkerhedstoken][25]

8. På siden Confirmation, kan du vælge at modtage besked via mail til, når klargøring fejl opstår. Klik på **udført** for at lukke dialogboksen.

    ![Skriv i din e-mail-adresse til at modtage meddelelser][26]

##<a name="step-4-assign-users-to-salesforce"></a>Trin 4: Føje brugere til Salesforce

1. For at teste din konfiguration, du starte med at oprette en ny testkonto i kataloget.

2. Klik på knappen **Tildel brugere** på siden Salesforce Hurtig Start.

    ![Klik på Tildel brugere][27]

3. Vælg din testbruger, og klik på knappen **tildele** nederst på skærmen:

 - Hvis du ikke har aktivere automatiseret brugerklargøring, og derefter får du vist følgende meddelelse for at bekræfte:

        ![Confirm the assignment.][28]

 - Hvis du har aktiveret Automatiske bruger klargøring, får du vist en meddelelse om at angive, hvilken type Salesforce profil brugeren skal have. Nyligt klargjort brugere skal vises i dit miljø, Salesforce efter et par minutter.

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] Hvis du klargør til et Salesforce **udvikler** miljø, har du en meget begrænset antal licenser, der er tilgængelige for hver profil. Det er bedst at klargøring af brugere til **Chatter gratis** brugerprofil, som har 4,999 tilgængelige licenser.

4. Åbn panelet Access på [https://myapps.microsoft.com](https://myapps.microsoft.com/), for at teste dine enkelt sign-on – indstillinger, og derefter logge på kontoen test, og klik på **Salesforce**.

##<a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Liste over selvstudier om, hvordan du integrerer SaaS Apps](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png

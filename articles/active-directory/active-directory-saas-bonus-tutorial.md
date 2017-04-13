<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Bonus.ly | Microsoft Azure" 
    description="Lær, hvordan du bruger Bonus.ly med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Selvstudium: Azure Active Directory-Integration med Bonus.ly

Formålet med dette selvstudium er at vise integration af Azure og Bonus.ly. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En test lejer i Bonus.ly

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Bonus.ly
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-bonus-tutorial/IC773679.png "Scenarie")
##<a name="enabling-the-application-integration-for-bonusly"></a>Aktivere programmet integrationen for Bonus.ly

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Bonus.ly.

###<a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>For at aktivere programmet integration for Bonus.ly skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Aktivere enkeltlogon] (./media/active-directory-saas-bonus-tutorial/IC773680.png "Aktivere enkeltlogon")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-bonus-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-bonus-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-bonus-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Bonus.ly**.

    ![Galleri med programmet] (./media/active-directory-saas-bonus-tutorial/IC773681.png "Galleri med programmet")

7.  Vælg **Bonus.ly**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Bonus.ly med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Bonus.ly, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Bonus.ly** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-bonus-tutorial/IC749323.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Bonus.ly** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-bonus-tutorial/IC773683.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Bonus.ly lejer URL-adressen** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. Bonus.ly*", og klik derefter på **Næste**: 

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-bonus-tutorial/IC773684.png "Konfigurere app URL-adresse")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Bonus.ly** , og Gem derefter certifikatfilen lokalt som **c:\\Bonusly.cer**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-bonus-tutorial/IC773685.png "Konfigurere Enkeltlogon")

5.  Log på din **Bonus.ly** lejer i et andet browservindue.

6.  Klik på **Indstillinger**på værktøjslinjen øverst, og vælg derefter **integrationer og apps**.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  Vælg under **Single Sign-On** **SAML**.

8.  På siden **SAML** dialogboksen skal du udføre følgende trin:

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Bonus.ly** dialogboksen siden, og Indsæt det i tekstfeltet **IdP SSO URL-adresse** .
    2.  Kopiér værdien **Udsteder-ID** i Azure klassisk-portalen på **Konfigurer single sign-on – på Bonus.ly** dialogboksen siden, og Indsæt det i tekstfeltet **IdP udsteder** .
    3.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Bonus.ly** dialogboksen siden, og Indsæt det i tekstfeltet **IdP Login URL-adresse** .
    4.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **Cert fingeraftryk** .

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

9.  Klik på **Gem**.

10. Vælg konfiguration bekræftelse på portalen Microsoft Azure klassisk, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-bonus-tutorial/IC773689.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Bonus.ly, skal de klargøres til Bonus.ly.  
Klargøring af er Bonus.ly, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på din Bonus.ly lejer i et browservindue.

2.  Klik på **Indstillinger**

    ![Indstillinger] (./media/active-directory-saas-bonus-tutorial/IC781041.png "Indstillinger")

3.  Klik på fanen **brugere og bonus** .

    ![Brugere og bonus] (./media/active-directory-saas-bonus-tutorial/IC781042.png "Brugere og bonus")

4.  Klik på **Administrer brugere**.

    ![Administrere brugere] (./media/active-directory-saas-bonus-tutorial/IC781043.png "Administrere brugere")

5.  Klik på **Tilføj bruger**.

    ![Tilføj bruger] (./media/active-directory-saas-bonus-tutorial/IC781044.png "Tilføj bruger")

6.  I dialogboksen **Tilføj bruger** skal du udføre følgende trin:

    ![Tilføj bruger] (./media/active-directory-saas-bonus-tutorial/IC781045.png "Tilføj bruger")

    1.  Skriv "**mail**, **Fornavn**, **Efternavn**" på en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Gem**.

    >[AZURE.NOTE] AAD kontoindehaver modtager en mail, der indeholder et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Bonus.ly bruger konto værktøjer til oprettelse eller API'er, som Bonus.ly til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>Hvis du vil tildele brugere til Bonus.ly, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden Bonus.ly application integration.

    ![Tildele brugere] (./media/active-directory-saas-bonus-tutorial/IC773690.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-bonus-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

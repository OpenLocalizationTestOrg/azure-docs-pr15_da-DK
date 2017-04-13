<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med FreshService | Microsoft Azure" 
    description="Lær, hvordan du bruger FreshService med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Selvstudium: Azure Active Directory-integration med FreshService
  
Formålet med dette selvstudium er at vise integration af Azure og FreshService.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En FreshService single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til FreshService kunne enkelt Log på det program, ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for FreshService
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scenarie")
##<a name="enabling-the-application-integration-for-freshservice"></a>Aktivere programmet integrationen for FreshService
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for FreshService.

###<a name="to-enable-the-application-integration-for-freshservice-perform-the-following-steps"></a>For at aktivere programmet integration for FreshService skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-freshservice-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-freshservice-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-freshservice-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **FreshService**.

    ![Galleri med programmet] (./media/active-directory-saas-freshservice-tutorial/IC790808.png "Galleri med programmet")

7.  Vælg **FreshService**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Freshservice] (./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende FreshService med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til FreshService, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **FreshService** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790810.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge FreshService** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790811.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse, der bruges af dine brugere til at logge din Freshdesk-program i tekstfeltet **FreshService Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*http://democompany.freshservice.com/*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-freshservice-tutorial/IC790812.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på FreshService** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790813.png "Konfigurere Single Sign-On")

5.  Log på webstedet FreshService virksomhed som administrator i et andet browservindue.

6.  Klik på **administrator**i menuen øverst.

    ![Administrator] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administrator")

7.  **Kunde-portalen**, klik på **sikkerhed**.

    ![Sikkerhed] (./media/active-directory-saas-freshservice-tutorial/IC790815.png "Sikkerhed")

8.  I sektionen **sikkerhed** skal du udføre følgende trin:

    ![Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign-On")

    1.  Skifte **Single Sign-OnON**.
    2.  Vælg **SAML SSO**.
    3.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på FreshService** dialogboksen siden, og Indsæt det i tekstfeltet **SAML Login URL-adresse** .
    4.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på FreshService** dialogboksen siden, og Indsæt det i tekstfeltet **Log URL-adresse** .
    5.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **Sikkerhed certifikat fingeraftryk** .
    
        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790817.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på FreshService, skal de klargøres til FreshService.  
Klargøring af er FreshService, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **FreshService** virksomhed som administrator.

2.  Klik på **administrator**i menuen øverst.

    ![Administrator] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administrator")

3.  Klik på **anmodere**i sektionen **Brugeradministration** .

    ![Anmodere] (./media/active-directory-saas-freshservice-tutorial/IC790818.png "Anmodere")

4.  Klik på **ny anmoder**.

    ![Ny anmodere] (./media/active-directory-saas-freshservice-tutorial/IC790819.png "Ny anmodere")

5.  I sektionen **Nye anmoder** skal du udføre følgende trin:

    ![Ny anmoder] (./media/active-directory-saas-freshservice-tutorial/IC790820.png "Ny anmoder")

    1.  Angiv **Fornavn** og **mail** attributterne for en gyldig Azure Active Directory-konto, du vil at blive klargjort i den relaterede tekstbokse.
    2.  Klik på **Gem**.

    >[AZURE.NOTE] Azure Active Directory-kontoindehaver får mails, der indeholder et link for at bekræfte kontoen, før den bliver aktiv

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden FreshService bruger konto værktøjer til oprettelse eller API'er, som FreshService til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-freshservice-perform-the-following-steps"></a>Hvis du vil tildele brugere til FreshService, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **FreshService **application integration.

    ![Tildele brugere] (./media/active-directory-saas-freshservice-tutorial/IC790821.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-freshservice-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
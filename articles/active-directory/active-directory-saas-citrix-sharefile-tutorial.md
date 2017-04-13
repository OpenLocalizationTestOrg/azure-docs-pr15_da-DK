<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Citrix ShareFile | Microsoft Azure" 
    description="Lær, hvordan du bruger Citrix ShareFile med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Selvstudium: Azure Active Directory-Integration med Citrix ShareFile

Formålet med dette selvstudium er at vise integration af Azure og Citrix ShareFile.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Citrix ShareFile lejer

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Citrix ShareFile kunne enkelt Log på programmet på webstedet Citrix ShareFile virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integration til Citrix ShareFile
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scenarie")
##<a name="enabling-the-application-integration-for-citrix-sharefile"></a>Aktivere programmet integration til Citrix ShareFile

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integration til Citrix ShareFile.

###<a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>For at aktivere programmet integration for Citrix ShareFile skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Citrix ShareFile**.

    ![Galleri med programmet] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Galleri med programmet")

7.  Vælg **Citrix ShareFile**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Citrix ShareFile] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Citrix ShareFile med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Citrix ShareFile** application integration.

    ![Aktivere enkeltlogon] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Aktivere enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge Citrix ShareFile** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Citrix ShareFile Log på URL-adressen** på siden **Konfigurer App URL-adressen** `https://<tenant-name>.shareFile.com`, og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Citrix ShareFile** , og Gem derefter certifikatfilen på din computer.

    ![ConfigureSingle Sign-On] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle Sign-On")

5.  Log på webstedet **Citrix ShareFile** virksomhed som administrator i et andet browservindue.

6.  Klik på **administrator**i værktøjslinjen øverst.

7.  Vælg **Konfigurere Single Sign-On**i den venstre navigationsrude.

    ![Kontoadministration] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Kontoadministration")

8.  På den **Single Sign-On / SAML 2.0 konfiguration** dialogboksside under **Grundlæggende indstillinger**skal du udføre følgende trin:

    ![Single sign-on] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Single sign-on")

    1.  Klik på **Aktiver SAML**.
    2.  Kopiér værdien **Enheds-ID** i Azure klassisk-portalen på **Konfigurer single sign-on – på Citrix ShareFile** dialogboksen siden, og derefter indsætte det i den **dine IDP udsteder / enheds-ID** tekstfelt.
    3.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Citrix ShareFile** dialogboksen siden, og Indsæt det i tekstfeltet **URL-adressen til logon** .
    4.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **den konfigurere Enkeltlogon på Citrix ShareFile** dialogboksside, og Indsæt det i tekstfeltet **Log URL-adresse** .
    5.  Klik på **Skift** ud for feltet **X.509-certifikat** , og overfør derefter det certifikat, du har hentet fra portalen Azure AD klassisk.
        ![Grundlæggende indstillinger] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Grundlæggende indstillinger")

9.  Klik på **Gem** på Citrix ShareFile management-portalen.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Citrix ShareFile, skal de klargøres til Citrix ShareFile.  
Hvis det er Citrix ShareFile er klargøring en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **Citrix ShareFile** lejer.

2.  Klik på **administrere brugere \> administrere brugere hjem \> + Opret medarbejder**.

    ![Oprette medarbejder] (./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Oprette medarbejder")

3.  Angiv **mailen**, **Fornavn** og **Efternavn** af en gyldig Azure AD-konto, du vil klargøre.

    ![Grundlæggende oplysninger] (./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Grundlæggende oplysninger")

4.  Klik på **Tilføj bruger**.

    >[AZURE.NOTE] AAD kontoindehaver vil modtage en mail og følge et link for at bekræfte deres konto, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Citrix ShareFile bruger konto værktøjer til oprettelse eller API'er, som Citrix ShareFile til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>Hvis du vil tildele brugere til Citrix ShareFile, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Citrix ShareFile **application integration.

    ![Tildele brugere] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

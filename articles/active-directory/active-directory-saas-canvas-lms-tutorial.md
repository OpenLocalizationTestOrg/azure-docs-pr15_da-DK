<properties
    pageTitle="Selvstudium: Azure Active Directory-Integration med lærred LMS | Microsoft Azure" 
    description="Lær, hvordan du bruger lærred LMS med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Selvstudium: Azure Active Directory-Integration med lærred LMS

Formålet med dette selvstudium er at vise integration af Azure og lærredet.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   Et lærred lejer

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til lærred kunne enkelt Log på programmet på webstedet lærred virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integration til lærred
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scenarie")
##<a name="enabling-the-application-integration-for-canvas"></a>Aktivere programmet integration til lærred

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for lærredet.

###<a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>For at aktivere programmet integration for lærredet skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **lærred**.

    ![Galleri med programmet] (./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Galleri med programmet")

7.  Vælg **lærredet**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Lærred] (./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Lærred")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere Sådan brugerne mulighed for at godkende til lærred med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til lærredet, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **lærred** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge lærredet** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Lærred log i URL-adresse** på siden **Konfigurer App URL-adressen** `https://<tenant-name>.instructure.com`, og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på lærredet** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Konfigurere Single Sign-On")

5.  Log på din lærred virksomheds websted som en administrator i et andet browservindue.

6.  Gå til **kurser \> administrerede konti \> Microsoft**.

    ![Lærred] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Lærred")

7.  Vælg **godkendelse**i navigationsruden i venstre side, og klik derefter på **Tilføj ny SAML Config**.

    ![Godkendelse] (./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Godkendelse")

8.  På siden aktuelle Integration skal du udføre følgende trin:

    ![Aktuelle Integration] (./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Aktuelle Integration")

    1.  Kopiér værdien **Enheds-ID** i Azure klassisk-portalen på dialogboksside **konfigurere Enkeltlogon på lærredet** , og Indsæt det i tekstfeltet **IdP enheds-ID** .
    2.  Kopiér **URL-Remote Login** værdien i Azure klassisk-portalen på dialogboksside **konfigurere Enkeltlogon på lærredet** , og Indsæt det i tekstfeltet **Log på URL-adresse** .
    3.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på dialogboksside **konfigurere Enkeltlogon på lærredet** , og Indsæt det i tekstfeltet **Log af URL-adresse** .
    4.  Kopiér værdien **Skift adgangskode URL-adressen** på Azure-klassisk portalen på dialogboksside **konfigurere Enkeltlogon på lærredet** , og Indsæt det i tekstfeltet **Linket Skift adgangskode** .
    5.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **Certifikat fingeraftryk** .  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    6.  Vælg **NameID**fra listen **Login attribut** .
    7.  Vælg **mailadresse**på listen **Id Format** .
    8.  Klik på **Gem godkendelsesindstillinger**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på lærredet, skal de klargøres i lærredet.  
Klargøring af er lærredet, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **lærred** lejer.

2.  Gå til **kurser \> administrerede konti \> Microsoft**.

    ![Lærred] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Lærred")

3.  Klik på **brugere**.

    ![Brugere] (./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Brugere")

4.  Klik på **Tilføj ny bruger**.

    ![Brugere] (./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Brugere")

5.  På siden Tilføj en ny bruger dialogboksside skal du udføre følgende trin:

    ![Tilføj bruger] (./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Tilføj bruger")

    1.  Skriv brugerens navn i tekstfeltet **Fulde navn** .
    2.  Skriv brugerens mailadresse i tekstfeltet **mail** .
    3.  Skriv brugerens Azure AD-mailadresse i tekstfeltet **Login** .
    4.  Vælg **mail brugeren om oprettelse af denne konto**.
    5.  Klik på **Tilføj bruger**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden lærred bruger konto værktøjer til oprettelse eller API'er, som lærred til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Hvis du vil tildele brugere til lærredet, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **lærred **application integration.

    ![Tildele brugere] (./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

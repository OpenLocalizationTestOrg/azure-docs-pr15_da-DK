<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med SmarterU | Microsoft Azure" 
    description="Lær, hvordan du bruger SmarterU med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Selvstudium: Azure Active Directory-Integration med SmarterU
  
Formålet med dette selvstudium er at vise integration af Azure og SmarterU.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En SmarterU lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til SmarterU kunne enkelt Log på programmet på webstedet SmarterU virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for SmarterU
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-smarteru-tutorial/IC777320.png "Scenarie")

##<a name="enabling-the-application-integration-for-smarteru"></a>Aktivere programmet integrationen for SmarterU
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for SmarterU.

###<a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>For at aktivere programmet integration for SmarterU skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-smarteru-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-smarteru-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-smarteru-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **SmarterU**.

    ![Programmet fallery] (./media/active-directory-saas-smarteru-tutorial/IC777321.png "Programmet fallery")

7.  Vælg **SmarterU**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![SmarterU] (./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende SmarterU med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **SmarterU** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777323.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge SmarterU** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777324.png "Konfigurere Single Sign-On")

3.  Hente din metadata, skal du klikke på **Hent metadata**på **Konfigurer single sign-on – på SmarterU** , og derefter dataene filer lokalt som **c:\\SmarterUMetaData.cer**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777325.png "Konfigurere Single Sign-On")

4.  Log på webstedet SmarterU virksomhed som administrator i et andet browservindue.

5.  Klik på **Kontoindstillinger**på værktøjslinjen øverst.

    ![Kontoindstillinger] (./media/active-directory-saas-smarteru-tutorial/IC777326.png "Kontoindstillinger")

6.  På konfigurationssiden af konto skal du udføre følgende trin:

    ![Eksterne godkendelse] (./media/active-directory-saas-smarteru-tutorial/IC777327.png "Eksterne godkendelse")

    1.  Vælg **Aktiver eksterne godkendelse**.
    2.  Vælg fanen **SmarterU** i sektionen **Master Login kontrol** .
    3.  Vælg fanen **SmarterU** i sektionen **Standard brugerlogon** .
    4.  Vælg **Aktiver Okta**.
    5.  Kopiere indholdet af metadatafilen hentede, og Indsæt det i tekstfeltet **Okta Metadata** .
    6.  Klik på **Gem**.

7.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777328.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på SmarterU, skal de klargøres til SmarterU.  
Klargøring af er SmarterU, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **SmarterU** lejer.

2.  Gå til **brugere**.

3.  I sektionen bruger skal du udføre følgende trin:

    ![Ny bruger] (./media/active-directory-saas-smarteru-tutorial/IC777329.png "Ny bruger")

    1.  Klik på **+ bruger**.
    2.  Skriv relateret attributværdierne for Azure AD-brugerkontoen i følgende tekstfelterne: **primær mail** **Medarbejder-ID** **adgangskode**, **Bekræft adgangskode**, **får navn**, og **Efternavn**.
    3.  Klik på **aktiv**.
    4.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden SmarterU bruger konto værktøjer til oprettelse eller API'er, som SmarterU til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>Hvis du vil tildele brugere til SmarterU, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **SmarterU **application integration.

    ![Tildele brugere] (./media/active-directory-saas-smarteru-tutorial/IC777330.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-smarteru-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med drivhusgasser | Microsoft Azure" 
    description="Lær, hvordan du bruger drivhusgasser med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Selvstudium: Azure Active Directory-integration med drivhusgasser
  
Formålet med dette selvstudium er at vise integration af Azure og drivhusgasser.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   Et drivhusgasser enkelt sign-on – abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til drivhusgasser kunne enkelt Log på programmet på webstedet drivhusgasser virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for drivhusgasser
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scenarie")
##<a name="enabling-the-application-integration-for-greenhouse"></a>Aktivere programmet integrationen for drivhusgasser
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for drivhusgasser.

###<a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>For at aktivere programmet integration for drivhusgasser skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **drivhusgasser**.

    ![Galleri med programmet] (./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Galleri med programmet")

7.  Vælg **drivhusgasser**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Drivhusgasser] (./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Drivhusgasser")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende drivhusgasser med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **drivhusgasser** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge drivhusgasser** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://company.greenhouse.io*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på drivhusgasser** , og Gem derefter metadatafilen lokalt på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Konfigurere Enkeltlogon")

5.  Videresend metadatafilen til drivhusgasser supportteam.

    >[AZURE.NOTE] Enkeltlogon skal aktiveres af drivhusgasser supportteam.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på drivhusgasser, skal de klargøres til drivhusgasser.  
Klargøring af er væksthus, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **drivhusgasser** virksomhed som administrator.

2.  Klik på **Konfigurer**i menuen øverst, og klik derefter på **brugere**.

    ![Brugere] (./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Brugere")

3.  Klik på **nye brugere**.

    ![Ny bruger] (./media/active-directory-saas-greenhouse-tutorial/IC790792.png "Ny bruger")

4.  I sektionen **Tilføj ny bruger** skal du udføre følgende trin:

    ![Tilføje ny bruger] (./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Tilføje ny bruger")

    1.  Skriv en gyldig Azure Active Directory-konto, du vil klargøre mailadresse i tekstfeltet **Enter brugernes mail** .
    2.  Klik på **Gem**.
        
        >[AZURE.NOTE] Azure Active Directory-kontohavere modtager mails, der indeholder et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst andre drivhusgasser bruger konto værktøjer til oprettelse eller API'er leveres af drivhusgasser til klargøring AAD-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>Hvis du vil tildele brugere til drivhusgasser, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **drivhusgasser **application integration.

    ![Tildele brugere] (./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
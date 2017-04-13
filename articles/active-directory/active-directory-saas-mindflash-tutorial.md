<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Mindflash | Microsoft Azure" 
    description="Lær, hvordan du bruger Mindflash med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-mindflash"></a>Selvstudium: Azure Active Directory-integration med Mindflash
  
Formålet med dette selvstudium er at vise integration af Azure og Mindflash.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Mindflash single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Mindflash kunne enkelt Log på programmet på webstedet Mindflash virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Mindflash
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-mindflash-tutorial/IC787132.png "Scenarie")
##<a name="enabling-the-application-integration-for-mindflash"></a>Aktivere programmet integrationen for Mindflash
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Mindflash.

###<a name="to-enable-the-application-integration-for-mindflash-perform-the-following-steps"></a>For at aktivere programmet integration for Mindflash skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-mindflash-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-mindflash-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-mindflash-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Mindflash**.

    ![Galleri med programmet] (./media/active-directory-saas-mindflash-tutorial/IC787133.png "Galleri med programmet")

7.  Vælg **Mindflash**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Mindflash] (./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Mindflash med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Mindflash** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mindflash-tutorial/IC787135.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Mindflash** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mindflash-tutorial/IC787136.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*http://company.mindflash.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-mindflash-tutorial/IC787137.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på Mindflash** , og Gem derefter Metadatafilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mindflash-tutorial/IC787138.png "Konfigurere Single Sign-On")

5.  Sende metadatafile til Mindflash-supportteam.

    >[AZURE.NOTE] Konfigurationen af enkelt sign-on – der skal udføres af Mindflash supportteam. Du får en meddelelse, så snart konfigurationen er fuldført.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mindflash-tutorial/IC787139.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Mindflash, skal de klargøres til Mindflash.  
Klargøring af er Mindflash, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Mindflash** virksomhed som administrator.

2.  Gå til at **administrere brugere**.

    ![Administrere brugere] (./media/active-directory-saas-mindflash-tutorial/IC787140.png "Administrere brugere")

3.  Klik på **Tilføj brugere**, og klik derefter på **Ny**.

4.  I sektionen **Tilføj nye brugere** skal du udføre følgende trin:

    ![Tilføje nye brugere] (./media/active-directory-saas-mindflash-tutorial/IC787141.png "Tilføje nye brugere")

    1.  Skriv **Fornavn**, **Efternavn** og **mail** med en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Tilføj**.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden Mindflash bruger konto værktøjer til oprettelse eller API'er, som Mindflash til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-mindflash-perform-the-following-steps"></a>Hvis du vil tildele brugere til Mindflash, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Mindflash **application integration.

    ![Tildele brugere] (./media/active-directory-saas-mindflash-tutorial/IC787142.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-mindflash-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
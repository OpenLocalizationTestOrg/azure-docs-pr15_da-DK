<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Projectplace | Microsoft Azure" 
    description="Lær, hvordan du bruger Projectplace med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Selvstudium: Azure Active Directory-integration med Projectplace
  
Formålet med dette selvstudium er at vise integration af Azure og Projectplace.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Projectplace single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Projectplace kunne enkelt Log på programmet på webstedet Projectplace virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Projectplace
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scenarie")
##<a name="enabling-the-application-integration-for-projectplace"></a>Aktivere programmet integrationen for Projectplace
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Projectplace.

###<a name="to-enable-the-application-integration-for-projectplace-perform-the-following-steps"></a>For at aktivere programmet integration for Projectplace skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-projectplace-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-projectplace-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-projectplace-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Projectplace**.

    ![Galleri med programmet] (./media/active-directory-saas-projectplace-tutorial/IC790218.png "Galleri med programmet")

7.  Vælg **Projectplace**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![ProjectPlace] (./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Projectplace med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Projectplace** application integration.

    ![Konfigurere enkelt in] (./media/active-directory-saas-projectplace-tutorial/IC790220.png "Konfigurere enkelt in")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Projectplace** , og klik derefter på **Næste**.

    ![Konfigurere enkelt in] (./media/active-directory-saas-projectplace-tutorial/IC790221.png "Konfigurere enkelt in")

3.  Skriv din ProjectPlace lejer URL-adresse i tekstfeltet **Projectplace Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*http://company.projectplace.com*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-projectplace-tutorial/IC790222.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på Projectplace** , og Gem derefter Metadatafilen på din computer.

    ![Konfigurere enkelt in] (./media/active-directory-saas-projectplace-tutorial/IC790223.png "Konfigurere enkelt in")

5.  Sende metadatafile til Projectplace-supportteam.

    >[AZURE.NOTE] Konfigurationen af enkelt sign-on – der skal udføres af Projectplace supportteam. Du får en meddelelse, så snart konfigurationen er fuldført.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere enkelt in] (./media/active-directory-saas-projectplace-tutorial/IC790227.png "Konfigurere enkelt in")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Projectplace, skal de klargøres til Projectplace.  
Klargøring af er Projectplace, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Projectplace** virksomhed som administrator.

2.  Gå til **personer**, og klik derefter på **medlemmer**.

    ![Personer] (./media/active-directory-saas-projectplace-tutorial/IC790228.png "Personer")

3.  Klik på **Tilføj medlem**.

    ![Tilføje medlemmer] (./media/active-directory-saas-projectplace-tutorial/IC790232.png "Tilføje medlemmer")

4.  I sektionen **Tilføj medlem for** at udføre følgende trin:

    ![Nye medlemmer] (./media/active-directory-saas-projectplace-tutorial/IC790233.png "Nye medlemmer")

    1.  Skriv en gyldig AAD-konto, du vil klargøre til relaterede tekstfelterne mailadresse i tekstfeltet **Nye medlemmer** .
    2.  Klik på **Send**

        >[AZURE.NOTE] Der sendes en mail, herunder et link for at bekræfte kontoen, før den bliver aktiv til Azure Active Directory-Kontoindehaver.
    
>[AZURE.NOTE]Du kan bruge en hvilken som helst anden Projectplace bruger konto værktøjer til oprettelse eller API'er, som Projectplace til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-projectplace-perform-the-following-steps"></a>Hvis du vil tildele brugere til Projectplace, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Projectplace **application integration.

    ![Tildele brugere] (./media/active-directory-saas-projectplace-tutorial/IC790234.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-projectplace-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Kontiki | Microsoft Azure" 
    description="Lær, hvordan du bruger Kontiki med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Selvstudium: Azure Active Directory-integration med Kontiki
  
Formålet med dette selvstudium er at vise integration af Azure og Kontiki.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Kontiki single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Kontiki kunne enkelt Log på programmet på webstedet Kontiki virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Kontiki
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-kontiki-tutorial/IC790235.png "Scenarie")
##<a name="enabling-the-application-integration-for-kontiki"></a>Aktivere programmet integrationen for Kontiki
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Kontiki.

###<a name="to-enable-the-application-integration-for-kontiki-perform-the-following-steps"></a>For at aktivere programmet integration for Kontiki skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-kontiki-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-kontiki-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-kontiki-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Kontiki**.

    ![Galleri med programmet] (./media/active-directory-saas-kontiki-tutorial/IC790236.png "Galleri med programmet")

7.  Vælg **Kontiki**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Kontiki] (./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Kontiki med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Kontiki** application integration.

    ![Konfigurere enkelt in] (./media/active-directory-saas-kontiki-tutorial/IC790238.png "Konfigurere enkelt in")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Kontiki** , og klik derefter på **Næste**.

    ![Konfigurere enkelt in] (./media/active-directory-saas-kontiki-tutorial/IC790239.png "Konfigurere enkelt in")

3.  Skriv URL-adressen bruges af dine brugere til at logge Kontiki i tekstfeltet **Kontiki Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://company.mc.eval.kontiki.com/*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-kontiki-tutorial/IC790240.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på Kontiki** , og Gem derefter Metadatafilen på din computer.

    ![Konfigurere enkelt in] (./media/active-directory-saas-kontiki-tutorial/IC790241.png "Konfigurere enkelt in")

5.  Sende metadatafile til Kontiki-supportteam.

    >[AZURE.NOTE] Konfigurationen af enkelt sign-on – der skal udføres af Kontiki supportteam. Du får en meddelelse, så snart konfigurationen er fuldført.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere enkelt in] (./media/active-directory-saas-kontiki-tutorial/IC790242.png "Konfigurere enkelt in")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til Kontiki.  
Når en tildelt brugeren forsøger at logge på ved hjælp af panelet access Kontiki, kontrollerer Kontiki om brugeren findes.  
Hvis der findes ingen brugerkonto endnu, oprettes den automatisk ved Kontiki.
##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-kontiki-perform-the-following-steps"></a>Hvis du vil tildele brugere til Kontiki, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Kontiki **application integration.

    ![Tildele brugere] (./media/active-directory-saas-kontiki-tutorial/IC790243.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-kontiki-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Abintegro | Microsoft Azure" 
    description="Lær, hvordan du bruger Abintegro med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-abintegro"></a>Selvstudium: Azure Active Directory-integration med Abintegro

Formålet med dette selvstudium er at vise integration af Azure og Abintegro.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Abintegro single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Abintegro kunne enkelt Log på programmet på webstedet Abintegro virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Abintegro
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-abintegro-tutorial/IC790076.png "Scenarie")
##<a name="enabling-the-application-integration-for-abintegro"></a>Aktivere programmet integrationen for Abintegro

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Abintegro.

###<a name="to-enable-the-application-integration-for-abintegro-perform-the-following-steps"></a>For at aktivere programmet integration for Abintegro skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-abintegro-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-abintegro-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-abintegro-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **abintegro**.

    ![Galleri med programmet] (./media/active-directory-saas-abintegro-tutorial/IC790077.png "Galleri med programmet")

7.  Vælg **Abintegro**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Abintegro] (./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Abintegro med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Abintegro** application integration.

    ![Konfigurere enkelt in] (./media/active-directory-saas-abintegro-tutorial/IC790079.png "Konfigurere enkelt in")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Abintegro** , og klik derefter på **Næste**.

    ![Konfigurere enkelt in] (./media/active-directory-saas-abintegro-tutorial/IC790080.png "Konfigurere enkelt in")

3.  Skriv URL-adressen bruges af dine brugere til at logge Abintegro i tekstfeltet **Abintegro Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-abintegro-tutorial/IC790081.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på Abintegro** , og Gem derefter Metadatafilen på din computer.

    ![Konfigurere enkelt in] (./media/active-directory-saas-abintegro-tutorial/IC790082.png "Konfigurere enkelt in")

5.  Sende metadatafile til Abintegro-supportteam.

    >[AZURE.NOTE] Konfigurationen af enkelt sign-on – der skal udføres af Abintegro supportteam. Du får en meddelelse, så snart konfigurationen er fuldført.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere enkelt in] (./media/active-directory-saas-abintegro-tutorial/IC790083.png "Konfigurere enkelt in")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til Abintegro.  
Når en tildelt brugeren forsøger at logge på ved hjælp af panelet access Abintegro, kontrollerer Abintegro om brugeren findes.  
Hvis der findes ingen brugerkonto endnu, oprettes den automatisk ved Abintegro.
##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-abintegro-perform-the-following-steps"></a>Hvis du vil tildele brugere til Abintegro, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Abintegro **application integration.

    ![Tildele brugere] (./media/active-directory-saas-abintegro-tutorial/IC790084.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-abintegro-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

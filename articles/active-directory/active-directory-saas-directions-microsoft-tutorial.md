<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med kørselsvejledning på Microsoft | Microsoft Azure" 
    description="Lær, hvordan du bruger kørselsvejledning på Microsoft med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Selvstudium: Azure Active Directory-integration med kørselsvejledning på Microsoft

Formålet med dette selvstudium er at få vist integration af Azure Active Directory og kørselsvejledning på Microsoft.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En vejledning i Microsoft-abonnement

Hvis du ikke har et medlem af organisationsnetværket kørselsvejledning på Microsoft abonnement endnu, kan du sende en anmodning om at "*service@DirectionsOnMicrosoft.com*".

Når du har fuldført dette selvstudium, skal du har tildelt til kørselsvejledning på Microsoft Azure Active Directory-brugerne kunne enkelt Log på det program, ved hjælp af single sign-on –.

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for vejledning i at Microsoft
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Scenarie")
##<a name="enabling-the-application-integration-for-directions-on-microsoft"></a>Aktivere programmet integrationen for vejledning i at Microsoft

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for vejledning i at Microsoft.

###<a name="to-enable-the-application-integration-for-directions-on-microsoft-perform-the-following-steps"></a>For at aktivere programmet integration for vejledning i at Microsoft skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  Skriv **Kørselsvejledning på Microsoft**i **søgefeltet**.

    ![Galleri med programmet] (./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Galleri med programmet")

7.  Vælg **Kørselsvejledning på Microsoft**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Scenarie] (./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Scenarie")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende kørselsvejledning på Microsoft med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Kørselsvejledning på Microsoft** application integration.

    ![Aktivere Single Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Aktivere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge kørselsvejledning på Microsoft** , og klik derefter på **Næste**.

    ![Microsoft Azure AD Singel Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD Singel Sign-On")

3.  Skriv **https://www.directionsonmicrosoft.com/user/login**på siden **Konfigurer App URL-adressen** i tekstfeltet Log på URL-adressen, og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på kørselsvejledning på Microsoft** , og Gem derefter metadatafilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Konfigurere Single Sign-On")

5.  Sende metadatafilen til vejledningen på Microsoft supportteam (*service@DirectionsOnMicrosoft.com*). For at aktivere kørselsvejledning på Microsoft support-teamet til at finde dit medlemskab af organisationsnetværket websted skal du medtage dine firmaoplysninger i din mail.

    >[AZURE.NOTE] Single sign-on til kørselsvejledning på Microsoft skal aktiveres af vejledningen på Microsoft supportteam.
Du får en meddelelse, når enkeltlogon er blevet aktiveret.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til kørselsvejledning på Microsoft.  
Når en tildelt brugeren forsøger at logge på kørselsvejledning på Microsoft ved hjælp af panelet access, kontrollerer kørselsvejledning på Microsoft om brugeren findes. Hvis der findes ingen brugerkonto endnu, oprettes den automatisk ved kørselsvejledning på Microsoft.
##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-directions-on-microsoft-perform-the-following-steps"></a>Hvis du vil tildele brugere til kørselsvejledning på Microsoft, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Kørselsvejledning på Microsoft **application integration.

    ![Tildele brugere] (./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Ja")

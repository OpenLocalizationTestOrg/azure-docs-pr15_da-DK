<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Clarizen | Microsoft Azure" 
    description="Lær, hvordan du bruger Clarizen med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Selvstudium: Azure Active Directory-integration med Clarizen

Formålet med dette selvstudium er at vise integration af Azure og Clarizen.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Clarizen single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Clarizen kunne enkelt Log på programmet på webstedet Clarizen virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Clarizen
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenarie")
##<a name="enabling-the-application-integration-for-clarizen"></a>Aktivere programmet integrationen for Clarizen

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Clarizen.

###<a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>For at aktivere programmet integration for Clarizen skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-clarizen-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-clarizen-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-clarizen-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Clarizen**.

    ![Galleri med programmet] (./media/active-directory-saas-clarizen-tutorial/IC784680.png "Galleri med programmet")

7.  Vælg **Clarizen**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Clarizen] (./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Clarizen med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Clarizen** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784682.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Clarizen** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784683.png "Konfigurere Single Sign-On")

3.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Clarizen** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784684.png "Konfigurere Single Sign-On")

4.  Log på webstedet **Clarizen** virksomhed som administrator i et andet browservindue (f.eks.: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Klik på dit brugernavn, og klik derefter på **Indstillinger**.

    ![Indstillinger] (./media/active-directory-saas-clarizen-tutorial/IC784685.png "Indstillinger")

6.  Klik på fanen **Globale indstillinger** , og klik derefter på **Rediger**ud for **Samlet godkendelse**.

    ![Globale indstillinger] (./media/active-directory-saas-clarizen-tutorial/IC786906.png "Globale indstillinger")

7.  I dialogboksen **Del af et organisationsnetværk godkendelse** skal du udføre følgende trin:

    ![Samlet godkendelse] (./media/active-directory-saas-clarizen-tutorial/IC785892.png "Samlet godkendelse")

    1.  Klik på **Overfør** for at overføre dit hentede certifikat.
    2.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Clarizen** dialogboksen siden, og Indsæt det i tekstfeltet **logon URL-adresse** .
    3.  Kopiér værdien **Enkelt Sign-Out URL-adressen** på Azure-klassisk portalen på siden **Konfigurer enkelt logge på Clarizen** dialogboksen, og Indsæt det i tekstfeltet **Sign-out URL-adresse** .
    4.  Vælg **Brug INDLÆG**.
    5.  Klik på **Gem**.

8.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784688.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Clarizen, skal de klargøres til Clarizen.  
Klargøring af er Clarizen, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Clarizen** virksomhed som administrator.

2.  Klik på **personer**.

    ![Personer] (./media/active-directory-saas-clarizen-tutorial/IC784689.png "Personer")

3.  Klik på **Inviter bruger**.

    ![Invitere brugere] (./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invitere brugere")

4.  På siden dialogboksen Inviter personer skal du udføre følgende trin:

    ![Invitere personer] (./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invitere personer")

    1.  Skriv en gyldig Azure Active Directory-konto, du vil klargøre mailadresse i tekstfeltet **mail** .
    2.  Klik på **Inviter**.

    >[AZURE.NOTE] Azure Active Directory-kontoindehaver vil modtage en mail og følge et link for at bekræfte deres konto, før den bliver aktiv.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>Hvis du vil tildele brugere til Clarizen, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Clarizen **application integration.

    ![Tildele brugere] (./media/active-directory-saas-clarizen-tutorial/IC784692.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-clarizen-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

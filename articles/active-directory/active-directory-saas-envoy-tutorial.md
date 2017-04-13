<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Envoy | Microsoft Azure" 
    description="Lær, hvordan du bruger Envoy med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-envoy"></a>Selvstudium: Azure Active Directory-integration med Envoy
  
Formålet med dette selvstudium er at vise integration af Azure og Envoy.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Envoy lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Envoy kunne enkelt Log på programmet på webstedet Envoy virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Envoy
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-envoy-tutorial/IC776759.png "Scenarie")
##<a name="enabling-the-application-integration-for-envoy"></a>Aktivere programmet integrationen for Envoy
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Envoy.

###<a name="to-enable-the-application-integration-for-envoy-perform-the-following-steps"></a>For at aktivere programmet integration for Envoy skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-envoy-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-envoy-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-envoy-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Envoy**.

    ![Galleri med programmet] (./media/active-directory-saas-envoy-tutorial/IC776760.png "Galleri med programmet")

7.  Vælg **Envoy**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Envoy] (./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Envoy med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Envoy, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Envoy** application integration.

    ![Aktivere enkeltlogon] (./media/active-directory-saas-envoy-tutorial/IC776778.png "Aktivere enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Envoy** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-envoy-tutorial/IC776779.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Envoy log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. Envoy.com*", og klik derefter på **Næste**.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-envoy-tutorial/IC776780.png "Konfigurere app URL-adresse")

4.  På siden **Konfigurer single sign-on – på Envoy** for at downloade certifikatet, skal du klikke på **Hent certifikat**, og Gem derefter certifikatfil lokalt som **c:\\Envoy.cer**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-envoy-tutorial/IC776781.png "Konfigurere Enkeltlogon")

5.  Log på webstedet Envoy virksomhed som administrator i et andet browservindue.

6.  Klik på **Indstillinger**på værktøjslinjen øverst.

    ![Envoy] (./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")

7.  Klik på **firma**.

    ![Virksomhed] (./media/active-directory-saas-envoy-tutorial/IC776783.png "Virksomhed")

8.  Klik på **SAML**.

    ![SAML] (./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  I afsnittet **SAML godkendelse** konfiguration skal du udføre følgende trin:

    ![SAML godkendelse] (./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML godkendelse")

    >[AZURE.NOTE] Værdien for HQ placering ID er automatisk genereret af programmet.

    1.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **fingeraftryk** .  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    2.  Kopiér værdien **SAML SSO URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Envoy** dialogboksen siden, og Indsæt det i tekstfeltet **Identitet udbyder HTTP SAML URL-adresse** .
    3.  Klik på **Gem ændringer**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-envoy-tutorial/IC776786.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til Envoy.  
Når en tildelt brugeren forsøger at logge på ved hjælp af panelet access Envoy, kontrollerer Envoy om brugeren findes.  
Hvis der findes ingen brugerkonto endnu, oprettes den automatisk ved Envoy.
##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Hvis du vil tildele brugere til Envoy, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Envoy **application integration.

    ![Tildele brugere] (./media/active-directory-saas-envoy-tutorial/IC776787.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-envoy-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
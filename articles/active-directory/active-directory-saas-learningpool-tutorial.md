<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Learningpool | Microsoft Azure" 
    description="Lær, hvordan du bruger Learningpool med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Selvstudium: Azure Active Directory-integration med Learningpool
  
Formålet med dette selvstudium er at vise integration af Azure og Learningpool.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Learningpool single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Learningpool kunne enkelt Log på programmet på webstedet Learningpool virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Learningpool
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scenarie")
##<a name="enabling-the-application-integration-for-learningpool"></a>Aktivere programmet integrationen for Learningpool
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Learningpool.

###<a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>For at aktivere programmet integration for Learningpool skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-learningpool-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-learningpool-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-learningpool-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Learningpool**.

    ![Galleri med programmet] (./media/active-directory-saas-learningpool-tutorial/IC795073.png "Galleri med programmet")

7.  Vælg **Learningpool**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Learningpool] (./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Learningpool med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.
  
Programmets Learningpool forventer SAML påstande i et bestemt format, som kræver, at du kan tilføje brugerdefineret attributtilknytninger til konfigurationen af **saml token attributter** .  
Følgende skærmbillede viser et eksempel af dette.

![SAML Token attributter] (./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML Token attributter")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i Azure klassisk-portalen på **Learningpool** programmets integrationsside, i menuen i øverst.

    ![Attributter] (./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributter")

2.  For at tilføje påkrævet attributtilknytningerne skal du udføre følgende trin:

    ###  

  	|Attributnavn                |Attributværdien            |
  	|------------------------------|---------------------------|

     urn: oid:1.2.840.113556.1.4.221 | User.userPrincipalName
  	|-------------------------------|--------------------------|  
     urn: oid:2.5.4.42|User.givenname   
  	|urn: oid:0.9.2342.19200300.100.1.3|User.mail
  	|urn: oid:2.5.4.4|User.surname

    1.  Klik på **Tilføj bruger attribut**for hver datarække i tabellen ovenfor.
    2.  Skriv det attributnavn, der vises for den pågældende række i tekstfeltet **Attributnavn** .
    3.  Vælg attributværdien vises for den pågældende række fra listen **Attributværdien** .
    4.  Klik på **udført**.

3.  Klik på **Anvend ændringer**.

4.  I din browser skal du klikke på **tilbage** for at åbne dialogboksen **Hurtig Start** igen.

5.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Singel Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795076.png "Konfigurere Singel Sign-On")

6.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Learningpool** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795077.png "Konfigurere Single Sign-On")

7.  Skriv URL-adressen bruges af dine brugere til at logge din Learningpool-program i tekstfeltet **Learningpool Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: https://parliament.preview.learningpool.com/auth/shibboleth/index.php), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-learningpool-tutorial/IC795078.png "Konfigurere App URL-adresse")

8.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på Learningpool** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795079.png "Konfigurere Single Sign-On")

9.  Videresend metadatafilen til din Learningpool supportteam.

    >[AZURE.NOTE]Enkeltlogon skal aktiveres af Learningpool supportteam.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795080.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Learningpool, skal de klargøres til Learningpool.
  
Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til Learningpool.  
Brugere skal oprettes ved at supportteamet Learningpool.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden Learningpool bruger konto værktøjer til oprettelse eller API'er, som Learningpool til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>Hvis du vil tildele brugere til Learningpool, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Learningpool **application integration.

    ![Tildele brugere] (./media/active-directory-saas-learningpool-tutorial/IC795081.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-learningpool-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Benefitsolver | Microsoft Azure"
    description="Lær, hvordan du bruger Benefitsolver med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Selvstudium: Azure Active Directory-integration med Benefitsolver

Formålet med dette selvstudium er at vise integration af Azure og Benefitsolver.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Benefitsolver single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Benefitsolver kunne enkelt Log på ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)programmet.

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Benefitsolver
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Scenarie")
##<a name="enabling-the-application-integration-for-benefitsolver"></a>Aktivere programmet integrationen for Benefitsolver

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Benefitsolver.

###<a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>For at aktivere programmet integration for Benefitsolver skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Benefitsolver**.

    ![Galleri med programmet] (./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Galleri med programmet")

7.  Vælg **Benefitsolver**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Benefitssolver] (./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Benefitsolver med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Programmets Benefitsolver forventer SAML påstande i et bestemt format, som kræver, at du kan tilføje brugerdefineret attributtilknytninger til konfigurationen af **saml token attributter** .  
Følgende skærmbillede viser et eksempel af dette.

![Attributter] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributter")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Benefitsolver** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Benefitsolver** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Konfigurere Single Sign-On")

3.  På siden **Konfigurer App indstillinger** ved at udføre følgende trin:

    ![Konfigurere indstillinger for] (./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Konfigurere indstillinger for")

    1.  Skriv **http://azure.benefitsolver.com**i tekstfeltet **Log på URL-adresse** .
    2.  Skriv **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**i tekstfeltet **Svar URL-adresse** .  


    3.  Klik på **Næste**.

4.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på Benefitsolver** , og Gem derefter metadatafilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Konfigurere Single Sign-On")

5.  Sende metadatafilen hentede til supportteamet Benefitsolver.

    >[AZURE.NOTE] Supportteamet Benefitsolver har at gøre den faktiske SSO-konfiguration.
Du får en meddelelse, når SSO er blevet aktiveret for dit abonnement.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Konfigurere Single Sign-On")

7.  Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i menuen øverst.

    ![Attributter] (./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Attributter")

8.  For at tilføje påkrævet attributtilknytningerne skal du udføre følgende trin:

    ![Attributter] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributter")

  	|Attributnavn|Attributværdien|
  	|---|---|
  	|ClientID|Du har brug at få denne værdi fra supportteamet Benefitsolver.|
  	|ClientKey|Du har brug at få denne værdi fra supportteamet Benefitsolver.|
  	|LogoutURL|Du har brug at få denne værdi fra supportteamet Benefitsolver.|
  	|Medarbejder-id|Du har brug at få denne værdi fra supportteamet Benefitsolver.|

    1.  Klik på **Tilføj bruger attribut**for hver datarække i tabellen ovenfor.
    2.  Skriv det attributnavn, der vises for den pågældende række i tekstfeltet **Attributnavn** .
    3.  Vælg attributværdien vises for den pågældende række i tekstfeltet **Attributværdien** .
    4.  Klik på **udført**.

9.  Klik på **Anvend ændringer**.

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Benefitsolver, skal de klargøres til Benefitsolver.  
Hvis det er Benefitsolver er medarbejderdata i dit program, der er udfyldt via en tælling fil fra dit HRIS system (typisk om natten).  

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Benefitsolver bruger konto værktøjer til oprettelse eller API'er, som Benefitsolver til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>Hvis du vil tildele brugere til Benefitsolver, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Benefitsolver **application integration.

    ![Tildele brugere] (./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

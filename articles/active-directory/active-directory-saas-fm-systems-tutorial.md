<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med FM-: systemer | Microsoft Azure" 
    description="Lær at bruge FM: systemer med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-fm-systems"></a>Selvstudium: Azure Active Directory-integration med FM-: systemer
  
Formålet med dette selvstudium er at vise integration af Azure og FM:Systems.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En FM:Systems single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til FM:Systems kunne enkelt Log på programmet på webstedet FM:Systems virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for FM:Systems
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Scenarie")
##<a name="enabling-the-application-integration-for-fmsystems"></a>Aktivere programmet integrationen for FM:Systems
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for FM:Systems.

###<a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>For at aktivere programmet integration for FM:Systems skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **FM:Systems**.

    ![Galleri med programmet] (./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Galleri med programmet")

7.  Vælg **FM:Systems**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![FM: systemer] (./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: systemer")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er at oprette en disposition for at aktivere brugere til at godkende FM:Systems med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **FM:Systems** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge FM:Systems** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Konfigurere Single Sign-On")

3.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Konfigurere App URL-adresse")

    1.  Skriv FM:Systems **Svar URL-adressen** i tekstfeltet **FM:Systems Log på URL-adresse** (f.eks.: *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  

        >[AZURE.WARNING] Du kan finde denne værdi fra din FM: systemer dit supportteam.

    2.  Klik på **Næste**

4.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på FM:Systems** , og Gem derefter metadata på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Konfigurere Single Sign-On")

5.  Sender hentede metadatafilen til din FM: systemer dit supportteam.

    >[AZURE.NOTE] Din FM: Systemer supportteam har at gøre den faktiske SSO-konfiguration.
Du får en meddelelse, når SSO er blevet aktiveret for dit abonnement.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på FM:Systems, skal de klargøres til FM:Systems.  
Klargøring af er FM:Systems, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet FM:Systems virksomhed som administrator i et browservindue.

2.  Gå til **System Administration \> Administrer sikkerhed \> brugere \> brugerlisten**.

    ![Systemadministration af] (./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Systemadministration af")

3.  Klik på **Opret ny bruger**.

    ![Oprette ny bruger] (./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Oprette ny bruger")

4.  I sektionen **Create User** skal du udføre følgende trin:

    ![Oprette bruger] (./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Oprette bruger")

    1.  Skriv brugernavnet, adgangskoden og dens bekræftelse, mailadressen og medarbejder-ID for en gyldig Azure Active Directory-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Næste**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden FM:Systems bruger konto værktøjer til oprettelse eller API'er leveres af FM:Systems til klargøring AAD-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>Hvis du vil tildele brugere til FM:Systems, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **FM:Systems **application integration.

    ![Tildele brugere] (./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
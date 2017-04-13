<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Clever | Microsoft Azure" 
    description="Lær, hvordan du bruger Clever med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-clever"></a>Selvstudium: Azure Active Directory-integration med Clever

Formålet med dette selvstudium er at vise integration af Azure og Clever. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En intelligent lejer

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Clever kunne enkelt Log på programmet på webstedet intelligente virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Clever
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-clever-tutorial/IC798977.png "Scenarie")
##<a name="enabling-the-application-integration-for-clever"></a>Aktivere programmet integrationen for Clever

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Clever.

###<a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>For at aktivere programmet integration for Clever skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-clever-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-clever-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-clever-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Clever**.

    ![Galleri med programmet] (./media/active-directory-saas-clever-tutorial/IC798978.png "Galleri med programmet")

7.  Vælg **Clever**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Intelligent] (./media/active-directory-saas-clever-tutorial/IC798979.png "Intelligent")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Clever med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Intelligent programmet forventer SAML påstande i et bestemt format, som kræver, at du kan tilføje brugerdefineret attributtilknytninger til konfigurationen af **saml token attributter** .  
Følgende skærmbillede viser et eksempel af dette.

![Attributter] (./media/active-directory-saas-clever-tutorial/IC798980.png "Attributter")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Clever** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC784682.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Clever** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC798981.png "Konfigurere Single Sign-On")

3.  Skriv URL-adressen bruges af dine brugere at sign-on til dit intelligente program i tekstfeltet **Intelligente Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: *https://clever.com/in/azsandbox*), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-clever-tutorial/IC798982.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på Clever** , og Gem derefter metadatafilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC798983.png "Konfigurere Single Sign-On")

5.  Log på webstedet intelligente virksomhed som administrator i et andet browservindue.

6.  Klik på **Chat Login**i værktøjslinjen.

    ![Chat-logon] (./media/active-directory-saas-clever-tutorial/IC798984.png "Chat-logon")

7.  På siden **Chat Login** skal du udføre følgende trin:

    ![Chat-logon] (./media/active-directory-saas-clever-tutorial/IC798985.png "Chat-logon")

    1.  Skriv **URL-adressen til logon**.  

        >[AZURE.NOTE] **URL-adressen til logon** er en brugerdefineret værdi.
Du kan få den faktiske værdi fra intelligente supportteamet.

    2.  Vælg **ADFS**som **Identitetssystem**.
    3.  Klik på **Gem**.

8.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC798986.png "Konfigurere Single Sign-On")

9.  Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i menuen øverst.

    ![Attributter] (./media/active-directory-saas-clever-tutorial/IC795920.png "Attributter")

10. For at tilføje påkrævet attributtilknytningerne skal du udføre følgende trin:

    ![Saml token attributter] (./media/active-directory-saas-clever-tutorial/IC795921.png "Saml token attributter")

  	|Attributnavn|Attributværdien|
  	|---|---|
  	|clever.Student.Credentials.District\_brugernavn|User.userPrincipalName|

    1.  Klik på **Tilføj bruger attribut**for hver datarække i tabellen ovenfor.
    2.  Skriv det attributnavn, der vises for den pågældende række i tekstfeltet **Attributnavn** .
    3.  Vælg attributværdien vises for den pågældende række i tekstfeltet **Attributværdien** .
    4.  Klik på **udført**.

11. Klik på **Anvend ændringer**.

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Clever, skal de klargøres til Clever.  
Klargøring af er Clever, en manuel opgave, der skal udføres af intelligente supportteamet.

>[AZURE.NOTE] Du kan bruge en hvilken som helst andre intelligente bruger konto værktøjer til oprettelse eller API'er, som Clever til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-clever-perform-the-following-steps"></a>Hvis du vil tildele brugere til Clever, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Clever **application integration.

    ![Tildele brugere] (./media/active-directory-saas-clever-tutorial/IC798987.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-clever-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

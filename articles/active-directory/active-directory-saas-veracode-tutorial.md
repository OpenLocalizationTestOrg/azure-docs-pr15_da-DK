<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Veracode | Microsoft Azure" 
    description="Lær, hvordan du bruger Veracode med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-veracode"></a>Selvstudium: Azure Active Directory-integration med Veracode
  
Formålet med dette selvstudium er at vise integration af Azure og Veracode. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Veracode single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Veracode kunne enkelt Log på det program, ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Veracode
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-veracode-tutorial/IC802903.png "Scenarie")

##<a name="enabling-the-application-integration-for-veracode"></a>Aktivere programmet integrationen for Veracode
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Veracode.

###<a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>For at aktivere programmet integration for Veracode skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-veracode-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-veracode-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-veracode-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Veracode**.

    ![Galleri med programmet] (./media/active-directory-saas-veracode-tutorial/IC802904.png "Galleri med programmet")

7.  Vælg **Veracode**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Veracode] (./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Veracode med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Programmets Veracode forventer SAML påstande i et bestemt format, som kræver, at du kan tilføje brugerdefineret attributtilknytninger til konfigurationen af **saml token attributter** .  
Følgende skærmbillede viser et eksempel af dette.

![Attributter] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributter")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Veracode** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802907.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Veracode** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802908.png "Konfigurere Single Sign-On")

3.  Klik på **Næste**på siden **Konfigurer App-indstillinger** .

    ![Konfigurere indstillinger for] (./media/active-directory-saas-veracode-tutorial/IC802909.png "Konfigurere indstillinger for")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Veracode** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802910.png "Konfigurere Single Sign-On")

5.  Log på webstedet Veracode virksomhed som administrator i et andet browservindue.

6.  Klik på **Indstillinger**i menuen øverst, og klik derefter på **Administration**.

    ![Administration] (./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")

7.  Klik på fanen **SAML** .

8.  I sektionen **Indstillinger for organisationen SAML** skal du udføre følgende trin:

    ![Administration] (./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")

    1.  Kopiere værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Veracode** dialogboksen siden, og Indsæt det i tekstfeltet **udsteder**
    2.  Hvis du vil overføre hentede certifikatet, skal du klikke på **Vælg fil**.
    3.  Vælg **Aktiver selv registrering**.

9.  Udfør følgende trin i sektionen **Selvsigneret Registreringsindstillinger** , og klik derefter på **Gem**:

    ![Administration] (./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")

    1.  Vælg **Ingen aktivering påkrævet**som **Ny brugeraktivering**.
    2.  Vælg **Indstilling Veracode brugerdata**, som **Bruger dataopdateringer**.
    3.  Yderligere **Oplysninger om SAML attributten**, Vælg følgende:
        -   **Brugerroller**
        -   **Politik for Administrator**
        -   **Korrekturlæser**
        -   **Sikkerhed kundeemne**
        -   **Administrerende**
        -   **Afsenderen**
        -   **Creator**
        -   **Alle scanning typer**
        -   **Gruppemedlemskaber**
        -   **Standard Team**

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802914.png "Konfigurere Single Sign-On")

11. Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i menuen øverst.

    ![Attributter] (./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributter")

12. For at tilføje påkrævet attributtilknytningerne skal du udføre følgende trin:

    ![Attributter] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributter")

  	| Attributnavn | Attributværdien |
  	|:---------------|:----------------|
  	| Fornavn      | User.givenname  |
  	| Efternavn       | User.surname    |
  	| mail          | User.mail       |

    1.  Klik på **Tilføj bruger attribut**for hver datarække i tabellen ovenfor.
    
    2.  Skriv det attributnavn, der vises for den pågældende række i tekstfeltet **Attributnavn** .

    3.  Vælg attributværdien vises for den pågældende række i tekstfeltet **Attributværdien** .

    4.  Klik på **udført**.

13. Klik på **Anvend ændringer**.

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Veracode, skal de klargøres til Veracode.  
Klargøring af er Veracode, en automatiseret opgave.  
Der er ingen handlingselement til du..
  
Brugere oprettes automatisk eventuelt under det første enkelte enkeltlogon forsøg.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Veracode bruger konto værktøjer til oprettelse eller API'er, som Veracode til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Hvis du vil tildele brugere til Veracode, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Veracode **application integration.

    ![Tildele brugere] (./media/active-directory-saas-veracode-tutorial/IC802915.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-veracode-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
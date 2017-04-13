<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med slæk | Microsoft Azure" 
    description="Lær, hvordan du bruger slæk med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-slack"></a>Selvstudium: Azure Active Directory-integration med slæk
  
Formålet med dette selvstudium er at vise integration af Azure og slæk.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En slæk single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til slæk kunne enkelt Log på programmet på webstedet slæk virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for slæk
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-slack-tutorial/IC794980.png "Scenarie")

##<a name="enabling-the-application-integration-for-slack"></a>Aktivere programmet integrationen for slæk
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for slæk.

###<a name="to-enable-the-application-integration-for-slack-perform-the-following-steps"></a>For at aktivere programmet integration for slæk skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-slack-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-slack-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-slack-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **slæk**.

    ![Galleri med programmet] (./media/active-directory-saas-slack-tutorial/IC794981.png "Galleri med programmet")

7.  Vælg **slæk**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Scenarie] (./media/active-directory-saas-slack-tutorial/IC796925.png "Scenarie")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende slæk med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **slæk** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794982.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge slæk** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794983.png "Konfigurere Single Sign-On")

3.  Skriv URL-adressen for din slæk lejer i tekstfeltet **Slæk log i URL-adresse** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://azuread.slack.com*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-slack-tutorial/IC794984.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på slæk** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794985.png "Konfigurere Single Sign-On")

5.  Log på webstedet slæk virksomhed som administrator i et andet browservindue.

6.  Gå til **til Microsoft Azure AD \> indstillinger for Team**.

    ![Indstillinger for team] (./media/active-directory-saas-slack-tutorial/IC794986.png "Indstillinger for team")

7.  Klik på fanen **godkendelse** i sektionen **Indstillinger for Team** , og klik derefter på **Rediger indstillinger**.

    ![Indstillinger for team] (./media/active-directory-saas-slack-tutorial/IC794987.png "Indstillinger for team")

8.  I dialogboksen **Indstillinger for proxygodkendelse SAML** skal du udføre følgende trin:

    ![SAML indstillinger] (./media/active-directory-saas-slack-tutorial/IC794988.png "SAML indstillinger")

    1.  Kopiér værdien **SAML SSO URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på slæk** dialogboksen siden, og Indsæt det i tekstfeltet **SAML 2.0 slutpunkt (HTTP)** .
    2.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på slæk** dialogboksen siden, og Indsæt det i tekstfeltet **Identitet udbyder udsteder** .
    3.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.
    
        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    4.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **Offentlige certifikat** .
    5.  Fjern markeringen af **Tillad brugere at ændre deres mailadresse**.
    6.  Markér **Tillad brugere at vælge deres egen brugernavn**.
    7.  **Godkendelse til dit team skal bruges ved**at vælge **det er valgfrit**.
    8.  Klik på **Gem konfiguration**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794989.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på slæk, skal de klargøres til slæk.
  
Der er ingen handlingspunkt for dig at konfigurere brugerklargøring til slæk.  
Når en tildelt brugeren forsøger at logge på slæk, oprettes en slæk konto automatisk, hvis det er nødvendigt.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-slack-perform-the-following-steps"></a>Hvis du vil tildele brugere til slæk, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **slæk **application integration.

    ![Tildele brugere] (./media/active-directory-saas-slack-tutorial/IC794990.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-slack-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
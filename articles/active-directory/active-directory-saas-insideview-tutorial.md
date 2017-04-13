<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med InsideView | Microsoft Azure" 
    description="Lær, hvordan du bruger InsideView med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-insideview"></a>Selvstudium: Azure Active Directory-integration med InsideView
  
Formålet med dette selvstudium er at vise integration af Azure og InsideView.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En InsideView lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til InsideView kunne enkelt Log på programmet på webstedet InsideView virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for InsideView
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-insideview-tutorial/IC794128.png "Scenarie")
##<a name="enabling-the-application-integration-for-insideview"></a>Aktivere programmet integrationen for InsideView
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for InsideView.

###<a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>For at aktivere programmet integration for InsideView skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-insideview-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-insideview-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-insideview-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **InsideView**.

    ![Galleri med programmet] (./media/active-directory-saas-insideview-tutorial/IC794129.png "Galleri med programmet")

7.  Vælg **InsideView**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![InsideView] (./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende InsideView med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **InsideView** application integration.

    ![Konfigurere enkelt in] (./media/active-directory-saas-insideview-tutorial/IC794131.png "Konfigurere enkelt in")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge InsideView** , og klik derefter på **Næste**.

    ![Konfigurere enkelt in] (./media/active-directory-saas-insideview-tutorial/IC794132.png "Konfigurere enkelt in")

3.  Skriv din InsideView SSO URL-adresse i tekstfeltet **InsideView svar URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: `https://my.insideview.com/iv/<STS Name>/login.iv`), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-insideview-tutorial/IC794133.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på InsideView** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere enkelt in] (./media/active-directory-saas-insideview-tutorial/IC794134.png "Konfigurere enkelt in")

5.  Log på webstedet InsideView virksomhed som administrator i et andet browservindue.

6.  På værktøjslinjen i øverste, skal du klikke på **administrator**, **SingleSignOn indstillinger**, og klik derefter på **Tilføj SAML**.

    ![SAML Single Sign-On indstillinger] (./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign-On indstillinger")

7.  I sektionen **Tilføj en ny SAML** skal du udføre følgende trin:

    ![Tilføje en ny SAML] (./media/active-directory-saas-insideview-tutorial/IC794136.png "Tilføje en ny SAML")

    1.  Skriv et navn til din konfiguration i tekstfeltet **STS navn** .
    2.  Kopiér værdien **Service Provider (SP) startet slutpunkt** i Azure klassisk-portalen på **Konfigurer single sign-on – på InsideView** dialogboksen siden, og Indsæt det i tekstfeltet **SamlP/WS-Fed Unsolicated slutpunkt** .
    3.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.
        
        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    4.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **STS certifikat**
    5.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**i tekstfeltet **Crm bruger id-tilknytning** .
    6.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**i tekstfeltet **Crm mail tilknytning** .
    7.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**i tekstfeltet **Crm fornavn tilknytning** .
    8.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**i tekstfeltet **Crm efternavn tilknytning** .
    9.  Klik på **Gem**.

8.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere enkelt in] (./media/active-directory-saas-insideview-tutorial/IC794137.png "Konfigurere enkelt in")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på InsideView, skal de klargøres til InsideView.  
Klargøring af er InsideView, en manuel opgave.
  
For at få brugere eller kontakter, der er oprettet i InsideView skal du kontakte din kunde succes manager eller sender mails til**support@insideview.com**

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden InsideView bruger konto værktøjer til oprettelse eller API'er leveres af InsideView til at klargøre Azure AD-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Hvis du vil tildele brugere til InsideView, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **InsideView **application integration.

    ![Tildele brugere] (./media/active-directory-saas-insideview-tutorial/IC794138.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-insideview-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
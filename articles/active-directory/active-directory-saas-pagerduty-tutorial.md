<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Pagerduty | Microsoft Azure" 
    description="Lær, hvordan du bruger Pagerduty med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Selvstudium: Azure Active Directory-Integration med Pagerduty
  
Formålet med dette selvstudium er at vise integration af Azure og Pagerduty.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Pagerduty lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Pagerduty kunne enkelt Log på programmet på webstedet Pagerduty virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Pagerduty
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scenarie")
##<a name="enabling-the-application-integration-for-pagerduty"></a>Aktivere programmet integrationen for Pagerduty
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Pagerduty.

###<a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>For at aktivere programmet integration for Pagerduty skal du udføre følgende trin:

1.  Klik på **Active Directory**i portalen Azure administration i den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Pagerduty**.

    ![Galleri med programmet] (./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Galleri med programmet")

7.  Vælg **Pagerduty**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![PagerDuty] (./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Pagerduty med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Pagerduty** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Pagerduty** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Pagerduty log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. Pagerduty.com*", og klik derefter på **Næste**.

    ![Konfigurer app URL-adresse] (./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Konfigurer app URL-adresse")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Pagerduty** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Konfigurere Enkeltlogon")

5.  Log på webstedet Pagerduty virksomhed som administrator i et andet browservindue.

6.  Klik på **Kontoindstillinger**i menuen øverst.

    ![Kontoindstillinger] (./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Kontoindstillinger")

7.  Klik på **enkeltlogon**.

    ![Single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Single sign-on")

8.  På siden aktivere Single Sign-on (SSO) skal du udføre følgende trin:

    ![Aktivere enkeltlogon] (./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Aktivere enkeltlogon")

    1.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    2.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **X.509-certifikat**
    3.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Pagerduty** dialog siden, og Indsæt det i tekstfeltet **URL-adressen til logon** .
    4.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Pagerduty** dialog siden, og Indsæt det i tekstfeltet **Log URL-adresse** .
    5.  Vælg **Slå Single Sign-on**.
    6.  Klik på **Gem ændringer**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Pagerduty, skal de klargøres til Pagerduty.  
Klargøring af er Pagerduty, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **Pagerduty** lejer.

2.  Klik på **brugere**i menuen øverst.

3.  Klik på **Tilføj brugere**.

    ![Tilføje brugere] (./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Tilføje brugere")

4.  Dialogboksen **Inviter dit team** , Skriv **Fornavn og efternavn** og **mailadressen på den ønskede Klargør, klik på **Tilføj**, og klik derefter på **Send inviterer**Azure AD-bruger** .

    ![Invitere dit team] (./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invitere dit team")

    >[AZURE.NOTE] Alle tilføjede brugere, modtager en invitation til at oprette en PagerDuty-konto.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Pagerduty bruger konto værktøjer til oprettelse eller API'er, som Pagerduty til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Hvis du vil tildele brugere til Pagerduty, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Pagerduty **application integration.

    ![Tildele brugere] (./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
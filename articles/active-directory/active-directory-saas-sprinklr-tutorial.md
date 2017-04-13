<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Sprinklr | Microsoft Azure" 
    description="Lær, hvordan du bruger Sprinklr med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Selvstudium: Azure Active Directory-integration med Sprinklr
  
Formålet med dette selvstudium er at vise integration af Azure og Sprinklr.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Sprinklr lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Sprinklr kunne enkelt Log på programmet på webstedet Sprinklr virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Sprinklr
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scenarie")

##<a name="enabling-the-application-integration-for-sprinklr"></a>Aktivere programmet integrationen for Sprinklr
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Sprinklr.

###<a name="to-enable-the-application-integration-for-sprinklr-perform-the-following-steps"></a>For at aktivere programmet integration for Sprinklr skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Sprinklr**.

    ![Galleri med programmet] (./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Galleri med programmet")

7.  Vælg **Sprinklr**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Sprinklr] (./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Sprinklr med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Sprinklr** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Sprinklr** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Sprinklr log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. sprinklr.com*", og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Sprinklr** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Konfigurere Enkeltlogon")

5.  Log på webstedet Sprinklr virksomhed som administrator i et andet browservindue.

6.  Gå til **Administration \> indstillinger**.

    ![Administration] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

7.  Gå til **administrere Partner \> Single Sign** på i venstre rude.

    ![Administrere Partner] (./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Administrere Partner")

8.  Klik på **+ Tilføj Single Sign-tilføjelsesprogrammer**.

    ![Enkelt tegn-tilføjelsesprogrammer] (./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Enkelt tegn-tilføjelsesprogrammer")

9.  På siden **Single Sign-on** skal du udføre følgende trin:

    ![Enkelt tegn-tilføjelsesprogrammer] (./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Enkelt tegn-tilføjelsesprogrammer")

    1.  Skriv et navn til din konfiguration i tekstfeltet **navn** (f.eks.: *WAADSSOTest*).
    2.  Vælg **aktiveret**.
    3.  Vælg **Brug nyt SSO-certifikat**.
    4.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    5.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i din Udklipsholder og sætte den ind på tekstfeltet **Identitet udbyder certifikat**
    6.  Kopiér værdien **Identitet Provider-ID** i Azure klassisk-portalen på **Konfigurer single sign-on – på Sprinklr** dialogboksen siden, og Indsæt det i tekstfeltet **Enheds-Id** .
    7.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Sprinklr** dialogboksen siden, og Indsæt det i tekstfeltet **Identitet udbyder Login URL-adresse** .
    8.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Sprinklr** dialogboksen siden, og Indsæt det i tekstfeltet **Identitet udbyder log URL-adresse** .
    9.  Som **SAML bruger-id-Type**skal du markere **program indeholder bruger "s sprinklr.com brugernavn**.
    10. Vælg **bruger-ID er i elementet navne-id i sætningen emne**som **SAML brugerplacering-ID**.
    11. Luk **Gem**.

        ![SAML] (./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Konfigurere Enkeltlogon")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For AAD brugere skal kunne logge på, skal de klargøres for adgang i programmet Sprinklr.  
Dette afsnit beskrives, hvordan du opretter AAD brugerkonti i Sprinklr.

###<a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>Hvis du vil tildele en brugerkonto i Sprinklr, skal du udføre følgende trin:

1.  Log på webstedet Sprinklr virksomhed som administrator.

2.  Gå til **Administration \> indstillinger**.

    ![Administration] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

3.  Gå til **administrere klienten \> brugere** i venstre rude.

    ![Indstillinger] (./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Indstillinger")

4.  Klik på **Tilføj bruger**.

    ![Indstillinger] (./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Indstillinger")

5.  I dialogboksen **Rediger bruger** skal du udføre følgende trin:

    ![Redigere bruger] (./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Redigere bruger")

    1.  I **mail**, **Fornavn** og **Efternavn i** tekstbokse, type af oplysninger for en Azure AD-brugerkonto du vil klargøre.
    2.  Vælg **adgangskode deaktiveret**.
    3.  Vælg et **sprog**.
    4.  Vælg en **brugertype**.
    5.  Klik på **Opdater**.

    >[AZURE.IMPORTANT] **Adgangskode deaktiveret** skal være markeret til at aktivere en bruger til at logge på via en identitetsudbyder.

6.  Gå til **rolle**, og derefter udføre følgende trin:

    ![Partner roller] (./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Partner roller")

    1.  Vælg listen **Global** **alle\_tilladelser**.
    2.  Klik på **Opdater**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Sprinklr bruger konto værktøjer til oprettelse eller API'er leveres af Sprinklr til at klargøre Azure AD-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-sprinklr-perform-the-following-steps"></a>Hvis du vil tildele brugere til Sprinklr, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Sprinklr **application integration.

    ![Tildele brugere] (./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration integration med Druva | Microsoft Azure" 
    description="Lær, hvordan du bruger Druva med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Selvstudium: Azure Active Directory-integration integration med Druva

Formålet med dette selvstudium er at vise integration af Azure og Druva.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Druva single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Druva kunne enkelt Log på programmet på webstedet Druva virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Druva
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-druva-tutorial/IC795084.png "Scenarie")
##<a name="enabling-the-application-integration-for-druva"></a>Aktivere programmet integrationen for Druva

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Druva.

###<a name="to-enable-the-application-integration-for-druva-perform-the-following-steps"></a>For at aktivere programmet integration for Druva skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-druva-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-druva-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-druva-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Druva**.

    ![Galleri med programmet] (./media/active-directory-saas-druva-tutorial/IC795085.png "Galleri med programmet")

7.  Vælg **Druva**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Druva] (./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Druva med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

Programmets Druva forventer SAML påstande i et bestemt format, som kræver, at du kan tilføje brugerdefineret attributtilknytninger til konfigurationen af **saml token attributter** .  
Følgende skærmbillede viser et eksempel af dette.

![SAML Token attributter] (./media/active-directory-saas-druva-tutorial/IC795087.png "SAML Token attributter")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Druva** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795027.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Druva** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795088.png "Konfigurere Single Sign-On")

3.  Skriv URL-adressen bruges af dine brugere til at logge din Druva-program i tekstfeltet **Druva Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://cloud.druva.com/home/*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-druva-tutorial/IC795089.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Druva** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795090.png "Konfigurere Single Sign-On")

5.  Log på webstedet Druva virksomhed som administrator i et andet browservindue.

6.  Gå til **administrere \> indstillinger**.

    ![Indstillinger] (./media/active-directory-saas-druva-tutorial/IC795091.png "Indstillinger")

7.  I dialogboksen Indstillinger for enkelt Sign-On skal du udføre følgende trin:

    ![Indstillinger for Singl Sign-On] (./media/active-directory-saas-druva-tutorial/IC795092.png "Indstillinger for Singl Sign-On")

    1.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Druva** dialogboksen siden, og Indsæt det i tekstfeltet **ID udbyder Login URL-adresse** .
    2.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Druva** dialogboksen siden, og Indsæt det i tekstfeltet **ID udbyder log URL-adresse** .
    3.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    4.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **ID udbyder certifikat**
    5.  Klik på **Gem**for at åbne siden **Indstillinger** .

8.  Klik på **Generer SSO Token**på siden **Indstillinger** .

    ![Indstillinger] (./media/active-directory-saas-druva-tutorial/IC795093.png "Indstillinger")

9.  I **Enkelt Sign-on godkendelse Token** dialogboksen skal du udføre følgende trin:

    ![SSO Token] (./media/active-directory-saas-druva-tutorial/IC795094.png "SSO Token")

    1.  Klik på **Kopiér**.
    2.  Klik på **Luk**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795095.png "Konfigurere Single Sign-On")

11. Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i menuen øverst.

    ![Attributter] (./media/active-directory-saas-druva-tutorial/IC795096.png "Attributter")

12. For at tilføje påkrævet attributtilknytningerne skal du udføre følgende trin:

  	|Attributnavn|Attributværdien|
  	|---|---|
  	|overensstemmelse\_auth\_token|<*Udklipsholder værdi*>|

    1.  Klik på **Tilføj bruger attribut**for hver datarække i tabellen ovenfor.
    2.  Skriv det attributnavn, der vises for den pågældende række i tekstfeltet **Attributnavn** .
    3.  Skriv attributværdien vises for den pågældende række i tekstfeltet **Attributværdien** .
    4.  Klik på **udført**.

13. Klik på **Anvend ændringer**.
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Druva, skal de klargøres til Druva.  
Klargøring af er Druva, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet **Druva** virksomhed som administrator.

2.  Gå til **administrere \> brugere**.

    ![Administrere brugere] (./media/active-directory-saas-druva-tutorial/IC795097.png "Administrere brugere")

3.  Klik på **Opret et nyt**.

    ![Administrere brugere] (./media/active-directory-saas-druva-tutorial/IC795098.png "Administrere brugere")

4.  I dialogboksen Opret ny bruger skal du udføre følgende trin:

    ![Oprette NyBruger] (./media/active-directory-saas-druva-tutorial/IC795099.png "Oprette NyBruger")

    1.  Skriv mailadressen, og navnet på en gyldig Azure Active Directory-brugerkonto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Opret bruger**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Druva bruger konto værktøjer til oprettelse eller API'er, som Druva til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-druva-perform-the-following-steps"></a>Hvis du vil tildele brugere til Druva, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Druva **application integration.

    ![Tildele brugere] (./media/active-directory-saas-druva-tutorial/IC795100.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-druva-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

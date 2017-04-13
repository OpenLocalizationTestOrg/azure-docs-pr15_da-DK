<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med SumoLogic | Microsoft Azure" 
    description="Lær, hvordan du bruger SumoLogic med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Selvstudium: Azure Active Directory-Integration med SumoLogic
  
Formålet med dette selvstudium er at vise integration af Azure og SumoLogic.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En SumoLogic lejer
  
Når du har fuldført dette selvstudium, Firmaoplysninger de Azure AD-brugere, du har tildelt til SumoLogicwill skal kunne enkelt log i programmet på din SumoLogic websted (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for SumoLogic
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-sumologic-tutorial/IC778549.png "Scenarie")

##<a name="enabling-the-application-integration-for-sumologic"></a>Aktivere programmet integrationen for SumoLogic
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for SumoLogic.

###<a name="to-enable-the-application-integration-for-sumologic-perform-the-following-steps"></a>For at aktivere programmet integration for SumoLogic skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-sumologic-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-sumologic-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-sumologic-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **sumologic**.

    ![Galleri med programmet] (./media/active-directory-saas-sumologic-tutorial/IC778550.png "Galleri med programmet")

7.  Vælg **SumoLogic**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![SumoLogic] (./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende SumoLogic med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at overføre et base-64-kodet certifikat til din SumoLogictenant som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **SumoLogic** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-sumologic-tutorial/IC778552.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge SumoLogic** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-sumologic-tutorial/IC778553.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **SumoLogic log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. SumoLogic.com*", og klik derefter på **Næste**.

    ![Konfigurer aoo URL-adresse] (./media/active-directory-saas-sumologic-tutorial/IC778554.png "Konfigurer aoo URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på SumoLogic** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-sumologic-tutorial/IC778555.png "Konfigurere Enkeltlogon")

5.  Log på webstedet SumoLogic virksomhed som administrator i et andet browservindue.

6.  Gå til **administrere \> sikkerhed**.

    ![Administrere] (./media/active-directory-saas-sumologic-tutorial/IC778556.png "Administrere")

7.  Klik på **SAML**.

    ![Global sikkerhedsindstillinger] (./media/active-directory-saas-sumologic-tutorial/IC778557.png "Global sikkerhedsindstillinger")

8.  Vælg **Azure Active Directory**på listen **Vælg en konfiguration, eller Opret en ny** , og klik derefter på **Konfigurer**.

    ![Konfigurere SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778558.png "Konfigurere SAML 2.0")

9.  I dialogboksen **Konfigurer SAML 2.0** skal du udføre følgende trin:

    ![Konfigurere SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778559.png "Konfigurere SAML 2.0")

    1.  Skriv **Azure AD**i tekstboksen **Navn på konfiguration** .
    2.  Vælg **fejlfinding tilstand**.
    3.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på SumoLogic** dialog siden, og Indsæt det i tekstfeltet **udsteder** .
    4.  Kopiér **URL-godkendelse anmode om** værdien i Azure klassisk-portalen på **Konfigurer single sign-on – på SumoLogic** dialog siden, og Indsæt det i tekstfeltet **Authn anmode om URL** .
    5.  Oprette en **Base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    6.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder, og derefter indsætte det hele certifikat i **X.509-certifikat** tekstfelt.
    7.  Vælg **Brug SAML emne**som **E-mail-attributten**.
    8.  Vælg **SP startet Login konfiguration**.
    9.  Skriv **Azure**i tekstfeltet **Login sti** .
    10. Klik på **Gem**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen på **Konfigurer single sign-on – på SumoLogic** dialog siden, og klik derefter på **udført**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-sumologic-tutorial/IC778560.png "Konfigurere Enkeltlogon")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på SumoLogic, skal de klargøres til SumoLogic.  
Klargøring af er SumoLogic, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **SumoLogic** lejer.

2.  Gå til **administrere \> brugere**.

    ![Brugere] (./media/active-directory-saas-sumologic-tutorial/IC778561.png "Brugere")

3.  Klik på **Tilføj**.

    ![Brugere] (./media/active-directory-saas-sumologic-tutorial/IC778562.png "Brugere")

4.  I dialogboksen **Ny bruger** skal du udføre følgende trin:

    ![Ny bruger] (./media/active-directory-saas-sumologic-tutorial/IC778563.png "Ny bruger")

    1.  Skriv de relaterede oplysninger på den Azure AD-konto, du vil klargøre i tekstfelterne **Fornavn**, **Efternavn** og **mail** .
    2.  Vælg en rolle.
    3.  Som **Status**, vælge **aktive**.
    4.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden SumoLogic bruger konto værktøjer til oprettelse eller API'er, som SumoLogic til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-sumologic-perform-the-following-steps"></a>Hvis du vil tildele brugere til SumoLogic, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **SumoLogic** application integration.

    ![Tildele brugere] (./media/active-directory-saas-sumologic-tutorial/IC778564.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-sumologic-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
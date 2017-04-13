<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration integration med SugarCRM | Microsoft Azure" 
    description="Lær, hvordan du bruger SugarCRM med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-integration-with-sugarcrm"></a>Selvstudium: Azure Active Directory-integration integration med SugarCRM
  
Formålet med dette selvstudium er at vise integration af Azure og sukker CRM.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En sukker CRM single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til sukker CRM kunne enkelt Log på programmet på webstedet sukker CRM virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for sukker CRM
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Scenarie")

##<a name="enabling-the-application-integration-for-sugar-crm"></a>Aktivere programmet integrationen for sukker CRM
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for sukker CRM.

###<a name="to-enable-the-application-integration-for-sugar-crm-perform-the-following-steps"></a>For at aktivere programmet integration for sukker CRM skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Sukker CRM**.

    ![Galleri med programmet] (./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Galleri med programmet")

7.  Vælg **Sukker CRM**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Sukker CRM] (./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sukker CRM")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende sukker CRM med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at overføre et base-64-kodet certifikat til din sukker CRM lejer som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Sukker CRM** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge sukker CRM** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Konfigurere Single Sign-On")

3.  Skriv URL-adressen bruges af dine brugere at logge på din sukker CRM-programmet i tekstfeltet **Sukker CRM Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*http://company.sugarondemand.com*", og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på sukker CRM** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Konfigurere Single Sign-On")

5.  Log på din sukker CRM virksomheds websted som en administrator i et andet browservindue.

6.  Gå til **administrator**.

    ![Administrator] (./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Administrator")

7.  Klik på **Administration af adgangskoder**i sektionen **Administration** .

    ![Administration] (./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administration")

8.  Vælg **Aktiver SAML godkendelse**.

    ![Administration] (./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administration")

9.  I sektionen **SAML godkendelse** skal du udføre følgende trin:

    ![SAML godkendelse] (./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "SAML godkendelse")

    1.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på sukker CRM** dialogboksen siden, og Indsæt det i tekstfeltet **URL-adressen til logon** .
    2.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på sukker CRM** dialogboksen siden, og Indsæt det i tekstfeltet **Langsomme URL-adresse** .
    3.  Oprette en **Base-64-kodet** fil fra dit hentede certifikat.

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    4.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder, og derefter indsætte det hele certifikat i **X.509-certifikat** tekstfelt.
    5.  Klik på **Gem**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen i Azure klassisk-portalen på **Konfigurer single sign-on – på sukker CRM** dialogboksen siden, og klik derefter på **udført**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på sukker CRM, skal de klargøres til sukker CRM.  
Klargøring af er sukker CRM, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Sukker CRM** virksomhed som administrator.

2.  Gå til **administrator**.

    ![Administrator] (./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Administrator")

3.  Klik på **Brugeradministration**i sektionen **Administration** .

    ![Administration] (./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administration")

4.  Gå til **brugere \> oprette ny bruger**.

    ![Oprette ny bruger] (./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Oprette ny bruger")

5.  På fanen **Brugerprofil** skal du udføre følgende trin:

    ![Ny bruger] (./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "Ny bruger")

    1.  Skriv det brugernavn, sidste navn og mailadressen på en gyldig Azure Active Directory-bruger i den relaterede tekstbokse.

6.  Som **Status**, vælge **aktive**.

7.  På fanen adgangskode skal du udføre følgende trin:

    ![Ny bruger] (./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "Ny bruger")

    1.  Skriv adgangskoden i tekstfeltet relaterede.
    2.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden sukker CRM bruger konto værktøjer til oprettelse eller API'er, som sukker CRM til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-sugar-crm-perform-the-following-steps"></a>Hvis du vil tildele brugere til sukker CRM, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Sukker CRM** application integration.

    ![Tildele brugere] (./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
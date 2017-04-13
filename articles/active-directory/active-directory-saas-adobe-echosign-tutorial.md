<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Adobe EchoSign | Microsoft Azure" 
    description="Lær, hvordan du bruger Adobe EchoSign med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-adobe-echosign"></a>Selvstudium: Azure Active Directory-integration med Adobe EchoSign

Formålet med dette selvstudium er at vise integration af Azure og Adobe EchoSign.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En enkelt Adobe EchoSign logge på aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Adobe EchoSign kunne enkelt Log på programmet på webstedet Adobe EchoSign virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Adobe EchoSign
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Scenarie")
##<a name="enabling-the-application-integration-for-adobe-echosign"></a>Aktivere programmet integrationen for Adobe EchoSign

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Adobe EchoSign.

###<a name="to-enable-the-application-integration-for-adobe-echosign-perform-the-following-steps"></a>For at aktivere programmet integration for Adobe EchoSign skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Adobe EchoSign**.

    ![Galleri med programmet] (./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Galleri med programmet")

7.  Vælg **Adobe EchoSign**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Adobe EchoSign] (./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Adobe EchoSign med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Adobe EchoSign** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Adobe EchoSign** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://company.echosign.com/*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Adobe EchoSign Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Adobe EchoSign** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Konfigurere Single Sign-On")

5.  Log på webstedet Adobe EchoSign virksomhed som administrator i et andet browservindue.

6.  Klik på **konto**i menuen øverst, og klik derefter på **Indstillinger for SAML** under **Kontoindstillinger**i navigationsruden på den venstre terning.

    ![Konto] (./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Konto")

7.  I sektionen SAML Settings skal du udføre følgende trin:

    ![SAML indstillinger] (./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML indstillinger")

    1.  Vælg **SAML obligatorisk**som **SAML tilstand**.
    2.  Vælg **Tillad EchoSign konto administratorer til at logge på med deres EchoSign legitimationsoplysninger**.
    3.  Vælg **automatisk at tilføje brugere godkendt gennem SAML**som **Oprettelse af bruger**.

8.  Flytte til, at benytte følgende fremgangsmåde:

    ![SAML indstillinger] (./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML indstillinger")

    1.  Kopiér værdien **Enheds-ID** i Azure klassisk-portalen på **Konfigurer single sign-on – på Adobe EchoSign** dialogboksen siden, og Indsæt det i tekstfeltet **IdP enheds-ID** .
    2.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Adobe EchoSign** dialogboksen siden, og Indsæt det i tekstfeltet **IdP Login URL-adresse** .
    3.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Adobe EchoSign** dialogboksen siden, og Indsæt det i tekstfeltet **IdP log URL-adresse** .
    4.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o) 
 5.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **IdP certifikat** 6.  Klik på **Gem ændringer**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Adobe EchoSign, skal de klargøres i Adobe EchoSign.  
Klargøring af er Adobe EchoSign, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Adobe EchoSign** virksomhed som administrator.

2.  I menuen øverst **konto**, skal du klikke på og klik derefter på **brugere og grupper**, i navigationsruden på den venstre terning, og klik derefter på **Opret en ny bruger**.

    ![Konto] (./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Konto")

3.  I afsnittet **Oprette ny bruger** , kan du udføre følgende trin:

    ![Oprette bruger] (./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Oprette bruger")

    1.  Skriv den **Mailadresse**, **Fornavn** og **Efternavn** på en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Opret bruger**.

        >[AZURE.NOTE] Azure Active Directory-kontoindehaver modtager en mail, der indeholder et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Adobe EchoSign bruger konto værktøjer til oprettelse eller API'er, som Adobe EchoSign til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-adobe-echosign-perform-the-following-steps"></a>Hvis du vil tildele brugere til Adobe EchoSign, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Adobe EchoSign **application integration.

    ![Tildele brugere] (./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

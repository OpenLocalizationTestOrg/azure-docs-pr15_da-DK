<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Intacct | Microsoft Azure" 
    description="Lær, hvordan du bruger Intacct med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-intacct"></a>Selvstudium: Azure Active Directory-integration med Intacct
  
Formålet med dette selvstudium er at vise integration af Azure og Intacct.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Intacct lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Intacct kunne enkelt Log på programmet på webstedet Intacct virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Intacct
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-intacct-tutorial/IC790030.png "Scenarie")
##<a name="enabling-the-application-integration-for-intacct"></a>Aktivere programmet integrationen for Intacct
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Intacct.

###<a name="to-enable-the-application-integration-for-intacct-perform-the-following-steps"></a>For at aktivere programmet integration for Intacct skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-intacct-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-intacct-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-intacct-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Intacct**.

    ![Galleri med programmet] (./media/active-directory-saas-intacct-tutorial/IC790031.png "Galleri med programmet")

7.  Vælg **Intacct**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Intacct] (./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Intacct med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Intacct** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790033.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Intacct** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790034.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://Intacct.com/company*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Intacct Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-intacct-tutorial/IC790035.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Intacct** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790036.png "Konfigurere Single Sign-On")

5.  Log på webstedet Intacct virksomhed som administrator i et andet browservindue.

6.  Klik på fanen **virksomhed** , og klik derefter på **Firmaoplysninger**.

    ![Virksomhed] (./media/active-directory-saas-intacct-tutorial/IC790037.png "Virksomhed")

7.  Klik på fanen **sikkerhed** , og klik derefter på **Rediger**.

    ![Sikkerhed] (./media/active-directory-saas-intacct-tutorial/IC790038.png "Sikkerhed")

8.  I sektionen **Single sign-on (SSO)** skal du udføre følgende trin:

    ![Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790039.png "Single Sign-On")

    1.  Vælg **Aktiver enkelt Log på**.
    2.  Vælg **SAML 2.0**som **identitet udbyder type**.
    3.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Intacct** dialogboksen siden, og Indsæt det i tekstfeltet **Udsteder URL-adresse** .
    4.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Intacct** dialogboksen siden, og Indsæt det i tekstfeltet **URL-adressen til logon** .
    5.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.
        
        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    6.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **certifikat**
    7.  Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790040.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Intacct, skal de klargøres til Intacct.  
Klargøring af er Intacct, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **Intacct** lejer.

2.  Klik på fanen **virksomhed** , og klik derefter på **brugere**.

    ![Brugere] (./media/active-directory-saas-intacct-tutorial/IC790041.png "Brugere")

3.  Klik på fanen **Tilføj**

    ![Tilføje] (./media/active-directory-saas-intacct-tutorial/IC790042.png "Tilføje")

4.  I sektionen **Brugeroplysninger** skal du udføre følgende trin:

    ![Brugeroplysninger] (./media/active-directory-saas-intacct-tutorial/IC790043.png "Brugeroplysninger")

    1.  Skriv det **Bruger-ID**, **Efternavn**, **Fornavn**, **e-mailadresse**, **Titel** og **Telefon** på en Azure AD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Vælg en Azure AD-konto, du vil klargøre **administratortilladelser** .
    3.  Klik på **Gem**.
        
        >[AZURE.NOTE] AAD kontoindehaver vil modtage en mail og følge et link for at bekræfte deres konto, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Intacct bruger konto værktøjer til oprettelse eller API'er, som Intacct til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-intacct-perform-the-following-steps"></a>Hvis du vil tildele brugere til Intacct, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Intacct **application integration.

    ![Tildele brugere] (./media/active-directory-saas-intacct-tutorial/IC790044.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-intacct-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Gigya | Microsoft Azure" 
    description="Lær, hvordan du bruger Gigya med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-gigya"></a>Selvstudium: Azure Active Directory-integration med Gigya
  
Formålet med dette selvstudium er at vise integration af Azure og Gigya.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Gigya single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Gigya kunne enkelt Log på programmet på webstedet Gigya virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Gigya
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Konfigurere Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789512.png "Konfigurere Single Sign-On")
##<a name="enabling-the-application-integration-for-gigya"></a>Aktivere programmet integrationen for Gigya
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Gigya.

###<a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>For at aktivere programmet integration for Gigya skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-gigya-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-gigya-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-gigya-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Gigya**.

    ![Galleri med programmet] (./media/active-directory-saas-gigya-tutorial/IC789513.png "Galleri med programmet")

7.  Vælg **Gigya**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Gigya] (./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Gigya med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Gigya** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789528.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Gigya** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789529.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*http://company.gigya.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Gigya Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-gigya-tutorial/IC789530.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Gigya** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789531.png "Konfigurere Single Sign-On")

5.  Log på webstedet Gigya virksomhed som administrator i et andet browservindue.

6.  Gå til **indstillinger \> SAML Login**, og klik derefter på knappen **Tilføj** .

    ![SAML logon] (./media/active-directory-saas-gigya-tutorial/IC789532.png "SAML logon")

7.  I sektionen **SAML Login** skal du udføre følgende trin:

    ![SAML konfiguration] (./media/active-directory-saas-gigya-tutorial/IC789533.png "SAML konfiguration")

    1.  I tekstfeltet **navn** skal du skrive et navn til din konfiguration.
    2.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Gigya** dialogboksen siden, og Indsæt det i tekstfeltet **udsteder** .
    3.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Gigya** dialogboksen siden, og Indsæt det i tekstfeltet **Enkelt Sign-On URL-adressen** .
    4.  Kopiér værdien **Id navneformat** i Azure klassisk-portalen på **Konfigurer single sign-on – på Gigya** dialogboksen siden, og Indsæt det i tekstfeltet **Navn id-Format** .
    5.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.
        
        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    6.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **X.509-certifikat** .
    7.  Klik på **Gem indstillinger**.

8.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789534.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Gigya, skal de klargøres til Gigya.  
Klargøring af er Gigya, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Gigya** virksomhed som administrator.

2.  Gå til **administrator \> Administrer brugere**, og klik derefter på **Inviter brugere**.

    ![Administrere brugere] (./media/active-directory-saas-gigya-tutorial/IC789535.png "Administrere brugere")

3.  I dialogboksen Inviter brugere skal du udføre følgende trin:

    ![Invitere brugere] (./media/active-directory-saas-gigya-tutorial/IC789536.png "Invitere brugere")

    1.  I tekstfeltet **mail** skal du skrive mailalias for en gyldig Azure Active Directory-konto, du vil klargøre.
    2.  Klik på **Inviter bruger**.
    
        >[AZURE.NOTE] Azure Active Directory-kontoindehaver modtager en mail, der indeholder et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Gigya bruger konto værktøjer til oprettelse eller API'er, som Gigya til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-gigya-perform-the-following-steps"></a>Hvis du vil tildele brugere til Gigya, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Gigya **application integration.

    ![Tildele brugere] (./media/active-directory-saas-gigya-tutorial/IC789537.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-gigya-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
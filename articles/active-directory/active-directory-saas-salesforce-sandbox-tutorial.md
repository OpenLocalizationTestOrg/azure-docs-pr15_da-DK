<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Salesforce sandkassetilstand | Microsoft Azure"
    description="Lær at bruge Salesforce sandkassetilstand med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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
    ms.date="10/28/2016" 
    ms.author="jeedes" />


#<a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Selvstudium: Azure Active Directory-integration med Salesforce sandkassetilstand
>[AZURE.TIP]Feedback, ved at klikke [her](http://go.microsoft.com/fwlink/?LinkId=521878).
  
Formålet med dette selvstudium er at vise integration af Azure og Salesforce sandkassetilstand.  
Sandingsanlægget giver dig mulighed for at oprette flere kopier af din organisation i separate miljøer for en lang række formål, såsom udvikling, test og uddannelse, uden at bringe data og programmer i organisationen Salesforce fremstilling.  
Få mere at vide under [Oversigt over sandkassetilstand](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En sandkasse i Salesforce.com
  
Hvis du endnu ikke har en gyldig sandkasse i Salesforce.com, skal du kontakte Salesforce.
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integration til Salesforce sandkassetilstand
2.  Konfigurere Enkeltlogon
3.  Aktivering af dit domæne
4.  Konfiguration af brugerklargøring af
5.  Tildele brugere

![Scenarie] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scenarie")
##<a name="enabling-the-application-integration-for-salesforce-sandbox"></a>Aktivere programmet integration til Salesforce sandkassetilstand
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integration til Salesforce sandkassetilstand.

###<a name="to-enable-the-application-integration-for-salesforce-sandbox-perform-the-following-steps"></a>For at aktivere programmet integration for Salesforce sandkassetilstand skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj en App**for at åbne **Programmet galleriet**, og klik derefter på **Tilføj et program for organisationen at bruge**.

    ![Hvad vil du gøre?] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "Hvad vil du gøre?")

5.  Skriv **Salesforce sandkassetilstand**i **søgefeltet**.

    ![Galleri med programmet] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galleri med programmet")

6.  Vælg **Salesforce sandkassetilstand**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Salesforce sandkassetilstand] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce sandkassetilstand")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Salesforce med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Salesforce sandkassetilstand** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Salesforce sandkassetilstand** , og klik derefter på **Næste**.

    ![Salesforce sandkassetilstand] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce sandkassetilstand")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Log på URL-adressen** på siden **Konfigurer App URL-adressen** `http://company.my.salesforce.com`, og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Konfigurere App URL-adresse")

4. Hvis du allerede har konfigureret single sign-on til en anden forekomst af Salesforce sandkassetilstand i adresselisten, skal du også konfigurere **id** for at få den samme værdi som **logge på URL-adresse**. Feltet **id** kan findes ved at markere afkrydsningsfeltet **Vis avancerede indstillinger** på siden **Konfigurer App URL-adresse** i dialogboksen.

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Salesforce sandkassetilstand** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Konfigurere Single Sign-On")

5.  Log på din Salesforce sandkassetilstand som administrator i et andet browservindue.

6.  Klik på **konfiguration**i menuen øverst.

    ![Konfiguration] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Konfiguration")

7.  Klik på **Sikkerhedskontrol**i navigationsruden i venstre side, og klik derefter på **Indstillinger for enkelt Sign-On**.

    ![Enkelt Sign-On-indstillinger] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Enkelt Sign-On-indstillinger")

8.  I sektionen enkelt Sign-On Settings skal du udføre følgende trin:

    ![Enkelt Sign-On-indstillinger] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Enkelt Sign-On-indstillinger")

    en.  Vælg **SAML aktiveret**.
    
    b.  Klik på **Ny**.

9.  I sektionen SAML enkelt Sign-On Settings skal du udføre følgende trin:

    ![SAML enkelt Sign-On indstillinger] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML enkelt Sign-On indstillinger")

    en.  I tekstfeltet navn skal du skrive navnet på konfigurationen (f.eks.: *SPSSOWAAD\_Test*).
    
    b.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Salesforce sandkassetilstand** dialog siden, og Indsæt det i tekstfeltet **udsteder** .
    
    c.  Skriv **https://test.salesforce.com** i tekstfeltet **Enheds-Id** , hvis dette er den første Salesforce sandkassetilstand forekomst, du føjer til adresselisten. Hvis du allerede har tilføjet en forekomst af Salesforce sandkassetilstand, og klik derefter for typen **Enheds-ID** i **Log på URL-adresse**, der skal vises i dette format:`http://company.my.salesforce.com`
    
    d.  Klik på **Gennemse** for at overføre det certifikat, der er hentet.
    
    e.  Vælg **program indeholder sammenslutning-ID'ET fra brugerobjektet**som **SAML identitetstype**.
    
    f.  Vælg **identitet er i NameIdentifier elementet i sætningen emne**som **SAML identitet placering**.
    
    g.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Salesforce sandkassetilstand** dialog siden, og Indsæt det i tekstfeltet **Identitet udbyder Login URL-adresse** .
    
    h.  SFDC understøtter ikke SAML log.  Som en løsning kan du indsætte 'https://login.windows.net/common/wsfederation?wa=wsignout1.0' den i tekstfeltet **Identitet udbyder log URL-adresse** .
    
    Jeg.  Vælg **HTTP POST**, som **Service Provider startet anmode om indbinding**.
    
    j. Klik på **Gem**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Konfigurere Single Sign-On")

##<a name="enabling-your-domain"></a>Aktivering af dit domæne
  
Dette afsnit antages det, at du allerede har oprettet et domæne.  
Du kan finde flere detaljer, [Definere dit domænenavn](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###<a name="to-enable-your-domain-perform-the-following-steps"></a>For at aktivere dit domæne skal du udføre følgende trin:

1.  Klik på **Administration af domæne**i den venstre navigationsrude, og klik derefter på **mit domæne.**

    ![Mit domæne] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Mit domæne")

    >[AZURE.NOTE]Sørg for, at dit domæne er konfigureret korrekt.

2.  I sektionen **Indstillinger for logon side** skal du klikke på **Rediger**, derefter som **Godkendelsestjenesten**, Vælg navnet på den SAML enkelt Sign-On indstilling fra den forrige sektion og til sidst skal du klikke på **Gem**.

    ![Mit domæne] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Mit domæne")
  
Så snart du har et domæne, der er konfigureret, skal brugerne bruge domæne URL-adressen til at logge på Salesforce sandkassetilstand.  
Klik på den SSO-profil, du har oprettet i forrige afsnit for at få værdien af URL-adressen.
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugerklargøring af Active Directory-brugerkonti til Salesforce sandkassetilstand.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Vælg dit navn for at udvide menuen din bruger i Salesforce-portalen, i den øverste navigationslinje:

    ![Mine indstillinger] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Mine indstillinger")

2.  Vælg **Mine indstillinger** for at åbne siden **Indstillinger for mit** dit bruger-menuen.

3.  Klik på **personlig** for at udvide sektionen personlige i venstre rude, og klik derefter på **Nulstil min sikkerheds-id**:

    ![Mine indstillinger] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Mine indstillinger")

4.  Klik på **Nulstil sikkerheds-id** for at anmode om en mail, der indeholder dine Salesforce.com sikkerhedstoken på siden **Nulstil min sikkerheds-id** .

    ![Nyt id] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Nyt id")

5.  Markere din Indbakke efter en mail fra Salesforce.com med "**salesforce.com.com sikkerhed bekræftelse**" som emne.

6.  Gennemse denne e-mail og kopiere det sikkerhed token.

7.  Klik på **Konfigurer brugerklargøring** for at åbne dialogboksen **Konfigurer brugerklargøring** i Azure klassisk portalen, på siden **salesforce sandkassetilstand** application integration.

    ![Konfigurer brugerklargøring] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Konfigurer brugerklargøring")

8.  Angiv følgende konfigurationsindstillinger på siden **Angiv dine Salesforce sandkassetilstand legitimationsoplysninger for at aktivere automatisk brugerklargøring** :

    ![Salesforce sandkassetilstand] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce sandkassetilstand")

    en.  Skriv et navn i Salesforce sandkassetilstand konto, der indeholder **Systemadministrator** profilen i Salesforce.com tildelt i tekstfeltet **Brugernavn Salesforce sandkassetilstand administrator** .

    b.  I tekstfeltet **Salesforce sandkassetilstand administratoradgangskode** skal du skrive adgangskoden for denne konto.

    c.  Indsæt det sikkerhed token i tekstfeltet **Bruger sikkerheds-id** .

    d.  Klik på **Valider** for at bekræfte din konfiguration.

    e.  Klik på knappen **Næste** for at åbne siden **Confirmation** .

9.  Klik på **udført** for at gemme din konfiguration på siden **Confirmation** .
##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-salesforce-sandbox-perform-the-following-steps"></a>Hvis du vil tildele brugere til Salesforce sandkassetilstand, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Salesforce sandkassetilstand **application integration.

    ![Tildele brugere] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Ja")
  
Du bør nu vente 10 minutter og bekræfte, at kontoen er blevet synkroniseret med Salesforce sandkassetilstand.
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](https://msdn.microsoft.com/library/dn308586)kan finde flere oplysninger om panelet adgang.

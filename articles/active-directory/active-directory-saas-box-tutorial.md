<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med | Microsoft Azure" 
    description="Lær, hvordan du bruger feltet med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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




#<a name="tutorial-azure-active-directory-integration-with-box"></a>Selvstudium: Azure Active Directory-integration med


  
Formålet med dette selvstudium er at vise integration af Azure og feltet.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En test lejer i feltet
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til feltet kunne enkelt Log på programmet på webstedet for virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for boks
2.  Konfigurere Enkeltlogon
3.  Konfigurere brugere og gruppe klargøring
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-box-tutorial/IC769537.png "Scenarie")



##<a name="enabling-the-application-integration-for-box"></a>Aktivere programmet integrationen for boks
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for feltet.

###<a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>For at aktivere programmet integration for feltet skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-box-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-box-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-box-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  **I **søgefeltet**, Skriv.**

    ![Galleri med programmet] (./media/active-directory-saas-box-tutorial/IC701023.png "Galleri med programmet")

7.  **Markér**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Feltet] (./media/active-directory-saas-box-tutorial/IC701024.png "Feltet")



##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere Sådan brugerne mulighed for at godkende til feltet med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen. Som en del af denne procedure skal du overføre metadata til Box.com.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **feltet** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-box-tutorial/IC769538.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge feltet** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-box-tutorial/IC769539.png "Konfigurere Enkeltlogon")

3.  Skriv din feltet Lejer URL-adresse i tekstfeltet **Feltet Lejer URL-adressen** på siden **Konfigurere App URL-adresse** (f.eks.: https://<mydomainname>. box.com), og klik derefter på **Næste**.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-box-tutorial/IC669826.png "Konfigurere app URL-adresse")

4.  Klik på **hente metadata**, og klik derefter datafilen lokalt på din computer for at hente metadata for din, på siden **Konfigurer single sign-on – feltet** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-box-tutorial/IC669824.png "Konfigurere Enkeltlogon")

5.  Videresend metadatafil til dit supportteam. Understøttelse af team behov konfigurerer Enkeltlogon for dig.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-box-tutorial/IC769540.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer klargøring af Active Directory-brugerkonti til feltet.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1. Klik på **Konfigurer brugerklargøring** for at åbne dialogboksen **Konfigurer brugerklargøring** i Azure klassisk portalen, på siden **feltet** application integration. 

    ![Aktivere automatiske brugerklargøring] (./media/active-directory-saas-box-tutorial/IC769541.png "Aktivere automatiske brugerklargøring")

2. Klik på **Aktiver brugerklargøring af**på siden **Aktivér bruger klargøring til feltet** dialogboksen. 

    ![Aktivere automatiske brugerklargøring] (./media/active-directory-saas-box-tutorial/IC769544.png "Aktivere automatiske brugerklargøring")

3. Angive de nødvendige legitimationsoplysninger på siden **Log på give adgang til feltet** , og klik derefter på **Godkend**. 

    ![Aktivere automatiske brugerklargøring] (./media/active-directory-saas-box-tutorial/IC769546.png "Aktivere automatiske brugerklargøring")


4. Klik på **Tillad adgang til feltet** at tillade, at denne handling og vende tilbage til portalen Azure klassisk. 

    ![Aktivere automatiske brugerklargøring] (./media/active-directory-saas-box-tutorial/IC769549.png "Aktivere automatiske brugerklargøring")


5. På siden **Indstillinger for klargøring** **objekttyper at blive klargjort** afkrydsningsfelterne giver dig mulighed at markere eller ej gruppeobjekter er klargjort til feltet foruden user-objekter.  I afsnittet "tildele brugere og grupper" nedenfor for at få flere oplysninger.


6. Klik på knappen Afslut for at afslutte konfigurationen. 

    ![Aktivere automatiske brugerklargøring] (./media/active-directory-saas-box-tutorial/IC769551.png "Aktivere automatiske brugerklargøring")



##<a name="assigning-a-test-user"></a>Tildele en testbruger
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-box-perform-the-following-steps"></a>Hvis du vil tildele brugere til feltet, skal du udføre følgende trin:

1. Oprette en testkonto i Azure klassisk portalen.

2. Klik på **Tildel brugere**på siden **feltet **application integration. 

    ![Tildele brugere] (./media/active-directory-saas-box-tutorial/IC769552.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling. 

    ![Ja] (./media/active-directory-saas-box-tutorial/IC767830.png "Ja")
  
Du bør nu vente 10 minutter og bekræfte, at kontoen er blevet synkroniseret med feltet.

Du kan kontrollere klargøring status, ved at klikke på Dashboard i D på siden med feltet integration på portalen Azure klassisk som et første trin i bekræftelse.

![Dashboard] (./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

En korrekt færdige bruger klargøring cyklus er angivet med en relateret status:

![Integration af status] (./media/active-directory-saas-box-tutorial/IC769555.png "Integration af status")


I din feltet Lejer er synkroniserede brugere angivet under **Administrerede brugere** i **Administrationskonsollen**.

![Integration af status] (./media/active-directory-saas-box-tutorial/IC769556.png "Integration af status")


##<a name="assigning-users-and-groups"></a>Tildele brugere og grupper

Den **feltet > brugere og grupper** fane i portalen Azure klassisk kan du angive, hvilke brugere og grupper skal have tildelt adgang til feltet. Tildeling af en bruger eller gruppe får følgende ting skal udføres:

* Azure AD tillader tildelt brugeren (enten ved direkte tildeling eller gruppemedlemskab) til at godkende feltet. Hvis en bruger ikke er tildelt, derefter Azure AD bliver ikke tillader dem til at logge på feltet og vil returnere en fejl på siden Azure AD-logon.

* Et app-felt til feltet føjes til brugerens [programmet starteren](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

* Hvis automatisk klargøring er aktiveret, derefter føjes tildelte brugere og/eller grupper til klargøring køen skal klargøres automatisk.

    * Hvis der kun brugerobjekter er konfigureret til at blive klargjort, derefter alle direkte-tildelt brugere er placeret i klargøring køen, og alle brugere, der er medlemmer af en hvilken som helst tildelte grupper skal placeres i klargøring køen. 
    
    * Hvis gruppeobjekter er konfigureret til at blive klargjort, klargjort alle tildelte gruppeobjekter til feltet, samt alle brugere, der er medlemmer af disse grupper. Gruppere og bruger medlemskaberne bevares på skrives til feltet.
    
Du kan bruge den **attributter > Single Sign-On** tab for at konfigurere, hvilke brugerattributter (eller krav) vises for feltet under SAML-baseret godkendelse, og den **attributter > artikel Provisioning** tab for at konfigurere, hvordan brugere og grupper attributter flyde fra Azure AD til feltet under klargøringen handlinger. Se nedenstående ressourcer kan finde flere oplysninger.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Tilpasning af krav, der er udstedt i SAML tokenet](active-directory-saml-claims-customization.md)
* [Klargøring af: Tilpasse attributtilknytninger](active-directory-saas-customizing-attribute-mappings.md)
* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Zscaler én | Microsoft Azure" 
    description="Lær at bruge Zscaler én med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Selvstudium: Azure Active Directory-Integration med Zscaler én

Formålet med dette selvstudium er at vise integration med Azure og ZScaler én.  
 Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:  

-   Et gyldigt Azure-abonnement
-   En ZScaler én single sign-on aktiveret abonnement  

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til ZScaler én kunne enkelt Log på programmet på din ZScaler en virksomheds websted (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).  

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:  

1.  Aktivere programmet integrationen for ZScaler én
2.  Konfigurere Enkeltlogon
3.  Konfigurere indstillinger for proxyserver
4.  Konfiguration af brugerklargøring af
5.  Tildele brugere  

![Scenarie] (./media/active-directory-saas-zscaler-one-tutorial/IC800214.png "Scenarie")  

##<a name="enabling-the-application-integration-for-zscaler-one"></a>Aktivere programmet integrationen for ZScaler én

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for ZScaler én.  

###<a name="to-enable-the-application-integration-for-zscaler-one-perform-the-following-steps"></a>For at aktivere programmet integration for ZScaler én skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.  

    ![Active Directory] (./media/active-directory-saas-zscaler-one-tutorial/IC700993.png "Active Directory")  

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .  

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.  

    ![Programmer] (./media/active-directory-saas-zscaler-one-tutorial/IC700994.png "Programmer")  

4.  Klik på **Tilføj** i bunden af siden.  

    ![Tilføj program] (./media/active-directory-saas-zscaler-one-tutorial/IC749321.png "Tilføj program")  

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.  

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-zscaler-one-tutorial/IC749322.png "Tilføj et program fra gallerry")  

6.  I **søgefeltet**skal du skrive **ZScaler én**.  

    ![Galleri med programmet] (./media/active-directory-saas-zscaler-one-tutorial/IC800215.png "Galleri med programmet")  

7.  Vælg **ZScaler én**i resultatruden, og klik derefter på **udført** for at tilføje programmet.  

    ![ZScaler én] (./media/active-directory-saas-zscaler-one-tutorial/IC800216.png "ZScaler én")  

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere Sådan brugerne mulighed for at godkende med ZScaler én med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at overføre et base-64-kodet certifikat til din ZScaler én lejer som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)  

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Til Azure klassisk portalen, på siden med **ZScaler én** integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .  

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-zscaler-one-tutorial/IC800217.png "Konfigurere Single Sign-On")  

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge ZScaler én** , og klik derefter på **Næste**.  

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-zscaler-one-tutorial/IC800218.png "Konfigurere Single Sign-On")  

3.  Skriv URL-adressen bruges af dine brugere at logge på din ZScaler ét program, og klik derefter på **Næste**på siden **Konfigurer App URL-adressen** i tekstfeltet **ZScaler én log på URL-adresse** .  

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-zscaler-one-tutorial/IC800219.png "Konfigurere App URL-adresse")  

    >[AZURE.NOTE]Du kan få den faktiske værdi for dit miljø fra supportteamet ZScaler én, hvis du vil have den.  

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på ZScaler én** , og derefter gemme certifikatfilen på din computer.  

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-zscaler-one-tutorial/IC800220.png "Konfigurere Single Sign-On")  

5.  Log på din ZScaler en virksomheds websted som en administrator i et andet browservindue.  

6.  Klik på **Administration**i menuen øverst.  

    ![Administration] (./media/active-directory-saas-zscaler-one-tutorial/IC800206.png "Administration")  

7.  Klik på **Administrer brugere og godkendelse**under **Administrer administratorer og roller**.  

    ![Administrere brugere og -godkendelse] (./media/active-directory-saas-zscaler-one-tutorial/IC800207.png "Administrere brugere og -godkendelse")  

8.  I sektionen **Vælg indstillinger for godkendelse for din organisation** skal du udføre følgende trin:  

    ![Godkendelse] (./media/active-directory-saas-zscaler-one-tutorial/IC800208.png "Godkendelse")  

    1.  Vælg **Godkend ved hjælp af SAML Single Sign-On**.  
    2.  Klik på **Konfigurer SAML enkelt Sign-On parametre**.  

9.  Udfør følgende trin på siden **Configure SAML enkelt Sign-On Parameters** dialogboksen, og klik derefter på **udført**:  

    ![Single Sign-On] (./media/active-directory-saas-zscaler-one-tutorial/IC800209.png "Single Sign-On")  

    1.  Kopiér **URL-godkendelse anmode om** værdien i Azure klassisk-portalen på dialogboksside **konfigurere Enkeltlogon på ZScaler én** , og Indsæt det i tekstfeltet **URL-adressen til portalen SAML, som brugerne er sendt til godkendelse** .  
    2.  Skriv **NameID**i tekstfeltet **attribut med Login-navn** .  
    3.  Hvis du vil overføre hentede certifikatet, skal du klikke på **Zscaler pem**.  
    4.  Vælg **Aktivér automatisk klargøring af SAML**.  

10. På siden **Konfigurer brugergodkendelse** dialogboksen skal du udføre følgende trin:  

    ![Administration] (./media/active-directory-saas-zscaler-one-tutorial/IC800210.png "Administration")  

    1.  Klik på **Gem**.  
    2.  Klik på **Aktivér nu**.  

11. Vælge enkelt sign-on – konfiguration bekræftelsen i Azure klassisk-portalen på dialogboksside **konfigurere Enkeltlogon på ZScaler én** , og klik derefter på **udført**.  

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-zscaler-one-tutorial/IC800221.png "Konfigurere Single Sign-On")  

##<a name="configuring-proxy-settings"></a>Konfigurere indstillinger for proxyserver

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Du kan konfigurere proxyindstillingerne i Internet Explorer

1.  Start **Internet Explorer**.  

2.  Vælg **Internetindstillinger** i menuen **Funktioner** for at åbne dialogboksen **Internetindstillinger** .  

    ![Internetindstillinger] (./media/active-directory-saas-zscaler-one-tutorial/IC769492.png "Internetindstillinger")  

3.  Klik på fanen **forbindelser** .  

    ![Forbindelser] (./media/active-directory-saas-zscaler-one-tutorial/IC769493.png "Forbindelser")  

4.  Klik på **LAN-indstillinger** for at åbne dialogboksen **LAN-indstillinger** .  

5.  I sektionen Proxy-server skal du udføre følgende trin:  

    ![Proxy-server] (./media/active-directory-saas-zscaler-one-tutorial/IC769494.png "Proxy-server")  

    1.  Vælg Brug en proxyserver til LAN.  
    2.  I tekstfeltet mailadresse skal du skrive **gateway.zscalerone.net**.  
    3.  Skriv i tekstfeltet Port **80**.  
    4.  Vælg **Brug ikke proxyserver til lokale adresser**.  
    5.  Klik på **OK** for at lukke dialogboksen **Indstillinger for lokalnetværk (LAN)** .  

6.  Klik på **OK** for at lukke dialogboksen **Internetindstillinger** .  

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på ZScaler én, skal de klargøres til ZScaler én.  
 I forbindelse med ZScaler én er klargøring en manuel opgave.  

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på din **Zscaler én** lejer.  

2.  Klik på **Administration**.  

    ![Administration] (./media/active-directory-saas-zscaler-one-tutorial/IC781035.png "Administration")  

3.  Klik på **Brugeradministration**.  

    ![Tilføje] (./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Tilføje")  

4.  Klik på **Tilføj**under fanen **brugere** .  

    ![Tilføje] (./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Tilføje")  

5.  I sektionen Tilføj bruger skal du udføre følgende trin:  

    ![Tilføj bruger] (./media/active-directory-saas-zscaler-one-tutorial/IC781038.png "Tilføj bruger")  

    1.  Skriv **bruger-id**, **Brugerens viste navn**, **din adgangskode**, **Bekræft adgangskode**, og vælg derefter **grupper** og en gyldig AAD-konto, du vil klargøre **afdeling** .  
    2.  Klik på **Gem**.  

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden ZScaler én bruger konto værktøjer til oprettelse eller API'er, som ZScaler én til klargøring AAD brugerkonti.  

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.  

###<a name="to-assign-users-to-zscaler-one-perform-the-following-steps"></a>Hvis du vil tildele brugere til ZScaler én, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.  

2.  Klik på **Tildel brugere**på siden med **ZScaler én** integration.  

    ![Tildele brugere] (./media/active-directory-saas-zscaler-one-tutorial/IC800222.png "Tildele brugere")  

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.  

    ![Ja] (./media/active-directory-saas-zscaler-one-tutorial/IC767830.png "Ja")  

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.  

<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Zscaler | Microsoft Azure" 
    description="Lær at bruge Zscaler med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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

#<a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Selvstudium: Azure Active Directory-integration med Zscaler
  
Formålet med dette selvstudium er at vise integration af Azure og Zscaler. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En lejer i Zscaler
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Zscaler
2.  Konfigurere Enkeltlogon
3.  Konfigurere indstillinger for proxyserver
4.  Konfiguration af brugerklargøring af
5.  Tildele brugere

![Scenarie] (./media/active-directory-saas-zscaler-tutorial/IC769226.png "Scenarie")

##<a name="enabling-the-application-integration-for-zscaler"></a>Aktivere programmet integrationen for Zscaler
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Zscaler.

###<a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>For at aktivere programmet integration for Zscaler skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-zscaler-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-zscaler-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-zscaler-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Zscaler**.

    ![Galleri med programmet] (./media/active-directory-saas-zscaler-tutorial/IC769227.png "Galleri med programmet")

7.  Vælg **Zscaler**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Zscaler] (./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Zscaler med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Som en del af denne procedure, er det nødvendigt at overføre et certifikat til Zscaler.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Zscaler** application integration.

    ![Aktivere enkeltlogon] (./media/active-directory-saas-zscaler-tutorial/IC769229.png "Aktivere enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Zscaler** , og klik derefter på **Næste**.

    ![Konfigurer enkelt logge] (./media/active-directory-saas-zscaler-tutorial/IC769230.png "Konfigurer enkelt logge")

3.  Skriv din tilmelding URL-adresse, du har fået fra Zscaler, og klik derefter på **Næste**på siden **Konfigurer App URL-adressen** i tekstfeltet **Zscaler log i URL-adresse** : 

    >[AZURE.NOTE] Kontakt supportteamet Zscaler, hvis du ikke kender din tilmelding i URL-adresse.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-zscaler-tutorial/IC769231.png "Konfigurere app URL-adresse")

4.  På siden **Konfigurer single sign-on – på Zscaler** skal du udføre følgende trin:

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-zscaler-tutorial/IC769232.png "Konfigurere Enkeltlogon")

    1.  Klik på **Hent certifikat**, og Gem derefter certifikatfilen lokalt som **c:\\Zscaler.cer**.
    2.  Du kan kopiere **URL-anmodningen godkendelse** i din Udklipsholder.

5.  Log på din Zscaler lejer.

6.  Klik på **Administration**i menuen øverst.

    ![Administration] (./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administration")

7.  Under **Administrer administratorer og roller**, skal du klikke på **administrere brugere og godkendelse**.

    ![Administrer administratorer og roller] (./media/active-directory-saas-zscaler-tutorial/IC769487.png "Administrer administratorer og roller")

8.  I sektionen **Vælg godkendelsesindstilling for din organisation** skal du udføre følgende trin:

    ![Vælg indstillinger for godkendelse] (./media/active-directory-saas-zscaler-tutorial/IC769488.png "Vælg indstillinger for godkendelse")

    1.  Vælg **Godkend ved hjælp af SAML Single Sign-on**.
    2.  Klik på **Konfigurer SAML enkelt Sign-On parametre**.

9.  Udfør følgende trin på siden **Configure SAML enkelt Sign-On Parameters** dialogboksen, og klik derefter på **udført**:

    ![Overføre certifikat] (./media/active-directory-saas-zscaler-tutorial/IC769489.png "Overføre certifikat")

    1.  Indsæt værdien i feltet **URL-godkendelse anmodningen** fra Azure klassisk portal i tekstfeltet **URL-adressen til portalen SAML, som brugerne er sendt til godkendelse** .
    2.  Skriv **NameID**i tekstfeltet **attribut med Login-navn** .
    3.  Upload det certifikat, du har hentet fra portalen Azure klassisk i feltet **Overføre offentlige SSL-certifikat** .
    4.  Vælg **Aktivér automatisk klargøring af SAML**.

10. På siden **Konfigurer brugergodkendelse** dialogboksen skal du udføre følgende trin:

    ![Konfigurer brugergodkendelse] (./media/active-directory-saas-zscaler-tutorial/IC769490.png "Konfigurer brugergodkendelse")

    1.  Klik på **Gem**.
    2.  Klik på **Aktivér nu**.

11. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-zscaler-tutorial/IC769491.png "Konfigurere Enkeltlogon")

##<a name="configuring-proxy-settings"></a>Konfigurere indstillinger for proxyserver

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Du kan konfigurere proxyindstillingerne i Internet Explorer

1.  Start **Internet Explorer**.

2.  Vælg **Internetindstillinger** i menuen **Funktioner** for at åbne dialogboksen **Internetindstillinger** .

    ![Internetindstillinger] (./media/active-directory-saas-zscaler-tutorial/IC769492.png "Internetindstillinger")

3.  Klik på fanen **forbindelser** .

    ![Forbindelser] (./media/active-directory-saas-zscaler-tutorial/IC769493.png "Forbindelser")

4.  Klik på **LAN-indstillinger** for at åbne dialogboksen **LAN-indstillinger** .

5.  I sektionen Proxy-server skal du udføre følgende trin:

    ![Proxy-server] (./media/active-directory-saas-zscaler-tutorial/IC769494.png "Proxy-server")

    1.  Vælg Brug en proxyserver til LAN.
    2.  I tekstfeltet mailadresse skal du skrive **gateway.zscalertwo.net**.
    3.  Skriv i tekstfeltet Port **80**.
    4.  Vælg **Brug ikke proxyserver til lokale adresser**.
    5.  Klik på **OK** for at lukke dialogboksen **Indstillinger for lokalnetværk (LAN)** .

6.  Klik på **OK** for at lukke dialogboksen **Internetindstillinger** .

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Zscaler, skal de klargøres til Zscaler.  
Klargøring af er Zscaler, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på din **Zscaler** lejer.

2.  Klik på **Administration**.

    ![Administration] (./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administration")

3.  Klik på **Brugeradministration**.

    ![Brugeradministration] (./media/active-directory-saas-zscaler-tutorial/IC781036.png "Brugeradministration")

4.  Klik på **Tilføj**under fanen **brugere** .

    ![Tilføje] (./media/active-directory-saas-zscaler-tutorial/IC781037.png "Tilføje")

5.  I sektionen Tilføj bruger skal du udføre følgende trin:

    ![Tilføj bruger] (./media/active-directory-saas-zscaler-tutorial/IC781038.png "Tilføj bruger")

    1.  Skriv **bruger-id**, **Brugerens viste navn**, **din adgangskode**, **Bekræft adgangskode**, og vælg derefter **grupper** og en gyldig AAD-konto, du vil klargøre **afdeling** .
    2.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Zscaler bruger konto værktøj til oprettelse af eller API'er, som Zscaler til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Hvis du vil tildele brugere til Zscaler, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Zscaler** application integration.

    ![Tildele brugere] (./media/active-directory-saas-zscaler-tutorial/IC769495.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-zscaler-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

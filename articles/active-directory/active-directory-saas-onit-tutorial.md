<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Onit | Microsoft Azure" 
    description="Lær, hvordan du bruger Onit med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-onit"></a>Selvstudium: Azure Active Directory-integration med Onit
  
Formålet med dette selvstudium er at vise integration af Azure og Onit.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Onit single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Onit kunne enkelt Log på programmet på webstedet Onit virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Onit
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-onit-tutorial/IC791166.png "Scenarie")
##<a name="enabling-the-application-integration-for-onit"></a>Aktivere programmet integrationen for Onit
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Onit.

###<a name="to-enable-the-application-integration-for-onit-perform-the-following-steps"></a>For at aktivere programmet integration for Onit skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-onit-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-onit-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-onit-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Onit**.

    ![Galleri med programmet] (./media/active-directory-saas-onit-tutorial/IC791167.png "Galleri med programmet")

7.  Vælg **Onit**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Onit] (./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Onit med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Onit, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).
  
Programmets Onit forventer SAML påstande i et bestemt format, som kræver, at du kan tilføje brugerdefineret attributtilknytninger til konfigurationen af **saml token attributter** .  
Følgende skærmbillede viser et eksempel af dette.

![Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791168.png "Single Sign-On")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i Azure klassisk-portalen på **Onit** programmets integrationsside, i menuen i øverst.

    ![Attributter] (./media/active-directory-saas-onit-tutorial/IC791169.png "Attributter")

2.  For at tilføje påkrævet attributtilknytningerne skal du udføre følgende trin:

    
  	|Attributnavn|Attributværdien|
  	|---|---|
  	|Navn|User.userPrincipalName|
  	|mail|User.mail|

    1.  Klik på **Tilføj bruger attribut**for hver datarække i tabellen ovenfor.
    2.  Skriv det attributnavn, der vises for den pågældende række i tekstfeltet **Attributnavn** .
    3.  Vælg attributværdien vises for den pågældende række fra listen **Attributværdien** .
    4.  Klik på **udført**.

3.  Klik på **Anvend ændringer**.

4.  I din browser skal du klikke på **tilbage** for at åbne dialogboksen **Hurtig Start** igen.

5.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791170.png "Konfigurere Single Sign-On")

6.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Onit** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791171.png "Konfigurere Single Sign-On")

7.  Skriv URL-adressen bruges af dine brugere til at logge din Onit-program i tekstfeltet **Onit Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://ms-sso-test.onit.com*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-onit-tutorial/IC791172.png "Konfigurere App URL-adresse")

8.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Onit** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791173.png "Konfigurere Single Sign-On")

9.  Log på webstedet Onit virksomhed som administrator i et andet browservindue.

10. Klik på **Administration**i menuen øverst.

    ![Administration] (./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")

11. Klik på **Rediger Corporation**.

    ![Rediger Corporation] (./media/active-directory-saas-onit-tutorial/IC791175.png "Rediger Corporation")

12. Klik på fanen **sikkerhed** .

    ![Rediger firmaoplysninger] (./media/active-directory-saas-onit-tutorial/IC791176.png "Rediger firmaoplysninger")

13. På fanen **sikkerhed** skal du udføre følgende trin:

    ![Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791177.png "Single Sign-On")

    1.  Vælg **Single Sign-On og en adgangskode**, som **Strategi for godkendelse**.
    2.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Onit** dialogboksen siden, og Indsæt det i tekstfeltet **Idp URL-adresse** .
    3.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Onit** dialogboksen siden, og Indsæt det i tekstfeltet **Idp log URL-adresse** .
    4.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **Idp Cert fingeraftryk (SHA1)** .  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    5.  Vælg **SAML**som **SSO Type**.
    6.  Skriv en knappen tekst, der i tekstfeltet **SSO login knappen tekst** .
    7.  Vælg **logon med SSO: kræves til følgende domæner/brugere**skal du skrive en testbruger mailadresse i tekstfeltet relaterede og derefter klikke på **Opdater**.
        ![Rediger Corporation] (./media/active-directory-saas-onit-tutorial/IC791178.png "Rediger Corporation")

14. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791179.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Onit, skal de klargøres til Onit.  
Klargøring af er Onit, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Logge webstedet **Onit** virksomhed som administrator.

2.  Klik på **Tilføj bruger**.

    ![Administration] (./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")

3.  På siden **Tilføj bruger** dialogboksen skal du udføre følgende trin:

    ![Tilføj bruger] (./media/active-directory-saas-onit-tutorial/IC791181.png "Tilføj bruger")

    1.  Skriv **navnet** og **Mailadresse** på en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Opret**.  

        >[AZURE.NOTE] Kontoejeren får mails, der indeholder et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Onit bruger konto værktøjer til oprettelse eller API'er, som Onit til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-onit-perform-the-following-steps"></a>Hvis du vil tildele brugere til Onit, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Onit **application integration.

    ![Tildele brugere] (./media/active-directory-saas-onit-tutorial/IC791182.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-onit-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
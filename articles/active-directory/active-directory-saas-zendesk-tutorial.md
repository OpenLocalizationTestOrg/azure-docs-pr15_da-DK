<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Zendesk | Microsoft Azure" 
    description="Lær at bruge Zendesk med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Selvstudium: Azure Active Directory-integration med Zendesk
  
Formålet med dette selvstudium er at vise integration af Azure og Zendesk.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Zendesk lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Zendesk kunne enkelt Log på programmet på webstedet Zendesk virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Zendesk
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scenarie")

##<a name="enabling-the-application-integration-for-zendesk"></a>Aktivere programmet integrationen for Zendesk
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Zendesk.

###<a name="to-enable-the-application-integration-for-zendesk-perform-the-following-steps"></a>For at aktivere programmet integration for Zendesk skal du udføre følgende trin:

1.  Klik på **Active Directory**i portalen Azure administration i den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-zendesk-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-zendesk-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-zendesk-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Zendesk**.

    ![Galleri med programmet] (./media/active-directory-saas-zendesk-tutorial/IC773084.png "Galleri med programmet")

7.  Vælg **Zendesk**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Zendesk] (./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Zendesk med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Zendesk, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure AD-portalen på siden **Zendesk** application integration.

    ![Single sign-on] (./media/active-directory-saas-zendesk-tutorial/IC773086.png "Single sign-on")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Zendesk** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-zendesk-tutorial/IC773087.png "Konfigurere Enkeltlogon")

3.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-zendesk-tutorial/IC773088.png "Konfigurere app URL-adresse")
  
    en. I tekstfeltet **Zendesk log i URL-adresse** skal du skrive din URL-adresse ved hjælp af følgende mønster:`https://<tenant-name>.zendesk.com`

    b. Klik på **Næste**.



4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Zendesk** , og Gem derefter certifikatfilen lokalt på din compiter.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-zendesk-tutorial/IC777534.png "Konfigurere Enkeltlogon")

5.  Log på webstedet Zendesk virksomhed som administrator i et andet browservindue.

6.  Klik på **administrator**.

7.  Klik på **Indstillinger**i den venstre navigationsrude, og klik derefter på **sikkerhed**.

    ![Sikkerhed] (./media/active-directory-saas-zendesk-tutorial/IC773089.png "Sikkerhed")

8.  Klik på fanen **administrator og supportmedarbejdere** på siden **sikkerhed** .

9.  Vælg **Single sign-on (SSO) og SAML**, og vælg derefter **SAML**.

10. Kopiér værdien **SAML SSO URL-adressen** på Azure AD-portalen på siden **Konfigurer single sign-on – på Zendesk** , og Indsæt det i tekstfeltet **SAML SSO URL-adresse** .

11. Kopiér værdien **Remote log URL-adressen** på Azure AD-portalen på siden **Konfigurer single sign-on – på Zendesk** , og Indsæt det i tekstfeltet **Remote log URL-adresse** .

    ![Single sign-on] (./media/active-directory-saas-zendesk-tutorial/IC773090.png "Single sign-on")

12. Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **Certifikat fingeraftryk** .

    >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

13. Klik på **Gem**.

14. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure AD-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-zendesk-tutorial/IC773093.png "Konfigurere Enkeltlogon")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på **Zendesk**, skal de klargøres til **Zendesk**.  
Klargøring af er **Zendesk**, en manuel opgave.

###<a name="to-provision-a-user-account-to-zendesk-perform-the-following-steps"></a>Hvis du vil tildele en brugerkonto til Zendesk, skal du udføre følgende trin:

1.  Log på din **Zendesk** lejer.

2.  Vælg fanen **Liste over kundens** .

3.  Vælg fanen **bruger** , og klik på **Tilføj**.

    ![Tilføj bruger] (./media/active-directory-saas-zendesk-tutorial/IC773632.png "Tilføj bruger")

4.  Skriv mailadressen på en eksisterende Azure AD-konto, du vil klargøre, og klik derefter på **Gem**.

    ![Ny bruger] (./media/active-directory-saas-zendesk-tutorial/IC773633.png "Ny bruger")

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Zendesk bruger konto værktøjer til oprettelse eller API'er, som Zendesk til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-zendesk-perform-the-following-steps"></a>Hvis du vil tildele brugere til Zendesk, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure AD-portalen.

2.  Klik på **Tildel brugere**på siden **Zendesk **application integration.

    ![Tildele brugere] (./media/active-directory-saas-zendesk-tutorial/IC773094.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-zendesk-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

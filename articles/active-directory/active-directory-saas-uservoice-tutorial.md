<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med UserVoice | Microsoft Azure" 
    description="Lær at bruge UserVoice med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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

#<a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Selvstudium: Azure Active Directory-Integration med UserVoice
  
Formålet med dette selvstudium er at vise integration af Azure og UserVoice.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En UserVoice-lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til UserVoice kunne enkelt Log på programmet på webstedet UserVoice virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for UserVoice
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scenarie")

##<a name="enabling-the-application-integration-for-uservoice"></a>Aktivere programmet integrationen for UserVoice
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for UserVoice.

###<a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>For at aktivere programmet integration for UserVoice skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-uservoice-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-uservoice-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-uservoice-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **UserVoice**.

    ![Galleri med programmet] (./media/active-directory-saas-uservoice-tutorial/IC777513.png "Galleri med programmet")

7.  Vælg **UserVoice**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![UserVoice] (./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende UserVoice med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til UserVoice, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **UserVoice** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-uservoice-tutorial/IC777515.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge UserVoice** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-uservoice-tutorial/IC777516.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **UserVoice log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. UserVoice.com*", og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-uservoice-tutorial/IC777517.png "Konfigurere App URL-adresse")

4.  På siden **Konfigurer single sign-on – på UserVoice** for at downloade certifikatet, skal du klikke på **Hent certifikat**, og Gem derefter certifikatfil lokalt som **c:\\UserVoice.cer**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-uservoice-tutorial/IC777518.png "Konfigurere Enkeltlogon")

5.  Log på webstedet UserVoice virksomhed som administrator i et andet browservindue.

6.  Klik på indstillinger på værktøjslinjen øverst, og vælg derefter webportal i menuen.

    ![Indstillinger] (./media/active-directory-saas-uservoice-tutorial/IC777519.png "Indstillinger")

7.  Klik på **Rediger** for at åbne siden **Rediger brugergodkendelse** dialogboksen fanen **webportal** i sektionen **brugergodkendelse**

    ![Web-portalen] (./media/active-directory-saas-uservoice-tutorial/IC777520.png "Web-portalen")

8.  På siden **Rediger brugergodkendelse** dialogboksen skal du udføre følgende trin:

    ![Redigere brugergodkendelse] (./media/active-directory-saas-uservoice-tutorial/IC777521.png "Redigere brugergodkendelse")

    1.  Klik på **Single Sign-On (SSO)**.
    2.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på UserVoice** dialogboksen siden, og Indsæt det i tekstfeltet **SSO Remote-logon** .
    3.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på UserVoice** dialogboksen siden, og Indsæt det i **SSO Remote Sign-Out tekstfelt**.
    4.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **aktuelle certifikat SHA1 fingeraftryk** .  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    5.  Klik på **Gem godkendelsesindstillinger**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-uservoice-tutorial/IC777522.png "Konfigurere Enkeltlogon")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på UserVoice, skal de klargøres til UserVoice.  
Klargøring af er UserVoice, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **UserVoice** lejer.

2.  Gå til **Indstillinger**.

    ![Indstillinger] (./media/active-directory-saas-uservoice-tutorial/IC777811.png "Indstillinger")

3.  Klik på **Generelt**.

4.  Klik på **brugere og tilladelser**.

    ![Supportmedarbejdere og tilladelser] (./media/active-directory-saas-uservoice-tutorial/IC777812.png "Supportmedarbejdere og tilladelser")

5.  Klik på **Tilføj administratorer**.

    ![Tilføj administratorer] (./media/active-directory-saas-uservoice-tutorial/IC777813.png "Tilføj administratorer")

6.  I dialogboksen **Inviter administratorer** skal du udføre følgende trin:

    ![Invitere administratorer] (./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invitere administratorer")

    1.  Skriv mailadressen på den konto, du vil klargøre, og klik derefter på **Tilføj**i texbox mails.
    2.  Klik på **Inviter**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden UserVoice bruger konto værktøjer til oprettelse eller API'er, som UserVoice til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Hvis du vil tildele brugere til UserVoice, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **UserVoice **application integration.

    ![Tildele brugere] (./media/active-directory-saas-uservoice-tutorial/IC777523.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-uservoice-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
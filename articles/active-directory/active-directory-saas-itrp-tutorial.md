<properties
    pageTitle="Selvstudium: Azure Active Directory-Integration med ITRP | Microsoft Azure" 
    description="Lær, hvordan du bruger ITRP med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/07/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-itrp"></a>Selvstudium: Azure Active Directory-Integration med ITRP
  
Formålet med dette selvstudium er at vise integration af Azure og ITRP.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En ITRP lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til ITRP kunne enkelt Log på programmet på webstedet ITRP virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for ITRP
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-itrp-tutorial/IC775551.png "Scenarie")
##<a name="enabling-the-application-integration-for-itrp"></a>Aktivere programmet integrationen for ITRP
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for ITRP.

###<a name="to-enable-the-application-integration-for-itrp-perform-the-following-steps"></a>For at aktivere programmet integration for ITRP skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-itrp-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-itrp-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-itrp-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **ITRP**.

    ![Galleri med programmet] (./media/active-directory-saas-itrp-tutorial/IC775565.png "Galleri med programmet")

7.  Vælg **ITRP**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende ITRP med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til ITRP, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **ITRP** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-itrp-tutorial/IC771709.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge ITRP** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775567.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **ITRP log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. ITRP.com*", og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-itrp-tutorial/IC775568.png "Konfigurere App URL-adresse")

4.  På siden **Konfigurer single sign-on – på ITRP** for at downloade certifikatet, skal du klikke på **Hent certifikat**, og Gem derefter certifikatfil lokalt som **c:\\ITRP.cer**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775569.png "Konfigurere Single Sign-On")

5.  Log på webstedet ITRP virksomhed som administrator i et andet browservindue.

6.  Klik på **Indstillinger**på værktøjslinjen øverst.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  Vælg **Single Sign-On**i den venstre navigationsrude.

    ![Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775571.png "Single Sign-On")

8.  I Single Sign-On konfigurationsafsnittet skal du udføre følgende trin:

    ![Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775572.png "Single Sign-On")

    ![Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775573.png "Single Sign-On")

    1.  Klik på **Aktivér**.
    2.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på ITRP** dialogboksen siden, og Indsæt det i tekstfeltet **Remote log URL-adresse** .
    3.  Kopiér værdien **SAML SSO URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på ITRP** dialogboksen siden, og Indsæt det i tekstfeltet **SAML SSO URL-adresse** .
    4.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **Certifikat fingeraftryk** .
        
        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    5.  Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775574.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på ITRP, skal de klargøres til ITRP.  
Klargøring af er ITRP, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **ITRP** lejer.

2.  Klik på **poster**i værktøjslinjen øverst.

    ![Administrator] (./media/active-directory-saas-itrp-tutorial/IC775575.png "Administrator")

3.  Vælg **personer**fra i pop op-menuen.

    ![Personer] (./media/active-directory-saas-itrp-tutorial/IC775587.png "Personer")

4.  Klik på **Tilføj ny Person** ("+").

    ![Administrator] (./media/active-directory-saas-itrp-tutorial/IC775576.png "Administrator")

5.  I dialogboksen Tilføj ny Person skal du udføre følgende trin:

    ![Bruger] (./media/active-directory-saas-itrp-tutorial/IC775577.png "Bruger")

    1.  Skriv **navnet**, **mail** med en gyldig AAD-konto, du vil klargøre.
    2.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden ITRP bruger konto værktøjer til oprettelse eller API'er, som ITRP til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-itrp-perform-the-following-steps"></a>Hvis du vil tildele brugere til ITRP, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure AD-portalen.

2.  Klik på **Tildel brugere**på siden **ITRP **application integration.

    ![Tildele brugere] (./media/active-directory-saas-itrp-tutorial/IC775588.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-itrp-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
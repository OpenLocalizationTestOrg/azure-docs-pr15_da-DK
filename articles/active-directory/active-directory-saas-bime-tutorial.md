<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Bime | Microsoft Azure" 
    description="Lær, hvordan du bruger Bime med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-bime"></a>Selvstudium: Azure Active Directory-Integration med Bime

Formålet med dette selvstudium er at vise integration af Azure og Bime.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Bime lejer

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Bime kunne enkelt Log på programmet på webstedet Bime virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Bime
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-bime-tutorial/IC775552.png "Scenarie")
##<a name="enabling-the-application-integration-for-bime"></a>Aktivere programmet integrationen for Bime

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Bime.

###<a name="to-enable-the-application-integration-for-bime-perform-the-following-steps"></a>For at aktivere programmet integration for Bime skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-bime-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-bime-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-bime-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Bime**.

    ![Galleri med programmet] (./media/active-directory-saas-bime-tutorial/IC775553.png "Galleri med programmet")

7.  Vælg **Bime**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Bime] (./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Bime med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Bime, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Bime** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-bime-tutorial/IC771709.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Bime** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775555.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Bime log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. Bimeapp.com*", og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-bime-tutorial/IC775556.png "Konfigurere App URL-adresse")

4.  På siden **Konfigurer single sign-on – på Bime** for at downloade certifikatet, skal du klikke på **Hent certifikat**, og Gem derefter certifikatfil lokalt som **c:\\Bime.cer**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775557.png "Konfigurere Single Sign-On")

5.  Log på webstedet Bime virksomhed som administrator i et andet browservindue.

6.  Klik på **administrator**, og klik derefter på **konto**på værktøjslinjen.

    ![Administrator] (./media/active-directory-saas-bime-tutorial/IC775558.png "Administrator")

7.  På konfigurationssiden af konto skal du udføre følgende trin:

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775559.png "Konfigurere Single Sign-On")

    1.  Vælg **Aktivér SAML godkendelse**.
    2.  Kopiér **URL-Remote Login** værdien i Azure klassisk-portalen på **Konfigurer single sign-on – på Bime** dialogboksen siden, og Indsæt det i tekstfeltet **Remote Login URL-adresse** .
    3.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **Certifikat fingeraftryk** .  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    4.  Klik på **Gem**.

8.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775560.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Bime, skal de klargøres til Bime.  
Klargøring af er Bime, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på din **Bime** lejer.

2.  Klik på **Admin**og **brugere**i værktøjslinjen.

    ![Administrator] (./media/active-directory-saas-bime-tutorial/IC775561.png "Administrator")

3.  Klik på **Tilføj ny bruger** ("+") på **Listen brugere**.

    ![Brugere] (./media/active-directory-saas-bime-tutorial/IC775562.png "Brugere")

4.  På siden **Oplysninger om bruger** dialogboksen skal du udføre følgende trin:

    ![Oplysninger om bruger] (./media/active-directory-saas-bime-tutorial/IC775563.png "Oplysninger om bruger")

    1.  Angiv fornavn, efternavn, logon, mail med en gyldig AAD-konto, du vil klargøre.
    2.  Klik på Gem.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Bime bruger konto værktøjer til oprettelse eller API'er, som Bime til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-bime-perform-the-following-steps"></a>Hvis du vil tildele brugere til Bime, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Bime **application integration.

    ![Tildele brugere] (./media/active-directory-saas-bime-tutorial/IC775564.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-bime-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

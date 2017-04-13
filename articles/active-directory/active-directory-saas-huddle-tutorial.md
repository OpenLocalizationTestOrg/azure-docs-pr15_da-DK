<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med klumper sig tæt | Microsoft Azure" 
    description="Lær, hvordan du bruger klumper sig tæt med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-huddle"></a>Selvstudium: Azure Active Directory-integration med klumper sig tæt
  
Formålet med dette selvstudium er at vise integration af Azure og klumper sig tæt.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En klumper sig tæt single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til klumper sig tæt kunne enkelt Log på programmet på din klumper sig tæt virksomheds-websted (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen klumper sig tæt
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Konfigurere Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787830.png "Konfigurere Single Sign-On")
##<a name="enabling-the-application-integration-for-huddle"></a>Aktivere programmet integrationen klumper sig tæt
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen klumper sig tæt.

###<a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>For at aktivere programmet integrationen klumper sig tæt skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-huddle-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-huddle-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-huddle-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **klumper sig tæt**.

    ![Galleri med programmet] (./media/active-directory-saas-huddle-tutorial/IC787831.png "Galleri med programmet")

7.  Vælg **klumper sig tæt**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Klumper sig tæt] (./media/active-directory-saas-huddle-tutorial/IC787832.png "Klumper sig tæt")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende klumper sig tæt med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Til Azure klassisk portalen, på siden **klumper sig tæt** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787833.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge klumper sig tæt** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787834.png "Konfigurere Single Sign-On")

3.  Skriv Webadressen på din klumper sig tæt lejer ved hjælp af følgende mønster "*http://company.huddle.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Klumper sig tæt Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-huddle-tutorial/IC787835.png "Konfigurere App URL-adresse")

4.  På siden **Konfigurer single sign-on – på klumper sig tæt** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787836.png "Konfigurere Single Sign-On")

    1.  Klik på **Hent certifikat**, og Gem derefter certifikatfilen på din computer.
    2.  Kopiér værdien **Udsteder URL-adresse** , **SAML SSO URL-** værdien og hentede certifikatet, og derefter sende dem til supportteam klumper sig tæt.

    >[AZURE.NOTE] Skal være aktiveret af supportteam klumper sig tæt enkeltlogon.
Du får en meddelelse, når konfigurationen er fuldført.

5.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787837.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på klumper sig tæt, skal de klargøres i klumper sig tæt.  
Hvis det er klumper sig tæt er klargøring en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet **klumper sig tæt** virksomhed som administrator.

2.  Klik på **arbejdsområde**.

3.  Klik på **personer \> Inviter personer**.

    ![Personer] (./media/active-directory-saas-huddle-tutorial/IC787838.png "Personer")

4.  I sektionen **Opret en ny invitation** skal du udføre følgende trin:

    ![Ny Invitation] (./media/active-directory-saas-huddle-tutorial/IC787839.png "Ny Invitation")

    1.  Vælg **team**, på listen **Vælg en gruppe at invitere personer til at deltage** .
    2.  Skriv den **Mailadresse** for en gyldig AAD-konto, du vil klargøre i tekstfeltet relaterede.
    3.  Klik på **Inviter**.

    >[AZURE.NOTE] Azure AD-kontoindehaver modtager mails, der indeholder et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden klumper sig tæt bruger konto værktøjer til oprettelse eller API'er, der leveres af klumper sig tæt at blive klargjort AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-huddle-perform-the-following-steps"></a>Hvis du vil tildele brugere til klumper sig tæt, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **klumper sig tæt **application integration.

    ![Tildele brugere] (./media/active-directory-saas-huddle-tutorial/IC787840.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-huddle-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
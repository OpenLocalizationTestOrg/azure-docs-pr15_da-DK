<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Jitbit Helpdesk | Microsoft Azure" 
    description="Lær, hvordan du bruger Jitbit Helpdesk med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Selvstudium: Azure Active Directory-integration med Jitbit Helpdesk
  
Formålet med dette selvstudium er at vise integration af Azure og Jitbit Helpdesk.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Jitbit Helpdesk-lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Jitbit Helpdesk kunne enkelt Log på programmet på webstedet Jitbit Helpdesk virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Jitbit Helpdesk
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scenarie")
##<a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>Aktivere programmet integrationen for Jitbit Helpdesk
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Jitbit Helpdesk.

###<a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>For at aktivere programmet integration for Jitbit Helpdesk skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Jitbit Helpdesk**.

    ![Galleri med programmet] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Galleri med programmet")

7.  Vælg **Jitbit Helpdesk**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![JitBit] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er at oprette en disposition for at aktivere brugere til at godkende Jitbit Helpdesk med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML protokollen. .  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT] For at kunne konfigurere single sign-on på din Jitbit Helpdesk-lejer, skal du kontakte først Jitbit Helpdesk teknisk support for at få denne funktion er aktiveret.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Jitbit Helpdesk** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge Jitbit Helpdesk** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Jitbit Helpdesk log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. Jitbit.com*", og klik derefter på **Næste**.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Konfigurere app URL-adresse")

4.  På siden **Konfigurer single sign-on – på Jitbit Helpdesk** for at downloade certifikatet, skal du klikke på **Hent certifikat**, og Gem derefter certifikatfilen lokalt som **c:\\Jitbit Helpdesk.cer**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Konfigurere Enkeltlogon")

5.  Log på din Jitbit Helpdesk virksomheds websted som en administrator i et andet browservindue.

6.  Klik på **Administration**på værktøjslinjen øverst.

    ![Administration] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

7.  Klik på **generelle indstillinger**.

    ![Brugere, firmaer og tilladelser] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Brugere, firmaer og tilladelser")

8.  I afsnittet **godkendelsesindstillinger** konfiguration skal du udføre følgende trin:

    ![Indstillinger for proxygodkendelse] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Indstillinger for proxygodkendelse")

    1.  Vælg **Aktivér SAML 2.0 enkelt Log på** logon ved hjælp af enkelt enkeltlogon (SSO) med **OneLogin**.
    2.  Kopiér værdien **Service Provider (SP) startet slutpunkt** i Azure klassisk-portalen på **Konfigurer single sign-on – på Jitbit Helpdesk** dialog siden, og Indsæt det i tekstfeltet **Slutpunkt URL-adresse** .
    3.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.
        
        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    4.  Åbne dit base-64-kodet certifikat, kopiere indholdet af den til Udklipsholder og sætte den ind på tekstfeltet **X.509-certifikat**
    5.  Klik på **Gem ændringer**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Jitbit Helpdesk, skal de klargøres til Jitbit Helpdesk.  
Hvis det er Jitbit Helpdesk er klargøring en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **Jitbit Helpdesk** -lejer.

2.  Klik på **Administration**i menuen øverst.

    ![Administration] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

3.  Klik på **brugere, firmaer og tilladelser**.

    ![Brugere, firmaer og tilladelser] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Brugere, firmaer og tilladelser")

4.  Klik på **Tilføj bruger**.

    ![Tilføj bruger] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Tilføj bruger")

5.  Skriv dataene på den Azure AD-konto, du vil klargøre i den følgende tekstfelter i afsnittet oprette: **brugernavn**, **mail**, **Fornavn**, **Efternavn**

    ![Oprette] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Oprette")

6.  Klik på **Opret**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Jitbit Helpdesk bruger konto værktøjer til oprettelse eller API'er, som Jitbit Helpdesk til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>Hvis du vil tildele brugere til Jitbit Helpdesk, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Jitbit Helpdesk **application integration.

    ![Tildele brugere] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Bambus HR | Microsoft Azure" 
    description="Lær, hvordan du bruger Bambus HR med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Selvstudium: Azure Active Directory-Integration med Bambus HR

Formålet med dette selvstudium er at vise integration af Azure og BambooHR.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En BambooHR single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til BambooHR kunne enkelt Log på programmet på webstedet BambooHR virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for BambooHR
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenarie")
##<a name="enabling-the-application-integration-for-bamboohr"></a>Aktivere programmet integrationen for BambooHR

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for BambooHR.

###<a name="to-enable-the-application-integration-for-bamboohr-perform-the-following-steps"></a>For at aktivere programmet integration for BambooHR skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **BambooHR**.

    ![Galleri med programmet] (./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Galleri med programmet")

7.  Vælg **BambooHR**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![BambooHR] (./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende BambooHR med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **BambooHR** application integration.

    ![Scenarie] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenarie")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge BambooHR** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse, der bruges af dine brugere til at logge din BambooHR-program i tekstfeltet **BambooHR Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: https://company.bamboohr.com), og klik derefter på **Næste**.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Konfigurere app URL-adresse")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på BambooHR** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Konfigurere Enkeltlogon")

5.  Log på webstedet BambooHR virksomhed som administrator i et andet browservindue.

6.  Udfør følgende trin på startsiden, på:

    ![Single Sign-On] (./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Single Sign-On")

    1.  Klik på **Apps**.
    2.  Klik på **Single Sign-On**i menuen apps i venstre side.
    3.  Klik på **SAML Single Sign-On**.

7.  I sektionen **SAML Single Sign-On** skal du udføre følgende trin:

    ![SAML Single Sign-On] (./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")

    1.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på BambooHR** dialogboksen siden, og Indsæt det i tekstfeltet **SSO Login URL-adresse** .
    2.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    3.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **X.509-certifikat**
    4.  Klik på **Gem**.

8.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på BambooHR, skal de klargøres til BambooHR.  
Klargøring af er BambooHR, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **BambooHR** som administrator.

2.  Klik på **Indstillinger**på værktøjslinjen øverst.

    ![Angive] (./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Angive")

3.  Klik på **Oversigt**.

4.  Gå til i den venstre navigationsrude **sikkerhed \> brugere**.

5.  Skriv bruger navn, din adgangskode og e-mail-adressen for en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.

6.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden BambooHR bruger konto værktøjer til oprettelse eller API'er, som BambooHR til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-bamboohr-perform-the-following-steps"></a>Hvis du vil tildele brugere til BambooHR, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **BambooHR **application integration.

    ![Tildele brugere] (./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

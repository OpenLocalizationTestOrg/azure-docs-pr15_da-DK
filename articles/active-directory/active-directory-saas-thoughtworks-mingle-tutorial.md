<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Thoughtworks Mingle | Microsoft Azure" 
    description="Lær, hvordan du bruger Thoughtworks Mingle med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Selvstudium: Azure Active Directory-integration med Thoughtworks Mingle
  
Formålet med dette selvstudium er at vise integration af Azure og Thoughtworks Mingle.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Thoughtworks Mingle lejer
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen Thoughtworks Mingle
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Scenarie")

##<a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>Aktivere programmet integrationen Thoughtworks Mingle
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen Thoughtworks Mingle.

###<a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>For at aktivere programmet integrationen Thoughtworks Mingle skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **thoughtworks mingle**.

    ![Galleri med programmet] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Galleri med programmet")

7.  Vælg **Thoughtworks Mingle**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Thoughtworks Mingle] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere Sådan brugerne mulighed for at godkende til Thoughtworks Mingle med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Som en del af denne procedure, er det nødvendigt at overføre et certifikat til Thoughtworks Mingle.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Til Azure klassisk portalen, på siden **Thoughtworks Mingle **application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Thoughtworks Mingle** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*http://company.mingle.thoughtworks.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Thoughtworks Mingle lejer URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Konfigurere App URL-adresse")

4.  Klik på Hent metadata på siden **Konfigurer single sign-on – på Thoughtworks Mingle** , og derefter gemme den på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Konfigurere Enkeltlogon")

5.  Log på webstedet **Thoughtworks Mingle** virksomhed som administrator.

6.  Klik på fanen **administrator** , og klik derefter på **SSO Config**.

    ![SSO Config] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO Config")

7.  I sektionen **SSO Config** skal du udføre følgende trin:

    ![SSO Config] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO Config")

    1.  For at overføre metadatafilen, skal du klikke på **Vælg fil**.
    2.  Klik på **Gem ændringer**.

8.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Konfigurere Enkeltlogon")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For AAD brugere skal kunne logge på, skal de klargøres til programmet Thoughtworks Mingle ved hjælp af deres Azure Active Directory-brugernavne.  
Hvis det er Thoughtworks Mingle er klargøring en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet Thoughtworks Mingle virksomhed som administrator.

2.  Klik på **profil**.

    ![Dit første projekt] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Dit første projekt")

3.  Klik på fanen **administrator** , og klik derefter på **brugere**.

    ![Brugere] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Brugere")

4.  Klik på **ny bruger**.

    ![Ny bruger] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "Ny bruger")

5.  På siden **Ny bruger** dialogboksen skal du udføre følgende trin:

    ![Ny bruger] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "Ny bruger")

    1.  Skriv **logonnavn**, **vist navn**, **Vælg adgangskode**, **Bekræft adgangskode** til en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Vælg **fuld bruger**, som **brugertype**.
    3.  Klik på **Opret denne profil**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Thoughtworks Mingle bruger konto værktøjer til oprettelse eller API'er, som Thoughtworks Mingle til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>Hvis du vil tildele brugere til Thoughtworks Mingle, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Thoughtworks Mingle** application integration.

    ![Tildele brugere] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

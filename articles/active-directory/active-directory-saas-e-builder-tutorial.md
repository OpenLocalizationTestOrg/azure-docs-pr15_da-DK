<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med e-Builder | Microsoft Azure" 
    description="Lær at bruge e-Builder med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-e-builder"></a>Selvstudium: Azure Active Directory-integration med e-generator
  
Formålet med dette selvstudium er at vise integration af Azure og e-Builder.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En e-Builder-lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til e-Builder kunne enkelt Log på programmet på din e-Builder virksomheds websted (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integration til e-generator
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-e-builder-tutorial/IC777378.png "Scenarie")
##<a name="enabling-the-application-integration-for-e-builder"></a>Aktivere programmet integration til e-generator
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for e-Builder.

###<a name="to-enable-the-application-integration-for-e-builder-perform-the-following-steps"></a>For at aktivere programmet integration for e-Builder skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-e-builder-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-e-builder-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-e-builder-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **e-Builder**.

    ![Galleri med programmet] (./media/active-directory-saas-e-builder-tutorial/IC777379.png "Galleri med programmet")

7.  Vælg **e-Builder**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![E-generator] (./media/active-directory-saas-e-builder-tutorial/IC777380.png "E-generator")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende e-Opret med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **e-Builder** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-e-builder-tutorial/IC777381.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge e-Builder** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-e-builder-tutorial/IC777382.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **e-Builder logge på URL-adressen** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>.e Builder.com*", og klik derefter på **Næste**.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-e-builder-tutorial/IC777383.png "Konfigurere app URL-adresse")

4.  Side for at hente metadata for din, klik på **Hent metadata**på **Konfigurer single sign-on – på e-Builder** , og derefter dataene filer lokalt som **c:\\e-BuilderMetaData.xml**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-e-builder-tutorial/IC777384.png "Konfigurere Enkeltlogon")

5.  Videresend metadatafilen til e-Builder dit supportteam. Understøttelse af team behov konfigurerer Enkeltlogon for dig.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-e-builder-tutorial/IC777385.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til e-Builder.  
Når en tildelt brugeren forsøger at logge på e-Builder ved hjælp af panelet access, kontrollerer e-Builder om brugeren findes.  
Hvis der findes ingen brugerkonto endnu, bliver den automatisk oprettet af e-Builder.
##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-e-builder-perform-the-following-steps"></a>Hvis du vil tildele brugere til e-Builder, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **e-Builder **application integration.

    ![Tildele brugere] (./media/active-directory-saas-e-builder-tutorial/IC777386.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-e-builder-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

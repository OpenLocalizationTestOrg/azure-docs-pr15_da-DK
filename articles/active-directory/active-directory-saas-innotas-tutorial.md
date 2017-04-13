<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Innotas | Microsoft Azure"
    description="Lær, hvordan du bruger Innotas med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-innotas"></a>Selvstudium: Azure Active Directory-integration med Innotas
  
Formålet med dette selvstudium er at vise integration af Azure og Innotas.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Innotas lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Innotas kunne enkelt Log på programmet på webstedet Innotas virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Innotas
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-innotas-tutorial/IC777331.png "Scenarie")
##<a name="enabling-the-application-integration-for-innotas"></a>Aktivere programmet integrationen for Innotas
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Innotas.

###<a name="to-enable-the-application-integration-for-innotas-perform-the-following-steps"></a>For at aktivere programmet integration for Innotas skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-innotas-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-innotas-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-innotas-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Innotas**.

    ![Galleri med programmet] (./media/active-directory-saas-innotas-tutorial/IC777332.png "Galleri med programmet")

7.  Vælg **Innotas**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Innotas] (./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Innotas med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Innotas** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-innotas-tutorial/IC777334.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Innotas** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-innotas-tutorial/IC777335.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Innotas log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. Innotas.com*", og klik derefter på **Næste**.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-innotas-tutorial/IC777336.png "Konfigurere app URL-adresse")

4.  Hente din metadata, skal du klikke på **Hent metadata**på **Konfigurer single sign-on – på Innotas** , og derefter dataene filer lokalt som **c:\\InnotasMetaData.xml**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-innotas-tutorial/IC777337.png "Konfigurere Enkeltlogon")

5.  Videresend metadatafilen til Innotas dit supportteam. Understøttelse af team behov konfigurerer Enkeltlogon for dig.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-innotas-tutorial/IC777338.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til Innotas.  
Når en tildelt brugeren forsøger at logge på ved hjælp af panelet access Innotas, kontrollerer Innotas om brugeren findes.  
Hvis der findes ingen brugerkonto endnu, oprettes den automatisk ved Innotas.
##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-innotas-perform-the-following-steps"></a>Hvis du vil tildele brugere til Innotas, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Innotas **application integration.

    ![Tildele brugere] (./media/active-directory-saas-innotas-tutorial/IC777339.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-innotas-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
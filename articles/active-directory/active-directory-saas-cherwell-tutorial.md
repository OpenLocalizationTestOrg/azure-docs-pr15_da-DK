<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Cherwell | Microsoft Azure" 
    description="Lær, hvordan du bruger Cherwell med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="10/14/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Selvstudium: Azure Active Directory-integration med Cherwell

Formålet med dette selvstudium er at vise integration af Azure og Cherwell. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Cherwell single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Cherwell kunne enkelt Log på programmet på webstedet Cherwell virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Cherwell
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-cherwell-tutorial/IC798988.png "Scenarie")
##<a name="enabling-the-application-integration-for-cherwell"></a>Aktivere programmet integrationen for Cherwell

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Cherwell.

###<a name="to-enable-the-application-integration-for-cherwell-perform-the-following-steps"></a>For at aktivere programmet integration for Cherwell skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-cherwell-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-cherwell-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-cherwell-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Cherwell**.

    ![Cherwell] (./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  Vælg **Cherwell**i resultatruden, og klik derefter på **udført** for at tilføje programmet.
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Cherwell med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Cherwell** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798990.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Cherwell** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798991.png "Konfigurere Single Sign-On")

3.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-cherwell-tutorial/IC798992.png "Konfigurere App URL-adresse")

    en.  Skriv URL-adressen bruges af dine brugere til at logge på din **Cherwell** i tekstfeltet **Log på URL-adresse** (f.eks.: *https://\<firmanavn\>.cherwellondemand.com/cherwellclient*).

    b.  Klik på **Næste**

4.  På siden **Konfigurer single sign-on – på Cherwell** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798993.png "Konfigurere Single Sign-On")

    en.  Klik på **Hent certifikat**, og Gem derefter certifikatet lokalt på din computer.

    b.  Kopiere **identitet udbyder URL-adresse**.

    c.  Kopiere **URL-adressen til Single Sign-On-tjenesten**.

    d.  Klik på **Næste**.

5.  Sende hentede certifikatet, **Identitet udbyder URL-adresse** og **Enkelt Sign-On URL-adressen** til dit supportteam Cherwell.

    >[AZURE.NOTE] Supportteamet Cherwell har at gøre den faktiske SSO-konfiguration.
Du får en meddelelse, når SSO er blevet aktiveret for dit abonnement.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798994.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Cherwell, skal de klargøres til Cherwell.  
I forbindelse med Cherwell skal brugerkontiene oprettes ved at supportteamet Cherwell.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Cherwell bruger konto værktøjer til oprettelse eller API'er, der leveres af Cherwell at blive klargjort Azure Active Directory-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-cherwell-perform-the-following-steps"></a>Hvis du vil tildele brugere til Cherwell, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Cherwell** application integration.

    ![Tildele brugere] (./media/active-directory-saas-cherwell-tutorial/IC798995.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-cherwell-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

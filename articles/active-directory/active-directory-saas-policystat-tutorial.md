<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med PolicyStat | Microsoft Azure" 
    description="Lær, hvordan du bruger PolicyStat med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-policystat"></a>Selvstudium: Azure Active Directory-Integration med PolicyStat
  
Formålet med dette selvstudium er at vise integration af Azure og PolicyStat.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En PolicyStat lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til PolicyStat kunne enkelt Log på programmet på webstedet PolicyStat virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for PolicyStat
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-policystat-tutorial/IC808662.png "Scenarie")
##<a name="enabling-the-application-integration-for-policystat"></a>Aktivere programmet integrationen for PolicyStat
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for PolicyStat.

###<a name="to-enable-the-application-integration-for-policystat-perform-the-following-steps"></a>For at aktivere programmet integration for PolicyStat skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-policystat-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-policystat-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-policystat-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **PolicyStat**.

    ![Galleri med programmet] (./media/active-directory-saas-policystat-tutorial/IC808627.png "Galleri med programmet")

7.  Vælg **PolicyStat**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![PolicyStat] (./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende PolicyStat med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Programmets PolicyStat forventer SAML påstande i et bestemt format, som kræver, at du kan tilføje brugerdefineret attributtilknytninger til konfigurationen af **saml token attributter** .  
Følgende skærmbillede viser et eksempel af dette.

![Attributter] (./media/active-directory-saas-policystat-tutorial/IC808628.png "Attributter")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **PolicyStat** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808629.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge PolicyStat** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808630.png "Konfigurere Single Sign-On")

3.  Skriv URL-adressen bruges af dine brugere at sign-on til URL-adressen PolicyStat programmet i tekstfeltet **Log på URL-adressen** på siden **Konfigurer App-indstillinger** (f.eks.: *"https://demo-azure.policystat.com"*), og klik derefter på **Næste**.

    ![Konfigurere indstillinger for] (./media/active-directory-saas-policystat-tutorial/IC808631.png "Konfigurere indstillinger for")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på PolicyStat** , og Gem derefter Metadatafilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808632.png "Konfigurere Single Sign-On")

5.  Log på webstedet PolicyStat virksomhed som administrator i et andet browservindue.

6.  Klik på fanen **administrator** , og klik derefter på **Enkelt Sign-On konfiguration** i venstre navigationsrude.

    ![Menuen Administrator] (./media/active-directory-saas-policystat-tutorial/IC808633.png "Menuen Administrator")

7.  Vælg **Aktivér enkelt Sign-on-Integration**i sektionen **konfiguration** .

    ![Enkelt Sign-On-konfiguration] (./media/active-directory-saas-policystat-tutorial/IC808634.png "Enkelt Sign-On-konfiguration")

8.  Klik på **Konfigurer attributter**, og derefter i sektionen **Konfigurer attributter** skal du udføre følgende trin:

    ![Enkelt Sign-On-konfiguration] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Enkelt Sign-On-konfiguration")

    1.  Skriv **uid**i tekstfeltet **Brugernavn attribut** .
    2.  Skriv **Fornavn**i tekstfeltet **Fornavn attribut** .
    3.  Skriv **Efternavn**i tekstfeltet **Sidste Name-attribut** .
    4.  Skriv **mailadresse**i tekstfeltet **E-mail-attributten** .
    5.  Klik på **Gem ændringer**.

9.  Klik på **Dine IDP Metadata**, og derefter i sektionen **Dine IDP Metadata** skal du udføre følgende trin:

    ![Enkelt Sign-On-konfiguration] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Enkelt Sign-On-konfiguration")

    1.  Åbn metadatafilen hentede, kopiere indholdet, og Indsæt det i tekstfeltet **Din identitet udbyder Metadata**
    2.  Klik på **Gem ændringer**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC771723.png "Konfigurere Single Sign-On")

11. 12. Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i menuen øverst.

    ![Attributter] (./media/active-directory-saas-policystat-tutorial/IC795920.png "Attributter")

13. For at tilføje påkrævet attributtilknytningerne skal du udføre følgende trin:

    ![Attributter] (./media/active-directory-saas-policystat-tutorial/IC804823.png "Attributter")

    1.  Klik på **Tilføj bruger attributten**.
    2.  Skriv **uid**i tekstfeltet **Attributnavn** .
    3.  I tekstfeltet **Attributværdien** skal du vælge **ExtractMailPrefix()**.
    4.  Vælg **User.mail**fra listen **i E-mails** .
    5.  Klik på **udført**.
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på PolicyStat, skal de klargøres til PolicyStat.  
PolicyStat understøtter kun på tid brugerklargøring. Det betyder, du behøver ikke at føje brugerne manuelt til PolicyStat.  
Brugerne kan få tilføjet automatisk på deres første logon hjælp af single sign på.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden PolicyStat bruger konto værktøjer til oprettelse eller API'er, som PolicyStat til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-policystat-perform-the-following-steps"></a>Hvis du vil tildele brugere til PolicyStat, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **PolicyStat **application integration.

    ![Tildele brugere] (./media/active-directory-saas-policystat-tutorial/IC808636.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-policystat-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
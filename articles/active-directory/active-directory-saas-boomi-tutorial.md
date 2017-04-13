<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Boomi | Microsoft Azure" 
    description="Lær, hvordan du bruger Boomi med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-boomi"></a>Selvstudium: Azure Active Directory-integration med Boomi

Formålet med dette selvstudium er at vise integration af Azure og Boomi.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Boomi single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Boomi kunne enkelt Log på programmet på webstedet Boomi virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Boomi
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-boomi-tutorial/IC791134.png "Scenarie")
##<a name="enabling-the-application-integration-for-boomi"></a>Aktivere programmet integrationen for Boomi

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Boomi.

###<a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>For at aktivere programmet integration for Boomi skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-boomi-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-boomi-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-boomi-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Boomi**.

    ![Galleri med programmet] (./media/active-directory-saas-boomi-tutorial/IC790822.png "Galleri med programmet")

7.  Vælg **Boomi**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Boomi] (./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Boomi med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Boomi** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC790824.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Boomi** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC790825.png "Konfigurere Single Sign-On")

3.  Skriv **Boomi AtomSphere Login URL-adressen** i tekstfeltet **Boomi svar URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://platform.boomi.com/sso/AccountName/saml*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-boomi-tutorial/IC790826.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Boomi** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC790827.png "Konfigurere Single Sign-On")

5.  Log på webstedet Boomi virksomhed som administrator i et andet browservindue.

6.  Klik på virksomhedens navn og derefter **konfiguration**på værktøjslinjen øverst.

    ![Konfiguration] (./media/active-directory-saas-boomi-tutorial/IC790828.png "Konfiguration")

7.  Klik på **SSO-indstillinger**.

    ![SSO-indstillinger] (./media/active-directory-saas-boomi-tutorial/IC790829.png "SSO-indstillinger")

8.  I sektionen **Enkelt Sign-On indstillinger** skal du udføre følgende trin:

    ![Enkelt Sign-On-indstillinger] (./media/active-directory-saas-boomi-tutorial/IC790830.png "Enkelt Sign-On-indstillinger")

    1.  Vælg **Aktiver SAML Single Sign-On**.
    2.  Klik på **Importér**for at overføre det certifikat, der er hentet.
    3.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Boomi** dialogboksen siden, og Indsæt det i tekstfeltet **Identitet udbyder Login URL-adresse** .
    4.  Vælg **sammenslutning-id'et er i NameID element i emnet**som **Sammenslutning Id placering**.
    5.  Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC775560.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Boomi, skal de klargøres til Boomi.  
Klargøring af er Boomi, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet **Boomi** virksomhed som administrator.

2.  Gå til **Brugeradministration \> brugere**.

    ![Brugere] (./media/active-directory-saas-boomi-tutorial/IC790831.png "Brugere")

3.  Klik på **Tilføj bruger**.

    ![Tilføj bruger] (./media/active-directory-saas-boomi-tutorial/IC790832.png "Tilføj bruger")

4.  På siden **Tilføj brugerroller** dialogboksen skal du udføre følgende trin:

    ![Tilføj bruger] (./media/active-directory-saas-boomi-tutorial/IC790833.png "Tilføj bruger")

    1.  Skriv **Fornavn**, **Efternavn** og **mail** med en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på OK.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Boomi bruger konto værktøjer til oprettelse eller API'er, som Boomi til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Hvis du vil tildele brugere til Boomi, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Boomi **application integration.

    ![Tildele brugere] (./media/active-directory-saas-boomi-tutorial/IC790834.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-boomi-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

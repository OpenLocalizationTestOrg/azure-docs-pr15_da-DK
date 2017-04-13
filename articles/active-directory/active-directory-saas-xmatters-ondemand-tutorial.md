<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med xMatters anmodet arbejdsproces | Microsoft Azure"
    description="Lær, hvordan du bruger xMatters anmodet arbejdsproces med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Selvstudium: Azure Active Directory-Integration med xMatters anmodet arbejdsproces
  
Formålet med dette selvstudium er at vise integration af Azure og xMatters anmodet arbejdsproces. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En xMatters anmodet arbejdsproces lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til xMatters anmodet arbejdsproces kunne enkelt Log på programmet på webstedet xMatters anmodet arbejdsproces virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for xMatters anmodet arbejdsproces
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scenarie")

##<a name="enabling-the-application-integration-for-xmatters-ondemand"></a>Aktivere programmet integrationen for xMatters anmodet arbejdsproces
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for xMatters anmodet arbejdsproces.

###<a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>For at aktivere programmet integration for xMatters anmodet arbejdsproces skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **xMatters anmodet arbejdsproces**.

    ![Galleri med programmet] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Galleri med programmet")

7.  Vælg **XMatters anmodet arbejdsproces**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![xMatters anmodet arbejdsproces] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters anmodet arbejdsproces")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende XMatters OnDemand med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **XMatters anmodet arbejdsproces** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge XMatters anmodet arbejdsproces** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Konfigurere Enkeltlogon")

3.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Konfigurere app URL-adresse")

    en. I tekstfeltet **XMatters anmodet arbejdsproces log i URL-adresse** skal du skrive din URL-adresse ved hjælp af følgende mønster:`https://<tenant-name>.XMattersOnDemandapp.com`

    b. Klik på **Næste**.


4.  På siden **Konfigurer single sign-on – på XMatters anmodet arbejdsproces** for at downloade certifikatet, skal du klikke på **Hent certifikat**, og Gem derefter certifikatfilen lokalt som **c:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT] Du vil videresende certifikat til xMatters-supportteam. Certifikatet, der skal overføres af xMatters supportteam, før du kan færdiggøre enkelt enkeltlogon-konfigurationen.

    ![Konfigurer enkelt logge] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Konfigurer enkelt logge")

5.  Log på webstedet XMatters OnDemand virksomhed som administrator i et andet browservindue.

6.  Klik på **administrator**i værktøjslinjen øverst, og klik **Firmaoplysninger** i navigationslinjen til venstre.

    ![Administrator] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Administrator")

7.  På siden **SAML konfiguration** skal du udføre følgende trin:

    ![SAML konfiguration] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML konfiguration")

    1.  Vælg **Aktiver SAML**.
    2.  Kopiér værdien **Identitet Provider-ID** i Azure klassisk-portalen på dialogboksside **konfigurere Enkeltlogon i XMatters anmodet arbejdsproces** , og Indsæt det i tekstfeltet **Identitet Provider-ID** .
    3.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på dialogboksside **konfigurere Enkeltlogon i XMatters anmodet arbejdsproces** , og Indsæt det i tekstfeltet **Enkelt Log på URL-adresse** .
    4.  Kopiér værdien **Enkelt Sign-Out URL-adressen** på Azure-klassisk portalen på dialogboksside **konfigurere Enkeltlogon i XMatters anmodet arbejdsproces** , og Indsæt det i tekstfeltet **Enkelt log URL-adresse** .
    5.  Klik på **Save Changes**på siden oplysninger om en virksomhed er placeret øverst.
        ![Firmaoplysninger] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Firmaoplysninger")

8.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurer enkelt logge] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Konfigurer enkelt logge")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på XMatters OnDemand, skal de klargøres til XMatters OnDemand.  
Hvis det er XMatters OnDemand er klargøring en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **XMatters anmodet arbejdsproces** lejer.

2.  Klik på fanen **brugere** .

3.  Klik på **Tilføj bruger**.

    ![Brugere] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Brugere")

4.  Vælg **aktive**.

5.  I sektionen **Tilføj en bruger** skal du udføre følgende trin:

    ![Tilføje en bruger] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Tilføje en bruger")

    1.  Angiv **bruger-id**, **Fornavn**, **Efternavn**, **websted** for en gyldig AAD-konto, du vil klargøre.
    2.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden XMatters OnDemand bruger konto værktøjer til oprettelse eller API'er, som XMatters OnDemand til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>Hvis du vil tildele brugere til XMatters OnDemand, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **XMatters anmodet arbejdsproces **application integration.

    ![Tildele brugere] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
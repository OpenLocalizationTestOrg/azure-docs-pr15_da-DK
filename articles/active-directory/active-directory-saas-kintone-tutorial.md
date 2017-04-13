<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Kintone | Microsoft Azure" 
    description="Lær, hvordan du bruger Kintone med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-kintone"></a>Selvstudium: Azure Active Directory-Integration med Kintone
  
Formålet med dette selvstudium er at vise integration af Azure og Kintone.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Kintone single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Kintone kunne enkelt Log på programmet på webstedet Kintone virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Kintone
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-kintone-tutorial/IC785859.png "Scenarie")
##<a name="enabling-the-application-integration-for-kintone"></a>Aktivere programmet integrationen for Kintone
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Kintone.

###<a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>For at aktivere programmet integration for Kintone skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-kintone-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-kintone-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-kintone-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Kintone**.

    ![Galleri med programmet] (./media/active-directory-saas-kintone-tutorial/IC785867.png "Galleri med programmet")

7.  Vælg **Kintone**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Kintone] (./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Kintone med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Kintone** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785872.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Kintone** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785873.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://company.kintone.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Kintone Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-kintone-tutorial/IC785875.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Kintone** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785878.png "Konfigurere Single Sign-On")

5.  Log på webstedet **Kintone** virksomhed som administrator i et andet browservindue.

6.  Klik på **Indstillinger**.

    ![Indstillinger] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Indstillinger")

7.  Klik på **brugere og systemadministration af**.

    ![Brugere og systemadministration af] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Brugere og systemadministration af")

8.  Under **System Administration \> sikkerhed** Klik på **Login**.

    ![Logon] (./media/active-directory-saas-kintone-tutorial/IC785881.png "Logon")

9.  Klik på **Aktivér SAML godkendelse**.

    ![SAML godkendelse] (./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML godkendelse")

10. I sektionen SAML godkendelse skal du udføre følgende trin:

    ![SAML godkendelse] (./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML godkendelse")

    1.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Kintone** dialogboksen siden, og Indsæt det i tekstfeltet **URL-adressen til logon** .
    2.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Kintone** dialogboksen siden, og Indsæt det i tekstfeltet **Log URL-adresse** .
    3.  Klik på **Gennemse** for at overføre dit hentede certifikat.
    4.  Klik på **Gem**.

11. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785884.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Kintone, skal de klargøres til Kintone.  
Klargøring af er Kintone, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Kintone** virksomhed som administrator.

2.  Klik på **indstillingen**.

    ![Indstillinger] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Indstillinger")

3.  Klik på **brugere og systemadministration af**.

    ![Bruger og systemadministration] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Bruger og systemadministration")

4.  Klik på **afdelinger og brugere**under **Administration af brugeren**.

    ![Afdeling og brugere] (./media/active-directory-saas-kintone-tutorial/IC785888.png "Afdeling og brugere")

5.  Klik på **ny bruger**.

    ![Nye brugere] (./media/active-directory-saas-kintone-tutorial/IC785889.png "Nye brugere")

6.  I sektionen **Nye bruger** skal du udføre følgende trin:

    ![Nye brugere] (./media/active-directory-saas-kintone-tutorial/IC785890.png "Nye brugere")

    1.  Angiv et **Visningsnavn**, **Login-navn**, **Ny adgangskode**, **Bekræft adgangskode**, **E-mail-adresse** og andre oplysninger om en gyldig AAD-konto, du vil klargøre i den relaterede texboxes.
    2.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Kintone bruger konto værktøjer til oprettelse eller API'er, som Kintone til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Hvis du vil tildele brugere til Kintone, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Kintone **application integration.

    ![Tildele brugere] (./media/active-directory-saas-kintone-tutorial/IC785891.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-kintone-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
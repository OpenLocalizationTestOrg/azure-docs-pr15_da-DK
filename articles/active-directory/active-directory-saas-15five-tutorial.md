<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med 15Five | Microsoft Azure" 
    description="Lær, hvordan du bruger 15Five med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-15five"></a>Selvstudium: Azure Active Directory-integration med 15Five

Formålet med dette selvstudium er at vise integration af Azure og 15Five. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En 15Five single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til 15Five kunne enkelt Log på programmet på webstedet 15Five virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for 15Five
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-15five-tutorial/IC784667.png "Scenarie")
##<a name="enabling-the-application-integration-for-15five"></a>Aktivere programmet integrationen for 15Five

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for 15Five.

###<a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>For at aktivere programmet integration for 15Five skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-15five-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-15five-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-15five-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **15Five**.

    ![Galleri med programmet] (./media/active-directory-saas-15five-tutorial/IC784668.png "Galleri med programmet")

7.  Vælg **15Five**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![15Five] (./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er at oprette en disposition for at aktivere brugere til at godkende 15Five med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **15Five** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-15five-tutorial/IC784670.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge 15Five** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-15five-tutorial/IC784671.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://company.15Five.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **15Five log i URL-adresse** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-15five-tutorial/IC784672.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på 15Five** , og derefter videresende metadatafilen til 15Five-supportteam.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-15five-tutorial/IC784673.png "Konfigurere Enkeltlogon")

    >[AZURE.NOTE] Skal være aktiveret af supportteam 15Five enkeltlogon.

5.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-15five-tutorial/IC784674.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på 15Five, skal de klargøres til 15Five.  
Klargøring af er 15Five, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet **15Five** virksomhed som administrator.

2.  Gå til at **administrere virksomhedens**.

    ![Administrere virksomhed] (./media/active-directory-saas-15five-tutorial/IC784675.png "Administrere virksomhed")

3.  Gå til **personer \> tilføje personer**.

    ![Personer] (./media/active-directory-saas-15five-tutorial/IC784676.png "Personer")

4.  I sektionen Tilføj ny Person skal du udføre følgende trin:

    ![Tilføj ny Person] (./media/active-directory-saas-15five-tutorial/IC784677.png "Tilføj ny Person")

    1.  Skriv **Fornavn**, **Efternavn**, **Titel**, **mailadresse** for en gyldig Azure Active Directory-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **udført**.

    >[AZURE.NOTE] Azure AD-kontoindehaver modtager mails, der indeholder et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden 15Five bruger konto værktøjer til oprettelse eller API'er leveres af 15Five til klargøring AAD-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-15five-perform-the-following-steps"></a>Hvis du vil tildele brugere til 15Five, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **15Five **application integration.

    ![Tildele brugere] (./media/active-directory-saas-15five-tutorial/IC784678.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-15five-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

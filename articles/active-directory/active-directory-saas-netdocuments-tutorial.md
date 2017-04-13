<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med NetDocuments | Microsoft Azure" 
    description="Lær, hvordan du bruger NetDocuments med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Selvstudium: Azure Active Directory-integration med NetDocuments
  
Formålet med dette selvstudium er at vise integration af Azure og NetDocuments.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En NetDocuments lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til NetDocuments kunne enkelt Log på programmet på webstedet NetDocuments virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for NetDocuments
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Scenarie")
##<a name="enabling-the-application-integration-for-netdocuments"></a>Aktivere programmet integrationen for NetDocuments
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for NetDocuments.

###<a name="to-enable-the-application-integration-for-netdocuments-perform-the-following-steps"></a>For at aktivere programmet integration for NetDocuments skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **NetDocuments**.

    ![Galleri med programmet] (./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Galleri med programmet")

7.  Vælg **NetDocuments**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![NetDocuments] (./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende NetDocuments med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til NetDocuments, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **NetDocuments** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge NetDocuments** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Konfigurere Single Sign-On")

3.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Konfigurere App URL-adresse")

    1.  Skriv din URL-adresse, der bruges af dine brugere til at logge din NetDocuments-program i tekstfeltet **Log på URL-adresse** (f.eks.: "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*").
    2.  I tekstfeltet **NetDocuments svar URL-adresse** skal du skrive den samme værdi, du har skrevet i den tekstfeltet **Log på URL-adresse** .  

        >[AZURE.NOTE]Du kan finde den korrekte værdi i slutningen af dialogboksen **Del af et organisationsnetværk identitet** (se skærmbilledet til trin 9).

    3.  Klik på **Næste**

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på NetDocuments** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Konfigurere Single Sign-On")

5.  Log på webstedet NetDocuments virksomhed som administrator i et andet browservindue.

6.  Gå til **administrator**.

7.  Klik på **Tilføj og fjern brugere og grupper**.

    ![Lager] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Lager")

8.  Klik på **Konfigurer avancerede indstillinger for godkendelse**.

    ![Konfigurere avancerede indstillinger for godkendelse] (./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Konfigurere avancerede indstillinger for godkendelse")

9.  I **Den del af et organisationsnetværk identitet** dialogboksen skal du udføre følgende trin:

    ![Samlet Identitty] (./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Samlet Identitty")

    1.  Vælg **Active Directory Federation Services**som **Federated identitet servertype**.
    2.  Klik på **Vælg fil**, hvis du vil overføre metadatafilen hentede.
    3.  Klik på **OK**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på NetDocuments, skal de klargøres til NetDocuments.  
Klargøring af er NetDocuments, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Syng på webstedet **NetDocuments** virksomhed som administrator.

2.  Klik på **administrator**i menuen øverst.

    ![Administrator] (./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Administrator")

3.  Klik på **Tilføj og fjern brugere og grupper**.

    ![Lager] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Lager")

4.  I tekstfeltet **Mailadresse** skal du skrive en gyldig Azure Active Directory-konto, du vil klargøre, og klik derefter på **Tilføj bruger**mailadresse.

    ![Mailadresse] (./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Mailadresse")

    >[AZURE.NOTE]Azure Active Directory-kontoindehaver får en mail, der indeholder et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden NetDocuments bruger konto værktøjer til oprettelse eller API'er, som NetDocuments til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-netdocuments-perform-the-following-steps"></a>Hvis du vil tildele brugere til NetDocuments, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **NetDocuments **application integration.

    ![Tildele brugere] (./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Panorama9 | Microsoft Azure" 
    description="Lær, hvordan du bruger Panorama9 med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Selvstudium: Azure Active Directory-integration med Panorama9
  
Formålet med dette selvstudium er at vise integration af Azure og Panorama9.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Panorama9 single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Panorama9 kunne enkelt Log på programmet på webstedet Panorama9 virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Panorama9
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-panorama9-tutorial/IC790016.png "Scenarie")
##<a name="enabling-the-application-integration-for-panorama9"></a>Aktivere programmet integrationen for Panorama9
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Panorama9.

###<a name="to-enable-the-application-integration-for-panorama9-perform-the-following-steps"></a>For at aktivere programmet integration for Panorama9 skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-panorama9-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-panorama9-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-panorama9-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Panorama9**.

    ![Galleri med programmet] (./media/active-directory-saas-panorama9-tutorial/IC790017.png "Galleri med programmet")

7.  Vælg **Panorama9**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Panorama9] (./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Panorama9 med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Panorama9, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Panorama9** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790019.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Panorama9** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790020.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse, der bruges af dine brugere til at logge på Panorama9 i tekstfeltet **Panorama9 Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://dashboard.panorama9.com/saml/access/3262*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-panorama9-tutorial/IC790021.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Panorama9** , og derefter gemme den lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790022.png "Konfigurere Single Sign-On")

5.  Log på webstedet Panorama9 virksomhed som administrator i et andet browservindue.

6.  Klik på **Administrer**, og klik **filtypenavne**på værktøjslinjen øverst.

    ![Filtypenavne] (./media/active-directory-saas-panorama9-tutorial/IC790023.png "Filtypenavne")

7.  Klik på dialogboksen **filtypenavne** **Single Sign-On**.

    ![Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790024.png "Single Sign-On")

8.  I sektionen **Indstillinger** skal du udføre følgende trin:

    ![Indstillinger] (./media/active-directory-saas-panorama9-tutorial/IC790025.png "Indstillinger")

    1.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Panorama9** dialogboksen siden, og Indsæt det i tekstfeltet **identitet udbyder URL-adresse** .
    2.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **certifikat fingeraftryk** .  

        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    3.  Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på portalen Azure AD klassisk, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790026.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Panorama9, skal de klargøres til Panorama9.  
Klargøring af er Panorama9, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet **Panorama9** virksomhed som administrator.

2.  Klik på **Administrer**i menuen øverst, og klik derefter på **brugere**.

    ![Brugere] (./media/active-directory-saas-panorama9-tutorial/IC790027.png "Brugere")

3.  Klik på **+**.

4.  I sektionen bruger data skal du udføre følgende trin:

    ![Brugere] (./media/active-directory-saas-panorama9-tutorial/IC790028.png "Brugere")

    1.  Skriv en gyldig Azure Active Directory-bruger, du vil klargøre mailadresse i tekstfeltet **mail** .
    2.  Klik på **Gem**.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden Panorama9 bruger konto værktøjer til oprettelse eller API'er, som Panorama9 til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-panorama9-perform-the-following-steps"></a>Hvis du vil tildele brugere til Panorama9, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Panorama9** application integration.

    ![Tildele brugere] (./media/active-directory-saas-panorama9-tutorial/IC790029.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-panorama9-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
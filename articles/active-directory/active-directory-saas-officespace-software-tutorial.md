<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med OfficeSpace Software | Microsoft Azure" 
    description="Lær, hvordan du bruger OfficeSpace Software med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Selvstudium: Azure Active Directory-integration med OfficeSpace Software
  
Formålet med dette selvstudium er at vise integration af Azure og OfficeSpace Software.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En OfficeSpace Software single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, skal de Azure AD-brugere, du har tildelt til OfficeSpace Software kunne enkelt Log på programmet på webstedet OfficeSpace Software virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integration til OfficeSpace Software
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Scenarie")
##<a name="enabling-the-application-integration-for-officespace-software"></a>Aktivere programmet integration til OfficeSpace Software
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integration til OfficeSpace Software.

###<a name="to-enable-the-application-integration-for-officespace-software-perform-the-following-steps"></a>For at aktivere programmet integration for OfficeSpace Software skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **OfficeSpace Software**.

    ![Galleri med programmet] (./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Galleri med programmet")

7.  Vælg **OfficeSpace Software**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![OfficeSpace Software] (./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende OfficeSpace Software med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til OfficeSpace Software, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **OfficeSpace Software** application integration.

    ![Konfigurere enkelt Log = på] (./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Konfigurere enkelt Log = på")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge OfficeSpace Software** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Konfigurere Enkeltlogon")

3.  Skriv URL-adressen bruges af dine brugere til at logge programmet i OfficeSpace i tekstfeltet **OfficeSpace Software Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://company.officespacesoftware.com*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på OfficeSpace Software** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Konfigurere Enkeltlogon")

5.  Log på din OfficeSpace Software virksomheds websted som en administrator i et andet browservindue.

6.  Gå til **administrator \> forbindelser**.

    ![Administrator] (./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Administrator")

7.  Klik på **SAML godkendelse**.

    ![Forbindelser] (./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Forbindelser")

8.  I sektionen **SAML godkendelse** skal du udføre følgende trin:

    ![SAML konfiguration] (./media/active-directory-saas-officespace-software-tutorial/IC777771.png "SAML konfiguration")

    1.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på OfficeSpace Software** dialog siden, og Indsæt det i tekstfeltet **Log udbyder URL-adresse** .
    2.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på OfficeSpace Software** dialog siden, og Indsæt det i tekstfeltet **klient idp URL-adresse** .
    3.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **klient idp cert fingeraftryk** .  

        >[AZURE.TIP]
        Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    4.  Klik på **Gem indstillinger**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på OfficeSpace Software, skal de klargøres til OfficeSpace Software. Klargøring af er OfficeSpace Software, en automatiseret opgave.  
Der er ingen handlingspunkt for dig.  
Brugere oprettes automatisk eventuelt under det første enkelte enkeltlogon forsøg.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden OfficeSpace Software bruger konto værktøjer til oprettelse eller API'er, som OfficeSpace Software til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-officespace-software-perform-the-following-steps"></a>Hvis du vil tildele brugere til OfficeSpace Software, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **OfficeSpace Software **application integration.

    ![Tildele brugere] (./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
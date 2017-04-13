<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Rally Software | Microsoft Azure" 
    description="Lær, hvordan du bruger Rally Software med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Selvstudium: Azure Active Directory-Integration med Rally Software
  
Formålet med dette selvstudium er at vise integration af Azure og Rally Software.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Rally Software lejer
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integration til Rally Software
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scenarie")
##<a name="enabling-the-application-integration-for-rally-software"></a>Aktivere programmet integration til Rally Software
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integration til Rally Software.

###<a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>For at aktivere programmet integration for Rally Software skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-rally-software-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-rally-software-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-rally-software-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **rally**.

    ![Galleri med programmet] (./media/active-directory-saas-rally-software-tutorial/IC769526.png "Galleri med programmet")

7.  Vælg **Rally Software**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Rally Software] (./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Rally Software med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Som en del af denne procedure, er det nødvendigt at overføre et certifikat til Rally Software.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Rally Software **application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-rally-software-tutorial/IC749323.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Rally** , og klik derefter på **Næste**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Rally Software lejer URL-adressen** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. rally.com*", og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-rally-software-tutorial/IC769529.png "Konfigurere App URL-adresse")

4.  Klik på Hent metadata på siden **Konfigurer single sign-on – på Rally** , og derefter gemme den på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-rally-software-tutorial/IC769530.png "Konfigurere Enkeltlogon")

5.  Log på din **Rally Software** lejer.

6.  Klik på **Indstillinger**på værktøjslinjen øverst, og vælg derefter **abonnement**.

    ![Abonnement] (./media/active-directory-saas-rally-software-tutorial/IC769531.png "Abonnement")

7.  Klik på knappen **handling** i værktøjslinjen øverst til højre, og vælg derefter **Rediger abonnement**.

8.  Udfør følgende trin på siden **abonnementsoplysninger** dialogboksen, og klik derefter på **Gem og Luk**:

    ![Godkendelse] (./media/active-directory-saas-rally-software-tutorial/IC769542.png "Godkendelse")

    1.  Vælg godkendelse rullelisten **Rally eller SSO-godkendelse**
    2.  Kopiere værdien **Identitet Provider-ID** i Azure klassisk-portalen på dialogboksside **konfigurere Enkeltlogon på Rally Software** , og Indsæt det i tekstfeltet **Identitet udbyder URL-adresse**
    3.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på dialogboksside **konfigurere Enkeltlogon på Rally Software** .

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-rally-software-tutorial/IC769547.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For AAD brugere skal kunne logge på, skal de klargøres til programmet Rally Software ved hjælp af deres Azure Active Directory-brugernavne.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på din Rally Software lejer.

2.  Gå til **konfiguration \> brugere**, og klik derefter på **+ Add New**.

    ![Brugere] (./media/active-directory-saas-rally-software-tutorial/IC781039.png "Brugere")

3.  Skriv navnet i tekstfeltet ny bruger, og klik derefter på **Tilføj med detaljer**.

4.  I sektionen **Create User** skal du udføre følgende trin:

    ![Oprette bruger] (./media/active-directory-saas-rally-software-tutorial/IC781040.png "Oprette bruger")

    1.  I tekstfeltet **Brugernavn** skal du skrive navnet på den ønskede at blive klargjort Azure AD-bruger.
    2.  I tekstfeltet **Mailadresse** skal du skrive mailadressen på den ønskede at blive klargjort Azure AD-bruger.
    3.  Klik på **Gem og Luk**.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden Rally Software bruger konto værktøjer til oprettelse eller API'er, som Rally Software til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>Hvis du vil tildele brugere til Rally Software, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Rally Software** application integration.

    ![Tildele brugere] (./media/active-directory-saas-rally-software-tutorial/IC769548.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-rally-software-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.





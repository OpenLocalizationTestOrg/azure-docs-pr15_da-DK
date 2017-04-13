<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med ShiftPlanning | Microsoft Azure" 
    description="Lær, hvordan du bruger ShiftPlanning med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-shiftplanning"></a>Selvstudium: Azure Active Directory-integration med ShiftPlanning
  
Formålet med dette selvstudium er at vise integration af Azure og ShiftPlanning.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En ShiftPlanning single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til ShiftPlanning kunne enkelt Log på programmet på webstedet ShiftPlanning virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for ShiftPlanning
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Scenarie")
##<a name="enabling-the-application-integration-for-shiftplanning"></a>Aktivere programmet integrationen for ShiftPlanning
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for ShiftPlanning.

###<a name="to-enable-the-application-integration-for-shiftplanning-perform-the-following-steps"></a>For at aktivere programmet integration for ShiftPlanning skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **ShiftPlanning**.

    ![Galleri med programmet] (./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Galleri med programmet")

7.  Vælg **ShiftPlanning**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![ShiftPlanning] (./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende ShiftPlanning med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **ShiftPlanning** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge ShiftPlanning** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://company.shiftplanning.com/includes/saml/*" på siden **Konfigurer App URL-adressen** i tekstfeltet **ShiftPlanning Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på ShiftPlanning** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Konfigurere Single Sign-On")

5.  Log på webstedet **ShiftPlanning** virksomhed som administrator i et andet browservindue.

6.  Klik på **administrator**i menuen øverst.

    ![Administrator] (./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Administrator")

7.  Klik på **Single Sign-On**under **Integration**.

    ![Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Single Sign-On")

8.  I sektionen **Single Sign-On** skal du udføre følgende trin:

    ![Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Single Sign-On")

    1.  Vælg **SAML aktiveret**.
    2.  Vælg **Tillad adgangskode logon**
    3.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på ShiftPlanning** dialogboksen siden, og Indsæt det i tekstfeltet **SAML udsteder URL-adresse** .
    4.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på ShiftPlanning** dialogboksen siden, og Indsæt det i tekstfeltet **Remote log URL-adresse** .
    5.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    6.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **X.509-certifikat**
    7.  Klik på **Gem indstillinger**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på ShiftPlanning, skal de klargøres til ShiftPlanning.  
Klargøring af er ShiftPlanning, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **ShiftPlanning** virksomhed som administrator.

2.  Klik på **administrator**.

    ![Administrator] (./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Administrator")

3.  Klik på **Staff**.

    ![Personale] (./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Personale")

4.  Under **Handlinger**skal du klikke på **Tilføj medarbejder**.

    ![Tilføje medarbejdere] (./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Tilføje medarbejdere")

5.  I sektionen **Tilføje medarbejdere** skal du udføre følgende trin:

    ![Gemme medarbejdere] (./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Gemme medarbejdere")

    1.  Skriv **Fornavn**, **Efternavn** og **mail** med en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Gem medarbejdere**.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden ShiftPlanning bruger konto værktøjer til oprettelse eller API'er, som ShiftPlanning til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-shiftplanning-perform-the-following-steps"></a>Hvis du vil tildele brugere til ShiftPlanning, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **ShiftPlanning **application integration.

    ![Tildele brugere] (./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
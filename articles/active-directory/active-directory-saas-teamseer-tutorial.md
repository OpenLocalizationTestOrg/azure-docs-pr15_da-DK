<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med TeamSeer | Microsoft Azure" 
    description="Lær, hvordan du bruger TeamSeer med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Selvstudium: Azure Active Directory-integration med TeamSeer
  
Formålet med dette selvstudium er at vise integration af Azure og TeamSeer.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En TeamSeer lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til TeamSeer kunne enkelt Log på programmet på webstedet TeamSeer virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for TeamSeer
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scenarie")

##<a name="enabling-the-application-integration-for-teamseer"></a>Aktivere programmet integrationen for TeamSeer
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for TeamSeer.

###<a name="to-enable-the-application-integration-for-teamseer-perform-the-following-steps"></a>For at aktivere programmet integration for TeamSeer skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-teamseer-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-teamseer-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-teamseer-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **TeamSeer**.

    ![Galleri med programmet] (./media/active-directory-saas-teamseer-tutorial/IC789619.png "Galleri med programmet")

7.  Vælg **TeamSeer**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![TeamSeer] (./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende TeamSeer med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **TeamSeer** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789621.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge TeamSeer** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789628.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*http://www.teamseer.com/companyid*" på siden **Konfigurer App URL-adressen** i tekstfeltet **TeamSeer log i URL-adresse** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-teamseer-tutorial/IC789629.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på TeamSeer** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789630.png "Konfigurere Single Sign-On")

5.  Log på webstedet TeamSeer virksomhed som administrator i et andet browservindue.

6.  Gå til **HR administrator**.

    ![HR-administrator] (./media/active-directory-saas-teamseer-tutorial/IC789634.png "HR-administrator")

7.  Klikke på **installationen**.

    ![Konfiguration] (./media/active-directory-saas-teamseer-tutorial/IC789635.png "Konfiguration")

8.  Klik på **Konfigurer SAML udbyder detaljer**.

    ![SAML indstillinger] (./media/active-directory-saas-teamseer-tutorial/IC789636.png "SAML indstillinger")

9.  I afsnittet SAML udbyder oplysninger om at udføre følgende trin:

    ![SAML indstillinger] (./media/active-directory-saas-teamseer-tutorial/IC789637.png "SAML indstillinger")

    1.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på TeamSeer** dialogboksen siden, og Indsæt det i tekstfeltet **URL-adresse** .
    2.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    3.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **IdP offentlige certifikat** .

10. For at fuldføre SAML udbyder konfigurationen skal du udføre følgende trin:

    ![SAML indstillinger] (./media/active-directory-saas-teamseer-tutorial/IC789638.png "SAML indstillinger")

    1.  Skriv test brugerens e-mail-adresse i **Teste mailadresser**.
    2.  Skriv udbyderen af URL-adressen udsteder i tekstfeltet **udsteder** .
    3.  Klik på **Gem**.

11. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789639.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på TeamSeer, skal de klargøres til ShiftPlanning.  
Klargøring af er TeamSeer, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **TeamSeer** virksomhed som administrator.

2.  Udfør følgende trin:

    ![HR-administrator] (./media/active-directory-saas-teamseer-tutorial/IC789640.png "HR-administrator")

    1.  Gå til **HR administrator \> brugere**.
    2.  Klik på **Kør guiden Ny bruger**.

3.  I sektionen **Brugeroplysninger** skal du udføre følgende trin:

    ![Oplysninger om bruger] (./media/active-directory-saas-teamseer-tutorial/IC789641.png "Oplysninger om bruger")

    1.  Skriv **Fornavn**, **Efternavn**, **brugernavn (mailadresse)** på en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Næste**.

4.  Følg anvisningerne på skærmen for at tilføje en ny bruger, og klik på **Udfør**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden TeamSeer bruger konto værktøjer til oprettelse eller API'er leveres af TeamSeer til at klargøre Azure AD-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-teamseer-perform-the-following-steps"></a>Hvis du vil tildele brugere til TeamSeer, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **TeamSeer **application integration.

    ![Tildele brugere] (./media/active-directory-saas-teamseer-tutorial/IC789642.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-teamseer-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
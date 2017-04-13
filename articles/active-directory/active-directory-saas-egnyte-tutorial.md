<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Egnyte | Microsoft Azure" 
    description="Lær, hvordan du bruger Egnyte med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Selvstudium: Azure Active Directory-integration med Egnyte
  
Formålet med dette selvstudium er at vise integration af Azure og Egnyte.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Egnyte single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Egnyte kunne enkelt Log på programmet på webstedet Egnyte virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Egnyte
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-egnyte-tutorial/IC787812.png "Scenarie")
##<a name="enabling-the-application-integration-for-egnyte"></a>Aktivere programmet integrationen for Egnyte
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Egnyte.

###<a name="to-enable-the-application-integration-for-egnyte-perform-the-following-steps"></a>For at aktivere programmet integration for Egnyte skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-egnyte-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-egnyte-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-egnyte-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **egnyte**.

    ![Galleri med programmet] (./media/active-directory-saas-egnyte-tutorial/IC787813.png "Galleri med programmet")

7.  Vælg **Egnyte**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Egnyte] (./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Egnyte med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Egnyte** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-egnyte-tutorial/IC787815.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Egnyte** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-egnyte-tutorial/IC787816.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://company.egnyte.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Egnyte log i URL-adresse** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-egnyte-tutorial/IC787817.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Egnyte** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-egnyte-tutorial/IC787818.png "Konfigurere Single Sign-On")

5.  Log på webstedet Egnyte virksomhed som administrator i et andet browservindue.

6.  Klik på **Indstillinger**.

    ![Indstillinger] (./media/active-directory-saas-egnyte-tutorial/IC787819.png "Indstillinger")

7.  Klik på **Indstillinger**i menuen.

    ![Indstillinger] (./media/active-directory-saas-egnyte-tutorial/IC787820.png "Indstillinger")

8.  Klik på fanen **konfiguration** , og klik derefter på **sikkerhed**.

    ![Sikkerhed] (./media/active-directory-saas-egnyte-tutorial/IC787821.png "Sikkerhed")

9.  I sektionen **Godkendelse af enkelt Sign-On** skal du udføre følgende trin:

    ![Single Sign-On godkendelse] (./media/active-directory-saas-egnyte-tutorial/IC787822.png "Single Sign-On godkendelse")

    1.  Vælg **SAML 2.0**som **enkelt sign-on – godkendelse**.
    2.  Vælg **AzureAD**som **identitetsudbyder**.
    3.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Egnyte** dialogboksen siden, og Indsæt det i tekstfeltet **identitet udbyder login URL-adresse** .
    4.  Kopiér værdien **Enheds-ID** i Azure klassisk-portalen på **Konfigurer single sign-on – på Egnyte** dialogboksen siden, og Indsæt det i tekstfeltet **identitet udbyder enheds-ID** .
    5.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    6.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **identitet udbyder certifikat** .
    7.  Vælg **e-mailadresse**, som **standard Brugertilknytning**.
    8.  **Brug domæne-specifikke udsteder værdi**skal du markere **deaktiveret**.
    9.  Klik på **Gem**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-egnyte-tutorial/IC787823.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Egnyte, skal de klargøres til Egnyte.  
Klargøring af er Egnyte, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Egnyte** Egnyte virksomhed som administrator.

2.  Gå til **indstillinger \> brugere og grupper**.

3.  Klik på **Tilføj ny bruger**, og vælg derefter typen af bruger, du vil tilføje.

    ![Brugere] (./media/active-directory-saas-egnyte-tutorial/IC787824.png "Brugere")

4.  I sektionen **Nye standardbruger** skal du udføre følgende trin:

    ![Nye Standard bruger] (./media/active-directory-saas-egnyte-tutorial/IC787825.png "Nye Standard bruger")

    1.  Skriv **mailen**, **brugernavn** og andre detaljer for en gyldig Azure Active Directory-konto, du vil klargøre.
    2.  Klik på **Gem**.

    >[AZURE.NOTE] Azure Active Directory-kontoindehaver, modtager en besked via e-mail.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Egnyte bruger konto værktøjer til oprettelse eller API'er, som Egnyte til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-egnyte-perform-the-following-steps"></a>Hvis du vil tildele brugere til Egnyte, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Egnyte **application integration.

    ![Tildele brugere] (./media/active-directory-saas-egnyte-tutorial/IC787826.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-egnyte-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
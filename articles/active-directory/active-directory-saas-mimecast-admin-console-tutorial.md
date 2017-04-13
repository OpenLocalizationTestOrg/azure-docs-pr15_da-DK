<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Mimecast administrationskonsollen | Microsoft Azure" 
    description="Lær, hvordan du bruger Mimecast administrationskonsollen med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Selvstudium: Azure Active Directory-Integration med Mimecast administrationskonsollen
  
Formålet med dette selvstudium er at vise integration af Azure og Mimecast administrationskonsollen.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Mimecast administrationskonsollen single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Mimecast administrationskonsollen kunne enkelt Log på programmet på webstedet Mimecast administrationskonsollen virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Mimecast administrationskonsollen
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Scenarie")
##<a name="enabling-the-application-integration-for-mimecast-admin-console"></a>Aktivere programmet integrationen for Mimecast administrationskonsollen
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Mimecast administrationskonsollen.

###<a name="to-enable-the-application-integration-for-mimecast-admin-console-perform-the-following-steps"></a>For at aktivere programmet integration for Mimecast administrationskonsollen skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Mimecast administrationskonsollen**.

    ![Galleri med programmet] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Galleri med programmet")

7.  Vælg **Mimecast administrationskonsollen**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Mimecast administrationskonsollen] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Mimecast administrationskonsollen")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er at oprette en disposition for at aktivere brugere til at godkende Mimecast administrationskonsollen med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Mimecast administrationskonsollen** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge Mimecast administrationskonsollen** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Konfigurere Single Sign-On")

3.  Skriv URL-adressen bruges af dine brugere til at logge din Mimecast administrationskonsollen-program i tekstfeltet **Mimecast administrator Console Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "https://webmail-uk.mimecast.com" eller "https://webmail-us.mimecast.com"), og klik derefter på **Næste**.

    >[AZURE.NOTE] Log på URL-adresse er bestemt område.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Mimecast administrationskonsollen** , og derefter gemme certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Konfigurere Single Sign-On")

5.  Log på din Mimecast administrationskonsollen som administrator i et andet browservindue.

6.  Gå til **Services \> program**.

    ![Tjenester] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Tjenester")

7.  Klik på **godkendelse profiler**.

    ![Godkendelse profiler] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Godkendelse profiler")

8.  Klik på **ny godkendelse profil**.

    ![Nye godkendelse profiler] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "Nye godkendelse profiler")

9.  I sektionen **Godkendelse profil** skal du udføre følgende trin:

    ![Godkendelse af profil] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Godkendelse af profil")

    1.  Skriv et navn til din konfiguration i tekstboksen **Beskrivelse** .
    2.  Vælg **gennemtvinge SAML godkendelse for Mimecast administrationskonsollen**.
    3.  Vælg **Azure Active Directory**som **udbyder**.
    4.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Mimecast administrationskonsollen** dialogboksen siden, og Indsæt det i tekstfeltet **Udsteder URL-adresse** .
    5.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Mimecast administrationskonsollen** dialogboksen siden, og Indsæt det i tekstfeltet **URL-adressen til logon** .
    6.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Mimecast administrationskonsollen** dialogboksen siden, og Indsæt det i tekstfeltet **Log URL-adresse** .  

        >[AZURE.NOTE]Login URL-værdien og værdien log URL-adressen er til administrationskonsollen Mimecast på samme måde.

    7.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

    8.  Åbn dit base-64-kodet certifikat i Notesblok skal fjerne den første linje ("*--*") og den sidste linje ("*--*"), kopiere resterende indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **Identitet udbyder certifikat (Metadata)** .
    9.  Vælg **Tillad Single Sign på**.
    10. Klik på **Gem**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Mimecast administrationskonsollen, skal de klargøres til Mimecast administrationskonsollen.  
Klargøring af er Mimecast administrationskonsollen en manuel opgave.
  
Du kan registrere et domæne, før du kan oprette brugere.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log din **Mimecast administrationskonsollen** som administrator.

2.  Gå til **kataloger \> interne**.

    ![Mapper] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Mapper")

3.  Klik på **Registrer nyt domæne**.

    ![Registrere nyt domæne] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Registrere nyt domæne")

4.  Når dit nye domæne er blevet oprettet, kan du klikke på **Ny adresse**.

    ![Ny adresse] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "Ny adresse")

5.  I dialogboksen Ny adresse, du udføre følgende trin:

    ![Gemme] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Gemme")

    1.  Skriv **Mailadressen**, **Globale navn**, **din adgangskode** og **Bekræft adgangskode** attributterne for en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Gem**.

>[AZURE.NOTE]Du kan bruge andre værktøjer til oprettelse af Mimecast administrationskonsollen bruger konto eller API'er, der leveres af Mimecast-Administrationskonsol til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-mimecast-admin-console-perform-the-following-steps"></a>Hvis du vil tildele brugere til Mimecast administrationskonsollen, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Mimecast administrationskonsollen **application integration.

    ![Tildele brugere] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Zoom | Microsoft Azure" 
    description="Lær at bruge Zoom med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zoom"></a>Selvstudium: Azure Active Directory-integration med Zoom
  
Formålet med dette selvstudium er at vise integration af Azure og Zoom.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Zoom-lejer
  
Når du har fuldført dette selvstudium, skal kunne enkelt Log på programmet på webstedet Zoom virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md) til de Azure AD-brugere, du har tildelt til Zoom
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integration til Zoom
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-zoom-tutorial/IC784693.png "Scenarie")

##<a name="enabling-the-application-integration-for-zoom"></a>Aktivere programmet integration til Zoom
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Zoom.

###<a name="to-enable-the-application-integration-for-zoom-perform-the-following-steps"></a>For at aktivere programmet integration for Zoom skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-zoom-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-zoom-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-zoom-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Zoom**.

    ![Galleri med programmet] (./media/active-directory-saas-zoom-tutorial/IC784694.png "Galleri med programmet")

7.  Vælg **zoome**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Zoom] (./media/active-directory-saas-zoom-tutorial/IC784695.png "Zoom")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Zoom med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Til Azure klassisk portalen, på siden **zoome** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-zoom-tutorial/IC784696.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge Zoom** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-zoom-tutorial/IC784697.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*http://company.zoom.us*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Zoome log i URL-adresse** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-zoom-tutorial/IC784698.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Zoom** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-zoom-tutorial/IC784699.png "Konfigurere Enkeltlogon")

5.  Log på din Zoom virksomheds websted som en administrator i et andet browservindue.

6.  Klik på fanen **Single Sign-On** .

    ![Single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784700.png "Single sign-on")

7.  Klik på fanen **Sikkerhed kontrolelementet** , og derefter gå til indstillingerne for **Single Sign-On** .

8.  I sektionen Single Sign-On skal du udføre følgende trin:

    ![Single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784701.png "Single sign-on")

    1.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på dialogboksside **konfigurere Enkeltlogon på Zoom** , og Indsæt det i tekstfeltet **logon URL-adressen** .
    2.  Kopiér værdien **Enkelt Sign-Out URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Zoom** dialogboksen siden, og Indsæt det i tekstfeltet **hvor URL-adressen** .
    3.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    4.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **identitet udbyder certifikat**
    5.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Zoom** dialogboksen siden, og Indsæt det i tekstfeltet **udsteder** .
    6.  Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-zoom-tutorial/IC784702.png "Konfigurere Enkeltlogon")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Zoom, skal de klargøres til Zoom.  
Hvis det er Zoom er klargøring en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **zoome** virksomhed som administrator.

2.  Klik på fanen **Kontoadministration** , og klik derefter på **Brugeradministration**.

3.  Klik på **Tilføj brugere**i sektionen brugeradministration.

    ![Brugeradministration] (./media/active-directory-saas-zoom-tutorial/IC784703.png "Brugeradministration")

4.  På siden **Tilføj brugere** skal du udføre følgende trin:

    ![Tilføj brugere] (./media/active-directory-saas-zoom-tutorial/IC784704.png "Tilføj brugere")

    1.  Vælg **grundlæggende**som **Brugertype**.
    2.  Skriv en gyldig AAD-konto, du vil klargøre mailadresse i tekstfeltet **mails** .
    3.  Klik på **Tilføj**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden bruger konto oprettelse af zoomværktøjerne eller API'er, der leveres af Zoom at blive klargjort AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-zoom-perform-the-following-steps"></a>Hvis du vil tildele brugere til Zoom, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **tildele brugerne**på siden **zoome **application integration.

    ![Tildele brugere] (./media/active-directory-saas-zoom-tutorial/IC784705.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-zoom-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
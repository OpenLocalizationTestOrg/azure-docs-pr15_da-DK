<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Tinfoil sikkerhed | Microsoft Azure"
    description="Lær at bruge Tinfoil sikkerhed med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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

#<a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Selvstudium: Azure Active Directory-integration med Tinfoil sikkerhed
  
Formålet med dette selvstudium er at vise integration af Azure og Tinfoil sikkerhed.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Tinfoil sikkerhed single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, skal de Azure AD-brugere, du har tildelt til Tinfoil sikkerhed kunne enkelt Log på programmet på webstedet Tinfoil sikkerhed virksomhed (identitet udbyder startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Tinfoil sikkerhed
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Konfigurere Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Konfigurere Single Sign-On")

##<a name="enabling-the-application-integration-for-tinfoil-security"></a>Aktivere programmet integrationen for Tinfoil sikkerhed
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Tinfoil sikkerhed.

###<a name="to-enable-the-application-integration-for-tinfoil-security-perform-the-following-steps"></a>For at aktivere programmet integration for Tinfoil sikkerhed skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Tinfoil sikkerhed**.

    ![Galleri med programmet] (./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Galleri med programmet")

7.  Vælg **Tinfoil sikkerhed**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Tinfoil sikkerhed] (./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil sikkerhed")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Tinfoil sikkerhed med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Tinfoil sikkerhed, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Tinfoil sikkerhed** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Tinfoil sikkerhed** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Konfigurere Single Sign-On")

3.  Skriv din Tinfoil sikkerhed program Consumer ACS (Service) URL-adresse i tekstfeltet **Tinfoil sikkerhed svar URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://www.tinfoilsecurity.com/saml/consume*", og klik derefter på **Næste**.

    >[AZURE.NOTE] Du skulle kunne få ACS URL-adressen fra Tinfoil Sikkerhedsmetadata (https://www.tinfoilsecurity.com/saml/metadata).

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Konfigurere App URL-adresse")

4.  På siden **Konfigurer single sign-on – på Tinfoil sikkerhed** for at downloade certifikatet, skal du klikke på **Hent certifikat**, og Gem derefter certifikatfilen lokalt som **c:\\Tinfoil Security.cer**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Konfigurere Single Sign-On")

5.  Log på din Tinfoil sikkerhed virksomheds websted som en administrator i et andet browservindue.

6.  Klik på **My Account**i værktøjslinjen øverst.

    ![Dashboard] (./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Dashboard")

7.  Klik på **sikkerhed**.

    ![Sikkerhed] (./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Sikkerhed")

8.  På konfigurationssiden **Single Sign-On** du udføre følgende trin:

    ![Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Single Sign-On")

    1.  Vælg **Aktiver SAML**.
    2.  Klik på **manuel konfiguration**.
    3.  Kopiér værdien **SAML SSO URL-adressen** på Azure-klassisk portalen på dialogboksside **konfigurere Enkeltlogon på Tinfoil sikkerhed** , og Indsæt det i tekstfeltet **SAML Send URL-adresse** .
    4.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **SAML certifikat fingeraftryk** .  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    5.  Kopiere **din konto-ID**.
    6.  Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Konfigurere Single Sign-On")

10. Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i menuen øverst.

    ![Attributter] (./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Attributter")

11. For at tilføje påkrævet attributtilknytningerne skal du udføre følgende trin:

    ![Attributter] (./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Attributter")

    1.  Klik på **Tilføj bruger attributten**.
    2.  Skriv **accountid**i tekstfeltet **Attributnavn** .
    3.  I tekstfeltet **Attributværdien** skal du indsætte kontoværdien-ID, du har kopieret i forrige afsnit.
    4.  Klik på **udført**.

12. Klik på **Anvend ændringer**.

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Tinfoil sikkerhed, skal de klargøres til Tinfoil sikkerhed.  
Klargøring af er Tinfoil sikkerhed, en manuel opgave.

###<a name="to-get-a-user-provisioned-perform-the-following-steps"></a>For at få en bruger, der er klargjort skal du udføre følgende trin:

1.  Hvis brugeren er en del af en Enterprise-konto, skal du kontakte supportteam Tinfoil sikkerhed for at få den brugerkonto, der er oprettet.

2.  Hvis brugeren er en almindelig Tinfoil sikkerhed SaaS-bruger, kan derefter brugeren tilføje en collaborator for brugerens websteder. Dette udløser en proces for at sende en invitation til den angivne e-mail til at oprette en ny brugerkonto Tinfoil sikkerhed.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Tinfoil sikkerhed bruger konto værktøjer til oprettelse eller API'er, som Tinfoil sikkerhed til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-tinfoil-security-perform-the-following-steps"></a>Hvis du vil tildele brugere til Tinfoil sikkerhed, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Tinfoil sikkerhed **application integration.

    ![Tildele brugere] (./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
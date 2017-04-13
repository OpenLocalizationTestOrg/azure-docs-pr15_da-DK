<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Igloo Software | Microsoft Azure" 
    description="Lær, hvordan du bruger Igloo Software med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="10/20/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Selvstudium: Azure Active Directory-integration med Igloo Software
  
Formålet med dette selvstudium er at vise integration af Azure og Igloo Software.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En [Igloo Software](http://www.igloosoftware.com/) Single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, skal de Azure AD-brugere, du har tildelt til Igloo Software kunne enkelt Log på programmet på webstedet Igloo Software virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integration til Igloo Software
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Scenarie")
##<a name="enabling-the-application-integration-for-igloo-software"></a>Aktivere programmet integration til Igloo Software
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integration til Igloo Software.

###<a name="to-enable-the-application-integration-for-igloo-software-perform-the-following-steps"></a>For at aktivere programmet integration for Igloo Software skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Igloo Software**.

    ![Galleri med programmet] (./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Galleri med programmet")

7.  Vælg **Igloo Software**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Igloo] (./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Igloo Software med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at overføre et base-64-kodet certifikat til din Central skrivebord lejer som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Igloo Software** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Igloo Software** , og klik derefter på **Næste**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://company.igloocommunities.com/?signin*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Igloo Software log i URL-adresse** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Igloo Software** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Konfigurere Single Sign-On")

5.  Log på din Igloo Software virksomheds websted som en administrator i et andet browservindue.

6.  Gå til **Kontrolpanel**.

    ![I Kontrolpanel] (./media/active-directory-saas-igloo-software-tutorial/IC799949.png "I Kontrolpanel")

7.  Klik på **Log i indstillinger**på fanen **medlemskab** .

    ![Log på indstillinger] (./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Log på indstillinger")

8.  Klik på **Konfigurer SAML godkendelse**i sektionen SAML konfiguration.

    ![SAML konfiguration] (./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML konfiguration")

9.  I sektionen **Generelle konfiguration** skal du udføre følgende trin:

    ![Konfiguration af generelle] (./media/active-directory-saas-igloo-software-tutorial/IC783970.png "Konfiguration af generelle")

    1.  I tekstfeltet **Forbindelsesnavn** skal du skrive et brugerdefineret navn til din konfiguration.
    2.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Igloo Software** dialog siden, og Indsæt det i tekstfeltet **IdP Login URL-adresse** .
    3.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Igloo Software** dialog siden, og Indsæt det i tekstfeltet **IdP log URL-adresse** .
    4.  Som **Log svar og anmode om HTTP-Type**, skal du vælge **meddelelse**.
    5.  Oprette en tekstfil fra det hentede certifikat.
        
        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    6.  Fjerne den første linje og den sidste linje fra tekst filversion af dit certifikat, kopiere den resterende certifikat tekst og derefter indsætte det i tekstfeltet **Offentlige certifikat** .

10. I **svar og konfiguration af godkendelse**skal du udføre følgende trin:

    ![Svar og konfiguration af godkendelse] (./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Svar og konfiguration af godkendelse")

    1.  Vælg **Microsoft ADFS** **-Identitetsudbyder**.
    2.  Vælg **E-mailadresse**, som **Id Type**.
    3.  Skriv **mailadresse**i tekstfeltet **E-mail-attributten** .
    4.  Skriv **givenname**i tekstfeltet **Fornavn attribut** .
    5.  Skriv **Efternavn**i tekstfeltet **Sidste Name-attribut** .

11. Udføres følgende trin for at fuldføre konfigurationen:

    ![Oprettelse af bruger på Log på] (./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Oprettelse af bruger på Log på")

    1.  Vælg **Opret en ny bruger på dit websted, når de logger på**som **bruger oprettelse af på Log på**.
    2.  Som **logge på indstillinger**, skal du vælge **Brug SAML knappen på "Log på" skærm**.
    3.  Klik på **Gem**.

12. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Der er ingen handlingspunkt for dig at konfigurere brugerklargøring til Igloo Software.  
Når en tildelt brugeren forsøger at logge på Igloo Software ved hjælp af panelet access, kontrollerer Igloo Software om brugeren findes.  
Hvis der findes ingen brugerkonto endnu, oprettes den automatisk med Igloo Software.
##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-igloo-software-perform-the-following-steps"></a>Hvis du vil tildele brugere til Igloo Software, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Igloo Software **application integration.

    ![Tildele brugere] (./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
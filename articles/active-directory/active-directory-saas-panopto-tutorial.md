<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Panopto | Microsoft Azure" 
    description="Lær, hvordan du bruger Panopto med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-panopto"></a>Selvstudium: Azure Active Directory-integration med Panopto
  
Formålet med dette selvstudium er at vise integration af Azure og Panopto.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Panopto lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Panopto kunne enkelt Log på programmet på webstedet Panopto virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Panopto
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-panopto-tutorial/IC777665.png "Scenarie")
##<a name="enabling-the-application-integration-for-panopto"></a>Aktivere programmet integrationen for Panopto
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Panopto.

###<a name="to-enable-the-application-integration-for-panopto-perform-the-following-steps"></a>For at aktivere programmet integration for Panopto skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-panopto-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-panopto-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-panopto-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Panopto**.

    ![Appkication galleri] (./media/active-directory-saas-panopto-tutorial/IC777666.png "Appkication galleri")

7.  Vælg **Panopto**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Panopto] (./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Panopto med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Panopto** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-panopto-tutorial/IC777667.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Panopto** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-panopto-tutorial/IC777668.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Panopto log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. Panopto.com*", og klik derefter på **Næste**.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-panopto-tutorial/IC777528.png "Konfigurere app URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Panopto** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-panopto-tutorial/IC777669.png "Konfigurere Enkeltlogon")

5.  Log på webstedet Panopto virksomhed som administrator i et andet browservindue.

6.  Klik på **System**i værktøjslinjen til venstre, og klik **Id-udbydere**.

    ![System] (./media/active-directory-saas-panopto-tutorial/IC777670.png "System")

7.  Klik på **Tilføj en udbyder**.

    ![Identitetsudbydere] (./media/active-directory-saas-panopto-tutorial/IC777671.png "Identitetsudbydere")

8.  I sektionen SAML udbyder skal du udføre følgende trin:

    ![SaaS konfiguration] (./media/active-directory-saas-panopto-tutorial/IC777672.png "SaaS konfiguration")

    1.  Vælg **SAML20** fra listen **Udbyder Type**
    2.  Skriv et navn til forekomsten, i tekstfeltet **Forekomstnavn** .
    3.  Skriv en kort beskrivelse i tekstfeltet **Fulde beskrivelse** .
    4.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Panopto** dialogboksen siden, og Indsæt det i tekstfeltet **udsteder** .
    5.  Kopiér værdien **SAML SSO URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Panopto** dialogboksen siden, og Indsæt det i tekstfeltet **Bold URL-adressen** .
    6.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    7.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **Offentlig nøgle**
    8.  Klik på **Gem**.
        ![Gemme] (./media/active-directory-saas-panopto-tutorial/IC777673.png "Gemme")

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-panopto-tutorial/IC777674.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til Panopto.  
Når en tildelt brugeren forsøger at logge på ved hjælp af panelet access Panopto, kontrollerer Panopto om brugeren findes.  
Hvis der findes ingen brugerkonto endnu, oprettes den automatisk ved Panopto.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden Panopto bruger konto værktøjer til oprettelse eller API'er leveres af Panopto til at klargøre Azure AD-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-panopto-perform-the-following-steps"></a>Hvis du vil tildele brugere til Panopto, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Panopto **application integration.

    ![Tildele brugere] (./media/active-directory-saas-panopto-tutorial/IC777675.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-panopto-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
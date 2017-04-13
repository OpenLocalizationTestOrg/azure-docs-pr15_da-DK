<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med SAP HANA skyen Platform | Microsoft Azure" 
    description="Lær, hvordan du bruger SAP HANA skyen Platform med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>Selvstudium: Azure Active Directory-integration med SAP HANA skyen Platform
  
Formålet med dette selvstudium er at vise integration af Azure og SAP HANA skyen Platform.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En SAP HANA skyen Platform-konto
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til SAP HANA skyen Platform kunne enkelt Log på det program, ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

>[AZURE.IMPORTANT]Du skal installere dit eget program eller abonnere på et program på kontoen SAP HANA skyen Platform til at teste enkelt Log på. I dette selvstudium, der er implementeret et program i kontoen.
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for SAP HANA skyen Platform
2.  Konfigurere Enkeltlogon
3.  Tildele en rolle til en bruger
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scenarie")
##<a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>Aktivere programmet integrationen for SAP HANA skyen Platform
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for SAP HANA skyen Platform.

###<a name="to-enable-the-application-integration-for-sap-hana-cloud-platform-perform-the-following-steps"></a>For at aktivere programmet integration for SAP HANA skyen Platform skal du udføre følgende trin:

1.  Klik på **Active Directory**i portalen Azure administration i den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **SAP HANA skyen Platform**.

    ![Galleri med programmet] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Galleri med programmet")

7.  Vælg **SAP HANA skyen Platform**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![SAP Hana] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende SAP HANA skyen Platform med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at overføre et base-64-kodet certifikat til din SAP HANA skyen Platform lejer som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **SAP HANA skyen Platform** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge SAP HANA skyen Platform** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Konfigurere Single Sign-On")

3.  Log SAP HANA skyen Platform cockpittet på https://account i et andet browservindue. \<liggende host\>.ondemand.com/cockpit (f.eks.: *https://account.hanatrial.ondemand.com/cockpit*).

4.  Klik på fanen **sikkerhed og rettighedsadministration** .

    ![Hav tillid til] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Hav tillid til")

5.  I sektionen Administration af sikkerhed og rettighedsadministration, kan du udføre følgende trin:

    ![Få Metadata] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Få Metadata")

    1.  Klik på fanen **Lokale tjenesteudbyder** .
    2.  Hvis du vil hente filen SAP HANA skyen Platform metadata, skal du klikke på **Få Metadata**.

6.  Udfør følgende trin på Azure Active klassisk portalen, på siden **Konfigurer App URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Konfigurere App URL-adresse")

    1.  Skriv URL-adressen bruges af dine brugere til at logge på dit **SAP HANA skyen Platform** -program i tekstfeltet **Log på URL-adresse** . Dette er den konto-specifikke URL-adressen for en beskyttet ressource i dit SAP HANA skyen Platform-program. URL-adressen er baseret på følgende mønster: *https://\<applicationName\>\<kontonavn\>.\< liggende host\>.ondemand.com/\<stien\_til\_beskyttet\_ressource\> * (f.eks.: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

        >[AZURE.NOTE]Dette er URL-adressen i dit SAP HANA skyen Platform-program, der kræver, at brugeren til at godkende.

    2.  Åbne den hentede fil til SAP HANA skyen Platform metadata, og find derefter mærket **ns3:AssertionConsumerService** .
    3.  Kopiér værdien af attributten **placering** , og Indsæt det i tekstfeltet **URL-adressen til SAP HANA skyen Platform svar** .

7.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på SAP HANA skyen Platform** , og derefter gemme filen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Konfigurere Single Sign-On")

8.  På SAP HANA skyen Platform cockpittet, i sektionen **Lokale tjenesteudbyder** skal du udføre følgende trin:

    ![Hav tillid til administration] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Hav tillid til administration")

    1.  Klik på **Rediger**.
    2.  Vælg **brugerdefineret**som **Konfigurationstype**.
    3.  Lad standardværdien som **Lokale udbydernavn**.
    4.  Hvis du vil oprette en **Logge nøgle** og et **Certifikat til signering** nøgle, skal du klikke på **Generere nøglepar**.
    5.  Vælg **deaktiveret**som **Vigtigste overførsel**.
    6.  Vælg **deaktiveret**som **Kraft godkendelse**.
    7.  Klik på **Gem**.

9.  Klik på fanen **Der er tillid til identitetsudbyder** , og klik derefter på **Tilføj der er tillid til identitetsudbyder**.

    ![Hav tillid til administration] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Hav tillid til administration")

    >[AZURE.NOTE]Hvis du vil administrere listen over pålidelige identitetsudbydere, skal du har valgt typen brugerdefineret konfiguration i sektionen lokale tjenesteudbyder. Konfiguration af standardtypen har du en ikke-redigerbare og implicitte tillid til SAP-ID-tjenesten. Ingen, skal har du ikke nogen sikkerhed og rettighedsadministration indstillinger.

10. Klik på fanen **Generelt** , og klik derefter på **Gennemse** for at overføre metadatafilen hentede.

    ![Hav tillid til administration] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Hav tillid til administration")

    >[AZURE.NOTE] Når du har overført metadatafilen, udfyldes værdier for **enkelt Sign-on URL**, **Enkelt log URL-adresse** og **Signeringscertifikat** automatisk.

11. Klik på fanen **attributter** .

12. På fanen **attributter** skal du udføre følgende trin:

    ![Attributter] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributter")

    1.  Tilføj følgende program-baserede attributter ved at klikke på **Add Assertion-Based attribut**:

        |Program attribut| Hovedstolen attribut|
        |-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/givenname|   Fornavn|--------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/surname|        Efternavn|-----------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/emailaddress|mail|

    >[AZURE.NOTE]Konfigurationen af attributterne, afhænger af, hvordan program(mer) på HCP der udvikles, det vil sige hvilke attribut(ter) de forventer i SAML svaret og under, hvis navn (hovedstolen attribut) de åbne denne attribut i koden.
    >  
    >en.  **Standard attribut** i skærmbilledet bruges kun til illustration. Det er ikke kræves for at gøre dette scenario arbejde.  
    >
    >b.  Navne og værdier for **Attributten hovedstolen** vist i skærmbilledet afhænger af, hvordan programmet er udviklet. Det er muligt, at programmet kræver forskellige tilknytninger.

13. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen på **Konfigurer single sign-on – på SAP HANA skyen Platform** dialog siden, og klik derefter på **udført**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Konfigurere Single Sign-On")
  
Som et valgfrit trin, kan du konfigurere program-baserede grupper for din Azure Active Directory identitetsudbyder

>[AZURE.NOTE]Brug af grupper på SAP HANA skyen Platform, kan du tildele en eller flere brugere til en eller flere roller i programmerne SAP HANA skyen Platform, bestemmes af værdierne af attributter i SAML 2.0-program. Hvis program, der indeholder attributten eksempelvis "*kontrakt = midlertidige*", vil du måske alle berørte brugere der skal føjes til gruppen "*midlertidig*". Gruppen "*midlertidig*" kan indeholde et eller flere roller fra en eller flere programmer, der er implementeret i kontoen SAP HANA skyen Platform.
>  
>Bruge program-baserede grupper, hvis du vil masse Tildel mange brugere til en eller flere roller af programmer i kontoen SAP HANA skyen Platform. Hvis du kun vil tildele et enkelt eller lille antal brugere med (a) bestemte roller anbefaler vi tildele dem direkte i fanen "**tilladelser**" i det pågældende cockpit SAP HANA skyen Platform.

##<a name="assigning-a-role-to-a-user"></a>Tildele en rolle til en bruger
  
For at aktivere Azure AD-brugere at logge på SAP HANA skyen Platform, skal du tildele roller i SAP HANA skyen Platform hertil.

###<a name="to-assign-a-role-to-a-user-perform-the-following-steps"></a>Hvis du vil tildele en rolle til en bruger, skal du udføre følgende trin:

1.  Log på din **SAP HANA skyen Platform** cockpit.

2.  Udfør følgende trin:

    ![Tilladelser] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Tilladelser")

    1.  Klik på **godkendelse**.
    2.  Klik på fanen **brugere** .
    3.  I tekstfeltet **brugernavn** skal du skrive brugerens e-mail-adresse.
    4.  Klik på **Tildel** for at tildele brugeren til en rolle.
    5.  Klik på **Gem**.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-sap-hana-cloud-platform-perform-the-following-steps"></a>Hvis du vil tildele brugere til SAP HANA skyen Platform, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **SAP HANA skyen Platform** application integration.

    ![Tildele brugere] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
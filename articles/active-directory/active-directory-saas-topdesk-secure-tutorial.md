<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med TOPdesk - sikker | Microsoft Azure"
    description="Lær at bruge TOPdesk - Secure med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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

#<a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Selvstudium: Azure Active Directory-integration med TOPdesk - sikker
  
Formålet med dette selvstudium er at vise integration af Azure og TOPdesk - sikker.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En TOPdesk - sikker single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til TOPdesk - sikker kunne enkelt Log på programmet på din TOPdesk - virksomhedens sikre websted (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for TOPdesk - sikker
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Scenarie")

##<a name="enabling-the-application-integration-for-topdesk---secure"></a>Aktivere programmet integrationen for TOPdesk - sikker
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for TOPdesk - sikker.

###<a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>For at aktivere programmet-integration for TOPdesk - Secure, skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **TOPdesk - sikker**.

    ![Galleri med programmet] (./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Galleri med programmet")

7.  Vælg **TOPdesk - sikker**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![TOPdesk - sikker] (./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - sikker")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er at oprette en disposition for at aktivere brugere til at godkende TOPdesk - Secure med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til TOPdesk - sikker, skal du overføre filen logo ikonet. Kontakt supportteamet TOPdesk for at få ikonfilen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Logge webstedet **TOPdesk - sikker** virksomhed som administrator.

2.  Klik på **Indstillinger**i menuen **TOPdesk** .

    ![Indstillinger] (./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Indstillinger")

3.  Klik på **Indstillinger for logon**.

    ![Indstillinger for logon] (./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Indstillinger for logon")

4.  Udvide menuen **Indstillinger for logon** , og klik derefter på **Generelt**.

    ![Generelt] (./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Generelt")

5.  I sektionen **Secure** i sektionen **SAML login** konfiguration skal du udføre følgende trin:

    ![Technical Settings] (./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Technical Settings")

    1.  Klik på **Hent** for at hente metadatafilen offentlige, og derefter gemme den lokalt på din computer.
    2.  Åbn metadatafilen, og find derefter noden **AssertionConsumerService** .
        ![Program Consumer Service] (./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Program Consumer Service")
    3.  Kopiér værdien **AssertionConsumerService** .  

        >[AZURE.NOTE] Du skal bruge værdien i sektionen **Konfigurer App URL-adressen** senere i dette selvstudium.

6.  Log på din **Azure klassisk portalen** som administrator i et andet browservindue.

7.  På siden **TOPdesk - sikker** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Konfigurere Single Sign-On")

8.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge TOPdesk - sikker** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Konfigurere Single Sign-On")

9.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Konfigurere App URL-adresse")

    1.  Skriv URL-adressen bruges af dine brugere til at logge på din TOPdesk - programmet på computeren sikker i tekstfeltet **TOPdesk - Secure Log på URL-adresse** (f.eks.: "*https://qssolutions.topdesk.net*").
    2.  Indsæt **TOPdesk - Secure AssertionConsumerService URL-adressen** i tekstfeltet **TOPdesk – offentlig svar URL-adresse** (f.eks.: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
    3.  Klik på **Næste**.

10. Klik på **Hent metadata**for at hente metadatafilen, på siden **Konfigurer single sign-on – på TOPdesk - sikker** , og Gem derefter filen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Konfigurere Single Sign-On")

11. Hvis du vil oprette en certifikatfil, skal du udføre følgende trin:

    ![Certifikat] (./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certifikat")

    1.  Åbn metadatafilen hentede.
    2.  Udvid noden **RoleDescriptor** , der indeholder en **xsi: type** af **indføres: ApplicationServiceType**.
    3.  Kopiér værdien af noden **X509Certificate** .
    4.  Gemme værdien kopierede **X509Certificate** lokalt på din computer på en fil.

12. Klik på **Indstillinger**på din TOPdesk - sikker virksomheds websted, i menuen **TOPdesk** .

    ![Indstillinger] (./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Indstillinger")

13. Klik på **Indstillinger for logon**.

    ![Indstillinger for logon] (./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Indstillinger for logon")

14. Udvide menuen **Indstillinger for logon** , og klik derefter på **Generelt**.

    ![Generelt] (./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Generelt")

15. Klik på **Tilføj**i sektionen **offentlige** .

    ![Tilføje] (./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Tilføje")

16. På siden **SAML konfiguration assistent** dialogboksen skal du udføre følgende trin:

    ![SAML konfiguration assistent] (./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "SAML konfiguration assistent")

    1.  Klik på **Gennemse**for at overføre metadatafilen hentede under **Sammenslutning Metadata**.
    2.  Klik på **Gennemse**for at overføre certifikatfilen under **Certifikat (RSA)**.
    3.  Klik på **Gennemse**for at overføre logofilen, du har fået fra supportteam TOPdesk under **logoet ikon**.
    4.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**i tekstfeltet **bruger name-attribut** .
    5.  Skriv et navn til din konfiguration i tekstfeltet **visningsnavn** .
    6.  Klik på **Gem**.

17. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på TOPdesk - sikker, de skal klargøres til TOPdesk - sikker.  
Hvis det er TOPdesk - sikker, klargøring er en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Logge webstedet **TOPdesk - sikker** virksomhed som administrator.

2.  Klik på menuen øverst, **TOPdesk \> ny \> Support-filer \> Operator**.

    ![Operatoren] (./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operatoren")

3.  I dialogboksen **Ny Operator** skal du udføre følgende trin:

    ![Ny Operator] (./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "Ny Operator")

    1.  Klik på fanen Generelt.
    2.  I tekstfeltet **Efternavn** i sektionen **Generelt** skal du skrive efternavnet på en gyldig Azure Active Directory-konto, du vil klargøre.
    3.  Vælg et **websted** til kontoen i sektionen **placering** .
    4.  Skriv en login-navn for din bruger i tekstfeltet **Login-navn** for sektionen, **TOPdesk logon** .
    5.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge andre TOPdesk - værktøjer til sikker bruger konto oprettelse eller API'er, der leveres af TOPdesk - sikker til at klargøre AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>For at tildele brugere til TOPdesk - Secure, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **TOPdesk - sikker **application integration.

    ![Tildele brugere] (./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
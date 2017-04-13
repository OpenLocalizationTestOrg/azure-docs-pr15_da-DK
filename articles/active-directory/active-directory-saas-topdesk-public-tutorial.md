<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med TOPdesk - offentlige | Microsoft Azure" 
    description="Lær at bruge TOPdesk - offentlige med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-directory-integration-with-topdesk---public"></a>Selvstudium: Azure katalogintegration med TOPdesk - offentlige

Formålet med dette selvstudium er at vise integration af Azure og TOPdesk - offentlige.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En TOPdesk - offentlige single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, Azure AD-brugere, du har tildelt til TOPdesk - offentlige vil kunne enkelt Log på programmet på din TOPdesk - offentlige virksomheds websted (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for TOPdesk - offentlige
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Scenarie")

##<a name="enabling-the-application-integration-for-topdesk---public"></a>Aktivere programmet integrationen for TOPdesk - offentlige
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for TOPdesk - offentlige.

###<a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>At aktivere programmet integration for TOPdesk - offentligt tilgængelig, udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **TOPdesk - offentlige**.

    ![Galleri med programmet] (./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Galleri med programmet")

7.  Vælg **TOPdesk - offentlige**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![TOPdesk offentlige] (./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk offentlige")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er at oprette en disposition for at aktivere brugere til at godkende TOPdesk - offentlige med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML protokollen.  
Konfiguration af single sign-on til TOPdesk - offentlige kræver, at du at overføre filen logo ikon. Kontakt supportteamet TOPdesk for at få ikonfilen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Logge webstedet **TOPdesk - offentlige** virksomhed som administrator.

2.  Klik på **Indstillinger**i menuen **TOPdesk** .

    ![Indstillinger] (./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Indstillinger")

3.  Klik på **Indstillinger for logon**.

    ![Indstillinger for logon] (./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Indstillinger for logon")

4.  Udvide menuen **Indstillinger for logon** , og klik derefter på **Generelt**.

    ![Generelt] (./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Generelt")

5.  I sektionen **offentlige** i sektionen **SAML login** konfiguration skal du udføre følgende trin:

    ![Technical Settings] (./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Technical Settings")

    1.  Klik på **Hent** for at hente metadatafilen offentlige, og derefter gemme den lokalt på din computer.
    2.  Åbn metadatafilen, og find derefter noden **AssertionConsumerService** .
        ![AssertionConsumerService] (./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
    3.  Kopiér værdien **AssertionConsumerService** .  

        >[AZURE.NOTE] Du skal bruge værdien i sektionen **Konfigurer App URL-adressen** senere i dette selvstudium.

6.  Log på din **Azure klassisk portalen** som administrator i et andet browservindue.

7.  På siden **TOPdesk - offentlige** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Konfigurere Single Sign-On")

8.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge TOPdesk - offentlige** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Konfigurere Single Sign-On")

9.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Konfigurere App URL-adresse")

    1.  Skriv URL-adressen bruges af dine brugere til at logge på din TOPdesk - programmet på offentlige i tekstfeltet **TOPdesk - offentlig Log på URL-adresse** (f.eks.: "*https://qssolutions.topdesk.net*").
    2.  I tekstfeltet **TOPdesk – offentlig svar URL-adresse** skal du indsætte **TOPdesk - offentlig AssertionConsumerService URL-adresse** (f.eks.: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
    3.  Klik på **Næste**.

10. Klik på **Hent metadata**for at hente metadatafilen, på siden **Konfigurer single sign-on – på TOPdesk - offentlige** , og Gem derefter filen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Konfigurere Single Sign-On")

11. Hvis du vil oprette en certifikatfil, skal du udføre følgende trin:

    ![Certifikat] (./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certifikat")

    1.  Åbn metadatafilen hentede.
    2.  Udvid noden **RoleDescriptor** , der indeholder en **xsi: type** af **indføres: ApplicationServiceType**.
    3.  Kopiér værdien af noden **X509Certificate** .
    4.  Gemme værdien kopierede **X509Certificate** lokalt på din computer på en fil.

12. Klik på **Indstillinger**på din TOPdesk - offentlige virksomheds websted, i menuen **TOPdesk** .

    ![Indstillinger] (./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Indstillinger")

13. Klik på **Indstillinger for logon**.

    ![Indstillinger for logon] (./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Indstillinger for logon")

14. Udvide menuen **Indstillinger for logon** , og klik derefter på **Generelt**.

    ![Generelt] (./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Generelt")

15. Klik på **Tilføj**i sektionen **offentlige** .

    ![SAML logon] (./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML logon")

16. På siden **SAML konfiguration assistent** dialogboksen skal du udføre følgende trin:

    ![SAML konfiguration assistent] (./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML konfiguration assistent")

    1.  Klik på **Gennemse**for at overføre metadatafilen hentede under **Sammenslutning Metadata**.
    2.  Klik på **Gennemse**for at overføre certifikatfilen under **Certifikat (RSA)**.
    3.  Klik på **Gennemse**for at overføre logofilen, du har fået fra supportteam TOPdesk under **logoet ikon**.
    4.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**i tekstfeltet **bruger name-attribut** .
    5.  Skriv et navn til din konfiguration i tekstfeltet **visningsnavn** .
    6.  Klik på **Gem**.

17. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på TOPdesk - offentligt tilgængelig, de skal klargøres til TOPdesk - offentlige.  
Hvis det er TOPdesk - offentlige klargøring er en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Logge webstedet **TOPdesk - offentlige** virksomhed som administrator.

2.  Klik på menuen øverst, **TOPdesk \> ny \> Support-filer \> Person**.

    ![Person] (./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Person")

3.  I dialogboksen Ny Person skal du udføre følgende trin:

    ![Ny Person] (./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "Ny Person")

    1.  Klik på fanen Generelt.
    2.  Skriv efternavnet på en gyldig Azure Active Directory-konto, du vil klargøre i tekstfeltet efternavn.
    3.  Vælg et **websted** for kontoen.
    4.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden TOPdesk - værktøjer til oprettelse af offentlige bruger konto eller API'er leveres af TOPdesk - offentlige til klargøring AAD-brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>Tildele brugere til TOPdesk - offentligt tilgængelig, udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **TOPdesk - offentlige **application integration.

    ![Tildele brugere] (./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
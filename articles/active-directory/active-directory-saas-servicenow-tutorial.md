<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med ServiceNow og ServiceNow Express | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og ServiceNow og ServiceNow Express."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-servicenow-and-servicenow-express"></a>Selvstudium: Azure Active Directory-integration med ServiceNow og ServiceNow Express.


I dette selvstudium lærer du at integrere ServiceNow og ServiceNow Express med Azure Active Directory (Azure AD).

Integrere ServiceNow og ServiceNow Express med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til ServiceNow og ServiceNow Express
- Du kan aktivere dine brugere til automatisk få logget på ServiceNow og ServiceNow Express (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med ServiceNow og ServiceNow Express, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- For ServiceNow, en forekomst eller lejer af ServiceNow, Calgary version eller nyere
- For ServiceNow Express, som er en forekomst af ServiceNow Express, Helsinki version eller nyere
- ServiceNow lejer skal have [Flere udbyder enkelt Log på plug-in](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) aktiveret. Dette kan gøres ved at sende en serviceanmodning på <https://hi.service-now.com/> 


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø. Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje ServiceNow fra galleriet
2. Konfigurere og teste Azure AD single sign-on til ServiceNow eller ServiceNow Express


## <a name="adding-servicenow-from-the-gallery"></a>Tilføje ServiceNow fra galleriet
Hvis du vil konfigurere integrationen mellem ServiceNow eller ServiceNow Express Azure AD, skal du tilføje ServiceNow fra galleriet til listen over administrerede SaaS apps. 

**Hvis du vil tilføje ServiceNow fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **ServiceNow**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)

7. Vælg **ServiceNow**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og test Azure AD single sign-on – med ServiceNow eller ServiceNow Express baseret på en testbruger ved navn "Britta Simon".

Azure AD skal se, hvad brugeren modstykke i ServiceNow er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i ServiceNow oprettes.
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i ServiceNow. For at konfigurere og teste Azure AD single sign-on – med ServiceNow, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On for ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Konfiguration af Azure AD Single Sign-On til ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)** – til at aktivere dine brugere at bruge denne funktion.
3. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en ServiceNow teste bruger](#creating-a-servicenow-test-user)** - skal have et modstykke Britta Simon i ServiceNow, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
6. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

> [AZURE.NOTE] Hvis du vil konfigurere ServiceNow udelader trin 2. På samme måde, hvis du vil konfigurere ServiceNow Express udelader trin 1.

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Konfiguration af Azure AD-Single Sign-On til ServiceNow

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure AD klassisk portalen, på siden **ServiceNow** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge ServiceNow** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "Konfigurere Enkeltlogon")

3.  På siden **Konfigurer App indstillinger** ved at udføre følgende trin:

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Konfigurere app URL-adresse")

    en. Skriv din URL-adresse, der bruges af dine brugere at sign-on til ServiceNow programmet følge mønstret i tekstfeltet **ServiceNow Log på URL-adresse** : `https://<instance-name>.service-now.com`.

    b. Skriv din URL-adresse, der bruges af dine brugere til at logge på dit ServiceNow program følge mønstret i tekstfeltet **id** : `https://<instance-name>.service-now.com`.

    c. Klik på **Næste**

4.  Angiv dit ServiceNow navnet på forekomsten, admin brugernavn og administratoradgangskode i formularen **automatisk konfigurere single sign-on** for at få Azure AD automatisk konfigurere ServiceNow til SAML-baseret godkendelse, og klik på *Konfigurer*. Bemærk, at feltet administrator username leveres skal have rollen **security_admin** tildelt i ServiceNow i dette til at arbejde. Ellers skal for manuelt at konfigurere ServiceNow for at bruge Azure AD som en SAML identitetsudbyder, klik på **Konfigurer manuelt programmet til single sign-on**, og derefter klikke på **Næste** og benytte følgende fremgangsmåde.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Konfigurere app URL-adresse")



5.  Klik på **Hent certifikat**, Gem certifikatfil lokalt på din computer, på siden **Konfigurer single sign-on – på ServiceNow** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "Konfigurere Enkeltlogon")

1. Sign-on til dit ServiceNow program som administrator.
2. Aktivere _Integration - flere udbyder enkelt Sign-On Installer_ plug-in'en ved at følge de næste trin:

    en. Gå til **Filsystem Definition** sektion i navigationsruden i venstre side, og klik derefter på **plug-ins**.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Aktivér plug-in")
    
    b. Søg efter _Integration - flere udbyder enkelt Sign-On-installationsprogrammet_.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Aktivér plug-in")

    c. Vælg plug-in'et. Rigth skal du klikke på og vælg **Aktivér/opgraderingen**.
    
    d. Klik på knappen **Aktivér** .

2. Klik på **Egenskaber**i navigationsruden i venstre side.  

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Konfigurere app URL-adresse")


3. I dialogboksen **Flere udbyder SSO egenskaber** skal du udføre følgende trin:

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Konfigurere app URL-adresse")

    en. Som **aktivere flere udbyder SSO**, skal du vælge **Ja**.

    b. Som **Aktivér logføring af fejlfinding har flere provideren SSO integration**skal du vælge **Ja**.

    c. Skriv **brugernavn**i **feltet på brugeren tabel, der...** tekstfelt.

    d. Klik på **Gem**.



1. Klik på **x509 certifikater**i navigationsruden i venstre side.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Konfigurere Enkeltlogon")


1. Klik på **Ny**i dialogboksen **X.509-certifikater** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Konfigurere Enkeltlogon")


1. I dialogboksen **X.509-certifikater** skal du udføre følgende trin:

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Konfigurere Enkeltlogon")

    en. Klik på **Ny**.

    b. Skriv et navn til din konfiguration i tekstfeltet **navn** (f.eks.: **TestSAML2.0**).

    c. Vælg **aktive**.

    d. Vælg **PEM**som **Format**.

    e. Vælg **Hav tillid til Store Cert**som **Type**.
    
    f. Åbn dit Base64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **PEM certifikat** .

    g. Klik på **Opdater**.


1. Klik på **Identitetsudbydere**i navigationsruden i venstre side.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Konfigurere Enkeltlogon")

1. Klik på **Ny**under dialogboksen **Identitetsudbydere** :

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Konfigurere Enkeltlogon")


1. Klik på i dialogboksen **Identitetsudbydere** **SAML2 Update1?**:

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Konfigurere Enkeltlogon")


1. I dialogboksen Egenskaber for SAML2 Update1 skal du udføre følgende trin:

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Konfigurere Enkeltlogon")


    en. Skriv et navn til din konfiguration i tekstfeltet **navn** (f.eks.: **SAML 2.0**).

    b. I **Felt bruger** tekstfelt, Skriv **mail** eller **user_id**, afhængigt af hvilket felt bruges til at entydigt identificerer brugere i din ServiceNow installation. 
    
    > [AZURE.NOTE] Du kan configue Azure AD for at udlede Azure AD bruger-ID (brugerens hovednavn) eller mailadressen som det entydige id i SAML tokenet ved at gå til den **ServiceNow > attributter > Single Sign-On** punkt i portalen Azure klassisk og tilknytte det ønskede felt til attributten **nameidentifier** . Den værdi, der er gemt for den valgte attribut i Azure AD (fx brugerens hovednavn) skal svare til den værdi, der er gemt i ServiceNow for det angivne felt (fx user_id)

    c. Kopiér værdien **Identitet Provider-ID** på klassisk Azure AD-portalen, og Indsæt det i tekstfeltet **Identitet udbyder URL-adresse** .

    d. Kopiér **URL-godkendelse anmode om** værdien i portalen Azure AD klassisk, og Indsæt det i tekstfeltet **identitet udbyderens AuthnRequest** .

    e. Kopiér værdien **Enkelt Sign-Out URL-adressen** på klassisk Azure AD-portalen, og Indsæt det i tekstfeltet **identitet udbyderens SingleLogoutRequest** .

    f. Skriv Webadressen på startsiden ServiceNow forekomst i tekstfeltet **ServiceNow hjemmesiden** .

    > [AZURE.NOTE] På ServiceNow forekomst hjemmesiden er en sammenkædning af din **ServieNow lejer URL-adresse** og **/navpage.do** (f.eks.:`https://fabrikam.service-now.com/navpage.do`).
 

    g. I den **enheds-ID / udsteder** tekstfelt, Skriv URL-adressen for din ServiceNow lejer.

    h. Skriv URL-adressen for din ServiceNow lejer i tekstfeltet **Målgruppe URL-adresse** . 

    Jeg. Skriv i tekstfeltet **Protocol Binding for den IDP SingleLogoutRequest** **urn: oasis: navne: tc: SAML:2.0:bindings:HTTP-omdirigere**.

    j. Skriv i tekstfeltet NameID politik **urn: oasis: navne: tc: SAML:1.1:nameid-format: Uspecificeret**.

    k. Fjern markeringen af **oprette en AuthnContextClass**.

    l. Skriv i den **AuthnContextClassRef metode** `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Dette er kun nødvendigt, hvis du er skyen kun organisation. Hvis du bruger lokal ADFS eller MFA til godkendelse skal du ikke konfigurere denne værdi. 

    m. Skriv **60**i **Ur vride** tekstfelt.

    n. Vælg **MultiSSO_SAML2_Update1**som **Enkelt Log på Script**.

    o. Som **x509 certifikat**, Vælg det certifikat, du har oprettet i ovenstående trin.

    p. Klik på **Send**. 



6. Vælge enkelt sign-on – konfiguration bekræftelsen på portalen Azure AD klassisk, og klik derefter på **Næste**. 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Konfigurere Enkeltlogon")

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.
 
    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Konfigurere Enkeltlogon")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Konfiguration af Azure AD-Single Sign-On til ServiceNow Express

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure AD klassisk portalen, på siden **ServiceNow** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge ServiceNow** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "Konfigurere Enkeltlogon")

3.  På siden **Konfigurer App indstillinger** ved at udføre følgende trin:

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Konfigurere app URL-adresse")

    en. Skriv din URL-adresse, der bruges af dine brugere at sign-on til ServiceNow programmet følge mønstret i tekstfeltet **ServiceNow Log på URL-adresse** : `https://<instance-name>.service-now.com`.

    b. I tekstfeltet **Udsteder URL-adresse** skal du skrive din URL-adresse, der bruges af dine brugere til at logge på dit ServiceNow program følge mønstret `https://<instance-name>.service-now.com`.

    c. Klik på **Næste**

4.  Klik på **Konfigurer manuelt programmet til single sign-on**, og derefter klikke på **Næste** og benytte følgende fremgangsmåde.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Konfigurere app URL-adresse")

5.  Klik på **Hent certifikat**, Gem certifikatfil lokalt på din computer, og klik derefter på **Næste**på siden **Konfigurer single sign-on – på ServiceNow** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "Konfigurere Enkeltlogon")

6. Sign-on til dit ServiceNow Express program som administrator.

7. Klik på **Single Sign-On**i navigationsruden i venstre side.  

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Konfigurere app URL-adresse")


8. I dialogboksen **Single Sign-On** skal du klikke på ikonet konfiguration øverst til højre og angive følgende egenskaber:

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Konfigurere app URL-adresse")

    en. Skift **aktivere flere udbyder SSO** til højre.

    b. Slå **logføring til flere udbyder SSO integration af fejlfinding aktiveres** til højre.

    c. Skriv **brugernavn**i **feltet på brugeren tabel, der...** tekstfelt.


9. Klik på **Tilføj ny certifikat**i dialogboksen **Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Konfigurere Enkeltlogon")



10. I dialogboksen **X.509-certifikater** skal du udføre følgende trin:

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Konfigurere Enkeltlogon")

    en. Skriv et navn til din konfiguration i tekstfeltet **navn** (f.eks.: **TestSAML2.0**).

    b. Vælg **aktive**.

    c. Vælg **PEM**som **Format**.

    d. Vælg **Hav tillid til Store Cert**som **Type**.

    e. Oprette en Base64-kodet fil fra dit hentede certifikat.
   
    > [AZURE.NOTE] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).
    
    f. Åbn dit Base64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **PEM certifikat** .

    g. Klik på **Opdater**.


11. Klik på **Tilføj ny IdP**dialogboksen **Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Konfigurere Enkeltlogon")


12. Dialogboksen **Tilføj ny identitetsudbyder** under **Konfigurere identitetsudbyder**, kan du udføre følgende trin:

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Konfigurere Enkeltlogon")


    en. Skriv et navn til din konfiguration i tekstfeltet **navn** (f.eks.: **SAML 2.0**).

    b. Kopiér værdien **Identitet Provider-ID** på klassisk Azure AD-portalen, og Indsæt det i tekstfeltet **Identitet udbyder URL-adresse** .

    c. Kopiér **URL-godkendelse anmode om** værdien i portalen Azure AD klassisk, og Indsæt det i tekstfeltet **identitet udbyderens AuthnRequest** .

    d. Kopiér værdien **Enkelt Sign-Out URL-adressen** på klassisk Azure AD-portalen, og Indsæt det i tekstfeltet **identitet udbyderens SingleLogoutRequest** .

    e. Vælg det certifikat, du har oprettet i ovenstående trin, som **Identitet udbyder certifikat**.


13. Klik på **Avancerede indstillinger**, og under **Yderligere identitet udbyder egenskaber**, skal du udføre følgende trin:

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Konfigurere Enkeltlogon")

    en. Skriv i tekstfeltet **Protocol Binding for den IDP SingleLogoutRequest** **urn: oasis: navne: tc: SAML:2.0:bindings:HTTP-omdirigere**.

    b. Skriv i tekstfeltet **NameID politik** **urn: oasis: navne: tc: SAML:1.1:nameid-format: Uspecificeret**.    

    c. Skriv **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**i den **AuthnContextClassRef metode**.
    
    d. Fjern markeringen af **oprette en AuthnContextClass**.

14. Under **Yderligere egenskaber for tjenesten udbyder**, skal du udføre følgende trin:

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Konfigurere Enkeltlogon")

    en. Skriv Webadressen på startsiden ServiceNow forekomst i tekstfeltet **ServiceNow hjemmesiden** .

    > [AZURE.NOTE] På ServiceNow forekomst hjemmesiden er en sammenkædning af din **ServieNow lejer URL-adresse** og **/navpage.do** (f.eks.: `https://fabrikam.service-now.com/navpage.do`).

    b. I den **enheds-ID / udsteder** tekstfelt, Skriv URL-adressen for din ServiceNow lejer.

    c. Skriv URL-adressen for din ServiceNow lejer i tekstfeltet **URI for publikum** . 

    d. Skriv **60**i **Ur vride** tekstfelt.

    e. I **Felt bruger** tekstfelt, Skriv **mail** eller **user_id**, afhængigt af hvilket felt bruges til at entydigt identificerer brugere i din ServiceNow installation.
    
    > [AZURE.NOTE] Du kan configue Azure AD for at udlede Azure AD bruger-ID (brugerens hovednavn) eller mailadressen som det entydige id i SAML tokenet ved at gå til den **ServiceNow > attributter > Single Sign-On** punkt i portalen Azure klassisk og tilknytte det ønskede felt til attributten **nameidentifier** . Den værdi, der er gemt for den valgte attribut i Azure AD (fx brugerens hovednavn) skal svare til den værdi, der er gemt i ServiceNow for det angivne felt (fx user_id)

    f. Klik på **Gem**. 


15. Vælge enkelt sign-on – konfiguration bekræftelsen på portalen Azure AD klassisk, og klik derefter på **Næste**. 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Konfigurere Enkeltlogon")

16. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.
 
    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Konfigurere Enkeltlogon")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugerklargøring af Active Directory-brugerkonti til ServiceNow.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1. Klik på **Konfigurer brugerklargøring**i Azure Management klassisk portalen, på siden **ServiceNow** application integration. 

    ![Klargøring af bruger] (./media/active-directory-saas-servicenow-tutorial/IC769498.png "Klargøring af bruger")


2. På siden **Angiv dine ServiceNow legitimationsoplysninger for at aktivere automatisk brugerklargøring** angive følgende konfigurationsindstillinger: konfigurere klargøring af bruger 

     en. Skriv navnet på forekomsten ServiceNow i tekstfeltet **ServiceNow forekomstnavn** .

     b. I tekstfeltet **ServiceNow administrator brugernavn** skal du skrive navnet på ServiceNow administratorkonto.

     c. I tekstfeltet **ServiceNow administratoradgangskode** skal du skrive adgangskoden til kontoen.

     d. Klik på **Valider** for at bekræfte din konfiguration.

     e. Klik på knappen **Næste** for at åbne siden **Næste trin** .

     f. Hvis du vil tildele alle brugere til dette program, skal du vælge "**automatisk klargøring af alle brugerkonti i mappen til dette program**". 

    ![Næste trin] (./media/active-directory-saas-servicenow-tutorial/IC698804.png "Næste trin")

     g. Klik på **udført** for at gemme din konfiguration på siden **Næste trin** .

### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:
 
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   


### <a name="creating-a-servicenow-test-user"></a>Oprette en ServiceNow testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i ServiceNow. I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i ServiceNow. Hvis du ikke ved, hvordan du tilføjer en bruger i din ServiceNow eller ServiceNow Express-konto, kan du kontakte ServiceNow supportteam.

### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til ServiceNow.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til ServiceNow, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **ServiceNow**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen alle brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på feltet ServiceNow i panelet adgang, du bør få automatisk logget på ServiceNow-program.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png

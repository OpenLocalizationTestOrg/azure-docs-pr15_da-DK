<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med RunMyProcess | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og RunMyProcess."
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
    ms.date="10/21/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Selvstudium: Azure Active Directory-integration med RunMyProcess

Formålet med dette selvstudium er at se, hvordan du integrerer RunMyProcess med Azure Active Directory (Azure AD).

Integrering af RunMyProcess med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til RunMyProcess
- Du kan aktivere dine brugere til automatisk få logget på til RunMyProcess (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med RunMyProcess, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- RunMyProcess enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje RunMyProcess fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-runmyprocess-from-the-gallery"></a>Tilføje RunMyProcess fra galleriet
Hvis du vil konfigurere integrationen mellem RunMyProcess Azure AD, skal du tilføje RunMyProcess fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje RunMyProcess fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **RunMyProcess**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)

7. Vælg **RunMyProcess**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med RunMyProcess baseret på en testbruger ved navn "Britta Thomas".

For single sign-on til arbejde, er Azure AD skal se, hvad brugeren modstykke i RunMyProcess er til en bruger i Azure AD. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i RunMyProcess oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i RunMyProcess.

For at konfigurere og teste Azure AD single sign-on – med RunMyProcess, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en RunMyProcess teste bruger](#creating-a-runmyprocess-test-user)** - skal have et modstykke Britta Simon i RunMyProcess, der er knyttet til Azure AD-repræsentation af hun.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.


### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen klassisk og konfigurere Enkeltlogon i RunMyProcess programmet.

**Hvis du vil konfigurere Azure AD single sign-on – med RunMyProcess skal du udføre følgende trin:**

1. I portalen klassisk, på siden **RunMyProcess** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge RunMyProcess** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 

    en. I tekstfeltet **Log på URL-adresse** skal du skrive en URL-adresse ved hjælp af følgende mønster: `https://live.runmyprocess.com/live/<tenant id>`.
        
    b. Klik på **Næste**

    > [AZURE.NOTE] Vær opmærksom på, at du skal opdatere værdien med den faktiske Log på URL-adresse. For at få denne værdi skal du kontakte RunMyProcess supportteam via <mailto:support@runmyprocess.com>.
 
4. Klik på **Hent certifikat** på siden **Konfigurer single sign-on – på RunMyProcess** og derefter gemme filen på din computer:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)

5. I et andet browservindue sign-on til din RunMyProcess lejer som administrator.

6. Klik på **konto** i venstre navigationspanel, og vælg **konfiguration**.

    ![Konfigurere Single Sign-On på App-Side](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)

7. Gå til **godkendelsesmetode** sektion og udføre under trin:

    ![Konfigurere Single Sign-On på App-Side](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    en. Vælg **SSO med Samlv2**som **metode**.

    b. I den **SSO omdirigere** placere tekstfeltet værdien af **SAML SSO URL-adressen** fra Azure AD-program konfigurationsguiden.

    c. I den **Log omdirigere** placere tekstfeltet værdien af **Enkelt Sign-Out URL-adressen** fra Azure AD-program konfigurationsguiden.

    d. I **Id navneformat** placere tekstfeltet værdien af **Id navneformat** fra Azure AD-program konfigurationsguiden.

    e. Kopiere indholdet af den hentede certifikatfil, og Indsæt det i tekstfeltet **certifikat** . 

    f. Klik på **Gem** ikon.
    
8. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

9. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
 
    ![Azure AD-Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:  ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   

### <a name="creating-a-runmyprocess-test-user"></a>Oprette en RunMyProcess testbruger
  
For at aktivere Azure AD-brugere at logge på RunMyProcess, skal de klargøres til RunMyProcess. Klargøring af er RunMyProcess, en manuel opgave.

#### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet RunMyProcess virksomhed som administrator.

2.  Klik på **konto** og vælge **brugere** i venstre navigationspanel og derefter klikke på **Ny bruger**.

    ![Ny bruger] (./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "Ny bruger")

3.  I sektionen **Indstillinger** skal du udføre følgende trin:

    ![Profil] (./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")

    en. Skriv **navn** og **E-mail** for en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.

    b. Vælg et **IDE-sprog**, **sprog** og en **profil**.

    c. Vælg **Send konto oprettelse af e-mail til mig**.

    d. Klik på **Gem**.

    >[AZURE.NOTE] Du kan bruge en hvilken som helst anden RunMyProcess bruger konto værktøjer til oprettelse eller API'er, der leveres af RunMyProcess at blive klargjort Azure Active Directory-brugerkonti.
    

### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til RunMyProcess.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til RunMyProcess, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **RunMyProcess**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 

3. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. linjen til bunden, og klik på **Tildel**.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.
 
Når du klikker på feltet RunMyProcess i panelet adgang, du bør få automatisk logget på RunMyProcess-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png

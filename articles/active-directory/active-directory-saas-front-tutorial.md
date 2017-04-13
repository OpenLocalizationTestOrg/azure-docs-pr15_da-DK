<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med forside | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og forrest."
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
    ms.date="10/24/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-front"></a>Selvstudium: Azure Active Directory-integration med forside

Formålet med dette selvstudium er at se, hvordan du integrerer forside med Azure Active Directory (Azure AD).

Integrering af forside med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til forside
- Du kan aktivere dine brugere til automatisk få logget på forrest (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med forside, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Forside single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje forside i galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-front-from-the-gallery"></a>Tilføje forside i galleriet
Hvis du vil konfigurere integrationen mellem forside Azure AD, skal du tilføje forside fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje forside i galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.
    
    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.
    
    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **forside**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-front-tutorial/tutorial_front_01.png)

7. Vælg **forside**i resultatpanelet, og klik derefter på **udført** for at tilføje programmet.

    ![Valg af appen i galleriet](./media/active-directory-saas-front-tutorial/tutorial_front_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med forside, der er baseret på en testbruger ved navn "Britta Simon".

Azure AD skal se, hvad brugeren modstykke foran til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger foran oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien af **brugernavn** foran.

For at konfigurere og teste Azure AD single sign-on – med forside, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en forside teste bruger](#creating-a-front-test-user)** - skal have et modstykke Britta Simon foran, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen klassisk og konfigurere Enkeltlogon i programmet på forside.

**Hvis du vil konfigurere Azure AD single sign-on med forside, kan du udføre følgende trin:**

1. I portalen klassisk, på siden med **forside** integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge forsiden** , og klik derefter på **Næste**.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_03.png)

3. På siden **Konfigurer App indstillinger** dialogboksen Hvis du vil konfigurere programmet i **IDP startet tilstand**, skal du udføre følgende trin og klikke på **Næste**:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_04.png)

    en. I tekstfeltet **id** skal du skrive en URL-adresse ved hjælp af følgende mønster:`https://<company name>.frontapp.com`

    b. I tekstfeltet **Svar URL-adresse** skal du skrive en URL-adresse ved hjælp af følgende mønster:`https://<company name>.frontapp.com/sso/saml/callback`

    c. Klik på **Næste**

4. Hvis du vil konfigurere programmet i **SP startet tilstand** på siden **Konfigurer App-indstillinger** i dialogboksen, klik på **"Vis avancerede indstillinger (valgfrit)"** og derefter angive **Log på URL-adresse** og klikke på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_05.png)

    en. I tekstfeltet **Log på URL-adresse** skal du skrive en URL-adresse ved hjælp af følgende mønster:`https://<company name>.frontapp.com`

    b. Klik på **Næste**

    > [AZURE.NOTE] Vær opmærksom på, at de ikke er de reelle værdier. Du skal opdatere disse værdier med faktiske Log på URL-adresse, -id og svar URL-adresse. For at få disse værdier, kan du se **trin 12** for detaljer eller kontakte foran via [support@frontapp.com](emailTo:support@frontapp.com).

5. Udfør følgende trin på siden **Konfigurer single sign-on – på forsiden** og klikke på **Næste**:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_06.png)

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.

6. Sign-on til din forside lejer som administrator.

7. Gå til **Indstillinger (tandhjulsikonet ikon nederst i den venstre margentekst) > Indstillinger for**.

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)

8. Klik på linket **Single Sign-On** .

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)

9. Vælg **SAML** i rullelisten af **Single Sign-On**.

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)

10. I **Indgangspunkt** placere tekstfeltet værdien af **Enkelt Sign-on URL-adressen** fra Azure AD-program konfigurationsguiden.

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)

11. Kopiere indholdet af den hentede certifikatfil, og Indsæt det i tekstfeltet **signeringscertifikat** .

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)

12. Bekræft disse URL-adresser passer til din konfiguration i trin 3.

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)

13. Klik på knappen **Gem** .

14. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

15. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
    
    ![Azure AD-Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-front-tutorial/create_aaduser_09.png)

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-front-tutorial/create_aaduser_05.png)

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-front-tutorial/create_aaduser_06.png)

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-front-tutorial/create_aaduser_07.png)

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-front-tutorial/create_aaduser_08.png)

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-front-test-user"></a>Oprette en forside testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i Front.Please arbejde med supportteamet forrest for at tilføje brugere i kontoen forrest.

### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang forrest.
    
![Tildele bruger][200]

**Hvis du vil tildele Britta Simon forrest, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.
    
    ![Tildele bruger][201]

2. Vælg **forside**i listen over programmer.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_50.png)

1. Klik på **brugere**i menuen øverst.
    
    ![Tildele bruger][203]

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.
    
    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.
 
Når du klikker på feltet forside i panelet adgang, du bør få automatisk logget på forside-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-front-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-front-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-front-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-front-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-front-tutorial/tutorial_general_205.png

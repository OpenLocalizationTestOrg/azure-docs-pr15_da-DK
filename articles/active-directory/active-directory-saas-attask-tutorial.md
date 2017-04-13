<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med @Task| Microsoft Azure"
    description="Lær, hvordan du konfigurere single sign-on mellem Azure Active Directory og @Task."
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-task"></a>Selvstudium: Azure Active Directory-integration med@Task

Formålet med dette selvstudium er at se, hvordan du integrerer @Task med Azure Active Directory (Azure AD).  
Integrering af @Task med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, hvem der har adgang til@Task
- Du kan aktivere brugerne mulighed for at få automatisk logget på til @Task (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Sådan konfigureres Azure AD-integration med @Task, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- En @Task enkelt-sign-on aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af tre primære dokumentkomponenter:

1. Tilføje @Task fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-task-from-the-gallery"></a>Tilføje @Task fra galleriet
Konfigurere integration af @Task til Azure AD, skal du tilføje @Task fra galleriet til listen over administrerede SaaS apps.

**Tilføje @Task fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1] 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2] 

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3] 

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4] 

6. Skriv i søgefeltet, **@Task**.

    ![Programmer][5] 

7. Vælg i resultatruden **@Task**, og klik derefter på **udført** for at tilføje programmet.

    ![Programmer][30] 



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –

Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med @Task baseret på en testbruger ved navn "Britta Thomas".

For single sign-on til arbejde, Azure AD skal vide, hvilke modstykke brugeren i @Task til en bruger i Azure AD er. Med andre ord, et link forholdet mellem en Azure AD-bruger og den pågældende bruger i @Task skal oprettes.   
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i @Task.
 
Til at konfigurere og teste Azure AD single sign-on – med @Task, du skal fuldføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en @Tasktest bruger](#creating-a-halogen-software-test-user) ** - skal have et modstykke Britta Simon i @Taskthat er sammenkædet med Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i din @Task program.

**Til at konfigurere Azure AD single sign-on med @Task, udføre følgende trin:**

1. I Azure klassisk-portalen på den **@Task** programintegration på siden skal du klikke på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On][6] 

2. På den **hvordan vil du brugere til at logge på @Task ** siden, Vælg **Azure AD Single Sign-On**, og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][7] 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere indstillinger for][8] 
 
     en. I tekstfeltet **Log på URL-adressen** skal skrive URL-adressen bruges af dine brugere at sign-on til din @Task program (f.eks.:*https://<Tenant name>.attask ondemand.com*).

     b. Klik på **Næste**.

4. På den **konfigurere single sign-on på @Task ** siden, klik på **Hent metadata**, gemme metadatafilen lokalt på din computer og klik derefter på **Næste**.

    ![Hvad er Azure AD-forbindelse][9] 



1. Log på din @Task virksomheds websted som administrator.

2. Gå til **Single Sign-On konfiguration**.


1. I dialogboksen **Single Sign-On** skal du udføre følgende trin

    ![Konfigurere Single Sign-On][23]

    en. Vælg **SAML 2.0**som **Type**.

    b. Vælg **Service Provider-ID**.

    c. Kopiere **Remote Login URL-adressen**på Azure klassisk-portalen, og Indsæt det i tekstfeltet **URL-adressen til logon-Portal** .

    d. Kopiere **Enkelt Sign-Out URL-adressen**på Azure klassisk-portalen, og Indsæt det i tekstfeltet **Sign-Out URL-adresse** .

    e. Kopiere **Skift adgangskode URL-adressen**på Azure klassisk-portalen, og Indsæt det i tekstfeltet **URL-adressen Skift adgangskode** .

    f. Klik på **Gem**.

6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **Næste**. 

    ![Hvad er Azure AD-forbindelse][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Hvad er Azure AD-forbindelse][11]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.  

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
 
4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
 
    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .
    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
 
8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
  
    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   

  
 
### <a name="creating-an-task-test-user"></a>Oprette en @Task testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon @Task.


**Sådan oprettes en bruger, kaldet Britta Simon @Task, udføre følgende trin:**

1. Log på din @Task virksomheds websted som administrator.

2. Klik på **personer**i menuen øverst.

3. Klik på **Ny Person**. 

4. I dialogboksen Ny Person skal du udføre følgende trin:

    ![Oprette en @Task testbruger][21] 

    en. Skriv "Britta" i tekstfeltet **Fornavn** .

    b. Skriv "Thomas" i tekstfeltet **Efternavn** .

    c. I tekstfeltet **Mailadresse** skal du skrive Britta Simons mailadresse i Azure Active Directory.

    d. Klik på **Tilføj Person**.




### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til @Task.

![Tildele bruger][200] 

**Tildele Britta Simon til @Task, udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg på listen programmer **@Task**.

    ![Tildele bruger][202] 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på den @Task side om side i panelet adgang, skal du få automatisk logget på til din @Task program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png







<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med planlægning af Predictix udvalg | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og planlægning af Predictix udvalg."
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
    ms.date="10/14/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Selvstudium: Azure Active Directory-integration med Predictix udvalg planlægning

I dette selvstudium lærer du at integrere Predictix udvalg planlægning med Azure Active Directory (Azure AD).

Integrering af planlægning af Predictix udvalg med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til planlægning af Predictix udvalg
- Du kan aktivere dine brugere til automatisk få logget på til planlægning af Predictix udvalg (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Predictix udvalg planlægning, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Planlægning af Predictix udvalg single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Microsoft Azure AD Single Sign-On i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Predictix udvalg planlægning fra galleriet
2. Konfigurere og teste Microsoft Azure AD Single Sign-On


## <a name="adding-predictix-assortment-planning-from-the-gallery"></a>Tilføje Predictix udvalg planlægning fra galleriet
Hvis du vil konfigurere integrationen mellem Predictix udvalg planlægning Azure AD, skal du tilføje Predictix udvalg planlægning fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Predictix udvalg planlægning fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Active Directory][1]
2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Predictix udvalg planlægning**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_01.png)

7. Vælg **Predictix udvalg planlægning**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_02.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Konfigurere og teste Microsoft Azure AD Single Sign-On
I dette afsnit, skal du konfigurere og teste Microsoft Azure AD Single Sign-On med planlægning af Predictix udvalg baseret på en testbruger ved navn "Britta Simon".

Azure AD skal se, hvad brugeren modstykke i planlægning af Predictix udvalg er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i planlægning af Predictix udvalg oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Predictix udvalg planlægning.

For at konfigurere og teste Microsoft Azure AD Single Sign-On med Predictix udvalg planlægning, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Microsoft Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Microsoft Azure AD Single Sign-On med Britta Simon.
3. **[Oprette en bruger Predictix udvalg planlægning test](#creating-a-predictix-price-reporting-test-user)** - skal have et modstykke Britta Simon Predictix udvalg planlægning, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Microsoft Azure AD Single Sign-On.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Konfiguration af Microsoft Azure AD Single Sign-On

I dette afsnit, skal du aktivere Microsoft Azure AD Single Sign-On i portalen klassisk og konfigurere Enkeltlogon i din planlægning af Predictix udvalg programmet.


**Hvis du vil konfigurere Microsoft Azure AD Single Sign-On med planlægning af Predictix udvalg skal du udføre følgende trin:**

1. I portalen klassisk, på siden **Predictix udvalg planlægning** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Predictix udvalg planlægning** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_04.png) 

    en. Skriv URL-adressen bruges af dine brugere at logge på din planlægning af Predictix udvalg program ved hjælp af følgende mønster i tekstfeltet **Log på URL-adresse** : **https://\<virksomhedens navn priser\>.ap.predictix.com/sso/request**.
    
    b. Klik på **Næste**
 
4. På siden **Konfigurer enkeltlogon ved planlægning af Predictix udvalg** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_05.png)

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


5. Kontakt Predictix udvalg planlægning supportteam for at få SSO konfigureret for dit program, og give dem med følgende:

    • Hentede certifikatet

    • **Enheds-ID**

    • **SAML SSO URL-adresse**

    • **Enkelt log af URL-adressen**

6. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
 
    ![Azure AD-Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.


![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:  ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-an-predictix-assortment-planning-test-user"></a>Oprette en Predictix udvalg planlægning testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i planlægning af Predictix udvalg. Skal du samarbejde med planlægning af Predictix udvalg supportteam for at føje brugerne i Predictix udvalg planlægning platformen.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til planlægning af Predictix udvalg.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til planlægning af Predictix udvalg, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Predictix udvalg planlægning**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_50.png) 

3. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af Single Sign-On

I dette afsnit, skal teste du Microsoft Azure AD Single Sign-On konfigurationen ved hjælp af panelet adgang.

Når du klikker på feltet Predictix udvalg planlægning i panelet adgang, du skal få automatisk logget på din planlægning af Predictix udvalg-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_205.png

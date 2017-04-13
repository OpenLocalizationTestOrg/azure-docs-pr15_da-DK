<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Origami | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Origami."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Selvstudium: Azure Active Directory-integration med Origami

I dette selvstudium lærer du at integrere Origami med Azure Active Directory (Azure AD).

Integrering af Origami med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Origami
- Du kan aktivere dine brugere til automatisk få logget på til Origami (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Origami skal bruge du følgende elementer:

- Et Azure AD-abonnement
- Origami single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Origami fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-origami-from-the-gallery"></a>Tilføje Origami fra galleriet
Hvis du vil konfigurere integrationen mellem Origami Azure AD, skal du tilføje Origami fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Origami fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Active Directory][1]
2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Origami**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-origami-tutorial/tutorial_origami_01.png)
7. Vælg **Origami**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-origami-tutorial/tutorial_origami_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med Origami baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Origami er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Origami oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Origami.

For at konfigurere og teste Azure AD single sign-on – med Origami, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette et Origami teste bruger](#creating-a-origami-test-user)** - skal have et modstykke Britta Simon i Origami, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen klassisk og konfigurere Enkeltlogon i Origami programmet.


**Hvis du vil konfigurere Azure AD single sign-on – med Origami skal du udføre følgende trin:**

1. I portalen klassisk, på siden **Origami** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge Origami** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_04.png) 

    en. Skriv URL-adressen bruges af dine brugere at sign-on til Origami programmet ved hjælp af følgende mønster i tekstfeltet **Log på URL-adresse** : **https://live.origamirisk.com/origami/account/login?account=\<firmanavn\> **
    
    b. Klik på **Næste**
 
4. På siden **Konfigurer single sign-on – på Origami** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_05.png)

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.



1. Log på kontoen Origami med administratorrettigheder.

1. Klik på **administrator**i menuen øverst.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
  

1. På siden enkelt Log på konfiguration dialogboksen skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/123.png)

    en. Vælg **Aktiver Single Sign på**.

    b. Kopiere **SAML SSO URL-adressen**på Azure-klassisk portalen, og Indsæt det i tekstfeltet **Identitet udbyderens logon URL-adressen** .

    c. Kopiere **Enkelt log af URL-adressen**på Azure-klassisk portalen, og Indsæt det i tekstfeltet **Identitet udbyderens Sign-out URL-adressen** .

    d. Klik på **Gennemse** for at overføre det certifikat, du har hentet fra Azure klassisk portal.

    e. Klik på **Gem ændringer**.


6. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
 
    ![Azure AD-Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.


![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-origami-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:  ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-origami-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-origami-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-origami-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-origami-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-an-origami-test-user"></a>Oprette en Origami testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i Origami. 

1. Log på kontoen Origami med administratorrettigheder.

2. Klik på **administrator**i menuen øverst.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

3. Dialogboksen **brugere og sikkerhed** , klik på **brugere**.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)

4. Klik på **Tilføj ny bruger**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)

5. I dialogboksen Tilføj ny bruger skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)

    en. I tekstfeltet **Brugernavn** skal du skrive bruger navn af Britta Simon i portalen Azure klassisk.

    b. I tekstfeltet **adgangskode** skal du skrive en passwotd.

    c. Skriv adgangskoden igen i tekstfeltet **Bekræft adgangskode** .

    d. Skriv **Britta**i tekstfeltet **Fornavn** .

    e. Skriv **Thomas**i tekstfeltet **Efternavn** .

    f. Klik på **Gem**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)

1. Tildele **Brugerroller** og **Klientadgang** til brugeren. 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til Origami.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Origami, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Origami**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_50.png) 

3. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af Single Sign-On

I dette afsnit, skal teste du din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet Access.

Når du klikker på feltet Origami i panelet adgang, du bør få automatisk logget på Origami-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-origami-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-origami-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-origami-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-origami-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-origami-tutorial/tutorial_general_205.png

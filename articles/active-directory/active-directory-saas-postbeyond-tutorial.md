<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med PostBeyond | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og PostBeyond."
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


# <a name="tutorial-azure-active-directory-integration-with-postbeyond"></a>Selvstudium: Azure Active Directory-integration med PostBeyond

I dette selvstudium lærer du at integrere PostBeyond med Azure Active Directory (Azure AD).

Integrering af PostBeyond med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til PostBeyond
- Du kan aktivere dine brugere til automatisk få logget på til PostBeyond (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med PostBeyond, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- **PostBeyond** enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø. Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje PostBeyond fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-postbeyond-from-the-gallery"></a>Tilføje PostBeyond fra galleriet
Hvis du vil konfigurere integration af PostBeyond til Azure AD, skal du tilføje PostBeyond fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje PostBeyond fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **PostBeyond**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_01.png)

7. Vælg **PostBeyond**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med PostBeyond baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i PostBeyond er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i PostBeyond oprettes.
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i PostBeyond.

For at konfigurere og teste Azure AD single sign-on – med PostBeyond, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en PostBeyond teste bruger](#creating-a-PostBeyond-test-user)** - skal have et modstykke Britta Simon i PostBeyond, der er knyttet til Azure AD-repræsentation af hun.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i PostBeyond programmet.


**Hvis du vil konfigurere Azure AD single sign-on – med PostBeyond skal du udføre følgende trin:**

1. Klik på **Hurtig Start**i menuen øverst.

    ![Konfigurere Single Sign-On][6]

2. I portalen klassisk, på siden **PostBeyond** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On][7] 

3. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge PostBeyond** , og klik derefter på **Næste**.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_06.png)

4. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin: 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_07.png)


    en. Skriv en URL-adresse ved hjælp af følgende mønster i tekstfeltet Log på URL-adresse: `https://app.postbeyond.com`. 

    b. Klik på **Næste**.

5. Klik på på siden **Konfigurer single sign-on – på PostBeyond** **Hent certifikat**, og derefter gemme filen på din computer. Også kopiere udsteder URL-adresse, single sign-on-tjenesten URL-adresse og enkelt hvor tjenesten URL-adressen værdier. Du skal dele disse oplysninger med PostBeyond support for at få konfigureret SSO.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_08.png)

6. For at få SSO konfigureret for dit program skal du kontakte PostBeyond supportteam på <sso@postbeyond.com>. De kan hjælpe med at stort kanalen til at konfigurere SSO og give dem følgende: 

    - Det hentede certifikat
    - **Udsteder URL-adresse**
    - **SAML SSO URL-adresse**
    - **Enkelt hvor URL-adressen**

7. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

8. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
    
    ![Azure AD-Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:
 
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-postbeyond-test-user"></a>Oprette en PostBeyond testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i PostBeyond. Hvis du ikke ved, hvordan du tilføjer Britta Simon i PostBeyond, skal du samarbejde med PostBeyond supportteam til at føje brugeren test og aktivere SSO. Kontakte vedkommende på <sso@postbeyond.com>.

### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure single sign-on – ved at give adgang til PostBeyond.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til PostBeyond, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **PostBeyond**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_09.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen alle brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på feltet PostBeyond i panelet adgang, skal du åbne PostBeyond siden Log på. Klik på **Log på med Office 365**, skal du angive din Azure AD-legitimationsoplysninger. Derefter skal skal du være logget på i PostBeyond.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_205.png

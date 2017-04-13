<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med vxMaintain | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og vxMaintain."
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
    ms.date="10/18/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>Selvstudium: Azure Active Directory-integration med vxMaintain

I dette selvstudium lærer du at integrere vxMaintain med Azure Active Directory (Azure AD).

Integrering af vxMaintain med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til vxMaintain
- Du kan aktivere dine brugere til automatisk få logget på til vxMaintain (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med vxMaintain, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- VxMaintain enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje vxMaintain fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-vxmaintain-from-the-gallery"></a>Tilføje vxMaintain fra galleriet
Hvis du vil konfigurere integrationen mellem vxMaintain Azure AD, skal du tilføje vxMaintain fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje vxMaintain fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Active Directory][1]
2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **vxMaintain**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_01.png)
7. Vælg **vxMaintain**i resultatruden, og klik derefter på **udført** for at tilføje programmet.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med vxMaintain baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i vxMaintain er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i vxMaintain oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i vxMaintain.

For at konfigurere og teste Azure AD single sign-on – med vxMaintain, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en vxMaintain teste bruger](#creating-a-vxmaintain-test-user)** - skal have et modstykke af Britta Simon i vxMaintain, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen klassisk og konfigurere Enkeltlogon i vxMaintain programmet.


**Hvis du vil konfigurere Azure AD single sign-on – med vxMaintain skal du udføre følgende trin:**

1. I portalen klassisk, på siden **vxMaintain** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge vxMaintain** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_04.png) 

    en. Skriv URL-adressen ved hjælp af følgende mønster i tekstfeltet **Id URL-adresse** : **https://\<firmanavn\>. verisae.com**
    
    b. Skriv URL-adressen ved hjælp af følgende mønster i tekstfeltet **Svar URL-adresse** : **https://\<firmanavn\>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true** 

    c. Klik på **Næste**
 
4. På siden **Konfigurer single sign-on – på vxMaintain** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_05.png)

    en. Klik på **Hent metadata**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


5. For at få SSO konfigureret for dit program, skal du Henvend dig til Verisae konto ledelsen og han hjælper dig med installationen SSO for organisationen. Give dem følgende oplysninger:

    • Hentede **metadata**

6. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
 
    ![Azure AD-Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.


![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:  ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-an-vxmaintain-test-user"></a>Oprette en vxMaintain testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i vxMaintain. Skal du Henvend dig til Verisae konto ledelsen og han hjælper dig med at tilføje brugere i vxMaintain platformen.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til vxMaintain.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til vxMaintain, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **vxMaintain**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_50.png) 

3. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af Single Sign-On

I dette afsnit, skal teste du din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet Access.

Når du klikker på feltet vxMaintain i panelet adgang, du bør få automatisk logget på vxMaintain-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_205.png

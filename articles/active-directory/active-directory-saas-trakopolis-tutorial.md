<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Trakopolis | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Trakopolis."
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


# <a name="tutorial-azure-active-directory-integration-with-trakopolis"></a>Selvstudium: Azure Active Directory-integration med Trakopolis

Formålet med dette selvstudium er at se, hvordan du integrerer Trakopolis med Azure Active Directory (Azure AD).  
Integrering af Trakopolis med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Trakopolis
- Du kan aktivere dine brugere til automatisk få logget på til Trakopolis (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - Azure Active Directory 

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Trakopolis, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Trakopolis enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Trakopolis fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-trakopolis-from-the-gallery"></a>Tilføje Trakopolis fra galleriet
Hvis du vil konfigurere integrationen mellem Trakopolis Azure AD, skal du tilføje Trakopolis fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Trakopolis fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.
 
    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Trakopolis**i søgefeltet.

    ![Programmer](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_01.png)

7. Vælg **Trakopolis**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Programmer](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med Trakopolis baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Trakopolis til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Trakopolis oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Trakopolis.

For at konfigurere og teste Azure AD single sign-on – med Trakopolis, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en Trakopolis teste bruger](#creating-a-trakopolis-test-user)** - skal have et modstykke Britta Simon i Trakopolis, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i Trakopolis programmet.



**Hvis du vil konfigurere Azure AD single sign-on – med Trakopolis skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Trakopolis** application integration.

    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Trakopolis** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:
 
    ![Konfigurere Single Sign-On](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_04.png) 


    en. Skriv URL-adressen bruges af dine brugere at sign-on til dit Trakopolis program ved hjælp af følgende mønster i tekstfeltet Log på URL-adresse: **"https://\<firmanavn\>. trakopolis.com"**.

     b. Klik på **Næste**.

4. På siden **Konfigurer single sign-on – på Trakopolis** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_05.png) 

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


5. Kontakt supportteamet Trakopolis på for at få konfigureret for dit program SSO, [support@cantelematics.com](mailto:support@cantelematics.com), vedhæfte de hentede certifikat og give dem **Udsteder URL-adresse**, **SAML SSO URL-adresse** og **Log af URL-adresse**.


6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen, og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Azure AD-Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-trakopolis-test-user"></a>Oprette en Trakopolis test, der bruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i Trakopolis.  
Skal du samarbejde med supportteam Trakopolis at føje brugerne i Trakopolis. 



### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til Trakopolis.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Trakopolis, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Trakopolis**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_50.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet Trakopolis i panelet adgang, du bør få automatisk logget på Trakopolis-program.


## <a name="additional-resources"></a>Yderligere ressourcer

- [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)

- [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_205.png

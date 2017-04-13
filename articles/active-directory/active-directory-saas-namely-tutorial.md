<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med nemlig | Microsoft Azure"
    description="Lær, hvordan du konfigurere single sign-on mellem Azure Active Directory og nemlig."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="prasannas"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Selvstudium: Azure Active Directory-integration med nemlig

Formålet med dette selvstudium er at se, hvordan du integrerer nemlig med Azure Active Directory (Azure AD).

Integrere nemlig med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, hvem der har adgang til nemlig 
- Du kan aktivere brugerne mulighed for at få automatisk logget på til nemlig (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Hvis du vil konfigurere Azure AD integration med nemlig, du har brug for følgende elementer:

- Et Azure AD-abonnement
- En nemlig single-sign-on aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø. 

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje nemlig fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-namely-from-the-gallery"></a>Tilføje nemlig fra galleriet
Hvis du vil konfigurere integrationen mellem nemlig Azure AD, skal du tilføje nemlig fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje nemlig fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **nemlig**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-namely-tutorial/tutorial_namely_01.png)

7. Vælg **nemlig**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-namely-tutorial/tutorial_namely_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med nemlig baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i nemlig til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i nemlig oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i nemlig.
 
For at konfigurere og teste Azure AD single sign-on – med nemlig, du har brug at fuldføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprettelse af en nemlig teste bruger](#creating-a-namely-test-user)** - skal have et modstykke Britta Simon i nemlig, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i din nemlig program. 




**For at konfigurere Azure AD single sign-on – med nemlig skal du udføre følgende trin:**

1. I Azure klassisk-portalen på **nemlig** integration siden, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge på nemlig** .
 
    ![Konfigurere Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen udføre følgende trin:.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_04.png) 

    en. Skriv URL-adressen bruges af dine brugere til at logge din nemlig program i tekstfeltet **Log på URL-adresse** (f.eks.: *https://fabrikam.Namely.com/*).

    b. Klik på **Næste**.
 
 
4. På siden **konfigurere single sign-on på nemlig** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_05.png) 

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


1. I en anden browser-vindue, log på din nemlig virksomheds websted som administrator.

1. Klik på **firma**i værktøjslinjen øverst.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png) 

1. Klik på fanen **Indstillinger** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png) 


1. Klik på **SAML**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png) 


1. På siden **Indstillinger for SAML** du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png) 

    en. Klik på **Aktiver SAML**. 

    b. Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på siden **konfigurere single sign-on på nemlig** dialogboksen, og Indsæt det i tekstfeltet **identitet udbyder DDO URL-adresse** . 

    c. Åbn dit hentede certifikat i Notesblok, kopiere indholdet og derefter indsætte det i tekstfeltet **identitet udbyder certifikat** .    

    d. Klik på **Gem**.


6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen, og klik derefter på **Næste**. 

    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Azure AD-Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

![Oprette Azure AD-bruger][20]


**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-namely-tutorial/create_aaduser_09.png)  

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) 
 
4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-namely-tutorial/create_aaduser_05.png)  

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-namely-tutorial/create_aaduser_06.png) 
 
    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .
    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-namely-tutorial/create_aaduser_07.png) 
 
8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-namely-tutorial/create_aaduser_08.png) 
  
    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   

  
 
### <a name="creating-a-namely-test-user"></a>Oprette en nemlig teste bruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon nemlig.

**For at oprette en bruger, kaldet Britta Simon nemlig skal du udføre følgende trin:**

1. Log på din nemlig virksomheds websted som administrator.

1. Klik på **personer**på værktøjslinjen øverst.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png) 

1. Klik på fanen **mappe** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) 

1. Klik på **Tilføj ny Person**.



1. I dialogboksen **Tilføj ny Person** skal du udføre følgende trin:

    en. Skriv **Britta**i tekstfeltet **Fornavn** .

    b. Skriv **Thomas**i tekstfeltet **Efternavn** .

    c. I tekstfeltet **mail** skal du skrive Brittas mailadresse på portalen Azure klassisk.

    d. Klik på **Gem**.





### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til nemlig.

![Tildele bruger][200] 

**Sådan tildeles Britta Simon nemlig, udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **nemlig**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_50.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på den nemlig flisen i panelet adgang, du bør få automatisk logget på til din nemlig program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-namely-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-namely-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-namely-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-namely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-namely-tutorial/tutorial_general_205.png







<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med PerformanceCentre | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og PerformanceCentre."
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
    ms.date="09/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Selvstudium: Azure Active Directory-integration med PerformanceCentre

Formålet med dette selvstudium er at se, hvordan du integrerer PerformanceCentre med Azure Active Directory (Azure AD).  
Integrering af PerformanceCentre med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, der har adgang til PerformanceCentre 
- Du kan aktivere dine brugere til automatisk få logget på til PerformanceCentre (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure Active Directory klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Hvis du vil konfigurere Azure AD-integration med PerformanceCentre, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- PerformanceCentre enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af tre primære dokumentkomponenter:

1. Tilføje PerformanceCentre fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-performancecentre-from-the-gallery"></a>Tilføje PerformanceCentre fra galleriet
Hvis du vil konfigurere integrationen mellem PerformanceCentre Azure AD, skal du tilføje PerformanceCentre fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje PerformanceCentre fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **PerformanceCentre**i søgefeltet.
 
    ![Programmer][5]

7. Vælg **PerformanceCentre**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Programmer][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med PerformanceCentre baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i PerformanceCentre til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i PerformanceCentre oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i PerformanceCentre.
 
For at konfigurere og teste Azure AD single sign-on – med PerformanceCentre, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en PerformanceCentre teste bruger](#creating-a-halogen-software-test-user)** - skal have et modstykke Britta Simon i PerformanceCentre, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure AD klassisk og for at konfigurere single sign-on i PerformanceCentre programmet.

**Hvis du vil konfigurere Azure AD single sign-on – med PerformanceCentre skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure AD klassisk portalen, på siden **PerformanceCentre** application integration.

    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge PerformanceCentre** , og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][7] 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Azure AD-Single Sign-On][8] 
 
     en. Skriv URL-adressen bruges af dine brugere at logge på webstedet PerformanceCentre i tekstfeltet **Log på URL-adresse** (f.eks.: *http://companyname.performancecentre.com/saml/SSO*).
 
     b. Klik på **Næste**.
 
4. På siden **Konfigurer single sign-on – på PerformanceCentre** skal du udføre følgende trin:

    ![Azure AD-Single Sign-On][9] 

    en. Klik på **Hent metadata**, og derefter gemme filen på din computer.



1. Log på webstedet **PerformanceCentre** virksomhed som administrator.

2. Klik på **Konfigurer**under fanen på venstre side.

    ![Azure AD-Single Sign-On][10]

2. Klik på **Diverse**under fanen på venstre side, og klik derefter på **Single Sign-On**.

    ![Azure AD-Single Sign-On][11]

2. Vælg **SAML**som **protokol**.

    ![Azure AD-Single Sign-On][12]

2. Åbne hentede metadatafilen i Notesblok, kopiere indholdet, sætte det ind i tekstfeltet **Identitet udbyder Metadata** , og klik derefter på **Gem**.

    ![Azure AD-Single Sign-On][13]

2. Kontrollér, at værdierne for **Enhed Base URL-adresse** og **Enhed ID URL-adressen** er korrekte.

    ![Azure AD-Single Sign-On][14]


6. Vælge enkelt sign-on – konfiguration bekræftelsen på portalen Azure AD klassisk, og klik derefter på **Næste**. 

    ![Azure AD-Single Sign-On][15]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Azure AD-Single Sign-On][16]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.  

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
 
4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
 
    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .
    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
 
8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
  
    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   

  
 
### <a name="creating-a-performancecentre-test-user"></a>Oprette en PerformanceCentre testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i PerformanceCentre.

**Hvis du vil oprette en bruger, kaldet Britta Simon i PerformanceCentre, skal du udføre følgende trin:**

1. Logge webstedet PerformanceCentre virksomhed som administrator.

2. Klik på **Interrelate**i menuen til venstre, og klik derefter på **Opret deltager**.

    ![Oprette bruger][400]

4. I dialogboksen **Interrelate - oprette deltager** skal du udføre følgende trin:

    ![Oprette bruger][401]

    en. Skriv de nødvendige attributter for Britta Simon i relaterede tekstbokse.
    
    > [AZURE.IMPORTANT] Brittas brugernavn attribut i PerformanceCentre skal være den samme som brugernavnet i Azure AD.


    b. Vælg **Klient Administrator** som **Vælg rolle**. 

    c. Klik på **Gem**.   


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til PerformanceCentre.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til PerformanceCentre, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201]

2. Vælg **PerformanceCentre**i listen over programmer.

    ![Tildele bruger][202]

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203]

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet PerformanceCentre i panelet adgang, du bør få automatisk logget på PerformanceCentre-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png




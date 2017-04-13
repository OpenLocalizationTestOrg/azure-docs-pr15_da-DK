<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med hvori indgår halogenforbindelser Software"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og hvori indgår halogenforbindelser Software."
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


# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Selvstudium: Azure Active Directory-integration med hvori indgår halogenforbindelser Software

Formålet med dette selvstudium er at se, hvordan du integrerer hvori indgår halogenforbindelser Software med Azure Active Directory (Azure AD).

Integrering af hvori indgår halogenforbindelser Software med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, der har adgang til hvori indgår halogenforbindelser Software 
- Du kan aktivere dine brugere til automatisk få logget på for hvori indgår halogenforbindelser Software (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Hvis du vil konfigurere Azure AD-integration med hvori indgår halogenforbindelser Software, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Hvori indgår halogenforbindelser Software single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø. 

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje hvori indgår halogenforbindelser Software fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-halogen-software-from-the-gallery"></a>Tilføje hvori indgår halogenforbindelser Software fra galleriet
Hvis du vil konfigurere integrationen mellem hvori indgår halogenforbindelser Software Azure AD, skal du tilføje hvori indgår halogenforbindelser Software fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje hvori indgår halogenforbindelser Software fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden. 

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv i søgefeltet, **hvori indgår halogenforbindelser software**.

    ![Programmer][5]

7. Vælg **Hvori indgår halogenforbindelser Software**i resultatruden, og klik derefter på **udført** for at tilføje programmet.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med hvori indgår halogenforbindelser Software, der er baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i hvori indgår halogenforbindelser softwaren til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i hvori indgår halogenforbindelser Software oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i hvori indgår halogenforbindelser Software.
 
For at konfigurere og teste Azure AD single sign-on – med hvori indgår halogenforbindelser Software, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD enkelt Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en hvori indgår halogenforbindelser Software teste bruger](#creating-a-halogen-software-test-user)** - skal have et modstykke Britta Simon i hvori indgår halogenforbindelser Software, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Konfiguration af Azure AD enkelt Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i hvori indgår halogenforbindelser programmet.


**Hvis du vil konfigurere Azure AD single sign-on – med hvori indgår halogenforbindelser Software skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Hvori indgår halogenforbindelser Software** application integration.

    ![Konfigurere Single Sign-On][8]

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge hvori indgår halogenforbindelser Software** , og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][9]

3. På siden **Konfigurer App indstillinger** dialogboksen udføre følgende trin:  ![Konfigurer App-indstillinger][10]
 
     en. i tekstfeltet **Log på URL-adresse** skal du skrive din URL-adresse, der bruges af dine brugere til at logge din hvori indgår halogenforbindelser Software-program ved hjælp af følgende mønster: *https://global.hgncloud.com/fabrikam/welcome.jsp*

     b. Klik på **Næste**.
 
4. Klik på **Hent metadata**på siden **Konfigurer single sign-on – på hvori indgår halogenforbindelser Software** , og Gem derefter metadatafilen lokalt på din computer.
    
    ![Hvad er Azure AD-forbindelse][11]

5. I et andet browservindue sign-on til **Hvori indgår halogenforbindelser** programmet som administrator.

6. Klik på fanen **Indstillinger** . 

    ![Hvad er Azure AD-forbindelse][12]


7. Klik på **SAML konfiguration**i den venstre navigationsrude. 

    ![Hvad er Azure AD-forbindelse][13]

8. På siden **SAML konfiguration** skal du udføre følgende trin:  ![Hvad er Azure AD-forbindelse][14]

    en. Vælg **NameID**som **Entydigt id**.

    b. **Entydigt id kort til**, skal du vælge **brugernavn**.

    c. For at overføre metadatafilen hentede, skal du klikke på **Gennemse** for at vælge fil, og klik derefter **Overfør fil**.

    d. Klik på **Kør Test**for at teste konfigurationen. 

    > [AZURE.NOTE] Du skal vente til meddelelsen "*SAML test er fuldført. Luk vinduet*". Luk derefter åbnet browservinduet. Afkrydsningsfeltet **Aktivér SAML** aktiveres kun, hvis en test er afsluttet.

    e. Vælg **Aktiver SAML**.
    
    f. Klik på **Gem ændringer**. 


9. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** . 

    ![Hvad er Azure AD-forbindelse][15]

10. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Hvad er Azure AD-forbindelse][16]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Hvad er Azure AD-forbindelse][100] 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Hvad er Azure AD-forbindelse][101] 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Hvad er Azure AD-forbindelse][102] 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Hvad er Azure AD-forbindelse][103] 
 
    en. Vælg **ny bruger i organisationen**som **Type af bruger**.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på Næste.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Hvad er Azure AD-forbindelse][104] 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** txtbox, type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Hvad er Azure AD-forbindelse][105]  

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Hvad er Azure AD-forbindelse][106]   

    en. Notér værdien af den **Nye adgangskode**.
    b. Klik på **udført**.   
  
 
### <a name="creating-a-halogen-software-test-user"></a>Oprette en hvori indgår halogenforbindelser Software testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i hvori indgår halogenforbindelser Software.

**For at oprette en bruger, kaldet Britta Simon i hvori indgår halogenforbindelser Software skal du udføre følgende trin:**

1. Log din **Hvori indgår halogenforbindelser** program som administrator.

2. Klik på fanen **Bruger Center** , og klik derefter på **Opret bruger**.

    ![Hvad er Azure AD-forbindelse][300]  

3. På siden **Ny bruger** dialogboksen skal du udføre følgende trin:

    ![Hvad er Azure AD-forbindelse][301]

    en. Skriv **Britta**i tekstfeltet **Fornavn** . 
  
    b. Skriv **Thomas**i tekstfeltet **Efternavn** .
  
    c. I tekstfeltet **brugernavn** skal du skrive **Brita Thomas brugernavn i portalen Azure klassisk**.
  
    d. I tekstfeltet **adgangskode** skal du skrive en adgangskode til Britta.
  
    e. Klik på **Gem**.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til hvori indgår halogenforbindelser Software.

![Hvad er Azure AD-forbindelse][200]

**Hvis du vil tildele Britta Simon, hvori indgår halogenforbindelser Software skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Hvad er Azure AD-forbindelse][201]

2. Vælg **Hvori indgår halogenforbindelser Software**i listen over programmer.

    ![Hvad er Azure AD-forbindelse][202]

1. Klik på **brugere**i menuen øverst.

    ![Hvad er Azure AD-forbindelse][203]

1. Vælg **Britta Thomas**på listen brugere.

    ![Hvad er Azure AD-forbindelse][204]

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Hvad er Azure AD-forbindelse][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på feltet hvori indgår halogenforbindelser Software i panelet adgang, du bør få automatisk logget på hvori indgår halogenforbindelser Software-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png
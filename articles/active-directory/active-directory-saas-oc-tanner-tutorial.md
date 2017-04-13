<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med O.C. Nielsen - AppreciateHub | Microsoft Azure"
    description="Lær, hvordan du konfigurere single sign-on mellem Azure Active Directory og O.C. Nielsen - AppreciateHub."
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
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Selvstudium: Azure Active Directory-integration med O.C. Nielsen - AppreciateHub

Formålet med dette selvstudium er at se, hvordan du integrerer O.C. Nielsen - AppreciateHub med Azure Active Directory (Azure AD).  
Integrering af O.C. Nielsen - AppreciateHub med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, der har adgang til O.C. Nielsen - AppreciateHub 
- Du kan aktivere dine brugere til automatisk få logget på til O.C. Nielsen - AppreciateHub (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Konfigurere Azure AD-integration med O.C. Nielsen - AppreciateHub, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- EN O.C. Nielsen - AppreciateHub enkelt-sign-on aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af tre primære dokumentkomponenter:

1. Tilføje O.C. Nielsen - AppreciateHub fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Tilføje O.C. Nielsen - AppreciateHub fra galleriet
Konfigurere integration af O.C. Nielsen - AppreciateHub til Azure AD, skal du tilføje O.C. Nielsen - AppreciateHub fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje O.C. Nielsen - AppreciateHub fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1] 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2] 

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3] 

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4] 

6. Skriv **O.C. Nielsen - AppreciateHub**i søgefeltet.

    ![Programmer][5] 

7. Vælg **O.C. Nielsen - AppreciateHub**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Programmer][25] 




##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –

Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med O.C. Nielsen - AppreciateHub baseret på en testbruger ved navn "Britta Thomas".

For single sign-on til arbejde, skal Azure AD vide, hvilke modstykke brugeren i O.C. Nielsen - AppreciateHub til en bruger i Azure AD er. Med andre ord, et link forholdet mellem en Azure AD-bruger og den pågældende bruger i O.C. Nielsen - AppreciateHub skal oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i O.C. Nielsen - AppreciateHub.
 
Til at konfigurere og teste Azure AD single sign-on – med O.C. Nielsen - AppreciateHub, du har brug at fuldføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en O.C. Nielsen - AppreciateHub testbruger](#creating-a-halogen-software-test-user)** - skal have et modstykke Britta Simon i O.C. Nielsen - AppreciateHub, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i din O.C. Nielsen - programmet på computeren AppreciateHub.


**Hvis du vil konfigurere Azure AD single sign-on med O.C. Nielsen - AppreciateHub, skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **O.C. Nielsen - AppreciateHub** application integration.

    ![Konfigurere Single Sign-On][6]

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge O.C. Nielsen - AppreciateHub** , og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][7]

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere indstillinger for][8]
 
     en. Åbn metadatafilen ved hjælp af følgende link: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).

     b. Find noden **md:AssertionConsumerService** . 

     c. Kopiere værdien for attributten **placering** . 

     ![Konfigurere indstillinger for][12]
     
     d. I tekstfeltet **Log på URL-adressen** skal tidligere værdien har du anskaffet i ovenstående trin.

     > [AZURE.NOTE] Hvis du er expiriencing problemer med at få svar URL-adressen fra metadatafilen, kan du kontakte O.C. Nielsen - AppreciateHub supportteam via [sso@octanner.com](mailto:sso@octanner.com).

     e. Klik på **Næste**.
 
4. Klik på **Hent metadata**på siden **Konfigurer single sign-on – på O.C. Nielsen - AppreciateHub** , og Gem derefter metadatafilen lokalt på din computer.

    ![Hvad er Azure AD-forbindelse][9]

5. Kontakt O.C. Nielsen - AppreciateHub supportteam via xyz, giver dem med metadatafilen, og dem du oplyse dem om, at de skal aktivere SSO for dig.


6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **Næste**. 

    ![Hvad er Azure AD-forbindelse][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Hvad er Azure AD-forbindelse][11]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.  

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 
 
4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
 
    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .
    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png) 
 
8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png) 
  
    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   

  
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Oprette en O.C. Nielsen - AppreciateHub testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i O.C. Nielsen - AppreciateHub.

**Hvis du vil oprette en bruger, kaldet Britta Simon i O.C. Nielsen - AppreciateHub, skal du udføre følgende trin:**

1. Bed supportteamet OC Nielsen til at oprette en bruger, der har som nameID attribut samme værdi som brugernavnet på Britta Simon i Azure AD.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til O.C. Nielsen - AppreciateHub.

![Tildele bruger][200]

**Hvis du vil tildele Britta Simon O.C. Nielsen - AppreciateHub, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201]

2. Vælg **O.C. Nielsen - AppreciateHub**i listen over programmer.

    ![Tildele bruger][202]

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203]

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på O.C. Nielsen - AppreciateHub flise i panelet adgang, du bør få automatisk logget på til din O.C. Nielsen - programmet på computeren AppreciateHub.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png







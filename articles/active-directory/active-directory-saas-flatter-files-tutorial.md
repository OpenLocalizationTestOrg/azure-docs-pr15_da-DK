<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med fladere filer | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og fladere filer."
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Selvstudium: Azure Active Directory-integration med fladere filer

Formålet med dette selvstudium er at se, hvordan du integrerer fladere filer med Azure Active Directory (Azure AD).  
Integrering af fladere filer med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, der har adgang til fladere filer 
- Du kan aktivere dine brugere til automatisk få logget på til fladere filer (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure Active Directory klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Hvis du vil konfigurere Azure AD-integration med fladere filer, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Fladere filer single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje fladere filer fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-flatter-files-from-the-gallery"></a>Tilføje fladere filer fra galleriet
Hvis du vil konfigurere integration af fladere filer i Azure AD, skal du føje fladere filer fra galleriet til din liste over administrerede SaaS apps.

**Hvis du vil tilføje fladere filer fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Fladere filer**i søgefeltet.


    ![Oprette en Azure AD testbruger](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. Vælg **Fladere filer**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med fladere filer, der er baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i fladere filer til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i fladere filer oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i fladere filer.
 
For at konfigurere og teste Azure AD single sign-on – med fladere filer, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en fladere filer teste bruger](#creating-a-halogen-software-test-user)** - skal have et modstykke Britta Simon i fladere filer, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure AD klassisk og for at konfigurere single sign-on i programmet på fladere filer. Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure. Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

For at konfigurere single sign-on til fladere filer skal have du et registreret domæne. Hvis du ikke har et registreret domæne endnu, Kontakt filerne fladere dit supportteam via [support@flatterfiles.com](mailto:support@flatterfiles.com).  



**Hvis du vil konfigurere Azure AD single sign-on – med fladere filer skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure AD klassisk portalen, på siden **Fladere filer** application integration.

    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge fladere filer** , og klik derefter på **Næste**.
 
    ![Konfigurere Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) 

3. Klik på **Næste**på siden **Konfigurer indstillinger for App** -dialogboksen.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) 

    > [AZURE.NOTE] Fladere filer bruger den samme URL-adresse til SSO-logon til alle kunder: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
.
 
 
4. På siden **Konfigurer enkeltlogon fladere filer** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


1. Sign-on til dit fladere filer program som administrator.

2. Klik på Dashboard. 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  



2. Klik på **Indstillinger**, og derefter udføre følgende trin på fanen **firma** : 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  

    en. Vælg **Brug SAML 2.0 til godkendelse**.

    b. Klik på **Konfigurer SAML**.



2. I dialogboksen **SAML konfiguration** skal du udføre følgende trin: 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  

    en. I tekstfeltet domæne skal du skrive registrerede domænet.

    > [AZURE.NOTE] Hvis du ikke har et registreret domæne endnu, Kontakt filerne fladere dit supportteam via [support@flatterfiles.com](mailto:support@flatterfiles.com).
    
    b. I Azure klassisk-portalen på afkrydsningsfeltet Konfigurer enkelt sign-on på fladere filer i dialogboksen, copt enkelt Sign-On-tjenesten URL-adressen, og Indsæt det i tekstfeltet identitet udbyder URL-adresse.

    c.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

    >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    d.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **FlatterFiles identitet udbyder certifikat** .

    e. Klik på **Opdater**.

6. Vælge enkelt sign-on – konfiguration bekræftelsen på klassisk Azure AD-portalen, og klik derefter på **Næste**. 

    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Azure AD-Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
 
4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)  

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
 
    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .
    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
 
8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
  
    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   

  
 
### <a name="creating-a-flatter-files-test-user"></a>Oprette en fladere filer testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i fladere filer.

**Hvis du vil oprette en bruger, kaldet Britta Simon i fladere filer, skal du udføre følgende trin:**

1. Logge webstedet **Fladere filer** virksomhed som administrator.

2. Klik på **Indstillinger**i navigationsruden i venstre side, og derefter klikke på brugere **under fanen**.

    ![Cfreate en fladere filer bruger](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Klik på **Tilføj bruger**. 

4. I dialogboksen **Tilføj bruger** skal du udføre følgende trin:

    ![Cfreate en fladere filer bruger](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    en. Skriv **Britta**i tekstfeltet **Fornavn** .

    b. Skriv **Thomas**i tekstfeltet **Efternavn** . 

    c. I tekstfeltet **Mailadresse** skal du skrive Brittas mailadresse på portalen Azure klassisk.

    d. Klik på **Send**.   


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til fladere filer.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon fladere filer skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Fladere filer**i listen over programmer.

    ![Tildele bruger](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet fladere filer i panelet adgang, du bør få automatisk logget på i dit program fladere filer.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png







<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Novatus | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og LearnUpon."
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
    ms.date="08/15/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Selvstudium: Azure Active Directory-integration med LearnUpon

Formålet med dette selvstudium er at se, hvordan du integrerer LearnUpon med Azure Active Directory (Azure AD).  
Integrering af LearnUpon med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til LearnUpon
- Du kan aktivere dine brugere til automatisk få logget på til LearnUpon (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - Azure Active Directory klassisk 


Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med LearnUpon, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- LearnUpon enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje LearnUpon fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-learnupon-from-the-gallery"></a>Tilføje LearnUpon fra galleriet
Hvis du vil konfigurere integrationen mellem LearnUpon Azure AD, skal du tilføje LearnUpon fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje LearnUpon fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **LearnUpon**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)

7. Vælg **LearnUpon**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med LearnUpon baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i LearnUpon til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i LearnUpon oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i LearnUpon.

For at konfigurere og teste Azure AD single sign-on – med LearnUpon, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en LearnUpon teste bruger](#creating-a-learnupon-test-user)** - skal have et modstykke Britta Simon i LearnUpon, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i LearnUpon programmet.



**Hvis du vil konfigurere Azure AD single sign-on – med LearnUpon skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **LearnUpon** application integration.

    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge LearnUpon** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen udføre følgende trin:.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) 


    en. Skriv program Consumer Service URL-adressen ved hjælp af følgende mønster i tekstfeltet **Svar URL-adresse** :`https://\<companyname\>.learnupon.com/saml/consumer`

    b. Klik på **Næste**. 


4. På siden **Konfigurer single sign-on – på LearnUpon** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png) 

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer. Vi skal dette certifikat og Metadata URL-adresser, (enheds-ID, SSO log i URL-adresse og log af URL-adressen) til at konfigurere SSO LearnUpon side.

    b. Klik på **Næste**.




1. Åbn en anden forekomst af browseren, og log på LearnUpon med en administratorkonto. 

1. Klik på fanen **Indstillinger** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) 


1. Klik på **Single Sign-On - SAML**, og klik derefter på **Generelle indstillinger** for at konfigurere SAML-indstillinger.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 


5. I sektionen **Generelle indstillinger** , du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png) 

    en. Vælg **aktiveret**.

    b. Vælg **2.0**som **Version**.

    c. Vælg **Nej**som **Spring over betingelser**.

    d. Skriv navnet på parameter for anmodning om indlæg til SAML consumer URL-adresse, der er anført ovenfor, der indeholder SAML program til bekræftes og godkendte – for eksempel **SAMLResponse**i tekstfeltet **SAML Token skrive parameteren navn** .

    e. Skriv den værdi, der angiver, hvor i din SAML program brugere-id (mailadresse) er placeret – for eksempel i tekstfeltet **Id navneformat** **urn: oasis: navne: tc: SAML:1.1:nameid-format: mailadresse**.

    f. Skriv den værdi, der angiver, hvor brugerne sendes til, hvis de klikker på dine overførte ikon fra din Azure klassisk portalen logonskærmen i tekstfeltet **Identificere udbyder placering** .

    g.in Azure klassisk portalen, kopiere **Enkelt Sign-Out URL-adressen**, og Indsæt det i **logger af URL-adressen** textbos.

    h. Klik på **Administrer finger udskrives**, og overfør derefter fingeraftryk af dit hentede certifikat. 


1. Klik på **Indstillinger**, og derefter udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png) 

    en. Skriv den værdi, der fortæller os, hvor i din SAML program brugere Fornavn er placeret – for eksempel i tekstfeltet **Fornavn id Format** : **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ givenname**.

    b. Skriv den værdi, der fortæller os, hvor i din SAML program brugere Efternavn er placeret – for eksempel i tekstfeltet **Sidste navneformat id** : **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ efternavn**.


6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen, og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Azure AD-Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-learnupon-test-user"></a>Oprette en LearnUpon testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i LearnUpon. LearnUpon understøtter just-in-time klargøring, som er som standard aktiveret.

Der er ingen handlingspunkt for dig i dette afsnit. Der oprettes en ny bruger under et forsøg på at få adgang til LearnUpon, hvis den ikke findes. [Konfiguration af Azure AD-Single Sign-On](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Hvis du vil oprette en bruger manuelt, skal du kontakte LearnUpon supportteam.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til LearnUpon.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til LearnUpon, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **LearnUpon**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet LearnUpon i panelet adgang, du bør få automatisk logget på LearnUpon-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png

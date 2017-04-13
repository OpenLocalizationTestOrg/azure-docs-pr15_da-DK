<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med & frankly | Microsoft Azure"
    description="Lær, hvordan du konfigurere single sign-on mellem Azure Active Directory og & frankly."
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
    ms.date="08/12/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-frankly"></a>Selvstudium: Azure Active Directory-integration med & frankly

Formålet med dette selvstudium er at se, hvordan du integrerer & frankly med Azure Active Directory (Azure AD).

Integrere og frankly med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til og frankly
- Du kan aktivere brugerne mulighed for at få automatisk logget på, på & frankly (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD skal-integration med & frankly, du følgende elementer:

- Et Azure AD-abonnement
- A & frankly enkelt-sign-on aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje og frankly fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-frankly-from-the-gallery"></a>Tilføje og frankly fra galleriet
Hvis du vil konfigurere integration af & frankly til Azure AD, skal du tilføje & frankly fra galleriet til listen over administrerede SaaS apps.

**Tilføje & frankly fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.
    
    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.
    
    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **& frankly**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_01.png)

7. Vælg **og frankly**i resultatpanelet, og klik derefter på **udført** for at tilføje programmet.

    ![Valg af appen i galleriet](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at viser dig, hvordan du konfigurerer og test Azure AD enkelt enkeltlogon med & frankly baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal, hvad der er modstykke brugeren og frankly til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i & frankly oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i & frankly.

For at konfigurere og teste Azure AD single sign-on – med & frankly, du har brug at fuldføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprettelse af en & frankly testbruger](#creating-a-&frankly-test-user)** - skal have et modstykke Britta Simon i & frankly, som er knyttet til Azure AD-repræsentation af hun.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen klassisk og konfigurere single sign-on i din & frankly program.

**Konfigurere Azure AD single sign-on – med & frankly, udføre følgende trin:**

1. I portalen klassisk, på siden **& frankly** programmet integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge, på & frankly** , og klik derefter på **Næste**.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_03.png)

3. På siden **Konfigurer App indstillinger** dialogboksen Hvis du vil konfigurere programmet i **IDP startet tilstand**, skal du udføre følgende trin og klikke på **Næste**:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_04.png)

    en. I tekstfeltet **id** skal du skrive en URL-adresse ved hjælp af følgende mønster:`https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/metadata.php/<tenant id>`

    b. I tekstfeltet **Svar URL-adresse** skal du skrive en URL-adresse ved hjælp af følgende mønster:`https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/saml2-acs.php/<tenant id>`

    c. Klik på **Næste**

4. Hvis du vil konfigurere programmet i **SP startet tilstand** på siden **Konfigurer App-indstillinger** i dialogboksen, klik på **"Vis avancerede indstillinger (valgfrit)"** og derefter angive **Log på URL-adresse** og klikke på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_05.png)

    en. I tekstfeltet **Log på URL-adresse** skal du skrive en URL-adresse ved hjælp af følgende mønster:`https://andfrankly.com/saml/okta/?saml_sso=<tenant id>`

    b. Klik på **Næste**

    > [AZURE.NOTE] Vær opmærksom på, at de ikke er de reelle værdier. Du skal opdatere disse værdier med faktiske Log på URL-adresse, -id og svar URL-adresse. Kontakt [help@andfrankly.com](emailTo:help@andfrankly.com) at få disse værdier.

5. På den **konfigurere single sign-on på & frankly** skal du udføre følgende trin og klikke på **Næste**:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_06.png)

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.

6. For at få SSO konfigureret for dit program skal du kontakte din & frankly supportteam via [help@andfrankly.com](emailTo:help@andfrankly.com). Vedhæfte metadatafilen hentede og dele den med og frankly team for at konfigurere SSO på deres side.

7. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

8. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
    
    ![Azure AD-Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_09.png)

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_03.png)

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_04.png)

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_05.png)

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_06.png)

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_07.png)

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_08.png)

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-frankly-test-user"></a>Oprette en & frankly testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i & frankly. Skal du samarbejde med & frankly supportteam via [help@andfrankly.com](emailTo:help@andfrankly.com) at tilføje brugere i & frankly platform.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til og frankly.
    
![Tildele bruger][200]

**Hvis du vil tildele Britta Simon på & frankly, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.
    
    ![Tildele bruger][201]

2. Vælg **og frankly**i listen over programmer.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_50.png)

1. Klik på **brugere**i menuen øverst.
    
    ![Tildele bruger][203]

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.
    
    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.
 
Når du klikker på & frankly side om side i panelet adgang, skal får du automatisk logget på til din & frankly program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_205.png

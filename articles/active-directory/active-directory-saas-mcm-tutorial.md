<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Hændelsesinitialisering | Microsoft Azure" 
    description="Lær, hvordan du bruger Hændelsesinitialisering med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/30/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-mcm"></a>Selvstudium: Azure Active Directory-integration med Hændelsesinitialisering
  
Formålet med dette selvstudium er at se, hvordan du integrerer Hændelsesinitialisering med Azure Active Directory (Azure AD).

Integrering af Hændelsesinitialisering med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Hændelsesinitialisering
- Du kan aktivere dine brugere til automatisk få logget på til Hændelsesinitialisering (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Hændelsesinitialisering, du skal bruge følgende elementer:

- Et gyldigt Azure-abonnement
- Hændelsesinitialisering enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Hændelsesinitialisering fra galleriet
2. Konfigurere og teste Azure AD single sign-on –

## <a name="adding-mcm-from-the-gallery"></a>Tilføje Hændelsesinitialisering fra galleriet
Hvis du vil konfigurere integrationen mellem Hændelsesinitialisering Azure AD, skal du tilføje Hændelsesinitialisering fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Hændelsesinitialisering fra galleriet, skal du udføre følgende trin:**

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Hændelsesinitialisering**.

    ![Galleri med programmet] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Galleri med programmet")

7.  Vælg **Hændelsesinitialisering**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Hændelsesinitialisering] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "Hændelsesinitialisering")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med Hændelsesinitialisering baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Hændelsesinitialisering til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Hændelsesinitialisering oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Hændelsesinitialisering.

For at konfigurere og teste Azure AD single sign-on – med Hændelsesinitialisering, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en Hændelsesinitialisering teste bruger](#creating-a-mcm-test-user)** - skal have et modstykke Britta Simon i Hændelsesinitialisering, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon
  
I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen klassisk og konfigurere Enkeltlogon i Hændelsesinitialisering programmet.

**Hvis du vil konfigurere Azure AD single sign-on – med Hændelsesinitialisering skal du udføre følgende trin:**

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Hændelsesinitialisering** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Hændelsesinitialisering** , og klik derefter på **Næste**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD Single Sign-On")

3.  På siden Konfigurer App indstillinger dialogboksen du udføre følgende trin:

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Konfigurere App URL-adresse")

    en. Skriv i tekstfeltet **Log på URL-adresse** : `https://myaba.co.uk/client-access/<company name>/saml.php`.
    
    b. Klik på **Næste**

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på Hændelsesinitialisering** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Konfigurere Single Sign-On")

5. Kontakt supportteamet Hændelsesinitialisering for at få SSO konfigureret for dit program. Vedhæfte metadatafilen hentede, og del den med Hændelsesinitialisering team til at konfigurere SSO i deres side.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Konfigurere Single Sign-On")

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Konfigurere Single Sign-On")


### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger

Formålet med dette afsnit er at oprette en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette en Azure AD testbruger](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   

###<a name="creating-a-mcm-test-user"></a>Oprette en Hændelsesinitialisering testbruger
  
I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i Hændelsesinitialisering. Skal du samarbejde med Hændelsesinitialisering supportteam at føje brugerne i Hændelsesinitialisering platformen.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden Hændelsesinitialisering bruger konto værktøjer til oprettelse eller API'er, som Hændelsesinitialisering til klargøring AAD brugerkonti.


###<a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren
  
Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til Hændelsesinitialisering.
    
![Tildele brugere] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Tildele brugere")

**Hvis du vil tildele Britta Simon til Hændelsesinitialisering, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.
    
    ![Tildele brugere] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Tildele brugere")

2. Vælg **Hændelsesinitialisering**i listen over programmer.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

1. Klik på **brugere**i menuen øverst.
    
    ![Tildele brugere] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Tildele brugere")

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.
    
    ![Tildele brugere] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Tildele brugere")


### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.
 
Når du klikker på feltet Hændelsesinitialisering i panelet adgang, du bør få automatisk logget på Hændelsesinitialisering-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
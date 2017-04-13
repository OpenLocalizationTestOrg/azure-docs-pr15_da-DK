<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Genkend | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Genkend."
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
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Selvstudium: Azure Active Directory-integration med Genkend

Formålet med dette selvstudium er at se, hvordan du integrerer Genkend med Azure Active Directory (Azure AD).

Integrering af Genkend med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Genkend
- Du kan aktivere dine brugere til automatisk få logget på til Genkend (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Genkend skal bruge du følgende elementer:

- Et Azure AD-abonnement
- Genkend single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Genkend fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-recognize-from-the-gallery"></a>Tilføje Genkend fra galleriet
Hvis du vil konfigurere integrationen mellem Genkend Azure AD, skal du tilføje Genkend fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Genkend fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.
    
    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.
    
    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Genkend**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)

7. Vælg **Genkend**i resultatpanelet, og klik derefter på **udført** for at tilføje programmet.

    ![Valg af appen i galleriet](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med Genkend baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Genkend til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Genkend oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Genkend.

For at konfigurere og teste Azure AD single sign-on – med Genkend, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en Genkend teste bruger](#creating-a-recognize-test-user)** - skal have et modstykke Britta Simon i Genkend, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen klassisk og konfigurere Enkeltlogon i Genkend programmet.

**Hvis du vil konfigurere Azure AD single sign-on – med Genkend skal du udføre følgende trin:**

1. I portalen klassisk, på siden **Genkend** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Genkend** , og klik derefter på **Næste**.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)

3. Udføre følgende trin, og klik på **Næste**på siden **Konfigurer App indstillinger** dialogboksen:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)

    en. I tekstfeltet **Log på URL-adresse** skal du skrive en URL-adresse ved hjælp af følgende mønster: `https://recognizeapp.com/<your-domain>/saml/sso`.

    b. I tekstfeltet **id** skal du skrive en URL-adresse ved hjælp af følgende mønster: `https://recognizeapp.com/<your-domain>/saml/metadata`.

    c. Klik på **Næste**

    > [AZURE.NOTE] Hvis du ikke ved, om disse URL-adresser, du Indtast eksempel URL-adresser med eksempel mønster. For at få disse værdier, kan du se trin 9 for flere oplysninger eller kontakt Genkend supportteam via <mailto:support@recognizeapp.com>.

4. Klik på **Hent certifikat** på siden **Konfigurer single sign-on – på Genkend** og derefter gemme filen på din computer:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)

5. I et andet browservindue sign-on til din Genkend lejer som administrator.

6. Klik på **Menu**på det øverste højre hjørne. Gå til **virksomhedens administrator**.

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

7. Klik på **Indstillinger**i den venstre navigationsrude.

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

8. Udfør følgende trin på sektionen **SSO-indstillinger** .

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)

    en. **Aktivere SSO**, vælge **til**.

    b. I feltet **IDP enheds-ID** placere tekstfeltet værdien af **Udsteder URL-adressen** fra Azure AD-program konfigurationsguiden.

    c. I **Sso URL-adresse** placere tekstfeltet værdien af **Enkelt Sign-On URL-adressen** fra Azure AD-program konfigurationsguiden.

    d. I **langsomme URL-adresse** placere tekstfeltet værdien af **Enkelt Sign-Out URL-adressen** fra Azure AD-program konfigurationsguiden.

    e. Åbne hentede certifikatfilen i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **certifikat** . 

    f. Klik på knappen **Gem indstillinger** . 

9. Kopiér URL-adressen under **Service Provider Metadata URL-adresse**ud for sektionen **SSO-indstillinger** .
    
    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

10. Åbn **URL-Metadata hyperlink** under en tom browser for at hente Metadatadokumentet. Brug værdien fra EntityDescriptor Genkend givet dig **-id** i dialogboksen **Konfigurer App-indstillinger** .

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

11. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

12. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
    
    ![Azure AD-Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png)

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. Skriv **Thomas**i tekstfeltet **Efternavn** .

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png)

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-recognize-test-user"></a>Oprette en Genkend testbruger

For at aktivere Azure AD-brugere at logge på Genkend, skal de klargøres til Genkend. Klargøring af er Genkend, en manuel opgave.

Denne app understøtter ikke SCIM klargøring, men har en anden bruger synkronisering, der danner brugere. 

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Hvis du vil tildele en brugerkonto, skal du udføre følgende trin:

1.  Log på webstedet Genkend virksomhed som administrator.

2.  Klik på **Menu**på det øverste højre hjørne. Gå til **virksomhedens administrator**.

3.  Klik på **Indstillinger**i den venstre navigationsrude.

4.  Udfør følgende trin på **Bruger synkronisering** sektion.
    
    ![Ny bruger] (./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "Ny bruger")

    en. Vælg **på**som **Synkronisering aktiveret**.

    b. **Vælg Synkroniser udbyder**, Vælg **Microsoft / Office 365**.

    c. Klik på **Kør bruger synkronisering**

### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til Genkend.
    
![Tildele bruger][200]

**Hvis du vil tildele Britta Simon til Genkend, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.
    
    ![Tildele bruger][201]

2. Vælg **Genkend**i listen over programmer.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)

3. Klik på **brugere**i menuen øverst.
    
    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. Klik på **Tildel**på værktøjslinjen nederst.
    
    ![Tildele bruger][205]

### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.
 
Når du klikker på feltet Genkend i panelet adgang, du bør få automatisk logget på Genkend-program.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png

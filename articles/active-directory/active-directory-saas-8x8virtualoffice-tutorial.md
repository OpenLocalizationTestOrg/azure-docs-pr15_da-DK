<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med 8 x 8 virtuelle Office | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og 8 x 8 virtuelle Office."
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


# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Selvstudium: Azure Active Directory-integration med 8 x 8 virtuelle Office

Formålet med dette selvstudium er at se, hvordan du integrerer 8 x 8 virtuelle Office med Azure Active Directory (Azure AD).

Integrering af 8 x 8 giver virtuelle Office med Azure AD dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til 8 x 8 virtuelle Office
- Du kan aktivere dine brugere til automatisk få logget på 8 x 8 virtuelle Office (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

For at konfigurere Azure AD-integration med 8 x 8 virtuelle Office skal bruge du følgende elementer:

- Et Azure AD-abonnement
- 8 x 8 virtuelle Office single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Microsoft Azure AD Single Sign-On i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje 8 x 8 virtuelle Office fra galleriet
2. Konfigurere og teste Microsoft Azure AD Single Sign-On


## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Tilføje 8 x 8 virtuelle Office fra galleriet
Hvis du vil konfigurere integrationen mellem 8 x 8 virtuelle Office Azure AD, skal du tilføje 8 x 8 virtuelle Office fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje 8 x 8 virtuelle Office fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude.

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.
    
    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **8 x 8 virtuelle Office**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. Vælg **8 x 8 virtuelle Office**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Valg af appen i galleriet](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Konfigurere og teste Microsoft Azure AD Single Sign-On
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Microsoft Azure AD Single Sign-On med 8 x 8 virtuelle Office baseret på en testbruger ved navn "Britta Simon".

For single sign-on til arbejde, skal Azure AD vide, hvilke modstykke brugeren i 8 x 8 virtuelle Office til en bruger i Azure AD er. Med andre ord, et link forholdet mellem en Azure AD-bruger og den pågældende bruger i 8 x 8 virtuelle Office skal oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i 8 x 8 virtuelle Office.

For at konfigurere og teste Microsoft Azure AD Single Sign-On med 8 x 8 virtuelle Office, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Microsoft Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Microsoft Azure AD Single Sign-On med Britta Simon.
3. **[Oprette en 8 x 8 virtuelle Office test-bruger](#creating-a-8x8-virtual-office-test-user)** - skal have et modstykke Britta Simon i 8 x 8 virtuelle Office, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Microsoft Azure AD Single Sign-On.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Konfiguration af Microsoft Azure AD Single Sign-On

I dette afsnit, skal du aktivere Microsoft Azure AD Single Sign-On i portalen klassisk og konfigurere Enkeltlogon i dit 8 x 8 virtuelle Office-program.

**Hvis du vil konfigurere Microsoft Azure AD Single Sign-On med 8 x 8 virtuelle Office skal du udføre følgende trin:**

1. I portalen klassisk, på siden **8 x 8 virtuelle Office** -program integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge på, for 8 x 8 virtuelle Office** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 

3. Udføre følgende trin, og klik på **Næste**på siden **Konfigurer App indstillinger** dialogboksen:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)

    en. I tekstfeltet **Svar URL-adresse** skal du skrive:`https://sso.8x8.com/saml2`

    b. Klik på **Næste**

4. Udføre følgende trin, og klik på **Næste**på siden **konfigurere single sign-on på 8 x 8 virtuelle Office** :

    ![Konfigurere Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.

5. Sign-on til din 8 x 8 virtuelle Office-lejer som administrator.
6. Vælg **virtuelle Office konto Mgr** på programmet Panel.

    ![Konfigurere på App-Side](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

7. Vælg **Business** -konto for at administrere og klikke på knappen **Log på** .

    ![Konfigurere på App-Side](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

8. Klik på under fanen **konti** i listen i menuen.

    ![Konfigurere på App-Side](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

9. Klik på **Single Sign-On** på listen over konti.

    ![Konfigurere på App-Side](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

10. Vælg **Signle Sign-On** under godkendelsesmetode, og klik på **SAML**.

    ![Konfigurere på App-Side](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

11. Kopiere **SAML SSO URL-adressen**, **enkelt synger ud af URL-adressen** og **udsteder URL-adressen** fra Azure AD til **logge på URL-adresse**, **Log af URL-adresse** og **udsteder URL-adressen** i 8 x 8 virtuelle Office. 

    ![Konfigurere på App-Side](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    ![Konfigurere på App-Side](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)

12. Klik på knappen for at overføre det certifikat, som du har hentet fra Azure AD **Browser** .

13. Klik på knappen **Gem** .

14. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][10]

15. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Azure AD-Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i portalen klassisk kaldet Britta Simon.
    
![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-8x8-virtual-office-test-user"></a>Oprette en 8 x 8 virtuelle Office testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i 8 x 8 virtuelle Office. 8 x 8 virtuelle Office understøtter just-in-time klargøring, som er som standard aktiveret.

Der er ingen handlingspunkt for dig i dette afsnit. Der oprettes en ny bruger under et forsøg på at få adgang til 8 x 8 virtuelle Office, hvis den ikke findes. 

> [AZURE.NOTE] Hvis du vil oprette en bruger manuelt, skal du kontakte 8 x 8 virtuelle Office supportteam.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til 8 x 8 virtuelle Office.
    
![Tildele bruger][200]

**Hvis du vil tildele Britta Simon 8 x 8 virtuelle Office, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201]

2. Vælg **8 x 8 virtuelle Office**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)

3. Klik på **brugere**i menuen øverst.
    
    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Microsoft Azure AD Single Sign-On konfiguration ved hjælp af panelet adgang.

Når du klikker på 8 x 8 virtuelle Office-feltet i panelet adgang, du bør få automatisk logget på din 8 x 8 virtuelle Office-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png

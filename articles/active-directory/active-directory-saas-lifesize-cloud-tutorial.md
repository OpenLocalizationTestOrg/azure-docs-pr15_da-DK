<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Lifesize skyen | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Lifesize skyen."
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
    ms.date="10/04/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Selvstudium: Azure Active Directory-integration med Lifesize skyen

I dette selvstudium lærer du at integrere Lifesize skyen med Azure Active Directory (Azure AD).

Integrering af Lifesize skyen med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Lifesize skyen
- Du kan aktivere dine brugere til automatisk få logget på Lifesize skyen (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Lifesize skyen, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Lifesize skyen single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Lifesize skyen fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-lifesize-cloud-from-the-gallery"></a>Tilføje Lifesize skyen fra galleriet
Hvis du vil konfigurere integrationen mellem Lifesize skyen Azure AD, skal du tilføje Lifesize skyen fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Lifesize skyen fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Active Directory][1]
2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Lifesize skyen**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)

7. Vælg **Lifesize skyen**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og test Azure AD single sign-on – med Lifesize skyen baseret på en testbruger ved navn "Britta Simon".

Azure AD skal se, hvad brugeren modstykke i Lifesize skyen er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Lifesize skyen oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Lifesize skyen.

For at konfigurere og teste Azure AD single sign-on – med Lifesize skyen, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en Lifesize sky teste bruger](#creating-a-lifesize-cloud-test-user)** - skal have et modstykke Britta Simon i Lifesize skyen, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen klassisk og konfigurere Enkeltlogon i Lifesize skyen programmet.


**Hvis du vil konfigurere Azure AD single sign-on med Lifesize skyen, kan du udføre følgende trin:**

1. I portalen klassisk, på siden **Lifesize skyen** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge Lifesize skyen** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png) 

    en. Skriv URL-adressen bruges af dine brugere at logge på din Lifesize Cloud-program, ved hjælp af følgende mønster i tekstfeltet **Log på URL-adresse** : **https://login.lifesizecloud.com/ls/?acs**.
    
    b. Klik på **Næste**
 
4. På siden **Konfigurer single sign-on – på Lifesize skyen** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


5. For at få SSO konfigureret for dit program, login i programmet Lifesize skyen med administratortilladelser.

6. Klik på dit navn i øverste højre hjørne og klik derefter på på siden **Avancerede indstillinger**

    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

7. De avancerede indstillinger for nu skal du klikke på linket **SSO konfiguration** . Derved åbnes siden SSO konfiguration for din forekomst.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

8. Nu skal du konfigurere følgende værdier i Brugergrænsefladen for SSO konfiguration.    

    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    • Kopiere værdien af udsteder URL-adressen fra Azure AD og indsætte det i **Identitet udbyder udsteder** tekstfelt.

    • Kopiere værdien af Remote Login URL-adressen fra Azure AD og indsætte det i **URL-adressen til logon** tekstfelt.

    • Åbne hentede certifikatet i Notesblok og kopiere indholdet af certifikatet, undtagen linjerne begynder certifikat og slutningen certifikat skal du indsætte dette i tekstfeltet **X.509-certifikat** .

    • I SAML attribut tilknytningen for tekstboksen **Fornavn** angive værdien som **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**

    • I SAML attribut tilknytningen for tekstboksen **Efternavn** angive værdien som **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    • I SAML attribut tilknytningen for tekstboksen **mail** angive værdien som **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

9. Du kan klikke på knappen **Test** for at kontrollere konfigurationen.

    > [AZURE.NOTE] Til vellykket test skal du fuldføre konfigurationsguiden i Azure AD og giver også adgang til brugere eller grupper, der kan udføre testen.
    
10. Aktivere SSO ved at markere på knappen **Aktivér SSO** .

11. Klik nu på knappen **Opdater** , så alle indstillingerne gemmes. Der oprettes værdien RelayState. Kopiér værdien RelayState, som er oprettet i tekstfeltet. Vi skal bruge denne værdi i de næste trin.

12. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

13. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
 
    ![Azure AD-Single Sign-On][11]

14. Nu logon til Azure Management Portal **https://portal.azure.com** ved hjælp af Administratoroplysninger

15. Klik på **Flere tjenester** link i den venstre navigationsrude
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)

16. Søge efter Azure Active Directory og klik på linket **Azure Active Directory**
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)

17. Du kan finde alle dine SaaS programmer under knappen **Enterprise-programmer** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)

18. Klik nu på **Alle programmer** link i den næste blade
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)

19. Søg efter Lifesize program, du vil oprette RelayState. 
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)

20. Klik nu på **Single sign-on** -link i bladet

    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)

21. Du får vist feltet **Vis avancerede indstillinger for URL-adresse** . Klik på afkrydsningsfeltet.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
    
22. Nu konfigurere RelayState for programmet, som du ser på konfigurationssiden af Lifesize programmet SSO. 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)

23. Gemme indstillingerne.

### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.


![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:  ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-an-lifesize-cloud-test-user"></a>Oprette en Lifesize skyen testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i Lifesize skyen. Lifesize skyen understøtter automatisk brugerklargøring. Efter vellykket godkendelse på Azure AD kan bliver brugeren automatisk klargøres i programmet på computeren. 


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til Lifesize skyen.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon Lifesize skyen, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Lifesize skyen**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 

3. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af Single Sign-On

I dette afsnit, skal teste du din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet Access.

Når du klikker på feltet Lifesize skyen i panelet adgang, du bør få automatisk logget på Lifesize Cloud-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png

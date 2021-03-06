<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med HR2day ved Merces | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og HR2day ved Merces."
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Selvstudium: Azure Active Directory-integration med HR2day ved Merces

Formålet med dette selvstudium er at se, hvordan du integrerer HR2day ved Merces med Azure Active Directory (Azure AD).  
Integrering af HR2day ved Merces med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til HR2day ved Merces
- Du kan aktivere dine brugere til automatisk få logget på til HR2day ved Merces (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med HR2day ved Merces, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- En HR2day ved Merces enkelt-sign-on aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje HR2day ved Merces fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-hr2day-by-merces-from-the-gallery"></a>Tilføje HR2day ved Merces fra galleriet
Hvis du vil konfigurere integrationen mellem HR2day ved Merces Azure AD, skal du tilføje HR2day ved Merces fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje HR2day ved Merces fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.
 
    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **HR2day ved Merces**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)

7. Vælg **HR2day ved Merces**i resultatruden, og klik derefter på **udført** for at tilføje programmet.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med HR2day ved Merces baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i HR2day ved Merces til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i HR2day ved Merces oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i HR2day ved Merces.

For at konfigurere og teste Azure AD single sign-on – med HR2day ved Merces, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en HR2day ved Merces teste bruger](#creating-a-hr2day-by-merces-test-user)** - skal have et modstykke Britta Simon i HR2day ved Merces, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende HR2day ved Merces med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

Din HR2day af Merces program forventer SAML påstande i et bestemt format, som kræver, at du kan tilføje brugerdefineret attributtilknytninger til konfigurationen SAML token attributter. Følgende skærmbillede viser et eksempel af dette. 

![Konfigurere Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png) 

Før du kan konfigurere SAML-program, skal du kontakte supportteamet HR2day via [servicedesk@merces.nl](mailto:servicedesk@merces.nl) og Anmod om værdien for attributten entydigt id for din lejer. Du skal bruge denne værdi til at fuldføre trinnene i næste afsnit.


**Hvis du vil konfigurere Azure AD single sign-on – med HR2day ved Merces skal du udføre følgende trin:**

1. Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i Azure klassisk-portalen på **HR2day ved Merces** programmets integrationsside, i menuen i øverst. 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png) 

2. For at tilføje påkrævet attributtilknytningerne skal udføre følgende trin, skal du udføre følgende trin: 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png) 


    en. Klik på **Tilføj bruger attributten**.

    b. Skriv **"ATTR_LOGINCLAIM"**i tekstfeltet **Attributnavn** .

    c. Vælg **Join()**på listen **Attributværdien** . 

    d. Vælg **User.mail**på listen **streng1** . 

    e. Skriv et **entydigt id** fra teamet HR2day i tekstfeltet **streng2** . 

    f. Skriv i tekstfeltet **Separator** **@**.

    g. Klik på **udført**.

  
3. Klik på **Anvend ændringer**.


1. Klik på **Hurtig Start** for at åbne dialogboksen **Hurtig Start** i menuen øverst.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png) 



1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge HR2day ved Merces** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin: 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png) 


    en. Skriv URL-adressen bruges af dine brugere at logge på din HR2day ved Merces program ved hjælp af følgende mønster i tekstfeltet Log på URL-adresse: **"https://\<lejer navn\>.force.com/\<navnet på forekomsten\>"**.

    b. Klik på **Næste**.

4. På siden **Konfigurer single sign-on – på HR2day ved Merces** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png) 

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


5. For at få SSO konfigureret for dit program skal du kontakte din HR2day via Merces supportteam via [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) og vedhæfte filen hentet certifikat til din mail. Skal du give også SAML SSO URL-adresse, log af URL-adresse og udsteder URL så de kan være konfigureret til integration af SSO.


> [AZURE.NOTE] Skal du omtale til Merces team, denne integration skal enheds-ID med denne mønster **https://hr2day.force.com/INSTANCENAME**



6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen, og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Azure AD-Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.  

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-hr2day-by-merces-test-user"></a>Oprette en HR2day af Merces testbruger

Formålet med dette afsnit er at oprette en bruger, der kaldes Britta Simon i HR2day af Merces. Skal du samarbejde med HR2day af Merces supportteam at føje brugerne i kontoen HR2day. 


> [AZURE.NOTE]Hvis du vil oprette en bruger manuelt, skal du kontakte HR2day af Merces supportteam.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til HR2day ved Merces.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til HR2day ved Merces, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **HR2day ved Merces**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på HR2day ved Merces flise i panelet adgang, du bør få automatisk logget på til din HR2day af Merces program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png

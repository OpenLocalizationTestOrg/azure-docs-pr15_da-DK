<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Jive | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Jive."
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


# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Selvstudium: Azure Active Directory-integration med Jive

I dette selvstudium lærer du at integrere Jive med Azure Active Directory (Azure AD).

Integrering af Jive med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Jive
- Du kan aktivere dine brugere til automatisk få logget på til Jive (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Jive, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Jive enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Jive fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-jive-from-the-gallery"></a>Tilføje Jive fra galleriet
Hvis du vil konfigurere integration af Jive til Azure AD, skal du tilføje Jive fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Jive fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Active Directory][1]
2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Jive**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. Vælg **Jive**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med Jive baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Jive er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Jive oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Jive.

For at konfigurere og teste Azure AD single sign-on – med Jive, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en Jive teste bruger](#creating-a-jive-test-user)** - skal have et modstykke Britta Simon i Jive, der er knyttet til Azure AD-repræsentation af hende.
4. **[Konfigurere brugerklargøring](#configuring-user-provisioning)** – til at disponere hvordan du aktiverer brugerklargøring af Active Directory-bruger mailkonti til Jive.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
6. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i Jive programmet.

**Hvis du vil konfigurere Azure AD single sign-on – med Jive skal du udføre følgende trin:**

1. I portalen klassisk, på siden **Jive** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Jive** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png) 

    en. Skriv URL-adressen bruges af dine brugere at sign-on til dit Jive program ved hjælp af følgende mønster i tekstfeltet **Log på URL-adresse** : **https://\<kundenavn\>. jivecustom.com**.
    
    b. Klik på **Næste**
 
4. På siden **Konfigurer single sign-on – på Jive** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


5. Sign-on til din Jive lejer som administrator.

6. Klik på menuen øverst "**Saml**".

    ![Konfigurere Single Sign-On på App-Side](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)

    en. Vælg **aktiveret** under fanen **feltet** .

    b. Klik på knappen "**Gem alle saml indstillinger**".

7. Gå til fanen "**Idp Metadata**".

    ![Konfigurere Single Sign-On på App-Side](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)

    en. Kopiere indholdet af den hentede metadata XML-fil, og Indsæt det i tekstfeltet **identitet udbyder (IDP) Metadata** .

    b. Klik på knappen "**Gem alle saml indstillinger**". 

8. Gå til fanen "**Bruger attributten tilknytning**".

    ![Konfigurere Single Sign-On på App-Side](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)

    en. I tekstfeltet **mail** skal du kopiere og indsætte attributnavn for **mail** værdi.

    b. I tekstfeltet **Fornavn** skal du kopiere og indsætte attributnavn for **givenname** værdi.

    c. I tekstfeltet **Efternavn** skal du kopiere og indsætte attributnavn for **Efternavn** værdi.
    
9. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure AD-portalen, og klik derefter på **Næste**.
![Azure AD-Single Sign-On][10]

10. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
  ![Azure AD-Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.


![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:  ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



###<a name="creating-a-jive-test-user"></a>Oprette en Jive testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i Jive. Skal du samarbejde med Jive supportteam at føje brugerne i Jive platformen.


###<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugerklargøring af Active Directory-brugerkonti til Jive.  
Du er påkrævet for at give et bruger-sikkerhedstoken, skal du anmode om fra Jive.com som en del af denne procedure.
  
Følgende skærmbillede viser et eksempel på dialogboksen relaterede i Azure AD:

![Konfigurere klargøring af bruger] (./media/active-directory-saas-jive-tutorial/IC698794.png "Konfigurere klargøring af bruger")

####<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Klik på **Konfigurer brugerklargøring** for at åbne dialogboksen **Konfigurer brugerklargøring** i portalen Azure-administration på siden **Jive** application integration.

2.  Angiv følgende konfigurationsindstillinger på siden **Angiv dine Jive legitimationsoplysninger for at aktivere automatisk brugerklargøring** :

    1.  I tekstfeltet **Jive administrator brugernavn** skal du skrive en Jive kontonavn med profilen **Systemadministrator** i Jive.com, der er tildelt.

    2.  I tekstfeltet **Jive administratoradgangskode** skal du skrive adgangskoden til kontoen.

    3.  Skriv Jive lejer URL-adressen i tekstfeltet **Jive lejer URL-adresse** .

        >[AZURE.NOTE]Jive lejer URL er URL-adresse, der bruges af organisationen til at logge på Jive.  
        Typisk URL-adressen har følgende format: **www.\< organisation\>. jive.com**.

    4.  Klik på **Valider** for at bekræfte din konfiguration.

    5.  Klik på knappen **Næste** for at åbne siden **Confirmation** .

3.  Klik på markering for at gemme din konfiguration på siden **Confirmation** .
  
Du kan nu oprette en testkonto, vent 10 minutter og bekræfte, at kontoen er blevet synkroniseret med Jive.com.




### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til Jive.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Jive, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Jive**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png) 

3. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af Single Sign-On

I dette afsnit, skal teste du din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på feltet Jive i panelet adgang, du bør få automatisk logget på Jive-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png

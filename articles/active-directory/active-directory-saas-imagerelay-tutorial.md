<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med ImageRelay | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og ImageRelay."
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


# <a name="tutorial-azure-active-directory-integration-with-imagerelay"></a>Selvstudium: Azure Active Directory-integration med ImageRelay

Formålet med dette selvstudium er at se, hvordan du integrerer ImageRelay med Azure Active Directory (Azure AD).  
Integrering af ImageRelay med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til ImageRelay
- Du kan aktivere dine brugere til automatisk få logget på til ImageRelay (single sign-on –) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med ImageRelay, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- En ImageRelay single sign-on aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje ImageRelay fra galleriet

2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-imagerelay-from-the-gallery"></a>Tilføje ImageRelay fra galleriet
Hvis du vil konfigurere integrationen mellem ImageRelay Azure AD, skal du tilføje ImageRelay fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje ImageRelay fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **ImageRelay**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)

7. Vælg **ImageRelay**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med ImageRelay baseret på en testbruger ved navn "Britta Thomas".

Til single sign-on til at fungere sammen, skal Azure AD en brugerkonto, der repræsenterer den pågældende bruger i ImageRelay.  Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i ImageRelay oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i ImageRelay.

For at konfigurere og teste Azure AD single sign-on – med ImageRelay, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en ImageRelay teste bruger](#creating-a-userecho-test-user)** - skal have et modstykke Britta Simon i ImageRelay, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i ImageRelay programmet.


**Hvis du vil konfigurere Azure AD single sign-on – med ImageRelay skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **ImageRelay** application integration.

     ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge ImageRelay** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

     ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 

    en. Skriv URL-adressen bruges af dine brugere til at logge din ImageRelay-program i tekstfeltet **Log på URL-adresse** (for eksempel: *https://fabrikam.ImageRelay.com/*).

    b. Klik på **Næste**.

4. På siden **Konfigurer single sign-on – på ImageRelay** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.

5. I en anden browser-vindue, du logge på webstedet ImageRelay virksomhed som administrator.

1. Klik på **brugere og tilladelser** arbejdsbelastningen på værktøjslinjen øverst.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Klik på **Opret ny tilladelsesniveau**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 

1. Markér afkrydsningsfeltet **denne gruppe kan kun logge på via enkeltlogon** i arbejdsbelastningen **Enkelt Log på indstillinger** , og klik derefter på **Gem**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Gå til **Kontoindstillinger**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Gå til arbejdsbelastningen **Enkelt Log på indstillinger** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

1. I dialogboksen **Indstillinger for SAML** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)

    en. Kopiere **Enkelt Sign-On URL-adressen**på Azure-klassisk portalen, og Indsæt det i tekstfeltet **URL-adressen til logon** .


    b. Kopiere **Enkelt Sign-Out URL-adressen**på Azure-klassisk portalen, og Indsæt det i tekstfeltet **Log URL-adresse** .

    c. Som **Id navneformat**, Vælg **urn: oasis: navne: tc: SAML:1.1:nameid-format: mailadresse**.

    
    d. Vælg **INDLÆG Binding**som **Binding indstillinger for anmodninger fra tjenesteudbyder (Relay billede)**.
   

    e. Klik på **Opdateringscertifikat**under **x.509-certifikat**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Åbn downloadede certifikatet i Notesblok, kopiere indholdet og derefter indsætte det i tekstfeltet x.509-certifikat.
  
    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Vælg **Aktivér JIT brugerklargøring** **JIT brugerklargøring** sektionen.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Vælg den (for eksempel **SSO grundlæggende**) tilladelsesgruppe som har tilladelse til at logge på kun via enkeltlogon.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    Jeg. Klik på **Gem**.

6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen, og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.

    ![Azure AD-Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-imagerelay-test-user"></a>Oprette en ImageRelay testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i ImageRelay.

**Hvis du vil oprette en bruger, kaldet Britta Simon i ImageRelay, skal du udføre følgende trin:**

1. Sign-on til webstedet ImageRelay virksomhed som administrator.

1. Gå til **brugere og tilladelser** , og vælg **Opret SSO bruger**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Angiv **mail**, **Fornavn**, **Efternavn** og **virksomhed** for brugeren, du ønsker at klargøre og vælge den (for eksempel SSO grundlæggende) tilladelsesgruppe som er den gruppe, der kan logge på kun via enkeltlogon.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Klik på **Opret**.

### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til ImageRelay.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til ImageRelay, skal du udføre følgende trin:**

1. I Azure klassisk portalen for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **ImageRelay**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet ImageRelay i panelet adgang, du bør få automatisk logget på ImageRelay-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png

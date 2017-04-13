<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Keylight | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Keylight."
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


# <a name="tutorial-azure-active-directory-integration-with-keylight"></a>Selvstudium: Azure Active Directory-integration med Keylight

I dette selvstudium lærer du at integrere Keylight med Azure Active Directory (Azure AD).

Integrering af Keylight med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Keylight
- Du kan aktivere dine brugere til automatisk få logget på til Keylight (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Keylight, du skal bruge følgende elementer:

- Et Azure-abonnement
- Keylight enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø. 

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Keylight fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-keylight-from-the-gallery"></a>Tilføje Keylight fra galleriet
Hvis du vil konfigurere integrationen mellem Keylight Azure AD, skal du tilføje Keylight fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Keylight fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Keylight**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)

7. Vælg **Keylight**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med Keylight baseret på en testbruger ved navn "Britta Thomas".

For at konfigurere og teste Azure AD single sign-on – med Keylight, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en Keylight teste bruger](#creating-a-keylight-test-user)** - skal have et modstykke Britta Simon i Keylight, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen Azure klassisk og konfigurere Enkeltlogon i Keylight programmet.


**Hvis du vil konfigurere Azure AD single sign-on – med Keylight skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Keylight** application integration.

    ![Konfigurere Single Sign-On][6] 


2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Keylight** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:
 
    ![Konfigurere Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png) 


    en. Skriv URL-adressen bruges af dine brugere at sign-on til dit Keylight program ved hjælp af følgende mønster i tekstfeltet Log på URL-adresse: **"https://\<firmanavn\>.keylightgrc.com/Login.aspx?saml=1"**.


4. På siden **Konfigurer single sign-on – på Keylight** skal du udføre følgende trin:
 
    ![Konfigurere Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png) 

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


5. For at aktivere SSO i Keylight skal du udføre følgende trin:
 
    en. Sign-on til kontoen Keylight som administrator.

    b. Klik på **Person**i menuen øverst, og vælg **Keylight konfiguration**.
       
    ![Konfigurere Single Sign-On](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. Klik på **SAML**i trævisningen i venstre side.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. Dialogboksen med **SAML indstillinger** , klik på **Rediger**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-keylight-tutorial/404.png) 
  

5. På siden **Rediger indstillinger for SAML** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-keylight-tutorial/405.png) 

    en. Indstille **SAML godkendelse** til **aktiv**.


    b. Kopiér værdien **SAML SSO URL-adressen** Azure AD-klassisk portalen, og Indsæt det i tekstfeltet **Identitet udbyder Login URL-adresse** .

    c. Kopiér værdien **Enkelt Sign-Out URL-adressen** Azure AD-klassisk portalen, og Indsæt det i tekstfeltet **Identitet udbyder log URL-adresse** .

    d. Klik på **Vælg fil** for at vælge dit hentede Keylight certifikat, og klik derefter på **Åbn** for at overføre certifikatet.


    e. Angive **placeringen af SAML bruger-Id** til **NameIdentifier element i sætningen emne**.
   
    f. Angiv den **Keylight tjenesteudbyder ved hjælp af følgende mønster: **https://&lt;firmanavn&gt;. keylightgrc.com**.

    g. Angiv **automatisk klargøring af brugere** til **aktiv**.

    h. Indstille **automatisk klargøring kontotype** til **Fuld bruger**.

    Jeg. Vælg **Standardbruger med SAML**, som **automatisk klargøring sikkerhedsrolle**.
   
    j. Vælg **Standard Brugerkonfiguration**, som **automatisk klargøring sikkerhed config**.
   
    k. Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**i tekstfeltet mail-attributten.

    l. Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**i tekstfeltet **Fornavn attribut** .

    m. Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**i tekstfeltet **sidste name-attribut** .

    n. Klik på **Gem**.
   
  
   
  
6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen, og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Azure AD-Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i Azure klassisk portalen kaldet Britta Simon.

Vælg **Britta Thomas**på listen brugere.

![Oprette Azure AD-bruger][20]



**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png) 


2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 


4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-keylight-test-user"></a>Oprette en Keylight testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i Keylight. Keylight understøtter just-in-time klargøring, som er aktiveret som standard.

Der er ingen handlingspunkt for dig i dette afsnit. Der oprettes en ny bruger, når du åbner Keylight, hvis brugeren ikke findes endnu. 

> [AZURE.NOTE] Hvis du har brug at oprette en bruger manuelt, skal du kontakte Keylight supportteam.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til Keylight.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Keylight, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Keylight**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

I dette afsnit, skal teste du din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på feltet Keylight i panelet adgang, du bør få automatisk logget på Keylight-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png

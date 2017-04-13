<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Asana | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Asana."
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
    ms.date="10/24/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Selvstudium: Azure Active Directory-integration med Asana

I dette selvstudium lærer du at integrere Asana med Azure Active Directory (Azure AD).

Integrering af Asana med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Asana
- Du kan aktivere dine brugere til automatisk få logget på til Asana (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Asana, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- **Asana** enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø. Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Asana fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-asana-from-the-gallery"></a>Tilføje Asana fra galleriet
Hvis du vil konfigurere integrationen mellem Asana Azure AD, skal du tilføje Asana fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Asana fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Asana**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-asana-tutorial/tutorial_asana_01.png)

7. Vælg **Asana**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-asana-tutorial/tutorial_asana_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med Asana baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Asana er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Asana oprettes.
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Asana.

For at konfigurere og teste Azure AD single sign-on – med Asana, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en Asana teste bruger](#creating-an-Asana-test-user)** - skal have et modstykke Britta Simon i Asana, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i Asana programmet.

**Hvis du vil konfigurere Azure AD single sign-on – med Asana skal du udføre følgende trin:**

1. Klik på **Hurtig Start**i menuen øverst.

    ![Konfigurere Single Sign-On][6]
2. I portalen klassisk, på siden **Asana** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On][7] 

3. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Asana** , og klik derefter på **Næste**.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_06.png)

4. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin: 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_07.png)


    en. I tekstfeltet Log på URL-adresse skal du skrive en URL-adresse ved hjælp af følgende mønster:`https://app.asana.com`

    c. Klik på **Næste**.

5. Klik på på siden **Konfigurer single sign-on – på Asana** **Hent certifikat**, og derefter gemme filen på din computer. Også kopiere værdien for SAML SSO URL-adresse.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_08.png)

8. Højreklik på certifikatet, og derefter åbne certifikatfilen ved hjælp af Notesblok eller du foretrukne tekstredigeringsprogram. Kopiér indholdet mellem afsnittet Begynd og slutningen certifikat titlen. Dette er det x.509-certifikat, du vil bruge i Asana til at konfigurere SSO.

6. I et andet browservindue sign-on til dit Asana program. Hvis du vil konfigurere SSO i Asana, få adgang til indstillingerne arbejdsområde ved at klikke på navnet på arbejdsområdet i øverste højre hjørne af skærmen. Klik derefter på ** \<kaldt arbejdsområde\> indstillinger**. 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

7. Klik på **Administration**i vinduet **Indstillinger for organisationen** . Klik derefter på **medlemmer skal logge på via SAML** for at aktivere SSO-konfiguration. Udfør følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)

    en. Indsæt SAML at logge på URL-adressen fra Azure AD i texbox **logon URL-adressen** .

    b. Indsæt det x.509-certifikat, du har kopieret fra Azure AD i tekstfeltet **X.509-certifikat** .

9. Klik på **Gem**. Gå til [Asana vejledning til konfiguration af SSO](https://asana.com/guide/help/premium/authentication#gl-saml) , hvis du har brug for yderligere hjælp.

7. Gå til **Konfigurer single sign-on – på Asana** side i Azure AD, vælge enkelt sign-on – konfiguration bekræftelsen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

8. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
    
    ![Azure AD-Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-asana-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:
 
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-asana-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-asana-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-asana-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-asana-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-an-asana-test-user"></a>Oprette en Asana testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i Asana.

1. Gå til sektionen **grupper** i panelet til venstre på **Asana**. Klik på plustegnet (+). 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 

2. Skriv mailen britta.simon@contoso.com i tekstboksen og derefter vælge **Inviter**.
3. Klik på **Send invitation**. Den nye bruger modtager en mail i sin mailkonto. Hun skal oprette og validere kontoen.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til Asana.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Asana, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Asana**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_50.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen alle brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkeltlogon.

Gå til Asana logonside. E-mailen adresse tekstfelt Indsæt mailadressen britta.simon@contoso.com. Lade tekstfeltet adgangskode i tom, og klik derefter på **Log In**. Du bliver omdirigeret til Azure AD-logonside. Udføre dine Azure AD-legitimationsoplysninger. Nu er du logget på på Asana.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-asana-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-asana-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-asana-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-asana-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-asana-tutorial/tutorial_general_205.png

<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Tableau Online | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Tableau Online."
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
    ms.date="10/18/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Selvstudium: Azure Active Directory-integration med Tableau Online

I dette selvstudium lærer du at integrere Tableau Online med Azure Active Directory (Azure AD).

Integrering af Tableau Online med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Tableau Online
- Du kan aktivere dine brugere til automatisk få logget på Tableau online (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

For at konfigurere Azure AD-integration med Tableau Online skal bruge du følgende elementer:

- Et Azure AD-abonnement
- En **Tableau Online** enkelt logge på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø. Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Tableau Online fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-tableau-online-from-the-gallery"></a>Tilføje Tableau Online fra galleriet
Hvis du vil konfigurere integrationen mellem Tableau Online Azure AD, skal du føje Tableau Online fra galleriet til din liste over administrerede SaaS apps.

**Hvis du vil tilføje Tableau Online fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. I søgefeltet, skrive **Tableau Online**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_01.png)

7. Vælg **Tableau Online**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med Tableau Online baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Tableau Online er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Tableau Online oprettes.
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Tableau Online.

For at konfigurere og teste Azure AD single sign-on – med Tableau Online, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en Tableau Online teste bruger](#creating-a-Tableau-Online-test-user)** - skal have et modstykke Britta Simon i Tableau Online, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i dit Tableau Online-program.

**Hvis du vil konfigurere Azure AD single sign-on – med Tableau Online skal du udføre følgende trin:**

1. Klik på **Hurtig Start**i menuen øverst.

    ![Konfigurere Single Sign-On][6]
2. I portalen klassisk, på siden med **Tableau Online** integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On][7] 

3. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Tableau Online** , og klik derefter på **Næste**.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_06.png)

4. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin: 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_07.png)


    en. I tekstfeltet Log på URL-adresse skal du skrive en URL-adresse ved hjælp af følgende mønster:`https://sso.online.tableau.com`

    c. Klik på **Næste**.

5. Klik på på siden **Konfigurer single sign-on – på Tableau Online** **hente metadata**, og derefter gemme filen på din computer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_08.png)

6. Vælge enkelt sign-on – konfiguration bekræftelsen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
    
    ![Azure AD-Single Sign-On][11]
8. I et andet browservindue sign-on til dit Tableau Online-program. Gå til **Indstillinger** og derefter til **godkendelse**

    ![Konfigurere Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)

9. Under **Godkendelsestyper** sektion. Markér afkrydsningsfeltet **Single sign-on med SAML** for at aktivere SAML.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

10. Rul ned, indtil **importfilen metadata til Tableau Online** sektion.  Klik på Gennemse og importere den metadatafil, du har hentet fra Azure AD. Klik derefter på **Anvend**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

11. Indsætte tilsvarende identitetsudbyder program navnet for e-mail-adresse, fornavn og efternavn i sektionen **Match påstande** . At få disse oplysninger fra Azure AD:

    en. Gå tilbage til Azure AD. Klik på **attributter**i Azure klassisk-portalen på **Tableau Online** program integration siden i menuen i øverst. Kopiere og indsætte navnet til værdierne: userprincipalname, givenname og efternavn.
     
    ![Azure AD-Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)

    b. Skifte til Tableau Online programmet, og derefter angive sektionen **Tableau Online attributter** som følger:
    
    -  Mail: **mail** eller **userprincipalname**
    -  Fornavn: **givenname**
    -  Efternavn: **Efternavn**

    ![Konfigurere Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:
 
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-tableau-online-test-user"></a>Oprette en Tableau test onlinebruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i Tableau Online.

1. Klik på **Indstillinger** og derefter **godkendelse** sektion på **Tableau Online**. Rul ned til sektionen **Vælg brugere** . Klik på **Tilføj brugere** og derefter **angive mailadresser**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Vælg **Tilføj brugere til enkelt enkeltlogon (SSO) godkendelse**. Tilføj i tekstfeltet **Skriv mailadresser**britta.simon@contoso.com

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)

3.  Klik på **Opret**.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til Tableau Online.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Tableau Online, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

3. Vælg **Tableau Online**på listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_50.png) 

4. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

5. Vælg **Britta Thomas**på listen alle brugere.

6. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på Tableau Online-feltet i panelet adgang, du bør få automatisk logget på i dit Tableau Online program.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_205.png

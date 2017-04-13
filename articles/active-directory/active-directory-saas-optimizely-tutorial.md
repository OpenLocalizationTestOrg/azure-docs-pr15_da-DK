<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Optimizely | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Optimizely."
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
    ms.date="09/11/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Selvstudium: Azure Active Directory-integration med Optimizely

I dette selvstudium lærer du at integrere Optimizely med Azure Active Directory (Azure AD).

Integrering af Optimizely med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Optimizely
- Du kan aktivere dine brugere til automatisk få logget på til Optimizely (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Optimizely, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- **Optimizely** enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø. Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Optimizely fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-optimizely-from-the-gallery"></a>Tilføje Optimizely fra galleriet
Hvis du vil konfigurere integrationen mellem Optimizely Azure AD, skal du tilføje Optimizely fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Optimizely fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Optimizely**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. Vælg **Optimizely**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med Optimizely baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Optimizely er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Optimizely oprettes.
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Optimizely.

For at konfigurere og teste Azure AD single sign-on – med Optimizely, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en Optimizely teste bruger](#creating-an-optimizely-test-user)** - skal have et modstykke Britta Simon i Optimizely, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i Optimizely programmet.

Optimizely programmet forventer SAML påstande skal indeholde en attribut med navnet "mail". Værdien af "mail" skal være en Optimizely genkendte mail, der kan få bekræftet af Azure AD. Konfigurer "mail" krav om dette program. Du kan administrere værdierne i disse attributter fra fanen **"Atrributes"** af programmet. Følgende skærmbillede viser et eksempel af dette. 


![Konfigurere Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png) 


**Hvis du vil konfigurere Azure AD single sign-on – med Optimizely skal du udføre følgende trin:**

1. Klik på **attributter**i Azure klassisk-portalen på **Optimizely** programmets integrationsside, i menuen i øverst.
     
    ![Konfigurere Single Sign-On][5]

2. Føje attributten "mail" i dialogboksen SAML token attributter.

    en. Klik på **Tilføj bruger attribut** for at åbne dialogboksen **Tilføj bruger attribut** . 
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)

    b. Skriv attribut "mail" i tekstfeltet **Attributnavn** .

    c. På listen **Attributværdien** , Vælg attributten værdi "userprincipalname" eller en værdi, der indeholder en mail, som genkendes af Azure AD og Optimizely.

    d. Klik på **udført**.
3. Klik på **Hurtig Start**i menuen øverst.

    ![Konfigurere Single Sign-On][6]
4. I portalen klassisk, på siden **Optimizely** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On][7] 

5. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Optimizely** , og klik derefter på **Næste**.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin: 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)


    en. I tekstfeltet **Log på URL-adresse** skal du skrive:`https://app.optimizely.net/contoso`

    b. I tekstfeltet **id** skal du skrive:`urn:auth0:optimizely:contoso`

    c. Klik på **Næste**. 


    > [AZURE.NOTE] Værdierne for **Log på URL-adresse** og **id** er kun pladsholdere for de faktiske værdier. Du kan finde instruktioner til aquiring de faktiske værdier fra Optimizely senere i dette selvstudium.

7. På siden **Konfigurer single sign-on – på Optimizely** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Kopiere **URL-adressen til Single Sign-On-tjenesten**.

8. Kontakt din Optimizely Account Manager for at få SSO konfigureret for dit program, og Angiv følgende oplysninger:

    - Dit hentede certifikat 
    - Single Sign-On-tjenesten URL-adressen
 
    Svar til dine mails giver Optimizely dig Log på URL-adressen (SP-definerede SSO) og id (Service Provider enheds-ID) værdier.

9. Gå tilbage til **Konfigurere indstillinger for App** dialogboksside, og derefter udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

    en. Skriv **SP-definerede SSO URL-adressen** fra Optimizely i tekstfeltet **Log på URL-adresse** .

    b. Skriv det **Service Provider enheds-ID** , der leveres af Optimizely i tekstfeltet **id** .

    c. Klik på **Næste**.

10. På siden **Konfigurer single sign-on – på Optimizely** skal du udføre følgende trin:
    
    ![Azure AD-Single Sign-On][10]

    en. Vælge enkelt sign-on – konfiguration bekræftelsen.

    b. Klik på **Næste**.

11. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
    
    ![Azure AD-Single Sign-On][11]

12. I et andet browservindue sign-on til dit Optimizely program.
13. Klik på firmanavn i øverste højre hjørne og derefter på **Kontoindstillinger**.

    ![Azure AD-Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

14. I fanen konto skal du markere afkrydsningsfeltet **Aktivér SSO** under Single Sign-On i sektionen **Overview** .

    ![Azure AD-Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.
Vælg **Britta Thomas**på listen brugere.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:
 
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-an-optimizely-test-user"></a>Oprette en Optimizely testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i Optimizely.

1. På siden hjem vælge **samarbejdspartnere** fane
2. Klik på **Ny Collaborator** for at tilføje en ny collaborator til projektet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3.  Udfyld mailadressen og tildele dem til en rolle. Klik på **Inviter**.


    ![Oprette en Azure AD testbruger](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Inviter en e-mail-modtager. Brug af e-mail-adresse. de nødt til at logge på Optimizely.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til Optimizely.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Optimizely, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Optimizely**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen alle brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på feltet Optimizely i panelet adgang, du bør få automatisk logget på Optimizely-program.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png

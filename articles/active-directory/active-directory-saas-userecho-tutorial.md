<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med UserEcho | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og UserEcho."
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
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Selvstudium: Azure Active Directory-integration med UserEcho

Formålet med dette selvstudium er at se, hvordan du integrerer UserEcho med Azure Active Directory (Azure AD).  
Integrering af UserEcho med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, der har adgang til UserEcho 
- Du kan aktivere dine brugere til automatisk få logget på til UserEcho (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Hvis du vil konfigurere Azure AD-integration med UserEcho, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- UserEcho enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje UserEcho fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-userecho-from-the-gallery"></a>Tilføje UserEcho fra galleriet
Hvis du vil konfigurere integrationen mellem UserEcho Azure AD, skal du tilføje UserEcho fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje UserEcho fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **UserEcho**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)

7. Vælg **UserEcho**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med UserEcho baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i UserEcho til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i UserEcho oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i UserEcho.
 
For at konfigurere og teste Azure AD single sign-on – med UserEcho, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en UserEcho teste bruger](#creating-a-userecho-test-user)** - skal have et modstykke Britta Simon i UserEcho, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i UserEcho programmet. 




**Hvis du vil konfigurere Azure AD single sign-on – med UserEcho skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **UserEcho** application integration.

    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge UserEcho** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png) 

    en. Skriv URL-adressen bruges af dine brugere til at logge din UserEcho-program i tekstfeltet **Log på URL-adresse** (f.eks.: *https://fabrikam.UserEcho.com/*).

    b. Klik på **Næste**.
 
 
4. På siden **Konfigurer single sign-on – på UserEcho** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png) 

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


1. I en anden browser-vindue, du logge webstedet UserEcho virksomhed som administrator.

1. Klik på dit brugernavn for at udvide menuen på værktøjslinjen øverst, og klik **konfiguration**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 

1. Klik på **integration**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 


1. Klik på **websted**, og klik derefter på **Single sign-on (SAML2)**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 


1. På siden **Single sign-on (SAML)** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png) 

    en. Som **SAML-aktiveret**, kan du vælge **Ja**. 

    b. Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på den konfigurere Enkeltlogon på UserEcho dialogboksside, og derefter indsætter det i **URL-adressen SAML SSO** tekstfelt.

    c. Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på den konfigurere Enkeltlogon på UserEcho dialogboksside, og Indsæt det i tekstfeltet **Remote logoout URL-adresse** . 

    d. Åbn dit hentede certifikat i Notesblok, kopiere indholdet og derefter indsætte det i tekstfeltet **X.509-certifikat** .    

    e. Klik på **Gem**.


6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen, og klik derefter på **Næste**. 

    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Azure AD-Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png)  

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 
 
4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png)  

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png) 
 
    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .
    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png) 
 
8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png) 
  
    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   

  
 
### <a name="creating-a-userecho-test-user"></a>Oprette en UserEcho testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i UserEcho.

**Hvis du vil oprette en bruger, kaldet Britta Simon i UserEcho, skal du udføre følgende trin:**

1. Sign-on til webstedet UserEcho virksomhed som administrator.

1. Klik på dit brugernavn for at udvide menuen på værktøjslinjen øverst, og klik **konfiguration**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 

1. Klik på **brugere**, for at udvide afsnittet **brugere** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png) 

1. Klik på **brugere**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png) 

1. Klik på **Inviter en ny bruger**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)


1. I dialogboksen **invitere nye brugere** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png) 

    en. I tekstfeltet **navn** skal du skrive **Britta Thomas**.

    b. I tekstfeltet **mail** skal du skrive Brittas mailadresse på portalen Azure klassisk.

    c. Klik på **Inviter**.

Der sendes en invitation til Britta, som gør det muligt for hende at begynde at bruge UserEcho. 



### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til UserEcho.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til UserEcho, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **UserEcho**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet UserEcho i panelet adgang, du bør få automatisk logget på UserEcho-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png







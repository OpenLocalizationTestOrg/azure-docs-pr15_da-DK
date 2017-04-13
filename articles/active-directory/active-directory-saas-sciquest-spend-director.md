<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med SciQuest bruger direktør | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og SciQuest bruger direktør."
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


# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Selvstudium: Azure Active Directory-integration med SciQuest bruger direktør

Formålet med dette selvstudium er at se, hvordan du integrerer SciQuest bruger direktør med Azure Active Directory (Azure AD).  
Integrering af SciQuest bruger direktør med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, der har adgang til SciQuest bruger direktør 
- Du kan aktivere dine brugere til automatisk få logget på SciQuest bruger direktør (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Hvis du vil konfigurere Azure AD-integration med SciQuest bruger direktør, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- SciQuest bruger direktør single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje SciQuest bruger direktør fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Tilføje SciQuest bruger direktør fra galleriet
Hvis du vil konfigurere integrationen mellem SciQuest bruger direktør Azure AD, skal du tilføje SciQuest bruger direktør fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje SciQuest bruger direktør fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **sciQuest bruger direktør**i søgefeltet.

    ![Programmer][5]

7. Vælg **SciQuest bruger direktør**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Programmer][6]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med SciQuest bruger direktør baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i SciQuest bruger direktør til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i SciQuest bruger Director oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i SciQuest bruger Director.
 
For at konfigurere og teste Azure AD single sign-on – med SciQuest bruger direktør, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD enkelt Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en SciQuest bruger direktør teste bruger](#creating-a-halogen-software-test-user)** - skal have et modstykke Britta Simon i SciQuest bruger direktør, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Konfiguration af Azure AD enkelt Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i dit SciQuest bruger Director-program.

**Hvis du vil konfigurere Azure AD single sign-on med SciQuest bruger direktør, kan du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **SciQuest bruger direktør** application integration.

    ![Konfigurere Single Sign-On][8]

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge SciQuest bruger direktør** , og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][9]

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin: 

    ![Konfigurere indstillinger for][10]
 
     3.1. I tekstfeltet **Log på URL-adresse** skal du skrive din URL-adresse, der bruges af dine brugere til at logge din SciQuest bruger Director-program ved hjælp af følgende mønster: *https://.* SciQuest.com/.**

     3.2. Skriv den samme værdi, du har skrevet i tekstfeltet **Log på URL-adressen** i tekstfeltet **Svar URL-adresse** . 

     3.3. Klik på **Næste**.
 
4. Klik på **Hent metadata**på siden **Konfigurer single sign-on – på SciQuest bruger direktør** , og Gem derefter metadatafilen lokalt på din computer.

    ![Hvad er Azure AD-forbindelse][11]

5. Kontakt SciQuest support til at aktivere denne godkendelsesmetode ved hjælp af de ovenfor hentede metadata.

6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** . 

    ![Hvad er Azure AD-forbindelse][15]

10. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Hvad er Azure AD-forbindelse][100] 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .
3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Hvad er Azure AD-forbindelse][101] 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Hvad er Azure AD-forbindelse][102] 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Hvad er Azure AD-forbindelse][103] 

    en. Vælg **ny bruger i organisationen**som **Type af bruger**.
  
    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.
  
    c. Klik på Næste.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Hvad er Azure AD-forbindelse][104] 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  
  
    b. I **Efternavn** txtbox, type **Thomas**.
  
    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.
  
    d. Vælg **bruger,**på listen **rolle** .
  
    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Hvad er Azure AD-forbindelse][105]  

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Hvad er Azure AD-forbindelse][106]   

    en. Notér værdien af den **Nye adgangskode**.
  
    b. Klik på **udført**.   
  
 
### <a name="creating-a-sciquest-spend-director-test-user"></a>Oprette en SciQuest bruger direktør testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i SciQuest bruger Director.

Du skal kontakte supportteamet SciQuest bruger direktør og give dem med oplysninger om din testkonto for at få det oprettet.

Alternativt kan du også udnytte just-in-time klargøring af en enkelt sign-on – funktion, der understøttes af SciQuest bruger direktør.  
Hvis just-in-time klargøring er aktiveret, oprettes brugere automatisk af SciQuest bruger direktør under et enkelt sign-on – forsøg på, hvis de ikke findes. Denne funktion behovet for at oprette enkelt sign-on – modstykke brugere manuelt.

Hvis du vil have just-in-time klargøring aktiveret, skal du kontakte du er supportteamet SciQuest bruger direktør.
  

### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til SciQuest bruger direktør.

![Hvad er Azure AD-forbindelse][200]

**Hvis du vil tildele Britta Simon SciQuest bruger direktør, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Hvad er Azure AD-forbindelse][201]

2. Vælg **SciQuest bruger direktør**i listen over programmer.

    ![Hvad er Azure AD-forbindelse][202]

1. Klik på **brugere**i menuen øverst.

    ![Hvad er Azure AD-forbindelse][203]

1. Vælg **Britta Thomas**på listen brugere.

    ![Hvad er Azure AD-forbindelse][204]

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Hvad er Azure AD-forbindelse][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet SciQuest bruger direktør i panelet adgang, du bør få automatisk logget på SciQuest bruger Director-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png


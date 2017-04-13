<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Pluralsight | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Pluralsight."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Selvstudium: Azure Active Directory-integration med Pluralsight

Formålet med dette selvstudium er at se, hvordan du integrerer Pluralsight med Azure Active Directory (Azure AD).

Integrering af Pluralsight med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Pluralsight
- Du kan aktivere dine brugere til automatisk få logget på til Pluralsight (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk


Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Pluralsight, du skal bruge følgende elementer:

- Et Azure-abonnement
- Pluralsight enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø. 

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Pluralsight fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-pluralsight-from-the-gallery"></a>Tilføje Pluralsight fra galleriet
Hvis du vil konfigurere integrationen mellem Pluralsight Azure AD, skal du tilføje Pluralsight fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Pluralsight fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.
 
    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Pluralsight**i søgefeltet.
 
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)

7. Vælg **Pluralsight**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med Pluralsight baseret på en testbruger ved navn "Britta Thomas".

For at konfigurere og teste Azure AD single sign-on – med Pluralsight, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en Pluralsight teste bruger](#creating-a-pluralsight-test-user)** - skal have et modstykke Britta Simon i Pluralsight, der er knyttet til Azure AD-repræsentation af hun.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i Pluralsight programmet.

Programmets Pluralsight forventer SAML påstande i et bestemt format, som kræver, at du kan tilføje brugerdefineret attributtilknytninger til konfigurationen SAML token attributter. Følgende skærmbillede viser et eksempel af dette. 

![Konfigurere Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png) 

Du kan også tilføje attributten **"Entydigt ID"** med den relevante værdi som medarbejder-id eller noget andet, som passer til din organisation. Bemærk også, at dette ikke er den nødvendige attribut dog kan du tilføje den for at identificere den entydige bruger. 

**Hvis du vil konfigurere Azure AD single sign-on – med Pluralsight skal du udføre følgende trin:**

1. Klik på **attributter**i Azure klassisk-portalen på **Pluralsight** programmets integrationsside, i menuen i øverst.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_81.png) 



1. Hvis du vil fjerne overflødige **SAML token attributter**, skal du udføre følgende trin: 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/2829.png) 


    en. Hold markøren over attributten for hver bruger-attribut i det røde felt i tabellen ovenfor, og klik derefter på Slet. 




1. Hvis du vil tilføje de påkrævede **SAML token attributter**, for hver række, der er vist i nedenstående tabel, skal du udføre følgende trin:

  	| Attributnavn | Attributværdien |
  	| --- | --- |    
  	| Fornavn| User.givenname |
  	| Efternavn  | User.surname |
  	| Mail | User.mail |

    en. Klik på **Tilføj bruger attribut** for at åbne dialogboksen **Tilføj bruger Attribure** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_82.png) 


    b. Skriv det attributnavn, der vises for den pågældende række i tekstfeltet **Attrubute navn** .

    c. På listen **Attributværdien** , selsect attributværdien vises for den pågældende række.

    d. Klik på **udført**.  
    


1. Klik på **Anvend ændringer**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/3232.png)  



1. Klik på **Hurtig Start**i menuen øverst.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_83.png)  









1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Pluralsight** application integration.

    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Pluralsight** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen udføre følgende trin:.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png) 


    en. I tekstfeltet Log på URL-adresse skal du skrive URL-adressen bruges af dine brugere at sign-on til dit Pluralsight program ved hjælp af følgende mønster:`https://<instance name>.pluralsight.com/sso/<comapny name>`

    b. Klik på **Næste**.


4. På siden **Konfigurer single sign-on – på Pluralsight** skal du udføre følgende trin:  ![konfigurere Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png) 

    en. Klik på **Hent metadata**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


5. For at få SSO konfigureret for dit program skal du kontakte Pluralsight [Professional Services](mailTo:professionalservices@pluralsight.com) -teamet og give metadatafilen hentede.


6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen, og klik derefter på **Næste**.
  
    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
 
    ![Azure AD-Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

Vælg **Britta Thomas**på listen brugere.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-pluralsight-test-user"></a>Oprette en Pluralsight testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i Pluralsight. Skal du samarbejde med Pluralsight supportteam at føje brugerne i kontoen Pluralsight. 


> [AZURE.NOTE]Hvis du vil oprette en bruger manuelt, skal du kontakte Pluralsight supportteam.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til Pluralsight.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Pluralsight, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Pluralsight**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på feltet Pluralsight i panelet adgang, du bør få automatisk logget på Pluralsight-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png

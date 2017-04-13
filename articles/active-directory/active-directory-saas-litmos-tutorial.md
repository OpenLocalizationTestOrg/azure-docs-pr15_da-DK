<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Litmos | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Litmos."
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


# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Selvstudium: Azure Active Directory-integration med Litmos

Formålet med dette selvstudium er at se, hvordan du integrerer Litmos med Azure Active Directory (Azure AD).  
Integrering af Litmos med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, der har adgang til Litmos 
- Du kan aktivere dine brugere til automatisk få logget på til Litmos (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - Azure Active Directory 

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Hvis du vil konfigurere Azure AD-integration med Litmos, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Litmos enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af tre primære dokumentkomponenter:

1. Tilføje Litmos fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-litmos-from-the-gallery"></a>Tilføje Litmos fra galleriet
Hvis du vil konfigurere integrationen mellem Litmos Azure AD, skal du tilføje Litmos fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Litmos fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Litmos**i søgefeltet.

    ![Programmer][5]

7. Vælg **Litmos**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Programmer][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med Litmos baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Litmos til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Litmos oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Litmos.
 
For at konfigurere og teste Azure AD single sign-on – med Litmos, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en Litmos teste bruger](#creating-a-halogen-software-test-user)** - skal have et modstykke Britta Simon i Litmos, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure AD klassisk og for at konfigurere single sign-on i Litmos programmet.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

Som en del af konfigurationen skal du tilpasse **SAML Token attributter** for dit Litmos program.  

![Azure AD-Single Sign-On][17] 

**Hvis du vil konfigurere Azure AD single sign-on – med Litmos skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure AD klassisk portalen, på siden **Litmos** application integration.

    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Litmos** , og klik derefter på **Næste**.
 
    ![Azure AD-Single Sign-On][7] 


1. Log på din Litmos virksomheds websted (f.eks.: *https://azureapptest.litmos.com/account/Login*) som administrator.

    ![Azure AD-Single Sign-On][21] 


1. Klik på **konti**på navigationslinjen i venstre side.

    ![Azure AD-Single Sign-On][22] 


1. Klik på fanen **integrationer** .

    ![Azure AD-Single Sign-On][23] 


1. Rul ned til **3 part integrationer**under fanen **integration** , og klik derefter på **SAML 2.0** fanen.

    ![Azure AD-Single Sign-On][24] 

1. Kopiere værdien under **feltet SAML endoiint for litmos er:**.

    ![Azure AD-Single Sign-On][26] 


3. Udfør følgende trin på siden **Konfigurer App indstillinger** dialogboksen i Azure klassisk portalen:

    ![Azure AD-Single Sign-On][8] 
 
    en. Skriv URL-adressen bruges af dine brugere til at logge på dit Litmos program i tekstfeltet **id** (f.eks.: *https://azureapptest.litmos.com/account/Login*).
     
    b. Indsæt den værdi, du har kopieret fra programmet Litmos i det forrige trin i tekstfeltet **Svar URL-adresse** .

    c. Klik på **Næste**.
 
4. På siden **Konfigurer single sign-on – på Litmos** skal du udføre følgende trin:

    ![Azure AD-Single Sign-On][2] 

    en. Klik på Hent certifikat, og derefter gemme filen på din computer.


1. I din **Litmos** program skal du udføre følgende trin:

    ![Azure AD-Single Sign-On][25] 

    en. Klik på **Aktiver SAML**.

    b. Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

    >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    c. Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **SAML x.509-certifikat** .

    d. Klik på **Gem ændringer**.


6. Vælge enkelt sign-on – konfiguration bekræftelsen på portalen Azure AD klassisk, og klik derefter på **Næste**. 

    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
  
    ![Azure AD-Single Sign-On][11]


20. Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i menuen øverst. 

    ![Konfigurere Single Sign-On][12]


24. I dialogboksen **Tilføj bruger attribut** skal du udføre følgende trin: 

    ![Konfigurere Single Sign-On][14]

  	| Attributnavn | Attributværdien |
  	| ---            | ---             |
  	| Mail          | User.mail       |
  	| Fornavn      | User.givenname  |
  	| Efternavn       | User.surname    |

    For hver datarække med i ovenstående tabel, kan du udføre følgende trin:
   
    en. Klik på **Tilføj bruger attributten**. 

    ![Konfigurere Single Sign-On][15]


    en. Skriv **Attributnavn, der** vises for den pågældende række i tekstfeltet **Attributnavn** .

    b. Vælg den **Værdi for attributten** vises for den pågældende række.

    c. Klik på **udført** for at lukke dialogboksen **Tilføj bruger attribut** .


25. Klik på **Anvend ændringer**. 

    ![Konfigurere Single Sign-On][16]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.  

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure clasic portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)  

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) 
 
4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)  

    en. Vælg **ny bruger i organisationen**som **Type af bruger**.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) 
 
    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .
    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) 
 
8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) 
  
    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   

  
 
### <a name="creating-a-litmos-test-user"></a>Oprette en Litmos testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i Litmos.  
Programmet Litmos understøtter Just-in-Time klargøring. Betyder en brugerkonto oprettes automatisk eventuelt under et forsøg på at få adgang til det program, ved hjælp af panelet adgang.

**Hvis du vil oprette en bruger, kaldet Britta Simon i Litmos, skal du udføre følgende trin:**


1. Log på din Litmos virksomheds websted (f.eks.: *https://azureapptest.litmos.com/account/Login*) som administrator.

    ![Azure AD-Single Sign-On][21] 


1. Klik på **konti**på navigationslinjen i venstre side.

    ![Azure AD-Single Sign-On][22] 


1. Klik på fanen **integrationer** .

    ![Azure AD-Single Sign-On][23] 


1. Rul ned til **3 part integrationer**under fanen **integration** , og klik derefter på **SAML 2.0** fanen.

    ![Azure AD-Single Sign-On][24] 

1. Vælg **Generer brugere:**.

    ![Azure AD-Single Sign-On][27] 


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til Litmos.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Litmos, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Litmos**i listen over programmer.

    ![Tildele bruger][202] 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet Litmos i panelet adgang, du bør få automatisk logget på Litmos-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png






<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Amazon Web Service (AWS) | Microsoft Azure"
    description="Lær, hvordan du bruger Amazon Web Services (AWS) med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!"
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


# <a name="tutorial-azure-active-directory-integration-with-amazon-web-service-aws"></a>Selvstudium: Azure Active Directory-integration med Amazon Web Service (AWS)

Formålet med dette selvstudium er at se, hvordan du integrerer Amazon Web Service (AWS) med Azure Active Directory (Azure AD).  
Integrering af Amazon Web Service (AWS) med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, der har adgang til Amazon Web Service (AWS) 
- Du kan aktivere dine brugere til automatisk få logget på til Amazon Web Service (AWS) (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Hvis du vil konfigurere Azure AD-integration med Amazon Web Service (AWS), du skal bruge følgende elementer:

- Et Azure AD-abonnement
- En Amazon Web Service (AWS) enkelt – Log på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af tre primære dokumentkomponenter:

1. Tilføje Amazon Web Service (AWS) fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-amazon-web-service-aws-from-the-gallery"></a>Tilføje Amazon Web Service (AWS) fra galleriet
Hvis du vil konfigurere integration af Amazon Web Service (AWS) i Azure AD, skal du tilføje Amazon Web Service (AWS) fra galleriet til listen over administrerede SaaS apps.

### <a name="to-add-amazon-web-service-aws-from-the-gallery-perform-the-following-steps"></a>Hvis du vil tilføje Amazon Web Service (AWS) fra galleriet, skal du udføre følgende trin:

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1] 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu. 
   
    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden. 
   
    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**. 
   
    ![Programmer][4]

6. Skriv **Amazon Web Service (AWS)**i søgefeltet.
   
    ![Programmer][5]

7. Vælg **Amazon Web Service (AWS)**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Programmer][6]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med Amazon Web Service (AWS) baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal, hvad der er modstykke brugeren i Amazon Web Service (AWS) til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Amazon Web Service (AWS) oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Amazon Web Service (AWS).
 
For at konfigurere og teste Azure AD single sign-on – med Amazon Web Service (AWS), skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD enkelt Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en Amazon Web Service (AWS) teste bruger](#creating-a-halogen-software-test-user)** - skal have et modstykke Britta Simon i Amazon Web Service (AWS), der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Konfiguration af Azure AD enkelt Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i programmet på Amazon Web Service (AWS).  
Amazon Web Service (AWS) programmet forventer SAML påstande i et bestemt format, som kræver, at du kan tilføje brugerdefineret attributtilknytninger til konfigurationen af **saml token attributter** . Følgende skærmbillede viser et eksempel af dette.


![Konfigurere Single Sign-On][27]

**Hvis du vil konfigurere Azure AD single sign-on med Amazon Web Service (AWS), skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Amazon Web Service (AWS)** application integration.

    ![Konfigurere Single Sign-On][7]

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge til Amazon Web Service (AWS)** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On][8]

3. Klik på Næste på siden **Konfigurer indstillinger for App** -dialogboksen. 

    ![Konfigurere indstillinger for][9]
 
4. Klik på **Hent metadata**på siden **Konfigurer single sign-on – på Amazon Web Service (AWS)** , og Gem derefter metadatafilen lokalt på din computer.

    ![Konfigurere Single Sign-On][10]

5. I et andet browservindue sign-on til webstedet Amazon Web Service (AWS) virksomhed som administrator.

6. Klik på **Console hjem**.

    ![Konfigurere Single Sign-On][11]

7. Klik på **tilgængelig**. 

    ![Konfigurere Single Sign-On][12]

8. Klik på **Identitetsudbydere**, og klik derefter på **Opret udbyder**. 

    ![Konfigurere Single Sign-On][13]

9. På siden **Konfigurer udbyder** dialogboksen skal du udføre følgende trin: 

    ![Konfigurere Single Sign-On][14]

     en. Vælg **SAML**som **Udbyder Type**.

     b. Skriv et udbydernavn i tekstfeltet **Navn på udbyder** (f.eks.: *WAAD*).

     c. For at overføre metadatafilen hentede, skal du klikke på **Vælg fil**.

     d. Klik på **Næste trin**.


10. Klik på **Opret**på siden **Bekræft udbyder oplysninger** dialogboksen. 

    ![Konfigurere Single Sign-On][15]

11. Klik på **roller**, og klik derefter på **Opret ny rolle**. 

    ![Konfigurere Single Sign-On][16]

12. I dialogboksen **Angiv rollenavn** skal du udføre følgende trin: 

    ![Konfigurere Single Sign-On][17]

    en. Skriv et rollenavn i tekstfeltet **Rollenavn** (f.eks.: *TestUser*).

    b. Klik på **Næste trin**.

13. I dialogboksen **Vælg rolletype** skal du udføre følgende trin: 

    ![Konfigurere Single Sign-On][18]

    en. Vælg **rolle til identitet udbyder adgang**.

    b. Klik på **Vælg**i sektionen **Giv Web Single Sign-On (WebSSO) adgang til SAML udbydere** .


14. I dialogboksen **Oprette tillid til** skal du udføre følgende trin:  

    ![Konfigurere Single Sign-On][19]
     
     en. Som SAML-udbyder, vælge den SAML-udbyder, du har oprettet previousley (f.eks.: *WAAD*) 

     b. Klik på **Næste trin**.


15. Dialogboksen **Bekræft rolle tillid til** , klik på **Næste trin**. 

    ![Konfigurere Single Sign-On][32]


16. Dialogboksen **Vedhæfte politik** , klik på **Næste trin**.  

    ![Konfigurere Single Sign-On][33]


17. I dialogboksen **Gennemse** skal du udføre følgende trin:   

    ![Konfigurere Single Sign-On][34]

     en. Kopiér værdien **Rolle Lær** .

     b. Kopiere værdien, **Der er tillid til enheder** Lær.

     c. Klik på **Opret rolle**. 

18. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **Næste**.

    ![Hvad er Azure AD-forbindelse][20]

19. Klik på **udført** for at lukke dialogboksen **Konfigurer single sign-on –** på siden **enkelt sign-on – bekræftelse** .

    ![Hvad er Azure AD-forbindelse][22]


20. Klik på **attributter** for at åbne dialogboksen **SAML Token attributter** i menuen øverst. 

    ![Konfigurere Single Sign-On][21]

21. Klik på **Tilføj bruger attributten**. 

    ![Konfigurere Single Sign-On][23]

22. I dialogboksen Tilføj bruger attribut skal du udføre følgende trin. 

    ![Konfigurere Single Sign-On][24] 

    en. Skriv **https://aws.amazon.com/SAML/Attributes/Role**i tekstfeltet **Attributnavn** .

    b. I tekstfeltet **Attributværdien** skal du skrive **[rolle Lær værdien], [værdien der er tillid til enhed Lær]**.

    >[AZURE.TIP] Disse er de værdier, du har kopieret fra dialogboksen gennemse, når du har oprettet din rolle. 

    c. Klik på **udført** for at lukke dialogboksen **Tilføj bruger attribut** .

23. Klik på **Tilføj bruger attributten**. 

    ![Konfigurere Single Sign-On][23]


24. I dialogboksen Tilføj bruger attribut skal du udføre følgende trin. 

    ![Konfigurere Single Sign-On][25]


     en. Skriv **https://aws.amazon.com/SAML/Attributes/RoleSessionName**i tekstfeltet **Attributnavn** .

     b. I tekstfeltet **Attributværdien** skal du skrive eller vælge **user.userprincipalname** fra på rullelisten.
     
    ![Konfigurere Single Sign-On][35]
    

     c. Klik på **udført** for at lukke dialogboksen **Tilføj bruger attribut** .


25. Klik på **Anvend ændringer**. 

    ![Konfigurere Single Sign-On][26]





### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger

Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

![Oprette en Azure AD testbruger](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png) 
 
4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png) 

  1. Vælg ny bruger i organisationen som Type af bruger.
  2. Skriv **BrittaSimon**i brugernavn **tekstfelt**.
  3. Klik på Næste.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** txtbox, type **Thomas**.
  
    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .
  
    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png) 
 
8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.
  
    b. Klik på **udført**.   
  
 
### <a name="creating-a-amazon-web-service-aws-test-user"></a>Oprette en Amazon Web Service (AWS) testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i Amazon Web Service (AWS).

### <a name="to-create-a-user-called-britta-simon-in-amazon-web-service-aws-perform-the-following-steps"></a>Hvis du vil oprette en bruger, kaldet Britta Simon i Amazon Web Service (AWS), skal du udføre følgende trin:

1. Log på webstedet **Amazon Web Service (AWS)** virksomhed som administrator.

2. Klik på ikonet **Console hjem** . 

    ![Konfigurere Single Sign-On][11]

3. Klik på identitet og få adgang til administration. 

    ![Konfigurere Single Sign-On][28]

4. Klik på brugere i dashboardet til, og klik derefter på Opret nye brugere. 

    ![Konfigurere Single Sign-On][29]

5. I dialogboksen Opret bruger skal du udføre følgende trin: 

    ![Konfigurere Single Sign-On][30]

     en. Skriv Brita Simons brugernavn (userprincipalname) Azure AD i tekstfelterne **Angive brugernavne** .

     b. Klik på **Opret**.




### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til Amazon Web Service (AWS).

![Tildele bruger][31]

**Hvis du vil tildele Britta Simon til CloudPassage, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][26]

2. Vælg **Amazon Web Service (AWS)**i listen over programmer.

    ![Tildele bruger][27]

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][25]

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][29]

### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet Amazon Web Service (AWS) i panelet adgang, du bør få automatisk logget på Amazon Web Service (AWS)-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png























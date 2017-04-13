<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Questetra BPM pakke | Microsoft Aure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Questetra BPM pakke."
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
    ms.date="10/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Selvstudium: Azure Active Directory-integration med Questetra BPM pakke

Formålet med dette selvstudium er at se, hvordan du integrerer Questetra BPM pakke med Azure Active Directory (Azure AD).  
Integrering af Questetra BPM pakke med Azure AD giver dig følgende fordele: 

- Du kan styre i Azure AD, der har adgang til Questetra BPM pakke 
- Du kan aktivere dine brugere til automatisk få logget på til Questetra BPM pakke (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger 

Hvis du vil konfigurere Azure AD-integration med Questetra BPM pakke, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- En [Questetra BPM pakke](https://senbon-imadegawa-988.questetra.net/) enkelt – Log på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af tre primære dokumentkomponenter:

1. Tilføje Questetra BPM pakke fra galleriet 
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Tilføje Questetra BPM pakke fra galleriet
Hvis du vil konfigurere integrationen mellem Questetra BPM pakke Azure AD, skal du tilføje Questetra BPM pakke fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Questetra BPM pakke fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Questetra BPM pakke**i søgefeltet.

    ![Programmer][5]

7. Vælg **Questetra BPM pakke**i resultatruden, og klik derefter på **udført** for at tilføje programmet.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med Questetra BPM pakke baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Questetra BPM pakke til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Questetra BPM programpakken oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Questetra BPM pakke.
 
For at konfigurere og teste Azure AD single sign-on – med Questetra BPM pakke, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en Questetra BPM pakke teste bruger](#creating-a-questetra-bpm-suite-test-user)** - skal have et modstykke Britta Simon i Questetra BPM pakke, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i programmet Questetra BPM pakke.

**Hvis du vil konfigurere Azure AD single sign-on med Questetra BPM pakke, skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Questetra BPM pakke** application integration.

    ![Konfigurere Single Sign-On][8]

2. Vælg **Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge Questetra BPM pakke** .

    ![Azure AD-Single Sign-On][9]


3. Log på din **Questetra BPM pakke** virksomheds websted som en administrator i et andet browservindue.

4. Klik på **Systemindstillinger**i menuen øverst. 

    ![Azure AD-Single Sign-On][10]

5. For at åbne siden **SingleSignOnSAML** , skal du klikke på **SSO (SAML)**. 

    ![Azure AD-Single Sign-On][11]


6. Udfør følgende trin på siden **Konfigurer App indstillinger** dialogboksen i Azure klassisk portalen: 

    ![Konfigurere indstillinger for][13]
 
    en. Ved du **Questetra BPM pakke** virksomheds websted, i afsnittet SP oplysninger Kopier **ACS URL-adressen**, og Indsæt det i tekstfeltet **Log på URL-adresse** .

    b. Ved du **Questetra BPM pakke** virksomheds websted, i afsnittet SP oplysninger Kopiér **Enheds-ID**, og Indsæt det i tekstfeltet **Udsteder URL-adresse** .

    c. Ved du **Questetra BPM pakke** virksomheds websted, i afsnittet SP oplysninger Kopier **ACS URL-adressen**, og Indsæt det i tekstfeltet **Svar URL-adresse** .

    d. Klik på **Næste**.

 
7. Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Questetra BPM pakke** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On][14]


8. På du **Questetra BPM pakke** virksomheds websted, kan du udføre følgende trin: 

    ![Konfigurere Single Sign-On][15]

    en. Vælg **Aktiver Single Sign-On**.
     
    b. Kopiér værdien **Udsteder URL-adressen** på Azure klassisk-portalen, og Indsæt det i tekstfeltet **Enheds-ID** .

    c. Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure klassisk-portalen, og Indsæt det i tekstfeltet **logon URL-adressen** .

    d. Kopiér værdien **Enkelt Sign-Out URL-adressen** på Azure klassisk-portalen, og Indsæt det i tekstfeltet **hvor URL-adressen** .

    e. I tekstfeltet **NameID format** skal du skrive **urn: oasis: navne: tc: SAML:1.1:nameid-format: mailadresse**.


    f. Oprette en kodet base-64-fil fra dit hentede certifikat. 

    >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

    g. Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder, og Indsæt det i tekstfeltet **Validering certifikat** . 

    h. Klik på **Gem**.


9. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **Næste**. 

    ![Hvad er Azure AD-forbindelse][17]


10. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  

    ![Hvad er Azure AD-forbindelse][18]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i Azure klassisk portal kaldet Britta Simon.

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette Azure AD testbruger][100] 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette Azure AD testbruger][101] 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**. 

    ![Oprette Azure AD testbruger][102] 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette Azure AD testbruger][103]
 
    en. Vælg **ny bruger i organisationen**som **Type af bruger**.
  
    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på Næste.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: 

    ![Oprette Azure AD testbruger][104] 
  
    en. Skriv **Britta**i tekstfeltet **Fornavn** . 
 
    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette Azure AD testbruger][105]  

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette Azure AD testbruger][106]   
  
    en. Notér værdien af den **Nye adgangskode**.
  
    b. Klik på **udført**.   
  
 
### <a name="creating-a-questetra-bpm-suite-test-user"></a>Oprette en Questetra BPM pakke testbruger

Formålet med dette afsnit er at oprette en bruger, kaldet Britta Simon i Questetra BPM pakke.

**Oprette en bruger, kaldet Britta Simon i Questetra BPM pakke, skal du udføre følgende trin:**

1.  Sign-on til webstedet Questetra BPM pakke virksomhed som administrator.
2.  Gå til **systemindstillinger > brugerlisten > ny bruger**. 
3.  I dialogboksen Ny bruger skal du udføre følgende trin: 

    ![Oprette testbruger][300] 

    en. I tekstfeltet **navn** skal du skrive Brittas brugernavn i Azure AD.

    b. Skriv Brittas brugernavn Azure AD i tekstfeltet **mail** .

    c. I tekstfeltet **adgangskode** skal du skrive en adgangskode.

4.  Klik på **Tilføj ny bruger**.



### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til Questetra BPM pakke.

![Hvad er Azure AD-forbindelse][200]

**Hvis du vil tildele Britta Simon til Questetra BPM pakke, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Hvad er Azure AD-forbindelse][201]

2. Vælg **Questetra BPM pakke**i listen over programmer.

    ![Hvad er Azure AD-forbindelse][205]

1. Klik på **brugere**i menuen øverst.

    ![Hvad er Azure AD-forbindelse][202]

1. Vælg **Britta Thomas**på listen brugere.

    ![Hvad er Azure AD-forbindelse][203]

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Hvad er Azure AD-forbindelse][204]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet Questetra BPM pakke i panelet adgang, du bør få automatisk logget på Questetra BPM pakke-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png 
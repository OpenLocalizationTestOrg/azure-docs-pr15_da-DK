<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Cisco knallertmotor | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Cisco gnister."
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


# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Selvstudium: Azure Active Directory-integration med Cisco knallertmotor

I dette selvstudium lærer du at integrere Cisco knallertmotor med Azure Active Directory (Azure AD).

Integrering af Cisco knallertmotor med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Cisco knallertmotor
- Du kan aktivere dine brugere til automatisk få logget på til Cisco knallertmotor (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Cisco gnister, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- En **Cisco knallertmotor** enkelt – Log på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø. Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Cisco knallertmotor fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-cisco-spark-from-the-gallery"></a>Tilføje Cisco knallertmotor fra galleriet
Hvis du vil konfigurere integrationen mellem Cisco knallertmotor Azure AD, skal du tilføje Cisco knallertmotor fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Cisco knallertmotor fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Cisco knallertmotor**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)

7. Vælg **Cisco knallertmotor**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med Cisco knallertmotor baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Cisco knallertmotor er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Cisco knallertmotor oprettes.
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Cisco gnister. For at konfigurere og teste Azure AD single sign-on – med Cisco gnister, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
4. **[Oprette en Cisco knallertmotor teste bruger](#creating-a-cisco-spark-test-user)** - skal have et modstykke Britta Simon i Cisco gnister, der er knyttet til Azure AD-repræsentation af hende.
5. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

Formålet med dette afsnit er at aktivere Azure AD enkeltlogon i portalen Azure klassisk og for at konfigurere single sign-on i Cisco knallertmotor programmet.

Cisco knallertmotor programmet forventer SAML påstande skal indeholde bestemte attributter. Konfigurer følgende attributter for dette program. Du kan administrere værdierne i disse attributter fra fanen **"Atrributes"** af programmet. Følgende skærmbillede viser et eksempel af dette.

![Konfigurere Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Hvis du vil konfigurere Azure AD single sign-on med Cisco gnister, kan du udføre følgende trin:**

1. Klik på **attributter**i Azure klassisk-portalen på **Cisco knallertmotor** programmets integrationsside, i menuen i øverst.
     
    ![Konfigurere Single Sign-On][5]


2. I dialogboksen **SAML token attributter** skal du udføre følgende trin:

    en. Klik på **Tilføj bruger attribut** for at åbne dialogboksen **Tilføj bruger Attribure** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
    
    b. Skriv **uid**i tekstfeltet **Attributnavn** .
    
    c. Vælg **user.userprincipal**på listen **Attributværdien** .
    
    d. Klik på **udført**. Derefter **Anvend ændringer** i bunden af siden.

3. Klik på **Hurtig Start**i menuen øverst.

    ![Konfigurere Single Sign-On][6]

4. I portalen klassisk, på siden **Cisco knallertmotor** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On][7] 

5. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Cisco gnister** , og klik derefter på **Næste**.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)

6. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)


    en. I tekstfeltet Log på URL-adresse skal du skrive en URL-adresse ved hjælp af følgende mønster: `https://web.ciscospark.com/#/signin`.

    b. Klik på **Næste**.


7. Klik på på siden **Konfigurer single sign-on – på Cisco knallertmotor** **hente metadata**, og derefter gemme filen på din computer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)

8. Log på [Cisco skyen samarbejde administration](https://admin.ciscospark.com/) med dit fulde administratorrettigheder.
9. Vælg **Indstillinger** , og klik på **Rediger**under sektionen **godkendelse** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)

10. Vælg **integrere en 3 tredjepart identitetsudbyder. (Avanceret)** og gå til det næste skærmbillede.
11. Klik på **Hent Metadata fil** og Gem filen på din computer.
12. På siden **Importér Idp Metadata** enten træk og slip Azure AD metadatafilen ind på siden eller Brug indstillingen browser til at finde og overføre filen Azure AD metadata. Derefter Vælg **kræver certifikat, der er signeret af et nøglecenter i Metadata (mere sikker)** , og klik på **Næste**. 

    ![Konfigurere Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

13. Vælg **Test SSO forbindelse**, og når der åbnes en ny fane i browseren, skal godkende med Azure AD ved at logge ind.
14. Gå tilbage til fanen **Cisco skyen samarbejde Management** browser. Hvis testen blev fuldført, skal du vælge **denne test fuldføres. Single Sign-On indstillingen Aktivér** , og klik på **Næste**.

7. Vælge enkelt sign-on – konfiguration bekræftelsen på portalen Azure AD klassisk, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

8. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
    
    ![Azure AD-Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:
 
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-cisco-spark-test-user"></a>Oprette en Cisco knallertmotor testbruger

I dette afsnit opretter du en bruger, kaldet Britta Simon i Cisco gnister. I dette afsnit opretter du en bruger, kaldet Britta Simon i Cisco gnister.

1. Gå til [Administration af Cisco skyen samarbejde](https://admin.ciscospark.com/) med din fulde administratorrettigheder.
2. Klik på **brugere** og derefter **administrere brugere**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Vælg **manuelt at tilføje eller redigere brugere** i vinduet **Administrer bruger** , og klik på **Næste**.
4. Vælg **navne og e-mail-adresse**. Udfyld derefter tekstfeltet som følger:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    en. Skriv **Britta** i tekstfeltet **Fornavn**

    b. Skriv **Thomas** i tekstfeltet **Efternavn**

    c. I tekstfeltet **mailadresse** skal du skrive**britta.simon@contoso.com**

5. Klik på plustegnet for at tilføje Britta Simon. Klik derefter på **Næste**.
6. Klik på **Gem** og derefter **Afslut**i vinduet **Tilføj tjenester for brugere** .

### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til Cisco gnister.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Cisco gnister, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Cisco knallertmotor**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen alle brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på feltet Cisco knallertmotor i panelet adgang, du bør få automatisk logget på Cisco knallertmotor-program.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png

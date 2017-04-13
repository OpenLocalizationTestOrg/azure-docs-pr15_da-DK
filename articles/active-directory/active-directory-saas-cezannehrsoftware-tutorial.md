<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Cezanne HR Software | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Cezanne HR Software."
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
    ms.date="10/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Selvstudium: Azure Active Directory-integration med Cezanne HR Software

Formålet med dette selvstudium er at se, hvordan du integrerer Cezanne HR Software med Azure Active Directory (Azure AD).

Integrering af Cezanne HR Software med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Cezanne HR Software
- Du kan aktivere dine brugere til automatisk få logget på for Cezanne HR Software (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Cezanne HR Software, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Cezanne HR Software single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
Formålet med dette selvstudium er at gør det muligt at teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Cezanne HR Software fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Tilføje Cezanne HR Software fra galleriet
Hvis du vil konfigurere integrationen mellem Cezanne HR Software Azure AD, skal du tilføje Cezanne HR Software fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Cezanne HR Software fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.
    
    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.
    
    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Cezanne HR Software**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. Vælg **Cezanne HR Software**i resultatpanelet, og klik derefter på **udført** for at tilføje programmet.

    ![Valg af appen i galleriet](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Formålet med dette afsnit er at se, hvordan du konfigurerer og afprøver Azure AD single sign-on – med Cezanne HR Software, der er baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Cezanne HR softwaren til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Cezanne HR Software oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Cezanne HR Software.

For at konfigurere og teste Azure AD single sign-on – med Cezanne HR Software, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en Cezanne HR Software teste bruger](#creating-a-cezanne-hr-software-test-user)** - skal have et modstykke Britta Simon i Cezanne HR Software, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen klassisk og konfigurere Enkeltlogon i programmet i Cezanne HR.

**Hvis du vil konfigurere Azure AD single sign-on – med Cezanne HR Software skal du udføre følgende trin:**

1. I portalen klassisk, på siden **Cezanne HR Software** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Cezanne HR Software** , og klik derefter på **Næste**.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. Udføre følgende trin, og klik på **Næste**på siden **Konfigurer App indstillinger** dialogboksen:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

    en. I tekstfeltet **Log på URL-adresse** skal du skrive en URL-adresse ved hjælp af følgende mønster: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.

    b. I tekstfeltet **Svar URL-adresse** skal du skrive en URL-adresse ved hjælp af følgende mønster: `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.

    c. Klik på **Næste**

    > [AZURE.NOTE] Vær opmærksom på, at du skal opdatere disse værdier med de faktiske Log på URL-adresse og svar URL-adresse. Hent disse værdier, skal du kontakte Cezanne HR Software supportteam via <mailto:info@cezannehr.com>.

4. Udføre følgende trin, og klik på **Næste**på siden **Konfigurer single sign-on – på Cezanne HR Software** :

    ![Konfigurere Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.

5. I et andet browservindue sign-on til din Cezanne HR Software lejer som administrator.

6. Klik på **System konfiguration**på den venstre navigationsrude. Gå til **sikkerhedsindstillinger for**. Gå derefter til **Konfiguration af enkelt Sign-On**.

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. I dialogboksen **Tillad brugere at logge på ved hjælp af følgende enkelt enkeltlogon (SSO) tjeneste** panel afkrydsningsfeltet **SAML 2.0** og vælge indstillingen **Avanceret konfiguration** ud for den.

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. Klik på knappen **Tilføj ny** .

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. Udfør følgende trin på **SAML 2.0-IDENTITETSUDBYDERE** sektion.

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    en. Skriv navnet på din identitetsudbyder som **Vist navn**.

    b. I det **Enheds-id** placere tekstfeltet værdien af **Enheds-ID** fra Azure AD-program konfigurationsguiden.

    c. Ændre den **SAML Binding** til "POST".

    d. I **Sikkerhedstoken tjenesteslutpunkt** placere tekstfeltet værdien af **Enkelt Sign-on URL-adressen** fra Azure AD-program konfigurationsguiden.

    e. Angiv 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' i **id-attributten brugernavn**.

    f. Klik på **Overfør** ikon for at overføre det hentede certifikat fra Azure AD.

    g. Klik på knappen **Ok** . 

10. Klik på knappen **Gem** .

    ![Konfigurere enkelt Sign-On på App-side](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

12. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
    
    ![Azure AD-Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
Formålet med dette afsnit er at oprette en testbruger i portalen klassisk kaldet Britta Simon.

![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk Portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. Skriv **Thomas**i tekstfeltet **Efternavn** .

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:
    
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   



### <a name="creating-a-cezanne-hr-software-test-user"></a>Oprette en Cezanne HR Software testbruger

For at aktivere Azure AD-brugere at logge på Cezanne HR Software, skal de klargøres til Cezanne HR Software. Klargøring af er Cezanne HR Software, en manuel opgave.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Hvis du vil tildele en brugerkonto, skal du udføre følgende trin:

1.  Log på webstedet Cezanne HR Software virksomhed som administrator.

2.  Klik på **System konfiguration**på den venstre navigationsrude. Gå til at **administrere brugere**. Gå derefter til **Tilføj ny bruger**.

    ![Ny bruger] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Ny bruger")

3.  I sektionen med **Oplysninger om Person** , du Udfør under trin:

    ![Ny bruger] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Ny bruger")

    en. Angive **intern bruger** som fra.

    b. Skriv **Britta**i tekstfeltet **Fornavn** .  

    c. Skriv **Thomas**i tekstfeltet **Efternavn** .

    d. Skriv mailadressen på Britta Simon konto i tekstfeltet **E-mail** .

4.  Udføre på **Kontooplysninger** sektion under trin:

    ![Ny bruger] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Ny bruger")

    en. I tekstfeltet **brugernavn** skal du skrive mailadressen på Britta Simon.

    b. I tekstfeltet **adgangskode** skal du skrive adgangskoden for Britta Simon konto.

    c. Vælg **HR Professional** som **sikkerhedsrolle**.

    d. Klik på **OK**.

5. Gå til fanen **Single Sign-On** , og vælg **Tilføj ny** i området **SAML 2.0-id'er** .

    ![Bruger] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Bruger")

6. Vælg din identitetsudbyder for **Identitetsudbyder** i tekstboksen i **Bruger-id**, og Angiv mailadressen Britta Simon konto.

    ![Bruger] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Bruger")
    
7. Klik på knappen **Gem** .

    ![Bruger] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Bruger")


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Formålet med dette afsnit er at aktivere Britta Simon bruge Azure enkeltlogon ved at give adgang til Cezanne HR Software.
    
![Tildele bruger][200]

**Hvis du vil tildele Britta Simon Cezanne HR Software skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.
    
    ![Tildele bruger][201]

2. Vælg **Cezanne HR Software**i listen over programmer.
    
    ![Konfigurere Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. Klik på **brugere**i menuen øverst.
    
    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. Klik på **Tildel**på værktøjslinjen nederst.
    
    ![Tildele bruger][205]

### <a name="testing-single-sign-on"></a>Test af enkeltlogon

Formålet med dette afsnit er at teste din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.
 
Når du klikker på feltet Cezanne HR Software i panelet adgang, du bør få automatisk logget på Cezanne HR Software-program.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png

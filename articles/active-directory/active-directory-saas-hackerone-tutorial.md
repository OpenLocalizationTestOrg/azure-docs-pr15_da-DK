<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med HackerOne | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og HackerOne."
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


# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Selvstudium: Azure Active Directory-integration med HackerOne

I dette selvstudium, kan du integrere HackerOne med Azure Active Directory (Azure AD).

Integrering af HackerOne med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til HackerOne
- Du kan aktivere dine brugere til automatisk få logget på til HackerOne (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med HackerOne, du skal bruge følgende elementer:

- Et Azure-abonnement
- HackerOne enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium skal du konfigurere og teste Azure AD enkeltlogon i et testmiljø.  
Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje HackerOne fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-hackerone-from-the-gallery"></a>Tilføje HackerOne fra galleriet
Hvis du vil integrere HackerOne i Azure AD, skal du tilføje HackerOne fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje HackerOne fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude. 

    ![Active Directory][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

![Programmer][4]

6. Skriv **HackerOne**i søgefeltet.

![Oprette en Azure AD testbruger](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

7. Vælg **HackerOne**i resultatruden, og klik derefter på **udført** for at tilføje programmet.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
Derefter skal du konfigurere og teste Azure AD single sign-on – med HackerOne baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i HackerOne til en bruger i Azure AD er for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i HackerOne oprettes.  
Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i HackerOne.

For at konfigurere og teste Azure AD single sign-on – med HackerOne, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en HackerOne teste bruger](#creating-a-hackerone-test-user)** - skal have et modstykke Britta Simon i Bekræft, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD-Single Sign-On

Derefter skal aktivere du Azure AD enkeltlogon i klassisk portalen og til at konfigurere single sign-on i HackerOne programmet.

Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

**Hvis du vil konfigurere Azure AD single sign-on – med HackerOne skal du udføre følgende trin:**

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **HackerOne** application integration.

    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge HackerOne** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) 

3. Udføre følgende trin, og klik derefter på **Næste**på siden **Konfigurer App indstillinger** dialogboksen:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) 


    en. Skriv URL-adressen bruges af dine brugere at sign-on til dit HackerOne program ved hjælp af følgende mønster i tekstfeltet **Log på URL-adresse** : **"https://hackerone.com/\<firmanavn\>/authentication"**. 

    b. Kontakt supportteamet HackerOne via [support@hackerone.com](mailto:support@hackerone.com) at få din lejer URL-adresse, hvis du ikke kender den.

    c. Skriv lejer URL-adressen i tekstfeltet **id** . 

    d. Klik på **Næste**.


4. Udføre følgende trin, og klik derefter på **Næste**på siden **Konfigurer single sign-on – på HackerOne** :

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) 

    en. Klik på **Hent certifikat**, og derefter gemme filen på din computer.

    b. Klik på **Næste**.


1. Sign-on til din HackerOne lejer som administrator.

1. Klik på **Indstillinger**i menuen øverst.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 

1. Gå til "**godkendelse**", og klik på "**Tilføj SAML indstillinger**".

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 


1. I dialogboksen **Indstillinger for SAML** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 

    en. I tekstfeltet **Maildomæne** skal du skrive et registreret domæne.

    b. Kopiere **Enkelt Sign-On URL-adressen**på Azure klassisk-portalen, og Indsæt det i tekstfeltet enkelt Log på URL-adresse.

    c. Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

       >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)
    
    d. Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og derefter indsætter det til den **X509 certifikat** tekstfelt.

    e. Klik på **Gem**


1. I dialogboksen Indstillinger for proxygodkendelse skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 

    en. Klik på **Kør test**.

    b. Hvis værdien af **Status** felt er lig med **sidste teste status: oprettet**, Kontakt supportteamet HackerOne via [support@hackerone.com](mailto:support@hackerone.com) til at anmode om en gennemgang af konfigurationen.


6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure-klassisk portalen, og klik derefter på **Næste**.

    ![Azure AD-Single Sign-On][10]

7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
 
    ![Azure AD-Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger

Derefter skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.  

![Oprette Azure AD-bruger][20]

**For at oprette en SECURE levere testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.
    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   


### <a name="creating-a-hackerone-test-user"></a>Oprette en HackerOne testbruger

Derefter skal oprette du en bruger, kaldet Britta Simon i HackerOne. HackerOne understøtter just-in-time klargøring, som er aktiveret som standard.

Der er ingen handlingspunkt for dig i dette afsnit. Når du åbner HackerOne, oprettes en ny bruger, hvis den ikke findes. [Konfiguration af Azure AD-Single Sign-On](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Hvis du har brug at oprette en bruger manuelt, skal du kontakte Bekræft supportteam.




### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

Derefter skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til HackerOne.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til HackerOne, skal du udføre følgende trin:**

1. I Azure klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **HackerOne**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) 

1. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203] 

1. Vælg **Britta Thomas**på listen brugere.

2. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]



### <a name="testing-single-sign-on"></a>Test af Single Sign-On

Til sidst skal teste du din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.  
Når du klikker på feltet HackerOne i panelet adgang, du bør få automatisk logget på HackerOne-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png
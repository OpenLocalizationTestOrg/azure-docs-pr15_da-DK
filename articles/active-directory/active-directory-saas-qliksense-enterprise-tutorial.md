<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Qlik mening Enterprise | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Qlik mening Enterprise."
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
    ms.date="08/31/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Selvstudium: Azure Active Directory-integration med Qlik mening Enterprise

I dette selvstudium lærer du at integrere Qlik mening Enterprise med Azure Active Directory (Azure AD).

Integrering af Qlik mening Enterprise med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til Qlik mening Enterprise
- Du kan aktivere dine brugere til automatisk få logget på til Qlik mening Enterprise (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med Qlik mening Enterprise, du skal bruge følgende elementer:

- Et Azure AD-abonnement
- Qlik mening Enterprise single-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje Qlik mening Enterprise fra galleriet
2. Konfigurere og teste Azure AD single sign-on –


## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Tilføje Qlik mening Enterprise fra galleriet
Hvis du vil konfigurere integrationen mellem Qlik mening Enterprise Azure AD, skal du tilføje Qlik mening Enterprise fra galleriet til listen over administrerede SaaS apps.

**Hvis du vil tilføje Qlik mening Enterprise fra galleriet, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Active Directory][1]
2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Programmer][4]

6. Skriv **Qlik mening Enterprise**i søgefeltet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. Vælg **Qlik mening Enterprise**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med Qlik mening Enterprise baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i Qlik mening Enterprise er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i Qlik mening Enterprise oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i Qlik mening Enterprise.

For at konfigurere og teste Azure AD single sign-on – med Qlik mening Enterprise, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en Qlik mening Enterprise teste bruger](#creating-a-qliksense-enterprise-test-user)** - skal have et modstykke Britta Simon i Qlik mening Enterprise, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

I dette afsnit, skal du aktivere Azure AD enkeltlogon i portalen klassisk og konfigurere Enkeltlogon i dit Qlik mening Enterprise-program.


**Hvis du vil konfigurere Azure AD single sign-on med Qlik mening Enterprise, skal du udføre følgende trin:**

1. I portalen klassisk, på siden **Qlik mening Enterprise** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .
     
    ![Konfigurere Single Sign-On][6] 

2. Vælg **Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge Qlik mening Enterprise** .

    ![Konfigurere Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 

    en. Skriv URL-adressen bruges af dine brugere at logge på din Qlik mening Enterprise-program, ved hjælp af følgende mønster i tekstfeltet **Log på URL-adresse** : **https://\<Qlik mening fuldt Qualifed Hostname\>: 443 / < virtuelle Proxy præfiks\>/samlauthn/**.
    
    > [AZURE.NOTE] Bemærk, at den efterfølgende skråstreg i slutningen af denne URI.  Det er nødvendigt.

    b. Klik på **Næste**
 
4. På siden **Konfigurer single sign-on – på Qlik mening Enterprise** skal du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    en. Klik på **Hent metadata**, og derefter gemme filen på din computer.  Vær klar til at redigere metadatafilen før du overfører til Qlik mening-serveren.

    b. Klik på **Næste**.

5. Forberede sammenslutning Metadata XML-filen, så du kan overføre, til Qlik mening server.

    > [AZURE.NOTE] Før du overfører IdP metadata til Qlik mening-serveren, at filen skal redigeres, hvis du vil fjerne oplysninger til at sikre kan fungere korrekt mellem Azure AD og Qlik mening server.

    ![QlikSense][qs24]

    en. Åbn filen FederationMetadata.XML, der er hentet fra Azure i et tekstredigeringsprogram.

    b. Søg efter værdien **RoleDescriptor**.  Der vil være fire poster (to par åbne og lukke elementmærker).

    c. Slette ind imellem RoleDescriptor mærker og alle oplysninger fra filen.

    d. Gem filen, og hold det i nærheden, som bruges senere i dette dokument.

6. Gå til Qlik mening Qlik Management Console (QMC) som en bruger, der kan oprette virtuelle proxy konfigurationer.

7. Klik på menupunktet virtuelle Proxy i QMC.

    ![QlikSense][qs6] 

8. Klik på knappen Opret nyt nederst på skærmen.

    ![QlikSense][qs7]

9. Vises virtuel proxy Rediger skærmbilledet.  Er en menu til at oprette konfigurationsindstillinger synlig i højre side af skærmen.

    ![QlikSense][qs9]

10. Angiv identifikationsoplysninger til Azure virtuelle proxykonfiguration med menuindstillingen identifikation markeret.

    ![QlikSense][qs8]  
    
    en. Feltet Beskrivelse er et fuldt navn til den virtuelle proxykonfiguration.  Indtast en værdi for en beskrivelse.
    
    b. Feltet præfiks identificerer det virtuelle proxy slutpunkt til at oprette forbindelse til Qlik mening med Azure AD Single Sign-On.  Angiv et entydigt præfiks navn for denne virtuelle proxy.

    c. Inaktivitet Sessionstimeout (minutter) er timeout for forbindelser via denne virtuelle proxy.

    d. Session cookie overskriftsnavnet er cookie navnet lagring af session-id for en bruger modtager efter vellykket godkendelse Qlik mening sessionen.  Dette navn skal være entydig.

11. Klik på menupunktet godkendelse til at gøre den synlig.  Skærmbilledet godkendelse vises.

    ![QlikSense][qs10]

    en. **Anonym adgangstilstand** rullemenuen bestemmer, hvis anonyme brugere kan få adgang til Qlik mening via den virtuelle proxyserver.  Standardindstillingen er ingen anonym bruger.

    b. **Godkendelsesmetode** rullemenuen bestemmer godkendelse farveskema virtuelle proxyen bruger.  Vælg SAML på rullelisten.  Flere indstillinger vises som et resultat.

    c. Indtastet hostname brugerne kan angive for at få adgang til Qlik mening via denne virtuelle proxy SAML **SAML host URI felt**.  Værtsnavnet er uri på serveren, Qlik mening.

    d. **SAML enheds-ID**, Angiv den samme værdi, der er indtastet i feltet SAML host URI.

    e. **SAML IdP metadata** er filen redigeres tidligere i afsnittet **Redigere sammenslutning Metadata fra Azure AD-konfiguration** .  **Før du overfører metadata IdP filen skal redigeres** til at fjerne oplysninger for at sikre korrekt funktionalitet mellem Azure AD og Qlik mening server.  **Se vejledningen ovenfor Hvis filen endnu ikke har redigeres.**  Hvis filen er blevet redigeret klikke på knappen Gennemse, og vælg metadatafilen redigeres til at overføre den til den virtuelle proxykonfiguration.

    f. Angiv attributnavnet eller skemareference for attributten SAML, der repræsenterer **bruger-id** Azure AD sender til serveren Qlik mening.  Skema referenceoplysninger er tilgængelige i Azure-app skærme indlæg konfigurationen.  Bruge navneattributten, **angive http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.

    g. Skriv værdien for **bruger directory** , der skal knyttes til brugere, når de godkender til Qlik mening server via Azure AD.  Hårdt værdier skal være omgivet af **kantede parenteser []**.  For at bruge en attribut, der er sendt i Azure AD SAML-program skal du angive navnet på attributten i denne tekst i **uden** kantede parenteser.

    h. **SAML signerende algoritme** angiver service provider (i dette tilfælde Qlik mening server) signering af certifikat til den virtuelle proxykonfiguration.  Hvis Qlik mening server bruger et pålideligt certifikat, der er oprettet ved hjælp af Microsoft udvidet RSA og AES Cryptographic Provider, kan du ændre algoritmen SAML signerende til **SHA 256**.

    Jeg. Sektionen SAML attribut tilknytning gør det muligt for andre attributter som grupper skal sendes til Qlik mening til brug i sikkerhedsregler.

12. Klik på menuen mulighed for at gøre den synlig af belastning.  Skærmbilledet belastning vises.

    ![QlikSense][qs11]

13. Klik på ny server node knappen Tilføj, Vælg program node eller noder Qlik mening sender sessioner til for formål justering af belastning, og klik på knappen Tilføj.

    ![QlikSense][qs12]

14. Klik på menuen Avanceret mulighed for at gøre den synlig. Skærmbilledet Avanceret vises.

    ![QlikSense][qs13]

    en. Listen Host hvid identificerer værtsnavne, der er accepteret, når du opretter forbindelse til serveren Qlik mening.  **Angiv det værtsnavn, brugerne kan angive ved oprettelse af forbindelse til Qlik mening server.** Værtsnavnet er den samme værdi som SAML host uri uden https://.

15. Klik på knappen Anvend.

    ![QlikSense][qs14]

16. Klik på OK for at acceptere den advarselsmeddelelse, der angiver proxyer, der er knyttet til den virtuelle proxy skal genstartes.

    ![QlikSense][qs15]

17. Menuen associeret elementer vises i højre side af skærmen.  Klik på menupunktet proxyer.

    ![QlikSense][qs16]

18. Proxy-startskærmbilledet vises.  Klik på knappen Sammenkæd nederst sammenkæde en proxy til den virtuelle proxy.

    ![QlikSense][qs17]

19. Vælg noden proxy, der understøtter denne virtuelle proxyforbindelse og klikke på knappen Sammenkæd.  Når du sammenkæder, proxyen, vises under tilknyttede proxyer.

    ![QlikSense][qs18]
    ![QlikSense][qs19]

20. Når du om fem til ti sekunder vises meddelelsen opdatere QMC.  Klik på knappen Opdater QMC.

    ![QlikSense][qs20]

21. Når du opdaterer QMC, klik på menupunktet virtuelle proxyer. Den nye SAML virtuelle proxy post er angivet i tabellen på skærmen.  Enkeltklik på posten virtuelle proxy.

    ![QlikSense][qs51]

22. I bunden af skærmen aktiveres knappen Download SP metadata.  Klik på knappen Hent SP metadata for at gemme metadata til en fil.

    ![QlikSense][qs52]

23. Åbn filen sp metadata.  Se posten **enheds-id** og posten **AssertionConsumerService** .  Disse værdier er svarer til **id** , og **Log på URL-adresse** i programkonfiguration Azure AD. Hvis de ikke stemmer overens skal du erstatte dem i konfigurationsguiden Azure AD App.

    ![QlikSense][qs53]

24. Vælge enkelt sign-on – konfiguration bekræftelsen i klassisk-portalen, og klik derefter på **Næste**.
    
    ![Azure AD-Single Sign-On][10]

25. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.  
 
    ![Azure AD-Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Oprette en Azure AD testbruger
I dette afsnit, skal oprette du en testbruger i portalen klassisk kaldet Britta Simon.


![Oprette Azure AD-bruger][20]

**For at oprette en testbruger i Azure AD, skal du udføre følgende trin:**

1. Klik på **Active Directory**i **Azure klassisk portal**, i den venstre navigationsrude.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **brugere**for at få vist listen over brugere, i menuen øverst.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. For at åbne dialogboksen **Tilføj bruger** på værktøjslinjen nederst skal du klikke på **Tilføj bruger**.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. På siden **fortælle os om denne bruger** dialogboksen skal du udføre følgende trin:  ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 

    en. Vælg ny bruger i organisationen som Type af bruger.

    b. Skriv **BrittaSimon**i brugernavn **tekstfelt**.

    c. Klik på **Næste**.

6.  På siden **Brugerprofil** dialogboksen skal du udføre følgende trin: ![oprettelse af en Azure AD testbruger](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 

    en. Skriv **Britta**i tekstfeltet **Fornavn** .  

    b. I **Efternavn** tekstfeltet skal type **Thomas**.

    c. I tekstfeltet **Navn** skal du skrive **Britta Thomas**.

    d. Vælg **bruger,**på listen **rolle** .

    e. Klik på **Næste**.

7. Klik på **Opret**på siden **Hent midlertidige adgangskode** dialogboksen.

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. På siden **få midlertidige adgangskode** dialogboksen skal du udføre følgende trin:

    ![Oprette en Azure AD testbruger](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 

    en. Notér værdien af den **Nye adgangskode**.

    b. Klik på **udført**.   


### <a name="creating-an-qlik-sense-enterprise-test-user"></a>Oprette en Qlik mening Enterprise testbruger

I dette afsnit, skal oprette du en bruger, kaldet Britta Simon i Qlik mening Enterprise. Skal du samarbejde med Qlik mening Enterprise supportteam at føje brugerne i Qlik mening Enterprise-platform.


### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til Qlik mening Enterprise.

![Tildele bruger][200] 

**Hvis du vil tildele Britta Simon til Qlik mening Enterprise, skal du udføre følgende trin:**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **Qlik mening Enterprise**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]


## <a name="testing-single-sign-on"></a>Test af single sign-On

I dette afsnit, skal teste du din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet Access.

Når du klikker på feltet Qlik mening Enterprise i panelet adgang, du bør få automatisk logget på Qlik mening Enterprise-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
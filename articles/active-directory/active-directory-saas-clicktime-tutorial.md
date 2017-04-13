<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med ClickTime | Microsoft Azure" 
    description="Lær, hvordan du bruger ClickTime med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
    services="active-directory" 
    authors="jeevansd"
    documentationCenter="na" 
    manager="femila" />
<tags
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Selvstudium: Azure Active Directory-integration med ClickTime

I dette selvstudium lærer du at integrere ClickTime med Azure Active Directory (Azure AD).

Integrering af ClickTime med Azure AD giver dig følgende fordele:

- Du kan styre i Azure AD, der har adgang til ClickTime
- Du kan aktivere dine brugere til automatisk få logget på til ClickTime (Single Sign-On) med deres Azure AD-konti
- Du kan administrere dine konti på ét centralt sted - portalen Azure klassisk

Hvis du vil vide flere oplysninger om SaaS app integration med Azure AD, skal du se [Hvad er adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil konfigurere Azure AD-integration med ClickTime skal bruge du følgende elementer:

- Et Azure AD-abonnement
- ClickTime enkelt-logger på aktiveret abonnement


> [AZURE.NOTE] Hvis du vil afprøve trinnene i dette selvstudium skal anbefaler vi ikke ved hjælp af et produktionsmiljø.


Hvis du vil afprøve trinnene i dette selvstudium, skal du følge disse anbefalinger:

- Du skal ikke bruge dit produktionsmiljø, medmindre det er nødvendigt.
- Hvis du ikke har et prøveabonnement Azure AD-miljø, kan du få en én måned prøveversion [her](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenarie beskrivelse
I dette selvstudium, kan du teste Azure AD enkeltlogon i et testmiljø.

Dette scenario, der er beskrevet i dette selvstudium består af to primære dokumentkomponenter:

1. Tilføje ClickTime fra galleriet
2. Konfigurere og teste Azure AD single sign-on –

##<a name="adding-clicktime-from-the-gallery"></a>Tilføje ClickTime fra galleriet

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for ClickTime.

###<a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>For at aktivere programmet integration for ClickTime skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-clicktime-tutorial/tic700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-clicktime-tutorial/tic749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-clicktime-tutorial/tic749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **ClickTime**.

    ![Galleri med programmet] (./media/active-directory-saas-clicktime-tutorial/tic777275.png "Galleri med programmet")

7.  Vælg **ClickTime**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![ClickTime] (./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurere og teste Azure AD single sign-on –
I dette afsnit, skal du konfigurere og teste Azure AD single sign-on – med ClickTime baseret på en testbruger ved navn "Britta Thomas".

Azure AD skal se, hvad brugeren modstykke i ClickTime er til en bruger i Azure AD for single sign-on til arbejde. Med andre ord, skal et link forholdet mellem en Azure AD-bruger og den pågældende bruger i ClickTime oprettes.

Denne link relation er etableret ved at tildele værdien af det **brugernavn** i Azure AD som værdien i feltet **brugernavn** i ClickTime.

For at konfigurere og teste Azure AD single sign-on – med ClickTime, skal du udføre følgende grundpillerne:

1. **[Konfiguration af Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – til at aktivere dine brugere at bruge denne funktion.
2. **[Oprette en Azure AD teste bruger](#creating-an-azure-ad-test-user)** - teste Azure AD single sign-on – med Britta Simon.
3. **[Oprette en ClickTime teste bruger](#creating-a-clicktime-test-user)** - skal have et modstykke Britta Simon i ClickTime, der er knyttet til Azure AD-repræsentation af hende.
4. **[Tildele Azure AD teste bruger](#assigning-the-azure-ad-test-user)** – til at aktivere Britta Simon bruge Azure AD enkeltlogon.
5. **[Test af Single Sign-On](#testing-single-sign-on)** – til at kontrollere, om konfigurationen fungerer.


### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguration af Azure AD enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende ClickTime med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  


>[AZURE.IMPORTANT] For at kunne konfigurere single sign-on på din ClickTime lejer, skal du kontakte først ClickTime teknisk support for at få denne funktion er aktiveret.

**Hvis du vil konfigurere Azure AD single sign-on – med ClickTime skal du udføre følgende trin:**

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **ClickTime** application integration.

    ![Aktivere enkeltlogon] (./media/active-directory-saas-clicktime-tutorial/tic777277.png "Aktivere enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge ClickTime** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-clicktime-tutorial/tic777278.png "Konfigurere Enkeltlogon")

3. På siden **Konfigurer App indstillinger** dialogboksen du udføre følgende trin:

    ![Konfigurere Single Sign-On](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 

    en. I tekstfeltet **IdentifierL** skal du skrive URL-adressen ved hjælp af følgende mønster: **https://app.clicktime.com/sp/**
    
    b. Skriv URL-adressen ved hjælp af følgende mønster i tekstfeltet **Svar URL-adresse** : **https://app.clicktime.com/Login/**

    c. Klik på **Næste**

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på ClickTime** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-clicktime-tutorial/tic777279.png "Konfigurere Enkeltlogon")

4.  Log på webstedet ClickTime virksomhed som administrator i et andet browservindue.

5.  Klik på **Indstillinger**på værktøjslinjen øverst, og derefter klikke på **Sikkerhedsindstillinger**.

6.  I sektionen **Indstillinger for enkelt Sign-On** konfiguration skal du udføre følgende trin:

    ![Sikkerhedsindstillinger] (./media/active-directory-saas-clicktime-tutorial/tic777280.png "Sikkerhedsindstillinger")

    en.  Vælg **Tillad** logon ved hjælp af enkelt enkeltlogon (SSO) med **Azure AD**.
    
    b.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på ClickTime** dialogboksen siden, og Indsæt det i tekstfeltet **Identitet udbyder slutpunkt** .

    c.  Åbn base 64 kodet certifikatet i **Notesblok**, kopiere indholdet og derefter indsætte det i tekstfeltet **X.509-certifikat** .
    
    d.  Klik på **Gem**.

7.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-clicktime-tutorial/tic777281.png "Konfigurere Enkeltlogon")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på ClickTime, skal de klargøres til ClickTime.  
Klargøring af er ClickTime, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **ClickTime** lejer.

2.  Skal du klikke på **firma**på værktøjslinjen øverst, og klik derefter på **personer**.

    ![Personer] (./media/active-directory-saas-clicktime-tutorial/tic777282.png "Personer")

3.  Klik på **Tilføj Person**.

    ![Tilføje Person] (./media/active-directory-saas-clicktime-tutorial/tic777283.png "Tilføje Person")

4.  I sektionen ny Person skal du udføre følgende trin:

    ![Personer] (./media/active-directory-saas-clicktime-tutorial/tic777284.png "Personer")

    en.  I tekstfeltet **mailadresse** skal du skrive mailadressen til din Azure AD-konto.
    
    b.  I tekstfeltet **fulde navn** skal du skrive navnet på din Azure AD-konto.  

    >[AZURE.NOTE] Hvis du vil, kan du angive yderligere egenskaber for det nye person-objekt.

    c.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden ClickTime bruger konto værktøjer til oprettelse eller API'er leveres af ClickTime til at klargøre Azure AD-brugerkonti.

### <a name="assigning-the-azure-ad-test-user"></a>Tildele Azure AD test brugeren

I dette afsnit, skal aktivere du Britta Simon bruge Azure enkeltlogon ved at give adgang til ClickTime.

![Tildele bruger][200]

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

**Hvis du vil tildele Britta Simon til ClickTime, skal du udføre følgende trin**

1. I klassisk Portal, for at åbne visningen programmer i visningen directory, ved at klikke på **programmer** i den øverste menu.

    ![Tildele bruger][201] 

2. Vælg **ClickTime**i listen over programmer.

    ![Konfigurere Single Sign-On](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 

3. Klik på **brugere**i menuen øverst.

    ![Tildele bruger][203]

4. Vælg **Britta Thomas**på listen brugere.

5. Klik på **Tildel**på værktøjslinjen nederst.

    ![Tildele bruger][205]

## <a name="testing-single-sign-on"></a>Test af enkeltlogon
I dette afsnit, skal teste du din Azure AD enkelt sign-on – konfiguration ved hjælp af panelet adgang.

Når du klikker på feltet ClickTime i panelet adgang, du bør få automatisk logget på ClickTime-program.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png
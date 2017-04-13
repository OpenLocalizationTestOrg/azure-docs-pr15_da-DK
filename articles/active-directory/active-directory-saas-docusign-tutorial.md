<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med DocuSign | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og DocuSign."
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
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Selvstudium: Azure Active Directory-integration med DocuSign

Formålet med dette selvstudium er at vise integration af Azure og DocuSign.
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

- Et gyldigt Azure-abonnement
- En lejer i DocuSign



Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1. [Aktivere programmet integrationen for DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Konfigurere Enkeltlogon](#configuring-single-sign-on) 


3. [Konfiguration af konto klargøring](#configuring-account-provisioning) 


4. [Tildele brugere](#assigning-users) 

    ![Konfigurere Enkeltlogon][0]
 

## <a name="enabling-the-application-integration-for-docusign"></a>Aktivere programmet integrationen for DocuSign

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for DocuSign.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>For at aktivere programmet integration for DocuSign skal du udføre følgende trin:

1. Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Konfigurere Enkeltlogon][1]

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen adresseliste.

3. For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Konfigurere Enkeltlogon][2]

4. Klik på **Tilføj** i bunden af siden.

    ![Programmer][3]

5. Hvad du vil gøre dialogboksen skal du klikke på **Tilføj et program i galleriet**.

    ![Konfigurere Enkeltlogon][4]


6. Skriv **DocuSign**i søgefeltet.

    ![Konfigurere Enkeltlogon][5]

7. Vælg **DocuSign**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Konfigurere Enkeltlogon][6]


## <a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende DocuSign med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.


### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1. Klik på **Konfigurer single sign-on –** for at åbne dialogboksen Konfigurer Single Sign-On i Azure klassisk portalen, på siden **DocuSign programmet integration** .

    ![Konfigurere Enkeltlogon][7]

2. Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge DocuSign** , og klik derefter på Næste.

    ![Konfigurere Enkeltlogon][8]

3. På siden **Konfigurer App indstillinger** ved at udføre følgende trin:

    ![Konfigurere Enkeltlogon][61]

    en. I tekstfeltet **logge på URL-adresse** skal du skrive `https://account.docusign.com/*`.  

    b. Skriv i tekstfeltet **id** `https://account.docusign.com/*`.  
   
    c. Klik på **Næste**. 


    > [AZURE.TIP] Log på URL-adressen og id-værdier er kun pladsholdere. Vejledning i, hvordan til at hente de faktiske værdier for dit miljø er dækket senere i dette emne.
 

4. Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på DocuSign** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Enkeltlogon][10]


5. Log på din **DocuSign admin-portalen** som administrator i et andet browservindue.


6. Klik på **domæner**i navigationsmenuen i venstre side.

    ![Konfigurere Enkeltlogon][51]

7. Klik på **Kræv domæne**i højre rude.

    ![Konfigurere Enkeltlogon][52]

8. Skriv dit firmadomæne på dialogboksen **Kræv et domæne** , i tekstboksen **Domænenavn** , og klik derefter på **gør krav**. Sørg for, at du har bekræftet domænet og status er aktiv.

    ![Konfigurere Enkeltlogon][53]

9. I menuen til venstre, skal du klikke på **Identitetsudbydere**  

    ![Konfigurere Enkeltlogon][54]

10. Klik på **Tilføj identitetsudbyder**i højre rude. 
    
    ![Konfigurere Enkeltlogon][55]

11. Udfør følgende trin på siden **Indstillinger for identitet udbyder** :

    ![Konfigurere Enkeltlogon][56]


    en. I tekstfeltet **navn** skal du skrive et entydigt navn til din konfiguration. Brug ikke mellemrum.

    b. Kopiér udsteder URL-adressen på Azure-klassisk portalen, og Indsæt det i tekstfeltet **Identitet udbyder udsteder** .

    c. Kopiere **Remote Login URL-adressen**på Azure-klassisk portalen, og Indsæt det i tekstfeltet **Identitet udbyder Login URL-adresse** .

    d. Kopiere **Remote log URL-adressen**på Azure-klassisk portalen, og Indsæt det i tekstfeltet **Identitet udbyder log URL-adresse** .

    e. Vælg **Log AuthN anmodning**.

    f. **Sende AuthN anmodning ved**at vælge **INDLÆG**.

    g. **Send anmodning om Log af**, skal du markere **INDLÆG**. 


12. Vælg det felt, du vil tilknytte med Azure AD krav i sektionen **Brugerdefinerede attributter tilknytning** . I dette eksempel er tilknyttet **mailadresse** kravet med værdien af **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Dette er Kræv standardnavnet fra Azure AD for mail krav. 

    > [AZURE.NOTE] Brug den relevante **bruger-id** til at tilknytte brugeren fra Azure AD Docusign Brugertilknytning. Vælg feltet stort, og Angiv den relevante værdi baseret på organisationens indstillinger.

    ![Konfigurere Enkeltlogon][57]

13. Klik på **Tilføj certifikat**i sektionen **Identitet udbyder certifikat** , og overfør derefter det certifikat, du har hentet fra Azure AD klassisk portal.   

    ![Konfigurere Enkeltlogon][58]

14. Klik på **Gem**.

15. Klik på **Handlinger**i afsnittet **Identitetsudbydere** , og klik derefter på **slutpunkterne**.   

    ![Konfigurere Enkeltlogon][59]



10. Gå tilbage til siden **Konfigurer App-indstillinger** i Azure klassisk Portal. 

16. **DocuSign admin-portalen**, i sektionen **Vis SAML 2.0 slutpunkter** udføre, følgende trin:

    ![Konfigurere Enkeltlogon][60]

    en. Kopiere **Service Provider udsteder URL-adressen**, og Indsæt det i tekstfeltet **id** på Azure klassisk-portalen.

    b. Kopiere **Service Provider Login URL-adressen**, og sæt ind i tekstfeltet **Log på URL-adressen** på Azure klassisk-portalen.

    c.  Klik på **Luk**  


10. Azure klassisk portalen, klik på **Næste**. 


15. Vælge **enkelt sign-on – konfiguration bekræftelse**på Azure klassisk-portalen, og klik derefter på **Næste**.

    ![Programmer][14]

10. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.

    ![Programmer][15]
 

## <a name="configuring-account-provisioning"></a>Konfiguration af konto klargøring

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugerklargøring af Active Directory-brugerkonti til DocuSign.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1. Klik på **Konfigurer konto klargøring** for at åbne dialogboksen Konfigurer brugerklargøring i **Azure klassisk portal**, på siden **DocuSign programmet integration** .

    ![Konfiguration af konto klargøring][30]

2. Angive legitimationsoplysninger for en DocuSign konto med rettigheder for at aktivere automatisk bruger klargøring, på siden **Indstillinger og Administratoroplysninger** , og klik derefter på **Næste**. 

    ![Konfiguration af konto klargøring][31]

3. Klik på **Start test**dialogboksen **Test forbindelsen** , og på en vellykket test, skal du klikke på **Næste**.

    ![Konfiguration af konto klargøring][32]

3. Klik på **fuldført**på siden **Confirmation** .

    ![Konfiguration af konto klargøring][33]
 

## <a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>Hvis du vil tildele brugere til DocuSign, skal du udføre følgende trin:

1. Oprette en testkonto i **Azure klassisk portal**.

2. Klik på **Tildel brugere**på siden **DocuSign programmet integration** .

    ![Tildele brugere][40]
 

3. Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Tildele brugere][41]


Du bør nu vente 10 minutter og bekræfte, at kontoen er blevet synkroniseret med DocuSign.

Du kan kontrollere klargøring status, ved at klikke på Dashboard i D på siden DocuSign application integration på portalen Azure klassisk som et første trin i bekræftelse.

![Tildele brugere][42]

En korrekt færdige bruger klargøring cyklus er angivet med en relateret status:

![Tildele brugere][43]


Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang.

Se Introduktion til panelet adgang kan finde flere oplysninger om panelet adgang.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png
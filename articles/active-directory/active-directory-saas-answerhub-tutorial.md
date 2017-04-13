<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med AnswerHub | Microsoft Azure" 
    description="Lær, hvordan du bruger AnswerHub med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Selvstudium: Azure Active Directory-integration med AnswerHub

Formålet med dette selvstudium er at vise integration af Azure og [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software).  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til AnswerHub kunne enkelt Log på programmet på webstedet AnswerHub virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for AnswerHub
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scenarie")

##<a name="enabling-the-application-integration-for-answerhub"></a>Aktivere programmet integrationen for AnswerHub

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for AnswerHub.

###<a name="to-enable-the-application-integration-for-answerhub-perform-the-following-steps"></a>For at aktivere programmet integration for AnswerHub skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-answerhub-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-answerhub-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-answerhub-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **AnswerHub**.

    ![Galleri med programmet] (./media/active-directory-saas-answerhub-tutorial/IC785166.png "Galleri med programmet")

7.  Vælg **AnswerHub**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![AnswerHub] (./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende AnswerHub med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **AnswerHub** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-answerhub-tutorial/IC785168.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge AnswerHub** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-answerhub-tutorial/IC785169.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://company.answerhub.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **AnswerHub log i URL-adresse** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-answerhub-tutorial/IC785170.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på AnswerHub** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-answerhub-tutorial/IC785171.png "Konfigurere Enkeltlogon")

5.  Log på webstedet AnswerHub virksomhed som administrator i et andet browservindue.
    >[AZURE.NOTE] Hvis du har brug for hjælp til at konfigurere AnswerHub, kan du kontakte [Answerhubs supportteam](mailto:success@answerhub.com. ).








6.  Gå til **Administration**.

7.  Klik på fanen **bruger og grupper** .

8.  Klik på **Konfiguration af SAML**i navigationsruden i venstre side, i sektionen **Sociale indstillinger** .

9.  Klik på **IDP Config** .

10. På fanen **IDP Config** skal du udføre følgende trin:

    ![SAML konfiguration] (./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML konfiguration")

    1.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på AnswerHub** dialogboksen siden, og Indsæt det i tekstfeltet **IDP Login URL-adresse** .
    2.  Kopiér **URL-Remote log** værdien i Azure klassisk-portalen på **Konfigurer single sign-on – på AnswerHub** dialogboksen siden, og Indsæt det i tekstfeltet **IDP log URL-adresse** .
    3.  Kopiér værdien **Id navneformat** i Azure klassisk-portalen på **Konfigurer single sign-on – på AnswerHub** dialogboksen siden, og Indsæt det i tekstfeltet **IDP navneformat-id** .
    4.  Klik på **nøgler og certifikater**.

11. På fanen nøgler og certifikater udføre følgende trin:

    ![Nøgler og certifikater] (./media/active-directory-saas-answerhub-tutorial/IC785173.png "Nøgler og certifikater")

    1.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    2.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **IDP offentlig nøgle (x 509-Format)** .
    3.  Klik på **Gem**.

12. Klik på **Gem**under fanen **IDP Config** .

13. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-answerhub-tutorial/IC785174.png "Konfigurere Enkeltlogon")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på AnswerHub, skal de klargøres til AnswerHub.  
Klargøring af er AnswerHub, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet **AnswerHub** virksomhed som administrator.

2.  Gå til **Administration**.

3.  Klik på fanen **brugere og grupper** .

4.  Klik på **Opret eller Importér brugere**i navigationsruden i venstre side, i afsnittet **Administrere brugere** .

    ![Brugere og grupper] (./media/active-directory-saas-answerhub-tutorial/IC785175.png "Brugere og grupper")

5.  Skriv den **mailadresse**, **brugernavn** og **adgangskode** til en gyldig Azure Active Directory-konto, du vil klargøre i den relaterede tekstbokse, og klik derefter på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden AnswerHub bruger konto værktøjer til oprettelse eller API'er, som AnswerHub til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-answerhub-perform-the-following-steps"></a>Hvis du vil tildele brugere til AnswerHub, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **AnswerHub **application integration.

    ![Tildele brugere] (./media/active-directory-saas-answerhub-tutorial/IC785176.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-answerhub-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Concur | Microsoft Azure" 
    description="Lær, hvordan du bruger Concur med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-concur"></a>Selvstudium: Azure Active Directory-integration med Concur  


Formålet med dette selvstudium er at vise integration af Azure og Concur.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En lejer i Concur

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Concur
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-concur-tutorial/IC769766.png "Scenarie")

>[AZURE.NOTE] Konfiguration af abonnementet Concur på samlet SSO via SAML er en separat opgave, som skal du kontakte Concur til at udføre.

##<a name="enabling-the-application-integration-for-concur"></a>Aktivere programmet integrationen for Concur

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Concur.

###<a name="to-enable-the-application-integration-for-concur-perform-the-following-steps"></a>For at aktivere programmet integration for Concur skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  Fra listen **adresseliste** , Vælg mappen, vil aktivere katalogintegration.

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-concur-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj en App**for at åbne **Programmet galleriet**, og klik derefter på **Tilføj et program for organisationen at bruge**.

    ![Hvad vil du gøre?] (./media/active-directory-saas-concur-tutorial/IC700995.png "Hvad vil du gøre?")

5.  I **søgefeltet**skal du skrive **Concur**.

    ![Galleri med programmet] (./media/active-directory-saas-concur-tutorial/IC721727.png "Galleri med programmet")

6.  Vælg **Concur**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Tilsammen kan føre] (./media/active-directory-saas-concur-tutorial/IC721728.png "Tilsammen kan føre")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Concur med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

>[AZURE.NOTE] Konfiguration af abonnementet Concur på samlet SSO via SAML er en separat opgave, som skal du kontakte Concur til at udføre.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Concur **application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-concur-tutorial/IC769767.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Concur** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-concur-tutorial/IC769768.png "Konfigurere Enkeltlogon")

3.  Skriv din concur lejer logon URL-adresse, og klik derefter på **Næste**på siden **Konfigurer App URL-adressen** i tekstfeltet **Tilsammen kan føre log i URL-adresse** : 

    ![Konfigurer logge på URL-adresse] (./media/active-directory-saas-concur-tutorial/IC769769.png "Konfigurer logge på URL-adresse")

4.  På siden **Konfigurer single sign-on – på Concur** skal du udføre følgende trin.

    ![Konfigurer logge på URL-adresse] (./media/active-directory-saas-concur-tutorial/IC769770.png "Konfigurer logge på URL-adresse")

    1.  Klik på Hent metadata, og derefter sikre datafilen til din computer.
    2.  Kontakt supportteamet Concur for at konfigurere SSO til din lejer.
    3.  Vælge enkelt sign-on – konfiguration bekræftelsen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .  

    >[AZURE.NOTE] Konfiguration af abonnementet Concur på samlet SSO via SAML er en separat opgave, som skal du kontakte Concur til at udføre.

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

Formålet med dette afsnit er til at disponere hvordan du aktiverer klargøring af Active Directory-brugerkonti til Concur.

Hvis du vil aktivere apps i udgiftstjenesten, der skal være stort installation og brug af en Web Tjenesteadministrator profil. Ikke blot tilføje WS administratorrolle til din eksisterende administratorprofil, du bruger til T & E administrative funktioner.

Tilsammen kan føre konsulenter eller klient administratoren skal oprette en entydige Web tjenesteadministratoren profil og klienten administratoren skal bruge denne profil for funktionerne Web Services-Administrator (fx aktivere apps). Disse profiler skal holdes adskilt fra klient administratorens daglige T & E administratorprofil (profilen T & E-administrator ikke skal have tildelt rollen WSAdmin).

Når du opretter profilen, der skal bruges til at aktivere appen, kan du angive klienten administratorens navn i brugerprofilfelter. Dette vil tildele ejerskab til profilen. Når profil(er) er oprettet, skal klienten logge på med denne profil, klik på knappen "*Aktiver*" for en Partner App i menuen webtjenester.

Af følgende årsager skal denne handling ikke udføres med den profil, der bruges til normal T & E administration.

1.  Klienten skal være det, der klikker på "*Ja*" i dialogvinduet, der vises, når en app er aktiveret. Denne Klik accepterer klienten er villig til Partner program tilladelse til at få adgang til deres data, så du eller Partner ikke kan klikke på knappen Ja.
2.  Hvis en klient-administrator, der har aktiveret en app forlader ved hjælp af T & E administratoren profil virksomheden (resulterer i den profil, der har deaktiveret), nogen apps, der er aktiveret ved hjælp af, at profilen ikke fungerer, indtil appen er aktiveret med en anden aktive WS Admin-profil. Dette er grunden til, at du skal oprette entydige WS administrator profiler.
3.  Hvis en administrator forlader virksomheden, kan det navn, der er knyttet til WS administrator profilen ændres til erstatning administratoren, hvis du ønsker uden at påvirke den aktiverede app fordi profilen ikke behøver deaktiveret

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på din **Concur** lejer.

2.  Vælg **Webtjenester**fra menuen **Administration** .

    ![Concur lejer] (./media/active-directory-saas-concur-tutorial/IC721729.png "Concur lejer")

3.  Vælg **Aktivér Partner program**fra ruden **Webtjenester** i venstre side.

    ![Aktivere Partner program] (./media/active-directory-saas-concur-tutorial/IC721730.png "Aktivere Partner program")

4.  Vælg **Azure Active Directory**listen **Aktivere programmet** , og klik derefter på **Aktivér**.

    ![Microsoft Azure Active Directory] (./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  Klik på **Ja** for at lukke dialogboksen **Bekræft handling** .

    ![Bekræfte handling] (./media/active-directory-saas-concur-tutorial/IC721732.png "Bekræfte handling")

6.  Vælg **Concur** på listen programmer til at åbne siden **Concur** dialogboksen på Azure klassisk portalen.

7.  For at åbne siden i dialogboksen **Konfigurer klargøring af brugeren** , skal du klikke på **Konfigurer brugerklargøring**.

8.  Angiv brugernavnet og adgangskoden til din Concur administrator, og klik derefter på **Næste**.

9.  Klik på knappen **udført** for at afslutte konfigurationen, på siden **Confirmation** .

Du kan nu oprette en testkonto, vent 10 minutter og bekræfte, at kontoen er blevet synkroniseret med Concur.
##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-concur-perform-the-following-steps"></a>Hvis du vil tildele brugere til Concur, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Concur **application integration.

    ![Tildele brugere] (./media/active-directory-saas-concur-tutorial/IC769771.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-concur-tutorial/IC767830.png "Ja")

Du bør nu vente 10 minutter og bekræfte, at kontoen er blevet synkroniseret med Concur.

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
